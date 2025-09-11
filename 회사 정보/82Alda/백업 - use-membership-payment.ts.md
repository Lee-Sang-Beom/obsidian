```ts
import { useCallback, useEffect, useRef, useState } from 'react'  
import { toast } from 'sonner'  
import { useOpenMembershipDialog } from '@/pages/my-page/hooks/modal/use-open-membership-dialog.ts'  
import { useAuthStore } from '@/store/use-auth-store.ts'  
import type { DeviceType } from '@/pages/auth/types'  
import { useQueryClient } from '@tanstack/react-query'  
import { useGetUserInfo } from '@/hooks/use-get-user-info-query.ts'  
import { usePaymentInitiate } from '@/pages/my-page/hooks/action/use-payment-initiate-action.ts'  
import { useVerifyGoogleAction } from '@/pages/my-page/hooks/action/use-verify-google-action.ts'  
import type {  
  BasePlanTypeEnum,  
  InnerPaymentPlanId,  
  PaymentInitiationRequest,  
  PaymentInitiationResponse,  
  PaymentPlatformEnum,  
  PaymentVerificationGoogleRequest,  
  PaymentVerificationIOSRequest,  
  ProductTypeEnum,  
} from '@/pages/my-page/types'  
import { getCurrentMembershipPlan } from '@/pages/my-page/utils.ts'  
import { useGooglePayment } from '@/pages/my-page/hooks/payment/use-google-payment.ts'  
import { sleep } from '@/lib/utils'  
import { useAppStorePayment } from '@/pages/my-page/hooks/payment/use-app-store-payment.ts'  
import { useVerifyAppleAction } from '@/pages/my-page/hooks/action/use-verify-apple-action.ts'  
  
interface PaymentOption {  
  id: InnerPaymentPlanId  
  membershipType: 'PREMIUM' | 'BASIC'  
  plan: 'monthly' | 'semiannually'  
}  
  
interface PurchaseResult {  
  success: boolean  
  status?: 'success' | 'pending' | 'failed' | 'cancelled' | 'restored'  
  message?: string  
  error?: string  
  errorMessage?: string  
  errorCode?: string  
  debug?: any  
  availableProducts?: string[]  
  availableBasePlans?: string[]  
  platform?: string  
  receiptData?: string  
  productId?: string  
  transactionId?: string  
  secretKey?: string  
  fcmToken?: string  
  basePlanId?: string  
}  
  
type SubscriptionChangeType = 'new' | 'upgrade' | 'downgrade' | 'periodChange' | 'change'  
  
// Constants for better maintainability  
const PLATFORM_MAP: Record<DeviceType, PaymentPlatformEnum> = {  
  ANDROID: 'GOOGLE',  
  IOS: 'APPLE',  
  WEB: 'GOOGLE',  
}  
  
const PRODUCT_TYPE_MAP: Record<InnerPaymentPlanId, ProductTypeEnum> = {  
  'premium-monthly': 'PREMIUM',  
  'premium-semiannually': 'PREMIUM',  
  'basic-monthly': 'BASIC',  
  'basic-semiannually': 'BASIC',  
}  
  
const BASE_PLAN_TYPE_MAP: Record<InnerPaymentPlanId, BasePlanTypeEnum> = {  
  'premium-monthly': 'PREMIUM_MONTHLY',  
  'premium-semiannually': 'PREMIUM_SIX_MONTHS',  
  'basic-monthly': 'PREMIUM_MONTHLY',  
  'basic-semiannually': 'PREMIUM_SIX_MONTHS',  
}  
  
const PAYMENT_OPTION_MAP: Record<InnerPaymentPlanId, PaymentOption> = {  
  'premium-monthly': { id: 'premium-monthly', membershipType: 'PREMIUM', plan: 'monthly' },  
  'premium-semiannually': { id: 'premium-semiannually', membershipType: 'PREMIUM', plan: 'semiannually' },  
  'basic-monthly': { id: 'basic-monthly', membershipType: 'BASIC', plan: 'monthly' },  
  'basic-semiannually': { id: 'basic-semiannually', membershipType: 'BASIC', plan: 'semiannually' },  
}  
  
export function useMembershipPayment() {  
  const queryClient = useQueryClient()  
  const { open, onClose: originalOnClose } = useOpenMembershipDialog()  
  
  // State  
  const [deviceType, setDeviceType] = useState<DeviceType>('WEB')  
  const [selectedPaymentOption, setSelectedPaymentOption] = useState<InnerPaymentPlanId | null>(null)  
  const [secretKey, setSecretKey] = useState<string | null>(null)  
  const [isAuthReady, setIsAuthReady] = useState(false)  
  const [isPaymentProcessing, setIsPaymentProcessing] = useState(false)  
  const [authInitialized, setAuthInitialized] = useState(false)  
  const [paymentInitiationResponse, setPaymentInitiationResponse] = useState<PaymentInitiationResponse | null>(null)  
  const [pendingSecondStep, setPendingSecondStep] = useState<{  
    paymentInitiationResponse: PaymentInitiationResponse  
    paymentOption: PaymentOption  
  } | null>(null)  
  
  // Refs for preventing duplicate API calls  
  const paymentProcessingRef = useRef(false)  
  const receiptVerificationRef = useRef(false)  
  const processedReceiptsRef = useRef(new Set<string>())  
  const currentPaymentRequestRef = useRef<string | null>(null)  
  
  // Hooks  
  const { data: user } = useGetUserInfo(open)  
  const paymentInitiateMutation = usePaymentInitiate()  
  const verifyGoogleMutation = useVerifyGoogleAction()  
  const verifyAppleMutation = useVerifyAppleAction()  
  
  // Device-specific payment hooks  
  const isGooglePaymentEnabled = deviceType === 'ANDROID' && !!paymentInitiationResponse?.secretKey  
  const isAppStorePaymentEnabled = deviceType === 'IOS' && !!paymentInitiationResponse?.secretKey  
  
  const {  
    isProcessing: isGoogleProcessing,  
    processingOptionId: googleProcessingOptionId,  
    purchaseProduct: purchaseGoogleProduct,  
    subscriptionUpgrade,  
    subscriptionDowngrade,  
    subscriptionPeriodChange,  
    resetPaymentState: resetGooglePaymentState,  
  } = useGooglePayment({  
    enabled: isGooglePaymentEnabled,  
    paymentInitiationResponse,  
    onPurchaseSuccess: handleSecondStepSuccess,  
    onPurchaseError: (error: string) => handlePaymentFailure(`Google Play 결제 실패: ${error}`),  
    onReceiptVerification: handleReceiptVerification,  
  })  
  
  const {  
    isProcessing: isAppStoreProcessing,  
    processingOptionId: appStoreProcessingOptionId,  
    purchaseProduct: purchaseAppStoreProduct,  
    subscriptionUpgrade: subscriptionIOSUpgrade,  
    subscriptionDowngrade: subscriptionIOSDowngrade,  
    subscriptionPeriodChange: subscriptionIOSPeriodChange,  
    resetPaymentState: resetAppStorePaymentState,  
  } = useAppStorePayment({  
    enabled: isAppStorePaymentEnabled,  
    paymentInitiationResponse,  
    onPurchaseSuccess: handleSecondStepSuccess,  
    onPurchaseError: (error: string) => handlePaymentFailure(`App Store 결제 실패: ${error}`),  
    onReceiptVerification: handleReceiptIOSVerification,  
  })  
  
  // Utility functions  
  const resetAllStates = useCallback(() => {  
    setSelectedPaymentOption(null)  
    setIsPaymentProcessing(false)  
    setPaymentInitiationResponse(null)  
    setPendingSecondStep(null)  
  
    paymentProcessingRef.current = false  
    receiptVerificationRef.current = false  
    processedReceiptsRef.current.clear()  
    currentPaymentRequestRef.current = null  
  
    resetGooglePaymentState?.()  
    resetAppStorePaymentState?.()  
    paymentInitiateMutation.reset?.()  
    verifyGoogleMutation.reset?.()  
  }, [resetGooglePaymentState, resetAppStorePaymentState, paymentInitiateMutation, verifyGoogleMutation])  
  
  const initializeAuth = useCallback(async () => {  
    try {  
      setIsAuthReady(false)  
      setAuthInitialized(false)  
  
      const [type, token] = await Promise.all([useAuthStore.getDeviceType(), useAuthStore.getToken()])  
  
      setDeviceType(type as DeviceType)  
      setSecretKey(token)  
    } catch (error) {  
      setDeviceType('WEB')  
      setSecretKey(null)  
    } finally {  
      setIsAuthReady(true)  
      setAuthInitialized(true)  
    }  
  }, [])  
  
  const getSubscriptionChangeType = (targetOptionId: InnerPaymentPlanId): SubscriptionChangeType => {  
    if (!user?.membershipType || !user?.subscriptionPlan) return 'new'  
  
    const currentPlan = getCurrentMembershipPlan(user.membershipType, user.subscriptionPlan)  
    if (!currentPlan) return 'new'  
  
    // Upgrade: Basic → Premium (same period)  
    if (  
      (currentPlan === 'basic-monthly' && targetOptionId === 'premium-monthly') ||  
      (currentPlan === 'basic-semiannually' && targetOptionId === 'premium-semiannually')  
    ) {  
      return 'upgrade'  
    }  
  
    // Downgrade: Premium → Basic (same period)  
    if (currentPlan === 'premium-monthly' && targetOptionId === 'basic-monthly') {  
      return 'downgrade'  
    }  
  
    // Period change or plan + period change  
    if (  
      (['basic-monthly', 'premium-monthly'].includes(currentPlan) && targetOptionId.includes('semiannually')) ||  
      (currentPlan === 'basic-semiannually' && targetOptionId === 'premium-monthly')  
    ) {  
      return 'periodChange'  
    }  
  
    return 'change'  
  }  
  
  const convertToPaymentInitiationRequest = (optionId: InnerPaymentPlanId): PaymentInitiationRequest => ({  
    platform: PLATFORM_MAP[deviceType],  
    productType: PRODUCT_TYPE_MAP[optionId],  
    basePlanType: BASE_PLAN_TYPE_MAP[optionId],  
  })  
  
  const convertToPaymentOption = (optionId: InnerPaymentPlanId): PaymentOption | null =>  
    PAYMENT_OPTION_MAP[optionId] || null  
  
  const getSubscriptionInfo = (optionId?: InnerPaymentPlanId) => {  
    const planId = optionId || (user && getCurrentMembershipPlan(user.membershipType, user.subscriptionPlan))  
    if (!planId) return { plan: null, period: null, fullPlan: null }  
  
    const plan = planId.includes('basic')  
      ? ('basic' as const)  
      : planId.includes('premium')  
        ? ('premium' as const)  
        : null  
    const period = planId.includes('semiannually')  
      ? ('semiannually' as const)  
      : planId.includes('monthly')  
        ? ('monthly' as const)  
        : null  
  
    return { plan, period, fullPlan: planId }  
  }  
  
  // Event handlers  
  const handlePaymentFailure = useCallback(  
    (errorMessage: string) => {  
      console.error('Payment failed:', errorMessage)  
      resetAllStates()  
    },  
    [resetAllStates],  
  )  
  
  const onClose = () => {  
    originalOnClose(() => resetAllStates())  
  }  
  
  // 결제완료 시 쿼리 무효화  
  const handlePaymentComplete = async () => {  
    try {  
      // 관련 쿼리를 순차적으로 무효화하고 새로 fetch      await queryClient.invalidateQueries({  
        queryKey: ['useGetUserInfo'],  
        refetchType: 'all',  
      })  
  
      await queryClient.invalidateQueries({  
        queryKey: ['useInfiniteSignals'],  
        refetchType: 'all',  
      })  
    } catch (error) {  
      console.error('Failed to invalidate queries after payment:', error)  
    }  
  }  
  
  const executeSecondStep = async (  
    paymentInitiationResponse: PaymentInitiationResponse,  
    paymentOption: PaymentOption,  
  ) => {  
    try {  
      const changeType = getSubscriptionChangeType(paymentOption.id)  
  
      const executePaymentAction = async () => {  
        if (changeType === 'new') {  
          return deviceType === 'ANDROID'  
            ? purchaseGoogleProduct(paymentOption)  
            : purchaseAppStoreProduct(paymentOption)  
        }  
  
        const currentInfo = getSubscriptionInfo()  
        const targetInfo = getSubscriptionInfo(paymentOption.id)  
        const currentPeriod = currentInfo.period || 'monthly'  
  
        const androidActions = {  
          upgrade: subscriptionUpgrade,  
          downgrade: subscriptionDowngrade,  
          periodChange: subscriptionPeriodChange,  
        }  
        const iosActions = {  
          upgrade: subscriptionIOSUpgrade,  
          downgrade: subscriptionIOSDowngrade,  
          periodChange: subscriptionIOSPeriodChange,  
        }  
        const actions = deviceType === 'ANDROID' ? androidActions : iosActions  
  
        switch (changeType) {  
          case 'upgrade':  
            return actions.upgrade(currentPeriod)  
          case 'downgrade':  
            return actions.downgrade()  
          case 'periodChange':  
            if (!currentInfo.plan || !currentInfo.period) {  
              throw new Error('현재 구독 정보를 확인할 수 없습니다.')  
            }  
            return actions.periodChange(currentInfo.plan, targetInfo.plan!, currentInfo.period, targetInfo.period!)  
          default:  
            return deviceType === 'ANDROID'  
              ? purchaseGoogleProduct(paymentOption)  
              : purchaseAppStoreProduct(paymentOption)  
        }  
      }  
  
      if (deviceType === 'WEB') {  
        throw new Error('웹에서는 다른 결제 방식을 이용해주세요.')  
      }  
  
      await executePaymentAction()  
    } catch (error) {  
      const errorMessage = error instanceof Error ? error.message : '2단계 결제 처리 중 오류가 발생했습니다.'  
      handlePaymentFailure(errorMessage)  
    }  
  }  
  
  const handleFirstStepSuccess = async (  
    paymentInitiationResponse: PaymentInitiationResponse,  
    paymentOption: PaymentOption,  
  ) => {  
    try {  
      setPaymentInitiationResponse(paymentInitiationResponse)  
  
      if (!paymentInitiationResponse.secretKey) {  
        throw new Error('1단계 결제에서 secretKey를 받지 못했습니다.')  
      }  
  
      setPendingSecondStep({ paymentInitiationResponse, paymentOption })  
    } catch (error) {  
      const errorMessage = error instanceof Error ? error.message : '1단계 결제 완료 후 처리 중 오류가 발생했습니다.'  
      handlePaymentFailure(errorMessage)  
    }  
  }  
  
  async function handleSecondStepSuccess(_result: PurchaseResult) {  
    // Step 2 success handled automatically by payment hooks  
  }  
  
  async function handleReceiptVerification(result: PurchaseResult) {  
    if (receiptVerificationRef.current) return  
  
    const receiptKey = result.transactionId || result.receiptData || 'unknown'  
    if (processedReceiptsRef.current.has(receiptKey)) return  
  
    try {  
      receiptVerificationRef.current = true  
      processedReceiptsRef.current.add(receiptKey)  
  
      if (!paymentInitiationResponse) {  
        throw new Error('결제 초기화 정보가 없습니다.')  
      }  
  
      // Validate required receipt data  
      if (  
        !result.receiptData ||  
        !result.productId ||  
        !result.basePlanId ||  
        !result.transactionId ||  
        !result.secretKey ||  
        !result.fcmToken  
      ) {  
        throw new Error('필수 영수증 데이터가 누락되었습니다.')  
      }  
  
      if (verifyGoogleMutation.isPending) return  
  
      await sleep(2000)  
      const verificationRequest: PaymentVerificationGoogleRequest = {  
        platform: result.platform as PaymentPlatformEnum,  
        receiptData: result.receiptData,  
        productId: result.productId,  
        basePlanId: result.basePlanId,  
        transactionId: result.transactionId,  
        secretKey: result.secretKey,  
        fcmToken: result.fcmToken,  
      }  
  
      await verifyGoogleMutation.mutateAsync(verificationRequest)  
  
      const changeType = selectedPaymentOption ? getSubscriptionChangeType(selectedPaymentOption) : 'new'  
      const successMessage = changeType === 'new' ? '결제가 완료되었습니다!' : '구독 변경이 완료되었습니다!'  
  
      toast.success(successMessage)  
      resetAllStates()  
      onClose()  
      handlePaymentComplete()  
    } catch (error) {  
      processedReceiptsRef.current.delete(receiptKey)  
      const errorMessage = error instanceof Error ? error.message : '3단계 결제 처리 중 오류가 발생했습니다.'  
      console.error('Receipt verification failed:', error)  
      handlePaymentFailure(errorMessage)  
    } finally {  
      receiptVerificationRef.current = false  
    }  
  }  
  
  async function handleReceiptIOSVerification(result: PurchaseResult) {  
    if (receiptVerificationRef.current) return  
  
    const receiptKey = result.transactionId || result.receiptData || 'unknown'  
    if (processedReceiptsRef.current.has(receiptKey)) return  
  
    try {  
      receiptVerificationRef.current = true  
      processedReceiptsRef.current.add(receiptKey)  
  
      if (!paymentInitiationResponse) {  
        throw new Error('결제 초기화 정보가 없습니다.')  
      }  
  
      // Validate required receipt data  
      if (!result.receiptData || !result.productId || !result.transactionId || !result.secretKey || !result.fcmToken) {  
        throw new Error('필수 영수증 데이터가 누락되었습니다.')  
      }  
  
      if (verifyAppleMutation.isPending) return  
  
      await sleep(2000)  
      const verificationRequest: PaymentVerificationIOSRequest = {  
        platform: result.platform as PaymentPlatformEnum,  
        receiptData: result.receiptData,  
        productId: result.productId,  
        transactionId: result.transactionId,  
        secretKey: result.secretKey,  
        fcmToken: result.fcmToken,  
      }  
  
      await verifyAppleMutation.mutateAsync(verificationRequest)  
      const changeType = selectedPaymentOption ? getSubscriptionChangeType(selectedPaymentOption) : 'new'  
      const successMessage = changeType === 'new' ? '결제가 완료되었습니다!' : '구독 변경이 완료되었습니다!'  
  
      toast.success(successMessage)  
      resetAllStates()  
      onClose()  
      handlePaymentComplete()  
    } catch (error) {  
      processedReceiptsRef.current.delete(receiptKey)  
      const errorMessage = error instanceof Error ? error.message : '3단계 결제 처리 중 오류가 발생했습니다.'  
      console.error('Receipt verification failed:', error)  
      handlePaymentFailure(errorMessage)  
    } finally {  
      receiptVerificationRef.current = false  
    }  
  }  
  
  const handlePayment = async () => {  
    if (paymentProcessingRef.current || !selectedPaymentOption || !isAuthReady || !secretKey) {  
      if (!selectedPaymentOption) toast.error('결제 옵션을 선택해주세요.')  
      else if (!isAuthReady) toast.error('사용자 정보를 불러오고 있습니다. 잠시 후 다시 시도해주세요.')  
      else if (!secretKey) toast.error('사용자 정보를 불러올 수 없습니다. 다시 로그인해주세요.')  
      return  
    }  
  
    const paymentOption = convertToPaymentOption(selectedPaymentOption)  
    if (!paymentOption) {  
      toast.error('잘못된 결제 옵션입니다.')  
      return  
    }  
  
    const requestId = `${selectedPaymentOption}_${Date.now()}_${Math.random().toString(36).substr(2, 9)}`  
    if (currentPaymentRequestRef.current === requestId) return  
  
    try {  
      paymentProcessingRef.current = true  
      currentPaymentRequestRef.current = requestId  
      setIsPaymentProcessing(true)  
  
      if (paymentInitiateMutation.isPending) return  
  
      const paymentInitiationRequest = convertToPaymentInitiationRequest(selectedPaymentOption)  
      const response = await paymentInitiateMutation.mutateAsync(paymentInitiationRequest)  
      await handleFirstStepSuccess(response, paymentOption)  
    } catch (error) {  
      const errorMessage = error instanceof Error ? error.message : '1단계 결제 처리 중 오류가 발생했습니다.'  
      handlePaymentFailure(errorMessage)  
    } finally {  
      paymentProcessingRef.current = false  
      currentPaymentRequestRef.current = null  
      setIsPaymentProcessing(false)  
    }  
  }  
  
  const isPaymentButtonDisabled = () => {  
    return (  
      !selectedPaymentOption ||  
      !authInitialized ||  
      !isAuthReady ||  
      !secretKey ||  
      paymentProcessingRef.current ||  
      receiptVerificationRef.current ||  
      isPaymentProcessing ||  
      isGoogleProcessing ||  
      isAppStoreProcessing ||  
      paymentInitiateMutation.isPending ||  
      verifyGoogleMutation.isPending ||  
      !!pendingSecondStep  
    )  
  }  
  
  const getPaymentButtonText = () => {  
    if (!authInitialized) return '초기화 중...'  
    if (!isAuthReady) return '사용자 정보 확인 중...'  
    if (!selectedPaymentOption) return '결제 옵션을 선택해주세요'  
    if (!secretKey) return '로그인이 필요합니다'  
    if (paymentInitiateMutation.isPending || paymentProcessingRef.current) return '1단계 처리 중...'  
    if (pendingSecondStep) return '2단계 준비 중...'  
    if (isGoogleProcessing || isAppStoreProcessing) return '2단계 처리 중...'  
    if (verifyGoogleMutation.isPending || receiptVerificationRef.current) return '3단계 처리 중...'  
    if (isPaymentProcessing) return '결제 처리 중...'  
  
    const isCurrentOptionProcessing =  
      selectedPaymentOption === googleProcessingOptionId || selectedPaymentOption === appStoreProcessingOptionId  
  
    if (isCurrentOptionProcessing) return '결제 처리 중...'  
  
    const changeType = selectedPaymentOption ? getSubscriptionChangeType(selectedPaymentOption) : 'new'  
    const actionText = changeType === 'new' ? '결제하기' : '구독 변경하기'  
  
    const deviceActionMap = {  
      ANDROID: `Google Play로 ${actionText}`,  
      IOS: `App Store로 ${actionText}`,  
      WEB: actionText,  
    }  
  
    return deviceActionMap[deviceType] || actionText  
  }  
  
  const isAnyPaymentProcessing = () => {  
    return (  
      paymentProcessingRef.current ||  
      receiptVerificationRef.current ||  
      isPaymentProcessing ||  
      isGoogleProcessing ||  
      isAppStoreProcessing ||  
      paymentInitiateMutation.isPending ||  
      verifyGoogleMutation.isPending ||  
      !!pendingSecondStep  
    )  
  }  
  
  // Effects  
  useEffect(() => {  
    initializeAuth()  
  }, [open, initializeAuth])  
  
  useEffect(() => {  
    if (paymentInitiationResponse?.secretKey && pendingSecondStep && !paymentProcessingRef.current) {  
      executeSecondStep(pendingSecondStep.paymentInitiationResponse, pendingSecondStep.paymentOption)  
      setPendingSecondStep(null)  
    }  
  }, [paymentInitiationResponse?.secretKey, pendingSecondStep])  
  
  return {  
    isAnyPaymentProcessing,  
    selectedPaymentOption,  
    setSelectedPaymentOption,  
    handlePayment,  
    getPaymentButtonText,  
    isPaymentButtonDisabled,  
    onClose,  
  }  
}
```
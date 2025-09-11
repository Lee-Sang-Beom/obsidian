```ts
import { useCallback, useEffect, useRef, useState } from 'react'  
import { toast } from 'sonner'  
import {  
  GOOGLE_BASIC_MONTHLY_PLAN_ID,  
  GOOGLE_BASIC_SEMI_PLAN_ID,  
  GOOGLE_PREMIUM_MONTHLY_PLAN_ID,  
  GOOGLE_PREMIUM_SEMI_PLAN_ID,  
  GOOGLE_PRODUCT_ID,  
} from '../../data'  
import { useInfoLogger } from '@/hooks/log/use-info-logger.ts'  
  
interface PaymentInfo {  
  productId: string  
  productType: 'subs'  
  basePlanId: string  
  secretKey: string  
  currentProductId?: string  
  currentBasePlanId?: string  
  purchaseToken?: string | null  
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
  
interface PaymentOption {  
  id: string  
  membershipType: 'PREMIUM' | 'BASIC'  
  plan: 'monthly' | 'semiannually'  
}  
  
interface PaymentInitiationResponse {  
  platform: string  
  productId: string  
  basePlanId: string  
  secretKey: string  
  receiptData: string  
}  
  
interface SubscriptionChangeConfig {  
  currentProductId: string  
  currentBasePlanId: string  
  targetProductId: string  
  targetBasePlanId: string  
  description: string  
}  
  
interface UseGooglePaymentOptions {  
  onPurchaseSuccess: (result: PurchaseResult) => void  
  onPurchaseError?: (error: string) => void  
  onReceiptVerification: (receiptData: PurchaseResult) => void  
  enabled?: boolean  
  paymentInitiationResponse?: PaymentInitiationResponse | null  
}  
  
interface UseGooglePaymentReturn {  
  isProcessing: boolean  
  processingOptionId: string | null  
  purchaseProduct: (option: PaymentOption) => Promise<void>  
  subscriptionUpgrade: (currentPlan?: 'monthly' | 'semiannually') => Promise<void>  
  subscriptionDowngrade: () => Promise<void>  
  subscriptionPeriodChange: (  
    fromPlan: 'basic' | 'premium',  
    toPlan: 'basic' | 'premium',  
    fromPeriod: 'monthly' | 'semiannually',  
    toPeriod: 'monthly' | 'semiannually',  
  ) => Promise<void>  
  isReady: boolean  
  resetPaymentState: () => void  
}  
  
// 상수 및 유틸리티 - 기존 매핑 (fallback용)  
const PLAN_MAP = {  
  'basic-monthly': GOOGLE_BASIC_MONTHLY_PLAN_ID,  
  'basic-semiannually': GOOGLE_BASIC_SEMI_PLAN_ID,  
  'premium-monthly': GOOGLE_PREMIUM_MONTHLY_PLAN_ID,  
  'premium-semiannually': GOOGLE_PREMIUM_SEMI_PLAN_ID,  
}  
  
const GOOGLE_PRODUCT_MAP = {  
  'premium-monthly': { productId: GOOGLE_PRODUCT_ID, basePlanId: GOOGLE_PREMIUM_MONTHLY_PLAN_ID },  
  'premium-semiannually': { productId: GOOGLE_PRODUCT_ID, basePlanId: GOOGLE_PREMIUM_SEMI_PLAN_ID },  
  'basic-monthly': { productId: GOOGLE_PRODUCT_ID, basePlanId: GOOGLE_BASIC_MONTHLY_PLAN_ID },  
  'basic-semiannually': { productId: GOOGLE_PRODUCT_ID, basePlanId: GOOGLE_BASIC_SEMI_PLAN_ID },  
}  
  
// 멤버십 타입과 기간에 따른 플랜 ID 매핑 (fallback용)  
const getMembershipPlanId = (membershipType: 'PREMIUM' | 'BASIC', plan: 'monthly' | 'semiannually'): string => {  
  const planKey =  
    `${membershipType.toLowerCase()}-${plan === 'semiannually' ? 'semiannually' : 'monthly'}` as keyof typeof PLAN_MAP  
  return PLAN_MAP[planKey]  
}  
  
const getGoogleProductInfo = (option: PaymentOption) => GOOGLE_PRODUCT_MAP[option.id as keyof typeof GOOGLE_PRODUCT_MAP]  
  
const isGooglePlatform = (result: PurchaseResult) => !result.platform || result.platform === 'GOOGLE'  
  
export function useGooglePayment({  
  onPurchaseSuccess,  
  onPurchaseError,  
  onReceiptVerification,  
  enabled = true,  
  paymentInitiationResponse,  
}: UseGooglePaymentOptions): UseGooglePaymentReturn {  
  // State & Refs  
  const [isProcessing, setIsProcessing] = useState(false)  
  const [processingOptionId, setProcessingOptionId] = useState<string | null>(null)  
  const isActiveRef = useRef(enabled)  
  
  const loggerMutation = useInfoLogger()  
  
  // Reset 함수  
  const resetPaymentState = useCallback(() => {  
    setIsProcessing(false)  
    setProcessingOptionId(null)  
  }, [])  
  
  // 에러 처리 헬퍼  
  const handleError = useCallback(  
    (message: string, action = '결제') => {  
      toast.error(`${action} 요청이 실패했습니다.`)  
  
      onPurchaseError?.(message)  
      resetPaymentState()  
      throw new Error(message)  
    },  
    [onPurchaseError, resetPaymentState],  
  )  
  
  // 유효성 검사 헬퍼  
  const validatePaymentConditions = useCallback(  
    (additionalChecks?: () => boolean) => {  
      if (!enabled) {  
        handleError('Google Payment가 비활성화되어 있습니다.')  
        return false  
      }  
      if (isProcessing) {  
        toast.warning('이미 결제가 진행 중입니다.')  
        return false  
      }  
      if (!paymentInitiationResponse?.secretKey) {  
        handleError('결제 정보를 불러올 수 없습니다. 다시 시도해주세요.')  
        return false  
      }  
  
      return !(additionalChecks && !additionalChecks())  
    },  
    [enabled, isProcessing, paymentInitiationResponse?.secretKey, handleError],  
  )  
  
  // 결제 API 호출  
  const callPaymentApi = useCallback(  
    async (paymentInfo: PaymentInfo, actionType = '구매') => {  
      if (!window.flutter_inappwebview?.callHandler) {  
        handleError('앱 환경에서만 Google Play 결제가 가능합니다.', actionType)  
        return  
      }  
  
      // 테스트  
      await loggerMutation.mutateAsync({ message: JSON.stringify(paymentInfo) })  
  
      try {  
        const result: PurchaseResult = await window.flutter_inappwebview.callHandler('requestPayment', paymentInfo)  
  
        if (!result.success) {  
          const errorMessage = result.error || result.errorMessage || `${actionType} 요청에 실패했습니다.`  
          handleError(errorMessage, actionType)  
        }  
      } catch (error) {  
        const errorMessage = error instanceof Error ? error.message : `${actionType} 중 오류가 발생했습니다.`  
        handleError(errorMessage, actionType)  
      }  
    },  
    [handleError, loggerMutation],  
  )  
  
  // 구독 변경 실행  
  const executeSubscriptionChange = useCallback(  
    async (config: SubscriptionChangeConfig) => {  
      if (!validatePaymentConditions()) return  
  
      setIsProcessing(true)  
      setProcessingOptionId(config.targetBasePlanId)  
  
      // 구독전환 Payment Info      const paymentInfo: PaymentInfo = {  
        productId: config.targetProductId,  
        productType: 'subs',  
        basePlanId: config.targetBasePlanId,  
        secretKey: paymentInitiationResponse!.secretKey,  
  
        // currentProductId: config.currentProductId,  
        // currentBasePlanId: config.currentBasePlanId,        // purchaseToken: paymentInitiationResponse?.receiptData || null,      }  
  
      try {  
        await callPaymentApi(paymentInfo, '구독 전환')  
      } catch (error) {  
        console.error('Subscription change failed:', error)  
      }  
    },  
    [validatePaymentConditions, callPaymentApi, paymentInitiationResponse],  
  )  
  
  // 메인 함수들  
  const purchaseProduct = useCallback(  
    async (option: PaymentOption) => {  
      // PaymentInitiationResponse가 있으면 우선 사용, 없으면 기존 방식 사용  
      let productInfo: { productId: string; basePlanId: string } | undefined  
  
      if (paymentInitiationResponse) {  
        productInfo = {  
          productId: paymentInitiationResponse.productId,  
          basePlanId: paymentInitiationResponse.basePlanId,  
        }  
      } else {  
        productInfo = getGoogleProductInfo(option)  
      }  
  
      if (!productInfo) {  
        toast.error('지원하지 않는 결제 옵션입니다.')  
        return  
      }  
  
      if (!validatePaymentConditions()) return  
  
      setIsProcessing(true)  
      setProcessingOptionId(option.id)  
  
      // 최초구매 Payment Info      const paymentInfo: PaymentInfo = {  
        productId: productInfo.productId,  
        productType: 'subs',  
        basePlanId: productInfo.basePlanId,  
        secretKey: paymentInitiationResponse!.secretKey,  
      }  
  
      try {  
        await callPaymentApi(paymentInfo)  
      } catch (error) {  
        console.error('Purchase failed:', error)  
      }  
    },  
    [validatePaymentConditions, callPaymentApi, paymentInitiationResponse],  
  )  
  
  const subscriptionUpgrade = useCallback(  
    async (currentPlan: 'monthly' | 'semiannually' = 'monthly') => {  
      // PaymentInitiationResponse가 있으면 우선 사용  
      const targetProductId = paymentInitiationResponse?.productId || GOOGLE_PRODUCT_ID  
      const targetBasePlanId = paymentInitiationResponse?.basePlanId || getMembershipPlanId('PREMIUM', currentPlan)  
  
      const config: SubscriptionChangeConfig = {  
        currentProductId: GOOGLE_PRODUCT_ID,  
        currentBasePlanId: getMembershipPlanId('BASIC', currentPlan),  
        targetProductId,  
        targetBasePlanId,  
        description: `베이직 ${currentPlan === 'monthly' ? '월간' : '6개월'}에서 프리미엄 ${currentPlan === 'monthly' ? '월간' : '6개월'}으로 업그레이드`,  
      }  
  
      await executeSubscriptionChange(config)  
    },  
    [executeSubscriptionChange, paymentInitiationResponse],  
  )  
  
  const subscriptionDowngrade = useCallback(async () => {  
    // PaymentInitiationResponse가 있으면 우선 사용  
    const targetProductId = paymentInitiationResponse?.productId || GOOGLE_PRODUCT_ID  
    const targetBasePlanId = paymentInitiationResponse?.basePlanId || getMembershipPlanId('BASIC', 'monthly')  
  
    const config: SubscriptionChangeConfig = {  
      currentProductId: GOOGLE_PRODUCT_ID,  
      currentBasePlanId: getMembershipPlanId('PREMIUM', 'monthly'),  
      targetProductId,  
      targetBasePlanId,  
      description: '프리미엄 월간에서 베이직 월간으로 다운그레이드',  
    }  
  
    await executeSubscriptionChange(config)  
  }, [executeSubscriptionChange, paymentInitiationResponse])  
  
  const subscriptionPeriodChange = useCallback(  
    async (  
      fromPlan: 'basic' | 'premium',  
      toPlan: 'basic' | 'premium',  
      fromPeriod: 'monthly' | 'semiannually',  
      toPeriod: 'monthly' | 'semiannually',  
    ) => {  
      // PaymentInitiationResponse가 있으면 우선 사용  
      const targetProductId = paymentInitiationResponse?.productId || GOOGLE_PRODUCT_ID  
      const targetBasePlanId =  
        paymentInitiationResponse?.basePlanId ||  
        getMembershipPlanId(toPlan.toUpperCase() as 'BASIC' | 'PREMIUM', toPeriod)  
  
      const config: SubscriptionChangeConfig = {  
        currentProductId: GOOGLE_PRODUCT_ID,  
        currentBasePlanId: getMembershipPlanId(fromPlan.toUpperCase() as 'BASIC' | 'PREMIUM', fromPeriod),  
        targetProductId,  
        targetBasePlanId,  
        description: `${fromPlan} ${fromPeriod}에서 ${toPlan} ${toPeriod}로 구독 변경`,  
      }  
  
      await executeSubscriptionChange(config)  
    },  
    [executeSubscriptionChange, paymentInitiationResponse],  
  )  
  
  // 이벤트 핸들러  
  const handleReceiptVerification = useCallback(  
    (receiptData: PurchaseResult) => {  
      if (!enabled || !isActiveRef.current || !isGooglePlatform(receiptData)) return  
  
      resetPaymentState()  
      onReceiptVerification?.(receiptData)  
    },  
    [enabled, onReceiptVerification, resetPaymentState],  
  )  
  
  const handlePurchaseResult = useCallback(  
    (result: PurchaseResult) => {  
      if (!enabled || !isActiveRef.current || !isGooglePlatform(result)) return  
  
      const productName = result.productId || '상품'  
  
      switch (result.status) {  
        case 'success':  
          onPurchaseSuccess?.(result)  
          if (result.receiptData && result.transactionId) {  
            handleReceiptVerification(result)  
          }  
          break  
  
        case 'pending':  
          toast.info(`Google 구매 대기 중: ${productName} - ${result.message || '결제를 처리하고 있습니다'}`)  
          onPurchaseSuccess?.(result)  
          break  
  
        case 'failed': {  
          // const failMessage = `Google 구매 실패: ${productName} - ${result.errorMessage || '알 수 없는 오류'} ${result.errorCode ? `(코드: ${result.errorCode})` : ''}`  
          toast.error('구매 과정에서 오류가 발생했습니다.')  
          resetPaymentState()  
          onPurchaseError?.(result.errorMessage || '구매에 실패했습니다.')  
          break  
        }  
  
        case 'cancelled':  
          toast.info(`Google 구매 취소: ${productName} - ${result.message || '사용자가 결제를 취소했습니다'}`)  
          resetPaymentState()  
          break  
  
        case 'restored':  
          toast.success(  
            `Google 구매 복원: ${productName} ${result.transactionId ? `(거래 ID: ${result.transactionId})` : ''}`,  
          )  
          onPurchaseSuccess?.(result)  
          if (result.receiptData && result.transactionId) {  
            handleReceiptVerification(result)  
          }  
          break  
  
        default:  
          onPurchaseSuccess?.(result)  
          if (result.receiptData && result.transactionId) {  
            handleReceiptVerification(result)  
          }  
      }  
    },  
    [enabled, onPurchaseSuccess, onPurchaseError, handleReceiptVerification, resetPaymentState],  
  )  
  
  // Effects  
  useEffect(() => {  
    isActiveRef.current = enabled  
    if (!enabled) resetPaymentState()  
  }, [enabled, resetPaymentState])  
  
  useEffect(() => {  
    if (typeof window === 'undefined' || !enabled) return  
  
    const existingPurchaseHandler = window.handlePurchaseResult  
    const existingReceiptHandler = window.handleReceiptVerification  
  
    const safeCallHandler = (handler: any, data: any, handlerName: string) => {  
      if (handler && handler !== (handlerName === 'purchase' ? handlePurchaseResult : handleReceiptVerification)) {  
        try {  
          handler(data)  
        } catch (error) {  
          console.error(`기존 ${handlerName} 핸들러 호출 중 오류:`, error)  
        }  
      }  
    }  
  
    window.handlePurchaseResult = (result: PurchaseResult) => {  
      safeCallHandler(existingPurchaseHandler, result, 'purchase')  
      handlePurchaseResult(result)  
    }  
  
    window.handleReceiptVerification = (receiptData: PurchaseResult) => {  
      safeCallHandler(existingReceiptHandler, receiptData, 'receipt')  
      handleReceiptVerification(receiptData)  
    }  
  
    return () => {  
      window.handlePurchaseResult = existingPurchaseHandler  
      window.handleReceiptVerification = existingReceiptHandler  
    }  
  }, [handlePurchaseResult, handleReceiptVerification, enabled])  
  
  useEffect(() => {  
    isActiveRef.current = true  
    return () => {  
      isActiveRef.current = false  
      resetPaymentState()  
    }  
  }, [resetPaymentState])  
  
  return {  
    isProcessing: enabled ? isProcessing : false,  
    processingOptionId,  
    purchaseProduct,  
    subscriptionUpgrade,  
    subscriptionDowngrade,  
    subscriptionPeriodChange,  
    isReady: !!paymentInitiationResponse?.secretKey && enabled,  
    resetPaymentState,  
  }  
}  
  
declare global {  
  interface Window {  
    flutter_inappwebview?: {  
      callHandler: (method: string, ...args: any[]) => Promise<any>  
    }  
    handlePurchaseResult?: (result: PurchaseResult) => void  
    handleReceiptVerification?: (result: PurchaseResult) => void  
  }  
}
```
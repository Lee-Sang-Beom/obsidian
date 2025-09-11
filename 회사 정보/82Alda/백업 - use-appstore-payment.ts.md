```ts
import { useCallback, useEffect, useRef, useState } from 'react'  
import { toast } from 'sonner'  
import {  
  APPLE_BASIC_MONTHLY_PRODUCT_ID,  
  APPLE_BASIC_6MONTHS_PRODUCT_ID,  
  APPLE_PREMIUM_MONTHLY_PRODUCT_ID,  
  APPLE_PREMIUM_6MONTHS_PRODUCT_ID,  
} from '../../data'  
import { useInfoLogger } from '@/hooks/log/use-info-logger.ts'  
  
// ==================== Types ====================  
interface PaymentInfo {  
  productId: string  
  productType: 'subs'  
  secretKey: string  
  currentProductId?: string  
  currentBasePlanId?: string | null  
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
  basePlanId?: string | null // App Store는 null일 수 있음  
  secretKey: string  
  receiptData: string  
}  
  
interface SubscriptionChangeConfig {  
  currentProductId: string  
  currentBasePlanId: string | null  
  targetProductId: string  
  targetBasePlanId: string | null  
  description: string  
}  
  
interface UseAppStorePaymentOptions {  
  onPurchaseSuccess: (result: PurchaseResult) => void  
  onPurchaseError?: (error: string) => void  
  onReceiptVerification: (receiptData: PurchaseResult) => void  
  enabled?: boolean  
  paymentInitiationResponse?: PaymentInitiationResponse | null  
}  
  
interface UseAppStorePaymentReturn {  
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
  
// ==================== Constants ====================  
const PRODUCT_ID_MAP = {  
  'premium-monthly': { productId: APPLE_PREMIUM_MONTHLY_PRODUCT_ID, basePlanId: 'monthly' },  
  'premium-semiannually': { productId: APPLE_PREMIUM_6MONTHS_PRODUCT_ID, basePlanId: 'semiannually' },  
  'basic-monthly': { productId: APPLE_BASIC_MONTHLY_PRODUCT_ID, basePlanId: 'monthly' },  
  'basic-semiannually': { productId: APPLE_BASIC_6MONTHS_PRODUCT_ID, basePlanId: 'semiannually' },  
} as const  
  
const SUBSCRIPTION_PRODUCT_MAP = {  
  'basic-monthly': APPLE_BASIC_MONTHLY_PRODUCT_ID,  
  'basic-semiannually': APPLE_BASIC_6MONTHS_PRODUCT_ID,  
  'premium-monthly': APPLE_PREMIUM_MONTHLY_PRODUCT_ID,  
  'premium-semiannually': APPLE_PREMIUM_6MONTHS_PRODUCT_ID,  
} as const  
  
const BASE_PLAN_MAP = {  
  monthly: 'monthly',  
  semiannually: 'semiannually',  
} as const  
  
// ==================== Utility Functions ====================  
const getAppStoreProductInfo = (option: PaymentOption) => {  
  return PRODUCT_ID_MAP[option.id as keyof typeof PRODUCT_ID_MAP]  
}  
  
const createSubscriptionChangeConfig = (  
  fromPlan: 'basic' | 'premium',  
  toPlan: 'basic' | 'premium',  
  fromPeriod: 'monthly' | 'semiannually',  
  toPeriod: 'monthly' | 'semiannually',  
): SubscriptionChangeConfig => {  
  const fromKey = `${fromPlan}-${fromPeriod}` as keyof typeof SUBSCRIPTION_PRODUCT_MAP  
  const toKey = `${toPlan}-${toPeriod}` as keyof typeof SUBSCRIPTION_PRODUCT_MAP  
  
  return {  
    currentProductId: SUBSCRIPTION_PRODUCT_MAP[fromKey],  
    currentBasePlanId: BASE_PLAN_MAP[fromPeriod],  
    targetProductId: SUBSCRIPTION_PRODUCT_MAP[toKey],  
    targetBasePlanId: BASE_PLAN_MAP[toPeriod],  
    description: `${fromPlan} ${fromPeriod}에서 ${toPlan} ${toPeriod}로 구독 변경`,  
  }  
}  
  
const handleError = (error: unknown, actionType: string, onError?: (error: string) => void) => {  
  const errorMessage = error instanceof Error ? error.message : `${actionType} 중 오류가 발생했습니다.`  
  toast.error(`${actionType} 요청 오류: ${errorMessage}`)  
  onError?.(errorMessage)  
  throw error  
}  
  
const validateEnvironment = (enabled: boolean, onError?: (error: string) => void) => {  
  if (!enabled) {  
    const errorMessage = 'App Store Payment가 비활성화되어 있습니다.'  
    toast.error(errorMessage)  
    onError?.(errorMessage)  
    throw new Error(errorMessage)  
  }  
  
  if (!window.flutter_inappwebview?.callHandler) {  
    const errorMessage = '앱 환경에서만 App Store 결제가 가능합니다.'  
    toast.info(errorMessage)  
    onError?.(errorMessage)  
    throw new Error(errorMessage)  
  }  
}  
  
// ==================== Main Hook ====================  
export function useAppStorePayment({  
  onPurchaseSuccess,  
  onPurchaseError,  
  onReceiptVerification,  
  enabled = true,  
  paymentInitiationResponse,  
}: UseAppStorePaymentOptions): UseAppStorePaymentReturn {  
  // ==================== State & Refs ====================  
  const [isProcessing, setIsProcessing] = useState(false)  
  const [processingOptionId, setProcessingOptionId] = useState<string | null>(null)  
  const isActiveRef = useRef(enabled)  
  
  const loggerMutation = useInfoLogger()  
  
  // ==================== State Management ====================  
  const resetAllAppStoreStates = useCallback(() => {  
    setIsProcessing(false)  
    setProcessingOptionId(null)  
  }, [])  
  
  const validatePaymentState = () => {  
    if (isProcessing) {  
      toast.warning('이미 결제가 진행 중입니다.')  
      return false  
    }  
  
    if (!paymentInitiationResponse?.secretKey) {  
      toast.error('결제 정보를 불러올 수 없습니다. 다시 시도해주세요.')  
      return false  
    }  
  
    return true  
  }  
  
  // ==================== Core Payment Logic ====================  
  const purchaseProductWithBasePlan = async (  
    productId: string,  
    productType: 'subs',  
    basePlanId: string | null,  
    secretKeyForPayment: string,  
    currentProductId?: string,  
    currentBasePlanId?: string | null,  
  ): Promise<void> => {  
    const isSubscriptionChange = !!currentProductId  
    const actionType = isSubscriptionChange ? '구독 전환' : '구매'  
  
    validateEnvironment(enabled, onPurchaseError)  
  
    const paymentInfo: PaymentInfo = {  
      productId,  
      productType,  
      secretKey: secretKeyForPayment,  
    }  
  
    if (isSubscriptionChange) {  
      // TODO: currentProductId 사용 시 주석해제  
      // paymentInfo.currentProductId = currentProductId  
      // TODO: currentBasePlanId 사용 시 주석해제  
      // paymentInfo.currentBasePlanId = currentBasePlanId  
      // TODO: receiptData를 사용 시 주석해제: 구독 변경 시에만 paymentInitiationResponse의 receiptData를 purchaseToken으로 사용  
      // paymentInfo.purchaseToken = paymentInitiationResponse?.receiptData || null  
    }  
  
    // 테스트  
    await loggerMutation.mutateAsync({ message: JSON.stringify(paymentInfo) })  
  
    try {  
      const result: PurchaseResult = await window.flutter_inappwebview!.callHandler('requestPayment', paymentInfo)  
  
      if (!result.success) {  
        const errorMessage = result.error || result.errorMessage || `${actionType} 요청에 실패했습니다.`  
        toast.error(`${actionType} 요청 실패: ${errorMessage}`)  
        onPurchaseError?.(errorMessage)  
        throw new Error(errorMessage)  
      }  
    } catch (error) {  
      handleError(error, actionType, onPurchaseError)  
    }  
  }  
  
  const executeSubscriptionChange = async (config: SubscriptionChangeConfig): Promise<void> => {  
    if (!validatePaymentState()) return  
  
    setIsProcessing(true)  
    setProcessingOptionId(config.targetProductId)  
  
    try {  
      await purchaseProductWithBasePlan(  
        config.targetProductId,  
        'subs',  
        config.targetBasePlanId,  
        paymentInitiationResponse!.secretKey,  
        config.currentProductId,  
        config.currentBasePlanId,  
      )  
    } catch (error) {  
      console.error('Subscription change failed:', error)  
      resetAllAppStoreStates()  
    }  
  }  
  
  // ==================== Public Methods ====================  
  const purchaseProduct = async (option: PaymentOption): Promise<void> => {  
    if (!enabled) {  
      toast.error('App Store Payment가 비활성화되어 있습니다.')  
      return  
    }  
  
    if (!validatePaymentState()) return  
  
    // PaymentInitiationResponse가 있으면 우선 사용, 없으면 기존 방식 사용  
    let productInfo: { productId: string; basePlanId: string | null } | undefined  
  
    if (paymentInitiationResponse) {  
      productInfo = {  
        productId: paymentInitiationResponse.productId,  
        basePlanId: paymentInitiationResponse.basePlanId || null, // 앱스토어는 planId를 사용하지 않아, 1단계 api에서 null로 기본으로 내려줌  
      }  
    } else {  
      const fallbackInfo = getAppStoreProductInfo(option)  
      productInfo = fallbackInfo  
        ? {  
            productId: fallbackInfo.productId,  
            basePlanId: fallbackInfo.basePlanId,  
          }  
        : undefined  
    }  
  
    if (!productInfo) {  
      toast.error('지원하지 않는 결제 옵션입니다.')  
      return  
    }  
  
    setIsProcessing(true)  
    setProcessingOptionId(option.id)  
  
    try {  
      await purchaseProductWithBasePlan(  
        productInfo.productId,  
        'subs',  
        productInfo.basePlanId,  
        paymentInitiationResponse!.secretKey,  
      )  
    } catch (error) {  
      console.error('Purchase failed:', error)  
      resetAllAppStoreStates()  
    }  
  }  
  
  const subscriptionUpgrade = async (currentPlan: 'monthly' | 'semiannually' = 'monthly'): Promise<void> => {  
    // PaymentInitiationResponse가 있으면 우선 사용  
    let targetProductId: string  
    let targetBasePlanId: string | null = BASE_PLAN_MAP[currentPlan]  
  
    if (paymentInitiationResponse) {  
      targetProductId = paymentInitiationResponse.productId  
      // 서버에서 내려준 basePlanId를 그대로 사용 (null일 수 있음)  
      targetBasePlanId = paymentInitiationResponse.basePlanId || null  
    } else {  
      targetProductId = currentPlan === 'monthly' ? APPLE_PREMIUM_MONTHLY_PRODUCT_ID : APPLE_PREMIUM_6MONTHS_PRODUCT_ID  
    }  
  
    const config: SubscriptionChangeConfig = {  
      currentProductId: currentPlan === 'monthly' ? APPLE_BASIC_MONTHLY_PRODUCT_ID : APPLE_BASIC_6MONTHS_PRODUCT_ID,  
      currentBasePlanId: BASE_PLAN_MAP[currentPlan],  
      targetProductId,  
      targetBasePlanId,  
      description:  
        currentPlan === 'monthly'  
          ? '베이직 월간에서 프리미엄 월간으로 업그레이드'  
          : '베이직 6개월에서 프리미엄 6개월로 업그레이드',  
    }  
  
    await executeSubscriptionChange(config)  
  }  
  
  const subscriptionDowngrade = async (): Promise<void> => {  
    // PaymentInitiationResponse가 있으면 우선 사용  
    let targetProductId: string = APPLE_BASIC_MONTHLY_PRODUCT_ID  
    let targetBasePlanId: string | null = 'monthly'  
  
    if (paymentInitiationResponse) {  
      targetProductId = paymentInitiationResponse.productId  
      // 서버에서 내려준 basePlanId를 그대로 사용 (null일 수 있음)  
      targetBasePlanId = paymentInitiationResponse.basePlanId || null  
    }  
  
    const config: SubscriptionChangeConfig = {  
      currentProductId: APPLE_PREMIUM_MONTHLY_PRODUCT_ID,  
      currentBasePlanId: 'monthly',  
      targetProductId,  
      targetBasePlanId,  
      description: '프리미엄 월간에서 베이직 월간으로 다운그레이드',  
    }  
  
    await executeSubscriptionChange(config)  
  }  
  
  const subscriptionPeriodChange = async (  
    fromPlan: 'basic' | 'premium',  
    toPlan: 'basic' | 'premium',  
    fromPeriod: 'monthly' | 'semiannually',  
    toPeriod: 'monthly' | 'semiannually',  
  ): Promise<void> => {  
    // PaymentInitiationResponse가 있으면 우선 사용  
    let targetProductId: string  
    let targetBasePlanId: string | null = BASE_PLAN_MAP[toPeriod]  
  
    if (paymentInitiationResponse) {  
      targetProductId = paymentInitiationResponse.productId  
      // 서버에서 내려준 basePlanId를 그대로 사용 (null일 수 있음)  
      targetBasePlanId = paymentInitiationResponse.basePlanId || null  
    } else {  
      const toKey = `${toPlan}-${toPeriod}` as keyof typeof SUBSCRIPTION_PRODUCT_MAP  
      targetProductId = SUBSCRIPTION_PRODUCT_MAP[toKey]  
    }  
  
    const fromKey = `${fromPlan}-${fromPeriod}` as keyof typeof SUBSCRIPTION_PRODUCT_MAP  
    const config: SubscriptionChangeConfig = {  
      currentProductId: SUBSCRIPTION_PRODUCT_MAP[fromKey],  
      currentBasePlanId: BASE_PLAN_MAP[fromPeriod],  
      targetProductId,  
      targetBasePlanId,  
      description: `${fromPlan} ${fromPeriod}에서 ${toPlan} ${toPeriod}로 구독 변경`,  
    }  
  
    await executeSubscriptionChange(config)  
  }  
  
  // ==================== Event Handlers ====================  
  const handleReceiptVerification = useCallback(  
    (receiptData: PurchaseResult) => {  
      if (!enabled || !isActiveRef.current || (receiptData.platform && receiptData.platform !== 'APPLE')) {  
        return  
      }  
  
      resetAllAppStoreStates()  
      onReceiptVerification?.(receiptData)  
    },  
    [enabled, onReceiptVerification, resetAllAppStoreStates],  
  )  
  
  const handlePurchaseResult = useCallback(  
    (result: PurchaseResult) => {  
      if (!enabled || !isActiveRef.current || (result.platform && result.platform !== 'APPLE')) {  
        return  
      }  
  
      const { status, productId, message, errorMessage, errorCode, receiptData, transactionId } = result  
  
      switch (status) {  
        case 'success':  
          onPurchaseSuccess?.(result)  
          if (receiptData && transactionId) {  
            handleReceiptVerification(result)  
          }  
          break  
  
        case 'pending':  
          toast.info(`App Store 구매 대기 중: ${productId || '상품'} - ${message || '결제를 처리하고 있습니다'}`)  
          onPurchaseSuccess?.(result)  
          break  
  
        case 'failed': {  
          const failMessage = `App Store 구매 실패: ${productId || '상품'} - ${errorMessage || '알 수 없는 오류'} ${errorCode ? `(코드: ${errorCode})` : ''}`  
          toast.error(failMessage)  
          resetAllAppStoreStates()  
          onPurchaseError?.(errorMessage || '구매에 실패했습니다.')  
          break  
        }  
  
        case 'cancelled':  
          toast.info(`App Store 구매 취소: ${productId || '상품'} - ${message || '사용자가 결제를 취소했습니다'}`)  
          resetAllAppStoreStates()  
          break  
  
        case 'restored':  
          toast.success(  
            `App Store 구매 복원: ${productId || '상품'} ${receiptData} ${transactionId ? `(거래 ID: ${transactionId})` : ''}`,  
          )  
          onPurchaseSuccess?.(result)  
          if (receiptData && transactionId) {  
            handleReceiptVerification(result)  
          }  
          break  
  
        default:  
          onPurchaseSuccess?.(result)  
          if (receiptData && transactionId) {  
            handleReceiptVerification(result)  
          }  
      }  
    },  
    [enabled, onPurchaseSuccess, onPurchaseError, handleReceiptVerification, resetAllAppStoreStates],  
  )  
  
  // ==================== Effects ====================  
  useEffect(() => {  
    isActiveRef.current = true  
    return () => {  
      isActiveRef.current = false  
      resetAllAppStoreStates()  
    }  
  }, [resetAllAppStoreStates])  
  
  useEffect(() => {  
    isActiveRef.current = enabled  
  }, [enabled])  
  
  useEffect(() => {  
    if (typeof window === 'undefined' || !enabled) return  
  
    const existingPurchaseHandler = window.handlePurchaseResult  
    const existingReceiptHandler = window.handleReceiptVerification  
  
    window.handlePurchaseResult = (result: PurchaseResult) => {  
      if (existingPurchaseHandler && existingPurchaseHandler !== handlePurchaseResult) {  
        try {  
          existingPurchaseHandler(result)  
        } catch (error) {  
          console.error('기존 handlePurchaseResult 호출 중 오류:', error)  
        }  
      }  
      handlePurchaseResult(result)  
    }  
  
    window.handleReceiptVerification = (receiptData: PurchaseResult) => {  
      if (existingReceiptHandler && existingReceiptHandler !== handleReceiptVerification) {  
        try {  
          existingReceiptHandler(receiptData)  
        } catch (error) {  
          console.error('기존 handleReceiptVerification 호출 중 오류:', error)  
        }  
      }  
      handleReceiptVerification(receiptData)  
    }  
  
    return () => {  
      if (window.handlePurchaseResult) {  
        window.handlePurchaseResult = existingPurchaseHandler  
      }  
      if (window.handleReceiptVerification) {  
        window.handleReceiptVerification = existingReceiptHandler  
      }  
    }  
  }, [handlePurchaseResult, handleReceiptVerification, enabled])  
  
  // ==================== Return ====================  
  return {  
    isProcessing: enabled ? isProcessing : false,  
    processingOptionId,  
    purchaseProduct,  
    subscriptionUpgrade,  
    subscriptionDowngrade,  
    subscriptionPeriodChange,  
    isReady: !!paymentInitiationResponse?.secretKey && enabled,  
    resetPaymentState: resetAllAppStoreStates,  
  }  
}  
  
// ==================== Global Types ====================  
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
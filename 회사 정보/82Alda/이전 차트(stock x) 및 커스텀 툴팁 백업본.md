>`<StockChartCustomTooltip/>`
```tsx
import { Badge } from '@/components/ui/badge'  
import { type CustomTooltipState, TRADING_STYLE_KO_MAP } from '@/pages/signal/types'  
import { cn, formatPrice } from '@/lib/utils.ts'  
import { signalTypeColors, tradingStyleColors } from '@/pages/signal/constants.ts'  
  
interface StockChartCustomTooltipProps {  
  customTooltip: CustomTooltipState  
}  
  
export default function StockChartCustomTooltip({ customTooltip }: StockChartCustomTooltipProps) {  
  const data = customTooltip.data!  
  const isBuyType = data.signalType === 'BUY'  
  const showUpwardArrow = customTooltip.transform?.includes('translateY(-100%)')  
  
  // -3% 가격 계산  
  const minPrice = Math.round(data.price * 0.97)  
  const maxPrice = data.price  
  
  const formattedMinPrice = formatPrice(minPrice, data.currencyCode)  
  const formattedMaxPrice = formatPrice(maxPrice, data.currencyCode)  
  
  // 가격 텍스트 길이 확인 (대략적인 임계값 설정)  
  const priceText = `${formattedMinPrice} ~ ${formattedMaxPrice}`  
  const shouldVerticalLayout = priceText.length > 17 // 필요 시 임계값 조정  
  
  return (  
    <div  
      className="pointer-events-none fixed z-50"  
      style={{  
        left: customTooltip.x,  
        top: customTooltip.y,  
        transform: customTooltip.transform || 'translateX(-50%) translateY(-100%)',  
      }}  
    >  
      {/* 메인 툴팁 */}  
      <div className="w-[190px] max-w-[200px] overflow-hidden rounded-lg border border-gray-200 bg-white shadow-lg dark:border-gray-700 dark:bg-gray-800">  
        {/* 헤더 */}  
        <header className="border-b border-gray-100 px-3 py-2 dark:border-gray-700">  
          <div className="flex items-center justify-between gap-2">  
            <div className="flex items-center gap-0.5">  
              <Badge className={signalTypeColors[isBuyType ? 'BUY' : 'SELL']}>{isBuyType ? '매수' : '매도'}</Badge>  
              <Badge variant="outline" className={tradingStyleColors[data.tradingStyle]}>  
                {TRADING_STYLE_KO_MAP[data.tradingStyle]}  
              </Badge>  
            </div>          </div>          <p className="mt-1 text-[12px] font-semibold">{data.strategy}</p>  
        </header>  
        {/* 상세 정보 */}  
        <main className="px-3 py-2">  
          <div className="space-y-1">  
            {/* 발굴 시각 */}  
            <div className="flex items-start justify-between gap-2">  
              <span className="flex-shrink-0 text-[11px] text-gray-600 dark:text-gray-400">발굴 시각</span>  
              <span className="text-right text-[11px] font-medium break-all text-gray-900 dark:text-gray-100">  
                {data.date}  
              </span>  
            </div>  
            {/* 발굴 가격대 - 조건부 세로 배치 */}  
            <div className="flex items-start justify-between gap-2">  
              <span className="flex-shrink-0 text-[11px] text-gray-600 dark:text-gray-400">발굴 가격대</span>  
              {shouldVerticalLayout ? (  
                <div className="flex flex-col text-right text-[11px] font-medium text-gray-900 dark:text-gray-100">  
                  <span>{formattedMinPrice}</span>  
                  <span>~ {formattedMaxPrice}</span>  
                </div>              ) : (  
                <span className="text-right text-[11px] font-medium break-all text-gray-900 dark:text-gray-100">  
                  {formattedMinPrice} ~ {formattedMaxPrice}  
                </span>  
              )}  
            </div>  
          </div>        </main>      </div>  
      {/* 툴팁 화살표 */}  
      <div  
        className={cn(  
          'absolute left-1/2 h-0 w-0 -translate-x-1/2 transform',  
          showUpwardArrow  
            ? 'top-full border-t-[6px] border-r-[6px] border-l-[6px] border-transparent border-t-gray-200 dark:border-t-gray-700'  
            : 'bottom-full border-r-[6px] border-b-[6px] border-l-[6px] border-transparent border-b-gray-200 dark:border-b-gray-700',  
        )}  
      />  
    </div>  )  
}
```

> `<BackupStockChart/>`
```tsx
'use client'  
  
import React, { useEffect, useRef, useState } from 'react'  
import {  
  CategoryScale,  
  Chart as ChartJS,  
  type ChartOptions,  
  Filler,  
  Legend,  
  LinearScale,  
  LineElement,  
  type Plugin,  
  PointElement,  
  Title,  
  Tooltip,  
} from 'chart.js'  
import { Line } from 'react-chartjs-2'  
import { formatPrice, formatTimeKorean } from '@/lib/utils'  
import { type CustomTooltipState, type SignalResponse, type TimeFrameResponse } from '@/pages/signal/types'  
import moment from 'moment'  
import StockChartCustomTooltip from '@/pages/signal/_components/stock-chart-custom-tooltip.tsx'  
  
ChartJS.register(CategoryScale, LinearScale, PointElement, LineElement, Title, Tooltip, Legend, Filler)  
  
interface StockChartProps {  
  id: string  
  data: TimeFrameResponse[] // 변경된 데이터 타입  
  signalPoints: SignalResponse[]  
  height?: number  
  unit: string  
  totalMaxValue: number  
}  
  
// 툴팁 위치 계산 함수 - 뷰포트 안전성 보장  
const calculateTooltipPosition = (clickX: number, clickY: number) => {  
  const tooltipWidth = 160  
  const tooltipHeight = 140  
  const margin = 16  
  
  const viewportWidth = window.innerWidth  
  const viewportHeight = window.innerHeight  
  
  let finalX = clickX  
  let finalY = clickY - 10  
  let transform = 'translateX(-50%) translateY(-100%)'  
  
  // 수평 위치 조정  
  if (clickX - tooltipWidth / 2 < margin) {  
    finalX = margin + tooltipWidth / 2  
    transform = 'translateX(-50%) translateY(-100%)'  
  } else if (clickX + tooltipWidth / 2 > viewportWidth - margin) {  
    finalX = viewportWidth - margin - tooltipWidth / 2  
    transform = 'translateX(-50%) translateY(-100%)'  
  }  
  
  // 수직 위치 조정  
  if (clickY - tooltipHeight - 20 < margin) {  
    finalY = clickY + 20  
    transform = transform.replace('translateY(-100%)', 'translateY(0%)')  
  }  
  
  if (finalY + tooltipHeight > viewportHeight - margin) {  
    finalY = viewportHeight - margin - tooltipHeight  
    transform = transform.replace('translateY(-100%)', 'translateY(0%)').replace('translateY(0%)', 'translateY(0%)')  
  }  
  
  return { x: finalX, y: finalY, transform }  
}  
  
// 시간 포맷팅 함수 - ISO 문자열을 처리  
const formatTime = (timeString: string) => {  
  return moment(timeString).format('M월 D일 HH시 mm분')  
}  
// 차트 레이블 포맷팅 함수  
const formatChartLabel = (timeString: string) => {  
  const date = new Date(timeString)  
  return `${date.getHours().toString().padStart(2, '0')}:${date.getMinutes().toString().padStart(2, '0')}`  
}  
  
export default function BackupStockChart({  
  id,  
  data,  
  signalPoints,  
  height = 200,  
  unit = 'USD',  
  totalMaxValue,  
}: StockChartProps) {  
  const chartRef = useRef<ChartJS<'line'>>(null)  
  const containerRef = useRef<HTMLDivElement>(null)  
  const [signalPositions, setSignalPositions] = useState<  
    Array<{  
      id: string  
      x: number  
      y: number  
      signal: SignalResponse  
    }>  
  >([])  
  
  // 다크 모드 감지  
  const [isDark, setIsDark] = useState(false)  
  
  useEffect(() => {  
    const checkDarkMode = () => {  
      setIsDark(document.documentElement.classList.contains('dark'))  
    }  
  
    checkDarkMode()  
    const observer = new MutationObserver(checkDarkMode)  
    observer.observe(document.documentElement, {  
      attributes: true,  
      attributeFilter: ['class'],  
    })  
  
    return () => observer.disconnect()  
  }, [])  
  
  // 커스텀 툴팁 상태 관리  
  const [customTooltip, setCustomTooltip] = useState<CustomTooltipState & { transform?: string }>({  
    visible: false,  
    x: 0,  
    y: 0,  
    data: null,  
    transform: 'translateX(-50%) translateY(-100%)',  
  })  
  
  // 데이터 유효성 검사  
  const isDataEmpty = !data || data.length === 0  
  const hasOnlyOneDataPoint = data && data.length === 1  
  
  // close 값들로 min/max 계산  
  const values = data.map(d => d.close)  
  const minValue = Math.min(...values) * 0.98  
  const maxValue = totalMaxValue !== 0 ? totalMaxValue * 1.02 : Math.max(...values) * 1.02  
  
  // 시그널 포인트 좌표 계산 함수 - TimeFrameResponse 데이터와 매칭  
  const getSignalPointCoordinates = (signal: SignalResponse, chart: ChartJS<'line'>) => {  
    const {  
      scales: { x, y },  
    } = chart  
    const signalTime = new Date(signal.signalTime)  
  
    // 가장 가까운 시간의 데이터 포인트 찾기  
    let closestIndex = 0  
    let minTimeDiff = Infinity  
  
    data.forEach((d, index) => {  
      const dataTime = new Date(d.time)  
      const timeDiff = Math.abs(signalTime.getTime() - dataTime.getTime())  
      if (timeDiff < minTimeDiff) {  
        minTimeDiff = timeDiff  
        closestIndex = index  
      }  
    })  
  
    // 시그널 포인트는 실제 가격 값이 아닌 시그널의 closingPrice를 사용  
    const actualPrice = signal.closingPrice  
  
    return {  
      xPos: x.getPixelForValue(closestIndex),  
      yPos: y.getPixelForValue(actualPrice),  
      dataIndex: closestIndex,  
    }  
  }  
  
  // 시그널 포인트 클릭 핸들러  
  const handleSignalPointClick = (point: SignalResponse, event: React.MouseEvent) => {  
    event.preventDefault()  
    event.stopPropagation()  
  
    // 기존 툴팁이 같은 시그널 포인트인 경우 토글  
    if (customTooltip.visible && customTooltip.data && customTooltip.data.date === formatTime(point.signalTime)) {  
      setCustomTooltip(prev => ({  
        ...prev,  
        visible: false,  
        x: 0,  
        y: 0,  
        data: null,  
      }))  
      return  
    }  
  
    // 뷰포트 안전 위치 계산  
    const safePosition = calculateTooltipPosition(event.clientX, event.clientY)  
  
    setCustomTooltip({  
      visible: true,  
      x: safePosition.x,  
      y: safePosition.y,  
      transform: safePosition.transform,  
      data: {  
        date: formatTimeKorean(point.signalTime),  
        price: point.closingPrice,  
        lowPrice: point.lowPrice || -1, // -1이면 null값  
        highPrice: point.highPrice || -1, // -1이면 null값  
        currencyCode: point.currencyCode,  
        lowPriceTime: point.lowPriceTime || '',  
        highPriceTime: point.highPriceTime || '',  
        signalType: point.signalType,  
        strategy: point.strategyName,  
        tradingStyle: point.tradingStyle,  
      },  
    })  
  }  
  
  // 툴팁 닫기 핸들러  
  const handleCloseTooltip = () => {  
    setCustomTooltip(prev => ({  
      ...prev,  
      visible: false,  
      x: 0,  
      y: 0,  
      data: null,  
    }))  
  }  
  
  // 시그널 포인트 위치 업데이트  
  useEffect(() => {  
    if (chartRef.current && !isDataEmpty) {  
      const positions = signalPoints.map(signal => {  
        const coords = getSignalPointCoordinates(signal, chartRef.current!)  
        return {  
          id: `${id}_${coords.xPos}_${coords.yPos}`,  
          x: coords.xPos,  
          y: coords.yPos,  
          signal,  
        }  
      })  
      setSignalPositions(positions)  
    }  
  }, [data, signalPoints, isDark, isDataEmpty])  
  
  // 차트 리사이즈 시 시그널 포인트 위치 재계산  
  useEffect(() => {  
    const handleResize = () => {  
      if (chartRef.current && !isDataEmpty) {  
        setTimeout(() => {  
          const positions = signalPoints.map(signal => {  
            const coords = getSignalPointCoordinates(signal, chartRef.current!)  
            return {  
              id: `${id}_${coords.xPos}_${coords.yPos}`,  
              x: coords.xPos,  
              y: coords.yPos,  
              signal,  
            }  
          })  
          setSignalPositions(positions)  
        }, 100)  
      }  
    }  
  
    window.addEventListener('resize', handleResize)  
    return () => window.removeEventListener('resize', handleResize)  
  }, [signalPoints, data, isDataEmpty])  
  
  // 가격 텍스트 박스 위치 계산 함수  
  const calculateTextBoxPosition = (xPos: number, yPos: number, textWidth: number, chartWidth: number) => {  
    const padding = 6  
    const bgWidth = textWidth + padding * 2  
    const margin = 10 // 캔버스 가장자리에서의 최소 거리  
  
    let textX = xPos // 텍스트 중심 X 좌표  
    let bgX = xPos - bgWidth / 2 // 배경 박스 왼쪽 X 좌표  
  
    // 왼쪽 끝에 너무 가까운 경우  
    if (bgX < margin) {  
      bgX = margin  
      textX = bgX + bgWidth / 2  
    }  
    // 오른쪽 끝에 너무 가까운 경우  
    else if (bgX + bgWidth > chartWidth - margin) {  
      bgX = chartWidth - margin - bgWidth  
      textX = bgX + bgWidth / 2  
    }  
  
    return { textX, bgX }  
  }  
  
  // 시그널 포인트를 그리는 플러그인 - 세련된 디자인으로 개선  
  const signalPointPlugin: Plugin<'line'> = {  
    id: 'signalPoints',  
    afterDraw: chart => {  
      const { ctx, width: chartWidth } = chart  
  
      signalPoints.forEach(signal => {  
        const coords = getSignalPointCoordinates(signal, chart)  
        if (!coords) return  
  
        const { xPos, yPos } = coords  
        const isBuy = signal.signalType === 'BUY'  
  
        ctx.save()  
  
        // 그림자 효과 추가  
        ctx.shadowColor = 'rgba(0, 0, 0, 0.15)'  
        ctx.shadowBlur = 4  
        ctx.shadowOffsetX = 0  
        ctx.shadowOffsetY = 2  
  
        // 외부 원형 배경 (더 큰 원)  
        ctx.beginPath()  
        ctx.arc(xPos, yPos, 11, 0, 2 * Math.PI)  
        ctx.fillStyle = isBuy  
          ? 'linear-gradient(135deg, #ef4444, #dc2626)'  
          : 'linear-gradient(135deg, #3b82f6, #2563eb)'  
        // 그라데이션 효과를 위한 대체 색상  
        ctx.fillStyle = isBuy ? '#ef4444' : '#3b82f6'  
        ctx.fill()  
  
        // 내부 원형 배경 (작은 원)  
        ctx.shadowColor = 'transparent'  
        ctx.beginPath()  
        ctx.arc(xPos, yPos, 8.5, 0, 2 * Math.PI)  
        ctx.fillStyle = isBuy ? '#f87171' : '#60a5fa'  
        ctx.fill()  
  
        // 가장 안쪽 원 (더 밝은 색)  
        ctx.beginPath()  
        ctx.arc(xPos, yPos, 6, 0, 2 * Math.PI)  
        ctx.fillStyle = isBuy ? '#fca5a5' : '#93c5fd'  
        ctx.fill()  
  
        // 화살표 그리기 - 더 세련된 디자인  
        ctx.fillStyle = '#ffffff'  
        ctx.strokeStyle = '#ffffff'  
        ctx.lineWidth = 1.5  
        ctx.lineCap = 'round'  
        ctx.lineJoin = 'round'  
  
        if (isBuy) {  
          // 매수 화살표 - 더 날카롭고 세련된 형태  
          ctx.beginPath()  
          ctx.moveTo(xPos, yPos - 4.5)  
          ctx.lineTo(xPos - 3.5, yPos + 1.5)  
          ctx.lineTo(xPos - 1.5, yPos + 1.5)  
          ctx.lineTo(xPos - 1.5, yPos + 3.5)  
          ctx.lineTo(xPos + 1.5, yPos + 3.5)  
          ctx.lineTo(xPos + 1.5, yPos + 1.5)  
          ctx.lineTo(xPos + 3.5, yPos + 1.5)  
          ctx.closePath()  
          ctx.fill()  
  
          // 화살표 하이라이트  
          ctx.strokeStyle = 'rgba(255, 255, 255, 0.8)'  
          ctx.lineWidth = 0.5  
          ctx.stroke()  
        } else {  
          // 매도 화살표 - 더 날카롭고 세련된 형태  
          ctx.beginPath()  
          ctx.moveTo(xPos, yPos + 4.5)  
          ctx.lineTo(xPos - 3.5, yPos - 1.5)  
          ctx.lineTo(xPos - 1.5, yPos - 1.5)  
          ctx.lineTo(xPos - 1.5, yPos - 3.5)  
          ctx.lineTo(xPos + 1.5, yPos - 3.5)  
          ctx.lineTo(xPos + 1.5, yPos - 1.5)  
          ctx.lineTo(xPos + 3.5, yPos - 1.5)  
          ctx.closePath()  
          ctx.fill()  
  
          // 화살표 하이라이트  
          ctx.strokeStyle = 'rgba(255, 255, 255, 0.8)'  
          ctx.lineWidth = 0.5  
          ctx.stroke()  
        }  
  
        // 펄스 효과를 위한 외부 링  
        ctx.shadowColor = 'transparent'  
        ctx.beginPath()  
        ctx.arc(xPos, yPos, 13, 0, 2 * Math.PI)  
        ctx.strokeStyle = isBuy ? 'rgba(239, 68, 68, 0.3)' : 'rgba(59, 130, 246, 0.3)'  
        ctx.lineWidth = 2  
        ctx.stroke()  
  
        // 가격 텍스트 표시  
        const priceText = `${formatPrice(signal.closingPrice, signal.currencyCode)}`  
  
        // 텍스트 배경  
        ctx.font = '12px "Noto Sans KR", sans-serif'  
        const textMetrics = ctx.measureText(priceText)  
        const textWidth = textMetrics.width  
        const textHeight = 12  
        const padding = 6  
  
        // 텍스트 박스 위치 계산 (가장자리에서 잘리지 않도록)  
        const { textX, bgX } = calculateTextBoxPosition(xPos, yPos, textWidth, chartWidth)  
  
        const bgY = yPos + 18  
        const bgWidth = textWidth + padding * 2  
        const bgHeight = textHeight + padding  
  
        // 텍스트 배경 그리기  
        ctx.fillStyle = isBuy ? 'rgba(239, 68, 68, 0.9)' : 'rgba(59, 130, 246, 0.9)'  
        ctx.beginPath()  
        ctx.roundRect(bgX, bgY, bgWidth, bgHeight, 3)  
        ctx.fill()  
  
        // 텍스트 그리기 (계산된 위치 사용)  
        ctx.fillStyle = '#ffffff'  
        ctx.textAlign = 'center'  
        ctx.textBaseline = 'middle'  
        ctx.fillText(priceText, textX, bgY + bgHeight / 2)  
  
        ctx.restore()  
      })  
    },  
  }  
  
  // 단일 데이터 포인트를 위한 차트 데이터 처리  
  const getChartData = () => {  
    if (hasOnlyOneDataPoint) {  
      // 단일 포인트의 경우 시각적으로 표현하기 위해 같은 값을 2개 생성  
      const singleData = data[0]  
      return {  
        labels: [formatChartLabel(singleData.time), formatChartLabel(singleData.time)],  
        datasets: [  
          {  
            label: '가격',  
            data: [singleData.close, singleData.close],  
            borderColor: '#3b82f6',  
            backgroundColor: isDark ? 'rgba(59, 130, 246, 0.3)' : 'rgba(59, 130, 246, 0.2)',  
            fill: true,  
            tension: 0,  
            pointRadius: hasOnlyOneDataPoint ? 6 : 0, // 단일 포인트인 경우 점 표시  
            pointHoverRadius: hasOnlyOneDataPoint ? 8 : 4,  
            pointBackgroundColor: '#3b82f6',  
            pointBorderColor: '#ffffff',  
            pointBorderWidth: 2,  
            borderWidth: 2,  
          },  
        ],  
      }  
    }  
  
    return {  
      labels: data.map(d => formatChartLabel(d.time)),  
      datasets: [  
        {  
          label: '가격',  
          data: data.map(d => d.close),  
          borderColor: '#3b82f6',  
          backgroundColor: isDark ? 'rgba(59, 130, 246, 0.3)' : 'rgba(59, 130, 246, 0.2)',  
          fill: true,  
          tension: 0.1,  
          pointRadius: 0,  
          pointHoverRadius: 4,  
          borderWidth: 2,  
        },  
      ],  
    }  
  }  
  
  // 차트 데이터 설정  
  const chartData = getChartData()  
  
  // 차트 옵션 설정  
  const options: ChartOptions<'line'> = {  
    responsive: true,  
    maintainAspectRatio: false,  
    layout: {  
      padding: {  
        left: 5,  
        right: 5,  
        top: 0,  
        bottom: 0,  
      },  
    },  
    plugins: {  
      legend: {  
        display: false,  
      },  
      tooltip: {  
        enabled: true,  
        mode: 'index',  
        intersect: false,  
        yAlign: 'bottom',  
        backgroundColor: isDark ? 'rgba(31, 41, 55, 0.98)' : 'rgba(255, 255, 255, 0.98)',  
        titleColor: isDark ? '#f9fafb' : '#111827',  
        bodyColor: isDark ? '#f9fafb' : '#111827',  
        borderColor: isDark ? '#4b5563' : '#d1d5db',  
        borderWidth: 1,  
        cornerRadius: 6,  
        displayColors: false,  
        titleFont: {  
          size: 13,  
          family: "'Noto Sans KR', sans-serif",  
        },  
        bodyFont: {  
          size: 12,  
          family: "'Noto Sans KR', sans-serif",  
        },  
        callbacks: {  
          // 제목 스타일링 (아이콘 추가)  
          title: items => {  
            if (!items || items.length === 0) return ''  
            return `${moment(items[0].label, 'HH:mm').format('HH시 mm분')}`  
          },  
  
          // 라벨 스타일링 (아이콘, 색상, 포맷팅)  
          label: item => {  
            if (!item || item.parsed === undefined) return ''  
            const price = `${formatPrice(item.parsed.y, unit)}`  
            return `가격: ${price}`  
          },  
        },  
      },  
    },  
    scales: {  
      x: {  
        display: true,  
        grid: {  
          color: isDark ? '#374151' : '#e5e7eb',  
          display: false,  
        },  
        ticks: {  
          maxTicksLimit: hasOnlyOneDataPoint ? 1 : 8, // 단일 포인트인 경우 하나의 레이블만 표시  
          color: isDark ? '#d2e3ff' : '#111111',  
          font: {  
            size: 11,  
            family: "'Noto Sans KR', sans-serif",  
          },  
        },  
        border: {  
          display: false,  
        },  
      },  
      y: {  
        display: false,  
        min: minValue,  
        max: maxValue,  
        grid: {  
          color: isDark ? '#374151' : '#e5e7eb',  
          display: true,  
        },  
        ticks: {  
          display: true,  
          maxTicksLimit: 5,  
          color: isDark ? '#dddddd' : '#111111',  
          font: {  
            size: 11,  
            family: "'Noto Sans KR', sans-serif",  
          },  
          callback: value => {  
            const numValue = Number(value)  
            return numValue.toFixed(2)  
          },  
        },  
        border: {  
          display: false,  
        },  
      },  
    },  
    interaction: {  
      mode: 'nearest',  
      axis: 'x',  
      intersect: false,  
    },  
    onClick: () => {  
      try {  
        if (customTooltip.visible) {  
          handleCloseTooltip()  
        }  
      } catch (error) {  
        console.warn('Chart click error:', error)  
        handleCloseTooltip()  
      }  
    },  
    onHover: (_, elements) => {  
      try {  
        if (chartRef.current) {  
          chartRef.current.canvas.style.cursor = elements.length > 0 ? 'pointer' : 'default'  
        }  
      } catch (error) {  
        console.warn('Chart hover error:', error)  
      }  
    },  
  }  
  
  // 차트 외부 클릭 시 툴팁 닫기  
  useEffect(() => {  
    const handleClickOutside = (event: MouseEvent) => {  
      if (containerRef.current && !containerRef.current.contains(event.target as Node)) {  
        handleCloseTooltip()  
      }  
    }  
  
    document.addEventListener('mousedown', handleClickOutside)  
    return () => {  
      document.removeEventListener('mousedown', handleClickOutside)  
    }  
  }, [])  
  
  // 스크롤 시 툴팁 닫기 (추가된 부분)  
  useEffect(() => {  
    const handleScroll = () => {  
      if (customTooltip.visible) {  
        handleCloseTooltip()  
      }  
    }  
  
    // 전역 스크롤 이벤트 감지  
    window.addEventListener('scroll', handleScroll, true)  
  
    return () => {  
      window.removeEventListener('scroll', handleScroll, true)  
    }  
  }, [customTooltip.visible])  
  
  return (  
    <div className="bg-gray-50 dark:bg-gray-900">  
      <div        ref={containerRef}  
        className="relative w-full bg-white dark:bg-transparent"  
        style={{ height: `${height}px` }}  
      >  
        {/* Chart Container */}  
        <div className="relative w-full" style={{ height: `${height}px` }}>  
          <Line ref={chartRef} data={chartData} options={options} plugins={[signalPointPlugin]} />  
  
          {/* 단일 데이터 포인트 안내 메시지 */}  
          {hasOnlyOneDataPoint && (  
            <div className="absolute top-2 right-2 rounded bg-blue-100 px-2 py-1 text-xs text-blue-800 dark:bg-blue-900 dark:text-blue-200">  
              단일 데이터 포인트  
            </div>  
          )}  
  
          {/* 절대 위치 시그널 포인트 오버레이 */}  
          {signalPositions.map((pos, idx) => {  
            return (  
              <button  
                key={`${pos.id}_${idx}`}  
                className="absolute z-20 cursor-pointer transition-transform duration-200 ease-out hover:scale-110"  
                style={{  
                  left: `${pos.x - 16}px`,  
                  top: `${pos.y - 16}px`,  
                  width: '32px',  
                  height: '32px',  
                  borderRadius: '50%',  
                  backgroundColor: 'transparent',  
                  border: 'none',  
                  padding: 0,  
                }}  
                onClick={e => handleSignalPointClick(pos.signal, e)}  
                onMouseDown={e => e.preventDefault()}  
                title={`${pos.signal.signalType} Signal - ${formatPrice(pos.signal.closingPrice, pos.signal.currencyCode)}`}  
              />  
            )  
          })}  
  
          {/* 커스텀 툴팁 */}  
          {customTooltip.visible && customTooltip.data && <StockChartCustomTooltip customTooltip={customTooltip} />}  
        </div>  
      </div>    </div>  )  
}
```
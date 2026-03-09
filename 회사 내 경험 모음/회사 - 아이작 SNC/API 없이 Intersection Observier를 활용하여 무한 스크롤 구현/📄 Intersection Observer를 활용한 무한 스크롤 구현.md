
이 문서는 React 환경에서 `IntersectionObserver` API를 사용하여 **무한 스크롤(Infinite Scroll)** 을 구현한 코드에 사용된 주요 개념을 정리한 것입니다.

## 🔁 사용된 기술 및 개념

#### 1. IntersectionObserver API
- 브라우저 API로써, DOM 요소가 **뷰포트(Viewport)** 에 들어오는지를 감지하는 기능 제공
- 비효율적인 스크롤 이벤트 감지 방식(`scroll` 이벤트)보다 **성능면에서 우수**
- 주요옵션
	- `callback`: 요소가 뷰포트에 들어왔을 때 실행할 함수
	- `options`: 관찰 조건 (예: `threshold`, `root`, `rootMargin` 등)
```tsx
const observer = new IntersectionObserver(callback, options)
observer.observe(targetElement)
```

#### 2. React의 useEffect Hook
- React 함수형 컴포넌트에서 side effect (예: DOM 조작, 구독 설정 등)을 수행하는 Hook
- 의존성 배열 (`[hasMore, musicItems]`) 내 값이 변경될 때마다 실행됨
- 반환 함수는 clean-up 용도 (옵저버 해제 등)

#### 3. Ref 객체(useRef)
- `observerRef.current`를 통해 특정 DOM 요소에 접근 가능
- 컴포넌트가 다시 렌더링되더라도 참조값 유지됨

#### 4. 무한 스크롤 
- 사용자가 콘텐츠 하단에 도달하면 자동으로 다음 데이터를 로드하는 UX 패턴
- 구현 방식 요약:
    1. 하단 요소가 화면에 보이면
    2. 다음 항목 N개(`ITEMS_PER_PAGE`)를 `musicItems`에서 잘라서 보여줌
    3. 더 이상 불러올 항목이 없으면(`hasMore === false`) 옵저버 해제

#### 5. 전체 코드
| 변수명            | 설명                            |
|----------------|-------------------------------|
| observerRef    | 하단 감시 요소에 대한 참조 객체            |
| hasMore        | 더 불러올 데이터가 존재하는지 여부 (boolean) |
| musicItems     | 전체 음악 리스트 배열                  |
| visibleItems   | 현재 화면에 표시 중인 아이템 리스트          |
| ITEMS_PER_PAGE | 한 번에 추가로 보여줄 아이템 개수           |

```tsx
import { useEffect, useRef, useState } from 'react'  
import { Clock, Loader2Icon, Sparkles } from 'lucide-react'  
import { Button } from '@/components/ui/button'  
import { Card, CardContent, CardHeader, CardTitle } from '@/components/ui/card'  
import { Badge } from '@/components/ui/badge'  
import { Link, useNavigate } from 'react-router-dom'  
import MusicContent from '@/pages/music/_components/music-content.tsx'  
import { useGetMusicListActions } from '@/pages/music/hooks/use-get-music-list.ts'  
import { MusicDto } from '@/service/dto/music.ts'  
import { AudioMakeForm } from '@/pages/audio/types/internal.ts'  
import {  
  DEFAULT_BACKGROUND_MUSIC_TYPE,  
  DEFAULT_EMOTIONS,  
  DEFAULT_NOISE_REDUCTION_ENABLED,  
  DEFAULT_PITCH_STANDARD,  
  DEFAULT_VOICE_QUALITY_SCORE,  
} from '@/pages/audio/constants.ts'  
import { AudioEmotionType } from '@/service/dto/audio.ts'  
import { useGetMusicFile } from '@/pages/music/hooks/use-get-music-file.ts'  
import { toast } from 'sonner'  
  
export default function MusicPage() {  
  const navigate = useNavigate()  
  const { data: musicItems, isLoading } = useGetMusicListActions()  
  const { mutate: downloadMusic, isPending: isDownloadMusicPending } = useGetMusicFile()  
  const [currentlyPlaying, setCurrentlyPlaying] = useState<string | null>(null)  
  const [expandedItem, setExpandedItem] = useState<string | null>(null)  
  const audioRefs = useRef<Record<string, HTMLAudioElement | null>>({})  
  
  // 무한스크롤  
  const [visibleItems, setVisibleItems] = useState<MusicDto[]>([])  // 실제 화면에 렌더링할 항목 리스트 (초기에는 비어 있음)
  const [hasMore, setHasMore] = useState(true)  // 더 로드할 데이터가 있는지 여부
  const observerRef = useRef<HTMLDivElement | null>(null)  // 마지막 항목(더보기 트리거 역할)의 DOM 요소 참조
  const ITEMS_PER_PAGE = 5 // 한 번에 보여줄 항목 수 (페이지당 5개)
  
  const togglePlayPause = (id: string) => {  
    const audio = audioRefs.current[id]  
    if (!audio) return  
  
    if (currentlyPlaying === id) {  
      audio.pause()  
      setCurrentlyPlaying(null)  
    } else {  
      if (currentlyPlaying !== null && audioRefs.current[currentlyPlaying]) {  
        audioRefs.current[currentlyPlaying]?.pause()  
      }  
  
      // 음성 파일 요청  
      downloadMusic(id, {  
        onSuccess: blob => {  
          audio.src = URL.createObjectURL(blob)  
          audio.play().catch(err => {  
            toast.error('재생 중 오류가 발생했습니다.')  
            console.error(err)  
          })  
          setCurrentlyPlaying(id)  
        },  
        onError: error => {  
          toast.error(error.message || '음성 파일을 불러오지 못했습니다.')  
        },  
      })  
    }  
  }  
  
  const toggleExpand = (id: string) => {  
    setExpandedItem(prev => (prev === id ? null : id))  
  }  
  
  const deleteMusicItem = (id: string) => {  
    // 삭제로직 수행  
    if (currentlyPlaying === id) {  
      setCurrentlyPlaying(null)  
    }  
  }  
  
  const loadSettings = (item: MusicDto) => {  
    const { id: _id, fullFilelPath: _fullFilelPath, ...audioOption } = item  
    const audioFormValues: AudioMakeForm = {  
      ...audioOption,  
      emotionType: audioOption.emotionType ? (audioOption.emotionType as AudioEmotionType) : DEFAULT_EMOTIONS,  
      noiseReductionEnabled: audioOption.noiseReductionEnabled ?? DEFAULT_NOISE_REDUCTION_ENABLED,  
      pitchStandard: audioOption.pitchStandard ? audioOption.pitchStandard : DEFAULT_PITCH_STANDARD,  
      voiceQualityScore: audioOption.voiceQualityScore ? audioOption.voiceQualityScore : DEFAULT_VOICE_QUALITY_SCORE,  
      backgroundMusicType: DEFAULT_BACKGROUND_MUSIC_TYPE,  
    }  
  
    navigate('/audio', {  
      state: {  
        params: audioFormValues,  
      },  
    })  
  }  
  
  // 무한스크롤 useEffect  

  useEffect(() => {  
    if (!musicItems) return  
	// 처음 페이지 로드 후, musicItems가 있으면 ITEMS_PER_PAGE 만큼만 visibleItems로 세팅
    setVisibleItems(musicItems.slice(0, ITEMS_PER_PAGE))  
    
	// 처음 페이지 로드 후, musicItems가 있되, ITEMS_PER_PAGE보다 데이터 개수가 많으면 HasMore는 true가됨
    setHasMore(musicItems.length > ITEMS_PER_PAGE)  
  }, [musicItems])  
  
  useEffect(() => {  
    if (!observerRef.current || !hasMore) return  // 더 표시할 게 있을때만 아래 문 수행
    
    // 새 IntersectionObserver 인스턴스 생성
    // entries[0].isIntersecting는 감시 대상 요소가 뷰포트 안에 들어왔는지 여부를 나타냄.
    // entries[0].isIntersecting === true이면, 스크롤이 하단 요소에 도달했다는 의미.
    const observer = new IntersectionObserver(  
      entries => {  
        if (entries[0].isIntersecting && musicItems) {  
          setVisibleItems(prev => {  
            const nextItems = musicItems.slice(0, prev.length + ITEMS_PER_PAGE)  
            setHasMore(nextItems.length < musicItems.length) // musicItems로부터 더 표시할 게 있으면 true
            return nextItems  
          })  
        }  
      },  
      {  
	    // 이건 옵저버 옵션인데, `threshold: 1.0`은 감시 요소가 **100% 화면에 들어왔을 때만** `isIntersecting`이 true가 됨.
        threshold: 1.0,  
      },  
    ) 
    // 하단에 위치한 div 요소 (`<div ref={observerRef} />`)를 관찰 대상으로 설정.
    observer.observe(observerRef.current)  
    // 컴포넌트 언마운트 혹은 의존성 값 변경 시, 기존 옵저버를 **clean up** 해줘서 **메모리 누수 방지**!
    return () => observer.disconnect()  
  }, [hasMore, musicItems])  
  
  return (  
    <div className="col-span-12 lg:col-span-10">  
      <Card className="border-slate-700/50 bg-slate-900/60 backdrop-blur-sm">  
        <CardHeader className="flex flex-row items-center justify-between">  
          <CardTitle className="flex items-center text-2xl text-slate-100">  
            <Clock className="mr-2 h-5 w-5 text-cyan-500" />  
            히스토리  
          </CardTitle>  
          <div className="flex items-center space-x-2">  
            <Badge variant="outline" className="border-cyan-500/50 bg-slate-800/50 text-cyan-400">  
              {!isLoading && musicItems ? `총 ${musicItems.length}개 항목` : `조회되지 않음`}  
            </Badge>  
          </div>        </CardHeader>        <CardContent>          {!isLoading && musicItems && musicItems.length > 0 && (  
            <div className="max-h-[700px] space-y-4 overflow-y-auto pr-2">  
              {musicItems.length > 0 ? (  
                <>  
                  {visibleItems.map(item => (  
                    <MusicContent  
                      key={item.id}  
                      item={item}  
                      isExpanded={expandedItem === item.id}  
                      isPlaying={currentlyPlaying === item.id}  
                      togglePlayPause={togglePlayPause}  
                      toggleExpand={toggleExpand}  
                      deleteMusicItem={deleteMusicItem}  
                      loadSettings={loadSettings}  
                      audioRefSetter={(el: HTMLAudioElement | null) => {  
                        audioRefs.current[item.id] = el  
                      }}  
                    />  
                  ))}  
		          
		          {/* 영역이 100% 화면에 들어오면 isIntersecting이 true가 되면서 visibleItems가 업데이트됨 */}
                  {hasMore && <div ref={observerRef} className="h-8" />}  
                </>  
              ) : (  
                <div className="py-12 text-center">  
                  <h3 className="mb-2 text-xl font-medium text-slate-300">아직 히스토리가 없습니다</h3>  
                  <p className="mb-6 text-slate-400">음성을 생성하면 여기에 기록이 표시됩니다.</p>  
                  <Link to="/audio">  
                    <Button className="relative h-auto overflow-hidden rounded-md bg-gradient-to-r from-cyan-500 to-purple-600 px-6 py-2 text-white shadow-md transition-all before:absolute before:inset-0 before:bg-gradient-to-r before:from-purple-600 before:to-cyan-500 before:opacity-0 before:transition-opacity hover:translate-y-[-1px] hover:shadow-lg hover:before:opacity-100">  
                      <span className="relative z-10 flex items-center">  
                        <Sparkles className="mr-2 h-4 w-4" />  
                        음성 생성하기  
                      </span>  
                    </Button>
	                </Link>                
				</div>              
			)}  
		</div>  
	  )}  
	</CardContent>  
  </Card>      
  {isDownloadMusicPending && (  
        <div className="absolute inset-0 flex items-center justify-center">  
          <Loader2Icon className="text-muted-foreground size-16 animate-spin" />  
        </div>      )}  
    </div>  
  )  
}
```
```tsx
import { create } from 'zustand'  
import { toast } from 'sonner'  
import { isAppEnvironment } from '@/lib/utils.ts'  
import { useAppAudioHooks } from '@/hooks/audio/use-app-audio-hooks'  
import { useWebAudioHooks } from '@/hooks/audio/use-web-audio-hooks'  
  
interface AudioInfo {  
  url: string  
  title: string  
  artist: string  
  albumArt?: string  
}  
  
interface PlaylistInfo {  
  hasPlaylist: boolean  
  totalTracks: number  
  currentIndex: number  
  canSkipToPrevious: boolean  
  canSkipToNext: boolean  
}  
  
type PlayerType = 'app' | 'web' | null  
  
interface AppPlayerState {  
  isPlaying: boolean  
  isLoading: boolean  
  currentTrack: AudioInfo | null  
  error: string | null  
  currentPlayerType: PlayerType  
  playlist: AudioInfo[]  
  currentIndex: number  
  playlistStarted: boolean  
  playlistInfo: PlaylistInfo | null  
  webAudioElement: HTMLAudioElement | null  
  
  playAudio: (audioInfo: AudioInfo) => Promise<void>  
  pauseAudio: () => Promise<void>  
  stopAudio: () => Promise<void>  
  resumeAudio: () => Promise<void>  
  playAudioDirect: (audioInfo: AudioInfo) => Promise<void>  
  playPlaylist: (playlist: AudioInfo[], startIndex?: number) => Promise<void>  
  playNext: () => Promise<void>  
  playPrevious: () => Promise<void>  
  seekTo: (position: number) => Promise<void>  
  getPlaylistInfo: () => Promise<PlaylistInfo | null>  
  openPlaylistScreen: (playlist: AudioInfo[]) => Promise<void>  
  updatePlayerState: (data: any) => void  
  updatePlaylistInfo: (info: PlaylistInfo) => void  
  setError: (error: string | null) => void  
  setLoading: (loading: boolean) => void  
  setCurrentTrack: (track: AudioInfo | null) => void  
  getAbsoluteUrl: (url: string) => string  
  reset: () => void  
  initWebPlayer: () => HTMLAudioElement  
  cleanupWebPlayer: () => void  
  getCurrentTime: () => number  
  getDuration: () => number  
}  
  
// 환경에 따라 초기 playerType 설정  
const getInitialPlayerType = (): PlayerType => {  
  if (typeof window === 'undefined') return null  
  return isAppEnvironment() ? 'app' : 'web'  
}  
  
const initialState = {  
  isPlaying: false,  
  isLoading: false,  
  currentTrack: null,  
  error: null,  
  playlist: [],  
  currentIndex: -1,  
  playlistStarted: false,  
  playlistInfo: null,  
  currentPlayerType: getInitialPlayerType(),  
  webAudioElement: null,  
}  
  
export const useAppPlayerStore = create<AppPlayerState>((set, get) => {  
  const appHooks = useAppAudioHooks()  
  const webHooks = useWebAudioHooks()  
  
  console.log('initialState is ', initialState)  
  return {  
    ...initialState,  
  
    getAbsoluteUrl: (url: string) => {  
      return isAppEnvironment() ? appHooks.getAbsoluteUrl(url) : webHooks.getAbsoluteUrl(url)  
    },  
  
    initWebPlayer: (): HTMLAudioElement => {  
      const { webAudioElement } = get()  
      if (webAudioElement) return webAudioElement  
  
      const audio = webHooks.initWebPlayer(set)  
  
      // 웹 플레이어 종료 시 다음 곡 재생 처리  
      audio.addEventListener('ended', () => {  
        set({ isPlaying: false })  
        const { playlist, currentIndex } = get()  
        if (playlist.length > 0 && currentIndex < playlist.length - 1) {  
          get().playNext()  
        }  
      })  
  
      set({ webAudioElement: audio })  
      return audio  
    },  
  
    cleanupWebPlayer: () => {  
      const { webAudioElement } = get()  
      webHooks.cleanupWebPlayer(webAudioElement)  
      set({ webAudioElement: null })  
    },  
  
    getCurrentTime: (): number => {  
      const { webAudioElement } = get()  
      return webHooks.getCurrentTime(webAudioElement)  
    },  
  
    getDuration: (): number => {  
      const { webAudioElement } = get()  
      return webHooks.getDuration(webAudioElement)  
    },  
  
    seekTo: async (position: number) => {  
      const { currentPlayerType, webAudioElement } = get()  
  
      try {  
        if (currentPlayerType === 'app') {  
          await appHooks.seekTo(position)  
        } else {  
          await webHooks.seekTo(position, webAudioElement)  
        }  
      } catch (error) {  
        set({ error: '위치 이동에 실패했습니다.' })  
      }  
    },  
  
    getPlaylistInfo: async (): Promise<PlaylistInfo | null> => {  
      return await appHooks.getPlaylistInfo()  
    },  
  
    openPlaylistScreen: async (playlist: AudioInfo[]) => {  
      await appHooks.openPlaylistScreen(playlist, set)  
    },  
  
    setCurrentTrack: (track: AudioInfo | null) => set({ currentTrack: track }),  
  
    playAudio: async (audioInfo: AudioInfo) => {  
      const { currentTrack, isPlaying } = get()  
  
      if (currentTrack?.url === audioInfo.url && isPlaying) return  
  
      set({ isLoading: true, error: null, currentTrack: audioInfo })  
  
      try {  
        await new Promise(resolve => setTimeout(resolve, 200))  
  
        if (isAppEnvironment()) {  
          await appHooks.playAudio(audioInfo, set)  
        } else {  
          const webAudio = get().initWebPlayer()  
          await webHooks.playAudio(audioInfo, webAudio, set)  
        }  
      } catch (error) {  
        set({  
          isLoading: false,  
          isPlaying: false,  
          error: '오디오 재생에 실패했습니다.',  
          currentPlayerType: null  
        })  
        toast.error('오디오 재생에 실패했습니다.')  
      }  
    },  
  
    playAudioDirect: async (audioInfo: AudioInfo) => {  
      try {  
        await get().stopAudio()  
        await new Promise(resolve => setTimeout(resolve, 100))  
  
        const webAudio = get().initWebPlayer()  
        await webHooks.playAudioDirect(audioInfo, webAudio, set)  
      } catch (error) {  
        set({  
          isLoading: false,  
          isPlaying: false,  
          error: '오디오 재생에 실패했습니다.',  
          currentPlayerType: null  
        })  
        toast.error('오디오 재생에 실패했습니다.')  
      }  
    },  
  
    playPlaylist: async (playlist: AudioInfo[], startIndex = 0) => {  
      if (!playlist || playlist.length === 0) {  
        set({ error: '플레이리스트가 비어있습니다.' })  
        toast.error('플레이리스트가 비어있습니다.')  
        return  
      }  
  
      if (startIndex < 0 || startIndex >= playlist.length) {  
        set({ error: '잘못된 시작 인덱스입니다.' })  
        toast.error('잘못된 시작 인덱스입니다.')  
        return  
      }  
  
      set({  
        isLoading: true,  
        error: null,  
        playlist,  
        currentIndex: startIndex,  
        playlistStarted: true  
      })  
  
      try {  
        if (isAppEnvironment()) {  
          await appHooks.playPlaylist(playlist, startIndex, set)  
        } else {  
          // 웹에서는 연속재생 기능이 일단 없음  
          toast.info('웹에서는 플레이리스트 기능이 제한됩니다.')  
          set({ isLoading: false })  
        }  
      } catch (error) {  
        set({  
          isLoading: false,  
          error: '플레이리스트 재생에 실패했습니다.',  
          playlistStarted: false,  
          currentPlayerType: null  
        })  
        toast.error('플레이리스트 재생에 실패했습니다.')  
      }  
    },  
  
    pauseAudio: async () => {  
      const { currentPlayerType, webAudioElement } = get()  
      set({ isLoading: true })  
  
      try {  
        if (currentPlayerType === 'app') {  
          await appHooks.pauseAudio()  
        } else {  
          await webHooks.pauseAudio(webAudioElement)  
        }  
  
        set({ isPlaying: false, isLoading: false })  
      } catch (error) {  
        set({ isPlaying: false, isLoading: false })  
      }  
    },  
  
    resumeAudio: async () => {  
      const { currentPlayerType, webAudioElement, currentTrack } = get()  
  
      if (!currentTrack) {  
        set({ error: '재생할 트랙이 없습니다.' })  
        return  
      }  
  
      try {  
        if (currentPlayerType === 'app') {  
          await appHooks.resumeAudio()  
        } else {  
          await webHooks.resumeAudio(webAudioElement)  
        }  
  
        set({ isPlaying: true })  
      } catch (error) {  
        set({ error: '재생 재개에 실패했습니다.' })  
      }  
    },  
  
    stopAudio: async () => {  
      const { currentPlayerType, webAudioElement } = get()  
      set({ isLoading: true })  
  
      try {  
        if (currentPlayerType === 'app') {  
          await appHooks.stopAudio()  
        } else {  
          await webHooks.stopAudio(webAudioElement)  
        }  
  
        set({  
          isPlaying: false,  
          currentPlayerType: getInitialPlayerType(), // 초기 환경 타입으로 리셋  
          isLoading: false  
        })  
      } catch (error) {  
        set({  
          isPlaying: false,  
          currentPlayerType: getInitialPlayerType(), // 초기 환경 타입으로 리셋  
          isLoading: false  
        })  
      }  
    },  
  
    playNext: async () => {  
      const { currentPlayerType, playlist, currentIndex } = get()  
  
      try {  
        if (currentPlayerType === 'app') {  
          await appHooks.playNext()  
        } else {  
          await webHooks.playNext(playlist, currentIndex, get().playAudio, set)  
        }  
      } catch (error) {  
        set({ error: '다음곡 재생에 실패했습니다.' })  
      }  
    },  
  
    playPrevious: async () => {  
      const { currentPlayerType, playlist, currentIndex } = get()  
  
      try {  
        if (currentPlayerType === 'app') {  
          await appHooks.playPrevious()  
        } else {  
          await webHooks.playPrevious(playlist, currentIndex, get().playAudio, set)  
        }  
      } catch (error) {  
        set({ error: '이전곡 재생에 실패했습니다.' })  
      }  
    },  
  
    updatePlayerState: (data: any) => {  
      try {  
        const eventType = data.event  
  
        switch (eventType) {  
          case 'stateChanged':  
            set({  
              isPlaying: data.playing || false,  
              isLoading: false,  
              currentPlayerType: data.playing ? 'app' : get().currentPlayerType  
            })  
            break  
  
          case 'trackChanged': {  
            const { playlist } = get()  
            if (data.index !== undefined && playlist.length > 0) {  
              const newIndex = data.index  
              const newTrack = playlist[newIndex] || null  
              set({  
                currentIndex: newIndex,  
                currentTrack: newTrack,  
                currentPlayerType: 'app'  
              })  
            }  
            break  
          }  
  
          case 'playlistLoaded':  
            set({  
              playlistStarted: true,  
              isLoading: false,  
              currentPlayerType: 'app'  
            })  
            break  
  
          case 'mediaLoaded':  
            if (data.title) {  
              set({  
                currentTrack: {  
                  title: data.title,  
                  artist: data.artist || '알 수 없음',  
                  url: data.url || '',  
                  albumArt: data.albumArt  
                },  
                isLoading: false,  
                currentPlayerType: 'app'  
              })  
            }  
            break  
  
          case 'stop':  
            set({  
              isPlaying: false,  
              currentTrack: null,  
              playlistStarted: false,  
              currentIndex: -1,  
              playlist: [],  
              playlistInfo: null,  
              isLoading: false,  
              currentPlayerType: getInitialPlayerType() // 초기 환경 타입으로 리셋  
            })  
            break  
  
          case 'error':  
            set({  
              error: data.message || '오디오 재생 중 오류가 발생했습니다.',  
              isLoading: false,  
              currentPlayerType: null  
            })  
            toast.error(data.message || '오디오 재생 중 오류가 발생했습니다.')  
            break  
        }  
      } catch (error) {  
        set({ error: '상태 업데이트에 실패했습니다.' })  
      }  
    },  
  
    updatePlaylistInfo: (info: PlaylistInfo) => {  
      set({ playlistInfo: info })  
  
      if (info.currentIndex !== get().currentIndex) {  
        const { playlist } = get()  
        if (playlist.length > 0 && info.currentIndex >= 0 && info.currentIndex < playlist.length) {  
          set({  
            currentIndex: info.currentIndex,  
            currentTrack: playlist[info.currentIndex]  
          })  
        }  
      }  
    },  
  
    setError: (error: string | null) => set({ error }),  
    setLoading: (loading: boolean) => set({ isLoading: loading }),  
  
    reset: () => {  
      get().cleanupWebPlayer()  
      set({  
        ...initialState,  
        currentPlayerType: getInitialPlayerType() // 리셋 시에도 환경에 맞는 타입으로 설정  
      })  
    }  
  }  
})  
  
// 글로벌 이벤트 핸들러  
if (typeof window !== 'undefined') {  
  window.onAudioStateChanged = (state: any) => {  
    useAppPlayerStore.getState().updatePlayerState(state)  
  }  
  
  window.onPlaylistChanged = (playlistInfo: PlaylistInfo) => {  
    useAppPlayerStore.getState().updatePlaylistInfo(playlistInfo)  
  }  
  
  window.onMiniPlayerClosed = () => {  
    useAppPlayerStore.getState().reset()  
  }  
  
  window.updatePlayerState = (data: any) => {  
    useAppPlayerStore.getState().updatePlayerState(data)  
  }  
}  
  
declare global {  
  interface Window {  
    flutter_inappwebview?: {  
      callHandler: (method: string, ...args: any[]) => Promise<any>  
    }  
    onAudioStateChanged?: (state: any) => void  
    onPlaylistChanged?: (playlistInfo: PlaylistInfo) => void  
    onMiniPlayerClosed?: () => void  
    updatePlayerState?: (data: any) => void  
  }  
}  
  
export default useAppPlayerStore
```
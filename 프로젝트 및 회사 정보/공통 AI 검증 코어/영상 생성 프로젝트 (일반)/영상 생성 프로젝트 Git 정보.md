
> 프론트엔드(영상)
- [Lee-Sang-Beom/TF_AI_Frontend: TF_AI_Harness 연동 프론트엔드 프로젝트 - TF_AI_Frontend - iajk_TFT_dev](http://192.168.1.21:3000/Lee-Sang-Beom/TF_AI_Frontend)

> AI 코어  (백엔드 + AI)
- [YOHAN_PARK/TF_AI_harness: TFT_메인코어 - TF_AI_harness - iajk_TFT_dev](http://192.168.1.21:3000/YOHAN_PARK/TF_AI_harness)

> 영상제작 프론트 홈페이지
- http://192.168.1.21:3001/

> Comfy-UI
- http://192.168.1.21:8188

```
e-mail: tkdqja4192@nate-com
password: REPLACE_WITH_LOCAL_PASSWORD
사용자명: Lee-Sang-Beom
```


> .env.example
```
# --- Verification API ---  
VERIFICATION_API_BASE_URL=  
VERIFICATION_API_TIMEOUT_MS=  
  
# --- A4 video generation API ---  
A4_API_BASE_URL=  
A4_TEST_AUTH_TOKEN=  
A4_TEST_ACTOR_ID=  
  
# --- WorkOS AuthKit ---  
WORKOS_CLIENT_ID=  
WORKOS_API_KEY=  
WORKOS_COOKIE_PASSWORD=  
NEXT_PUBLIC_WORKOS_REDIRECT_URI=
```

> .env.local
```
# --- Verification API ---  
VERIFICATION_API_BASE_URL=http://localhost:8089  
VERIFICATION_API_TIMEOUT_MS=600000  
  
# --- A4 video generation API ---  
#A4_API_BASE_URL=http://localhost:9985  
A4_API_BASE_URL=http://192.168.1.58:9985  
A4_TEST_AUTH_TOKEN=REPLACE_WITH_LOCAL_AUTH_TOKEN  
A4_TEST_ACTOR_ID=testbed-owner  
  
# --- WorkOS AuthKit ---  
WORKOS_CLIENT_ID=client_01KYVDR3KK7BFZ3SJDX2PXCG6R  
WORKOS_API_KEY=REPLACE_WITH_LOCAL_API_KEY  
WORKOS_COOKIE_PASSWORD=REPLACE_WITH_LOCAL_COOKIE_PASSWORD  
NEXT_PUBLIC_WORKOS_REDIRECT_URI=http://localhost:3000/auth/callback
```


> .env.prod
```
# --- Verification API ---  
VERIFICATION_API_BASE_URL=http://localhost:8089  
VERIFICATION_API_TIMEOUT_MS=600000  
  
# --- A4 video generation API ---  
A4_API_BASE_URL=http://192.168.1.58:9985  
A4_TEST_AUTH_TOKEN=REPLACE_WITH_LOCAL_AUTH_TOKEN  
A4_TEST_ACTOR_ID=testbed-owner  
  
# --- WorkOS AuthKit ---  
WORKOS_CLIENT_ID=client_01KYVDR3KK7BFZ3SJDX2PXCG6R  
WORKOS_API_KEY=REPLACE_WITH_LOCAL_API_KEY  
WORKOS_COOKIE_PASSWORD=REPLACE_WITH_LOCAL_COOKIE_PASSWORD  
NEXT_PUBLIC_WORKOS_REDIRECT_URI=http://192.168.1.21:3001/auth/callback
```
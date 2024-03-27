
#### 1. useEffect란?

- `useEffect hook` 은 컴포넌트의 `부수동작(effect)` 을 관리하는 `hook`으로 기본 `hook` 중에서도 가장 중요한 `hook`이라 할 수 있다.
	- **부수동작**이란 `컴포넌트가 처음 마운트될 때`,`컴포넌트가 업데이트될 때`, `컴포넌트가 언마운트될 때` 일어나는 모든 동작을 의미한다.
	- 작성에 필요한 참고자료의 출처는 [여기](https://c17an.netlify.app/blog/React/useEffect-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0/article/)이다

- 즉, 쉽게 말해 `useEffect hook`이 포함된 컴포넌트가 처음 **마운트**되거나, 컴포넌트가 **렌더링** 및 **리렌더링**될 때, 또는 선언된 변수의 값 등이 변경될 때 실행할 구문들을 정의해놓은 함수`(hook)`이다.

- 마운트(mount), 언마운트(unmount), 렌더링(rendering) 및 리렌더링(rerendering)에 대한 용어는 아래 포스트에서 참고할 수 있다.
	- [리액트 컴포넌트 생명주기](https://lktprogrammer.tistory.com/130) 
	- [여기](https://medium.com/@heoh06/%EB%A6%AC%EC%95%A1%ED%8A%B8-%EB%A6%AC%EC%95%A1%ED%8A%B8-%EB%A9%B4%EC%A0%91%EC%A7%88%EB%AC%B8-1c6050d87c8b)는 면접질문을 모아놓은 [uoayop](https://velog.io/@uoayop/posts)님의 포스트인데, 볼만한 게 많다.


#### 2. 사용 방법

- `useEffect`의 구조는 아래와 같다.
```tsx
useEffect(() => { 
 // `이펙트 함수` 
}, [
 //`의존성 배열`
]);
```

 - **인자 1**
	 - **callback 함수**이다.
	 - **의존성 배열에 선언된 값이 바뀔 때마다** 수행되는 로직을 포함한다.
	 - 의존성 배열이 아예 없으면, 컴포넌트가 **렌더링**될 때마다 실행된다.
	 - 의존성 배열이 빈 배열이면, 컴포넌트가 **처음으로 마운트**되었을 때 실행된다.

 - **인자 2**
	 - 의존성 배열(dependancyArray): 배열 내 값이 업데이트될 때만 콜백함수를 호출한다.
	 - 여러 값을 넣을 수 있다.

- **사용 예시**
```tsx
  useEffect(() => {
    setIsMount(true);
  }, []);
```

- 또한, callback 함수에는  **클린업 함수**(cleanup)를 넣을 수도 있는데, 이 함수는 아래와 같은 상황에서 호출된다.
	- 이펙트 함수가 호출되기 전
	- 컴포넌트가 언마운트될 때
```js
import React, {useEffect} from 'react'

  useEffect(
    // (이펙트 함수)
    return {
      // (클린업 함수)
    }
  }, [
  // ...
  ]);
```

#### 3. 프로젝트 사용 경험 1 (클린업 함수)

- 클린업 함수는 `이펙트 함수가 호출되기 전`, `컴포넌트가 언마운트될 때` 호출된다.
	- `localStorage`를 관리하면서, 컴포넌트가 사라질 경우 `removeItem()`을 호출해야하는 상황이 발생했는데, `이펙트 함수가 호출되기 전`에는 이 로직이 수행되지 않아야 했다.

- 위의 문제를 해결하기 위해, 마운트 여부를 확인하는 `useEffect()`를 추가했다.
```tsx

  useEffect(() => {
    setIsMount(true);
  }, []);

  useEffect(() => {
    if (isMount) {
      // 마운트 된 후에로직이 수행됨
      return () => {
        // 이렇게 하면 언마운트 할때만 이 위치의 로직이 수행됨
      };
    }
  }, [isMount]);
```


#### 4. 프로젝트 사용 경험 2 (데이터 초기세팅)

- 이미 존재하는 데이터를 수정하는 경우, 세팅된 데이터를 입력 Form에 초기화시켜주는 경우가 굉장히 많이 필요하다.

- 이럴 경우, `useEffect`의 의존성 배열에 세팅할 `data`를 전달해주면 초기화 작업을 쉽게 수행할 수 있다. 

```tsx
  useEffect(() => {
    if (data) {
      if (data.fileDtoList.length) {
        setDelFileSeqList(data.fileDtoList.map((item) => item.fileSeq));
      }
      if (!data.dmgeEfctStdResponse) {
        // 스텝 종합데이터
        setSimulateCommandData(undefined);
      } else {
        // 추가 0322

        /**
         * step1
         */
        if (data.dmgeEfctStdResponse.companySeq === -1) {
          setSelectStep1Type("사고위치선택");
        } else {
          setSelectStep1Type("업체선택");
        }

        if (data.dmgeEfctStdResponse.lat && data.dmgeEfctStdResponse.lot) {
          setSelectLocation({
            lot: parseFloat(data.dmgeEfctStdResponse.lot.toString()),
            lat: parseFloat(data.dmgeEfctStdResponse.lat.toString()),
          });
        } else {
          setSelectLocation(undefined);
        }

        /**
         * step2
         */
        if (data.dmgeEfctStdResponse.i0202) {
          const param = data.dmgeEfctStdResponse.i0202.replaceAll(
            /[()\|※]/g,
            ""
          );

          fetch(`/api/chemical/getChemExtrAvgDetail?i0202=${param}`)
            .then((res2) => res2.json())
            .then((res2) => {
              if (res2.code == "OK" && res2.data) {
                setChemAvg({
                  i0202Extr: res2.data.i0202Extr || "",
                  i26Avg: res2.data.i26Avg || "",
                  i36Avg: res2.data.i36Avg || "",
                  i38Avg: res2.data.i38Avg || "",
                  pacPpm: res2.data.pacPpm || "",
                  hcompb: res2.data.hcompb || "",
                });
              } else {
                setChemAvg(undefined);
              }
            });
        }

        /**
         * step3
         */
        const newSimulateCommandData: SimulateCommand = {
          /**
           * step1
           */
          companySeq: data.dmgeEfctStdResponse.companySeq,
          companyNm: data.dmgeEfctStdResponse.companyNm,
          companyBizNo: data.dmgeEfctStdResponse.companyBizNo.replace(
            /(\d{3})(\d{2})(\d{5})/,
            "$1-$2-$3"
          ),
          companyRprsvNm: data.dmgeEfctStdResponse.companyRprsvNm,
          companyTpbizNm: data.dmgeEfctStdResponse.companyTpbizNm,
          companyRoadNmAddr: data.dmgeEfctStdResponse.companyRoadNmAddr,
          companyTelNo: data.dmgeEfctStdResponse.companyTelNo,
          fireYn: data.dmgeEfctStdResponse.fireYn || "N",
          explYn: data.dmgeEfctStdResponse.explYn || "N",
          dfsnYn: data.dmgeEfctStdResponse.dfsnYn || "N",

          // 0322 추가
          lat: data.dmgeEfctStdResponse.lat
            ? parseFloat(data.dmgeEfctStdResponse.lat.toString())
            : null,
          lot: data.dmgeEfctStdResponse.lot
            ? parseFloat(data.dmgeEfctStdResponse.lot.toString())
            : null,

          /**
           * step2
           */
          casNo: data.dmgeEfctStdResponse.casNo,
          mttrnmkor: data.dmgeEfctStdResponse.mttrnmkor || "",
          mttrnmeng: data.dmgeEfctStdResponse.mttrnmeng || "",
          // 성상
          i0202: data.dmgeEfctStdResponse.i0202 || "",
          // 색상
          i0204: data.dmgeEfctStdResponse.i0204 || "",
          // 냄새
          i04: data.dmgeEfctStdResponse.i04 || "",
          // 인화점
          i14: data.dmgeEfctStdResponse.i14 || "",
          // 밀도
          i26: data.dmgeEfctStdResponse.i26 || "",
          // 점도
          i36: data.dmgeEfctStdResponse.i36 || "",
          // 자연발화온도
          i32: data.dmgeEfctStdResponse.i32 || "",
          // 저장탱크용량
          strgTnkSz: data.dmgeEfctStdResponse.strgTnkSz || "",
          // 누출량 - default 탱크 설계용량과 동일
          lkgSz: data.dmgeEfctStdResponse.lkgSz || "",
          // 누출시간(초) - default 600초
          lkgTime: data.dmgeEfctStdResponse.lkgTime || 0,
          // mass
          mass: data.dmgeEfctStdResponse.mass || "",
          // pac2
          pac2: data.dmgeEfctStdResponse.pac2 || "",
          // pacUnit
          pacUnit: data.dmgeEfctStdResponse.pacUnit || "",
          // hcomb
          hcomb: data.dmgeEfctStdResponse.hcomb || "",
          /**
           * step3
           */
          // 기준일시
          stdDt: data.dmgeEfctStdResponse.stdDt,
          // 기온
          tmpr: data.dmgeEfctStdResponse.tmpr || "",
          // 습도
          hmdt: data.dmgeEfctStdResponse.hmdt || "",
          // 풍속
          wsd: data.dmgeEfctStdResponse.wsd || "",
          wsd1h: data.dmgeEfctStdResponse.wsd1h || "",
          wsd4h: data.dmgeEfctStdResponse.wsd4h || "",
          wsd8h: data.dmgeEfctStdResponse.wsd8h || "",
          // 풍향
          vec: data.dmgeEfctStdResponse.vec || "",
          // 대기 안정도
          atmsStblPct: data.dmgeEfctStdResponse.atmsStblPct || "",
        };

        setSimulateCommandData(newSimulateCommandData);
      }

      // 시뮬레이션 데이터
      if (!data.evlResponseList || !data.evlResponseList.length) {
        setSimulationRes(undefined);
      } else {
        let fireDmgeEvlResponse: DmgeEfctEvlResponse[] | null = null;
        let explDmgeEvlResponse: DmgeEfctEvlResponse[] | null = null;
        let dfsnDmgeEvlResponse: DmgeEfctEvlResponse[] | null = null;

        fireDmgeEvlResponse = data.evlResponseList.filter(
          (item) => item.acdntTypeEnu.type === "FIRE"
        );
        explDmgeEvlResponse = data.evlResponseList.filter(
          (item) => item.acdntTypeEnu.type === "EXPL"
        );
        dfsnDmgeEvlResponse = data.evlResponseList.filter(
          (item) => item.acdntTypeEnu.type === "DFSN"
        );
        setSimulationRes({
          dmgeEfctStdResponse: null,
          fireEvlResponseList: fireDmgeEvlResponse || null,
          explEvlResponseList: explDmgeEvlResponse || null,
          dfsnEvlResponseList: dfsnDmgeEvlResponse || null,
        });
        setAvailableMoveStep(3);
        setIsStep1Success(true);
        setIsStep2Success(true);
        setIsStep3Success(true);
      }
    }
  }, [data]);
```
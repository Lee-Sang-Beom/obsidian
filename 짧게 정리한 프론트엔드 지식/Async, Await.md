
#### 1. 정의

- `async`와 `await`는 JavaScript에서 **비동기 처리**를 보다 직관적이고 가독성 좋게 하기 위해 도입된 키워드이다. 
	- 기존의 `Promise`를 사용하는 방식에 비해 간결하고 직관적인 코드를 작성할 수 있게 해준다.
	- `Promise`는 비동기 처리을 할 때, 비동기 처리가 정상적으로 이루어지면 `resolve()` 함수를 실행하고, 실패하면 `reject()`함수를 실행한다.


#### 2. Async
```js
async function fetchData() {
    return "data"; // 이행된 Promise 객체 반환
}

fetchData().then(data => console.log(data)); // "data"
```
- `async`는 함수 앞에 붙이는 키워드로, 해당 함수를 `Promise`객체를 반환하는 함수**로 만들어 준다.
	- 함수가 `Promise`가 아닌 값을 반환하면, 자동으로 이행된(fulfilled) 상태의 `Promise`객체로 변환된다.


#### 3. Await
```js
async function fetchData() {
    let promise = new Promise((resolve, reject) => {
        setTimeout(() => resolve("Data received!"), 2000);
    });

    let result = await promise; // Promise가 해결될 때까지 기다림
    console.log(result);        // "Data received!" 출력
}

fetchData();
```
- `await`은 **`Promise`가 처리될 때까지 기다리는 역할**을 한다.
	- 이는 `async` 함수 내부에서만 사용할 수 있다.
	  
- `await`은 해당 `Promise`가 완료될 때까지 **코드를 일시 정지**시키고, 완료되면 결과 값을 반환한다.


#### 4. Promise와 Async/Await의 차이

> **예외 처리 방식**
- `Promise` : `catch()` 사용
- `async/await` : `try-catch`문 사용
```js
// Promise 예외 처리
fetch('invalid-url')
    .then(response => response.json())
    .catch(error => console.error('Error:', error));

// Async/Await 예외 처리
async function fetchData() {
    try {
        let response = await fetch('invalid-url');
        let data = await response.json();
    } catch (error) {
        console.error('Error:', error);
    }
}

fetchData();
```

> **가독성**
- `Promise` : `then()`을 여러 번 연결하여 Promise Chaining으로 비동기 흐름을 처리할 수 있지만, 콜백 지옥처럼 **가독성이 떨어질 수** 있다.
- `async/await` : 비동기 코드를 **동기 코드처럼 읽을 수** 있어 가독성이 더 좋다.
```js
// Promise 체이닝 예시
fetchData()
    .then(data => process(data))
    .then(result => display(result))
    .catch(error => console.error(error));

// Async/Await 예시
async function handleData() {
    try {
        const data = await fetchData();
        const result = await process(data);
        display(result);
    } catch (error) {
        console.error(error);
    }
}

handleData();
```

> **※ 참고**
- `async/await`은 비동기 작업을 동기 작업처럼 처리하는 것처럼 보이지만, **내부적으로는 여전히 비동기 작업을 진행한다.**
	- **Await**는 비동기 작업의 완료를 기다리기 때문에, **병렬 처리**가 필요할 때는 `Promise.all()`과 같이 사용해 성능을 최적화할 수 있다.
```js
async function parallelFetch() {
    let [data1, data2] = await Promise.all([fetchData1(), fetchData2()]);
    console.log(data1, data2);
}
```


#### 5. 요약

- **Async** 함수는 항상 `Promise`를 반환한다.
- **Await**는 `Promise`가 해결될 때까지 기다리고, `Promise`가 반환하는 값을 반환한다.
- `Promise`와 비교했을 때, `async/await`은 코드의 가독성을 높이고, 예외 처리를 `try-catch`로 더 직관적으로 처리할 수 있다.


#### 6. useQuery와 async/await 사용 예시

- 아래 코드는 지도에 표시할 마커 데이터를 API를 통해 받아온 후, 중간에 각 데이터의 좌표계를 EPSG:4326으로 통일시킨 다음 최종 데이터를 반환하는 코드이다.
```ts
// ...

const fetchMapGhMarkerList = async (
  remark: string | null
): Promise<MapGhMarkerResponse[] | null> => {
  if (!remark) {
    return null;
  }

  const res = await fetch(`API 주소`);

  if (!res.ok) {
    throw new Error("Network response was not ok");
  }

  const { data }: ApiResponse<MapGhMarkerResponse[]> = await res.json();

  return data;
};

export const useGetMapGhMarkerList = (
  selectGMapDepth3SubMenuState: AuthMenuTreeResponse | null
) => {
  return useQuery({
    queryKey: ["useGetMapGhMarkerList", selectGMapDepth3SubMenuState],

    queryFn: async () => { // async,await
      const res = await fetchMapGhMarkerList(
        selectGMapDepth3SubMenuState && selectGMapDepth3SubMenuState.remark
          ? selectGMapDepth3SubMenuState.remark
          : null
      );

      const epsg4326Data = res
        ? res
            .filter((marker) => {
              return marker.lat != null && marker.lot != null;
            })
            .map((marker) => {
              if (marker.gcs === "EPSG4326") {
                return marker;
              } else if (marker.gcs === "EPSG2097") {
                const { lat, lng } = transformEPSG2097Coords(
                  parseFloat(marker.lot),
                  parseFloat(marker.lat)
                );
                return {
                  ...marker,
                  lat: lat.toString(),
                  lot: lng.toString(),
                };
              } else if (marker.gcs === "EPSG3857") {
                const { lat, lng } = transformEPSG3857Coords(
                  parseFloat(marker.lot),
                  parseFloat(marker.lat)
                );
                return {
                  ...marker,
                  lat: lat.toString(),
                  lot: lng.toString(),
                };

                // return marker;
              } else if (marker.gcs === "EPSG5174") {
                const { lat, lng } = transformEPSG5174Coords(
                  parseFloat(marker.lot),
                  parseFloat(marker.lat)
                );
                return {
                  ...marker,
                  lat: lat.toString(),
                  lot: lng.toString(),
                };
              } else if (marker.gcs === "EPSG5179") {
                const { lat, lng } = transformEPSG5179Coords(
                  parseFloat(marker.lot),
                  parseFloat(marker.lat)
                );
                return {
                  ...marker,
                  lat: lat.toString(),
                  lot: lng.toString(),
                };
              } else {
                return marker;
              }
            })
        : null;

      return epsg4326Data;
    },
  });
};

```
- **Spread operator**와 **rest parameter**는 모두 `...`을 사용하지만, 그 역할과 사용하는 상황이 다르다.
	- 그렇다면, 대체 무엇이 다를까?


#### 1. Spread Operator (`...`)

- **역할**: 배열이나 객체의 요소들을 풀어 새로운 배열, 객체, 함수 인자 등으로 확장하는 데 사용된다.
- **사용 사례**:
    - 배열이나 객체의 복사, 결합
    - 함수 호출 시 여러 인자를 펼쳐서 전달
    - 문자열이나 iterable 객체를 배열로 변환

```ts
// 배열에서 사용
const arr1 = [1, 2, 3];
const arr2 = [...arr1, 4, 5];  // [1, 2, 3, 4, 5]

// 객체에서 사용
const obj1 = { a: 1, b: 2 };
const obj2 = { ...obj1, c: 3 };  // { a: 1, b: 2, c: 3 }

// 함수 호출에서 사용
function add(x, y, z) {
  return x + y + z;
}
const nums = [1, 2, 3];
console.log(add(...nums));  // 6
```

- 필자는 주로, 검색 조건 정보를 담는 QueryString 객체를 조작할 때 굉장히 많이 사용한다.
```ts
export default async function SearchTotalServer({ searchParams }: IProps) {
  let queryInstance: IntegratedSearchCommand = {
    ...searchParams, // spread operator (기존내용 가져오기)
    searchKeyword: decodeURIComponent(searchParams.searchKeyword),
  };

  const { data }: ApiResponse<IntegratedSearchAllResponse> =
    await getAllSearch(queryInstance);

  return (
    <WaContentWrap>
      <SearchTotalClient data={data} queryInstance={queryInstance} />
    </WaContentWrap>
  );
}
```
### 3. spread operator와 rest parameter의 차이

- 한마디로 이를 정리하자면, 아래와 같이 구분할 수 있습니다.
    - **spread operator**는 데이터를 풀어놓는다는 의미
    - **rest parameter**는 전달받은 남은 데이터를 배열이나, 객체 안에 채워넣는다는 의미

- 둘은 생김새는 비슷하지만, 역할에는 분명한 차이가 있습니다.
    - **spread operator**는 iterable한 객체에 대해. 기존 요소를 건드리지 않고, 값들을 펼쳐내어 새로운 객체나 배열을 만드는 데에 사용합니다.
        -또한, spread operator로 풀어낸 요소들은 함수의 인자로 전달할 수도 있습니다.

    - 반면, **rest parameter**는 함수의 파라미터가 몇 개인지 확실치 않은 상황에서 사용할 수 있습니다. rest parameter는 파라미터를 받는 맨 뒤의 위치에서 요소들을 받는 경우에 사용합니다.
        - 또한 객체나 배열요소에 대해 destructing 할당을 할 경우에도 사용할 수 있습니다.
        - 여기서 `...`는 파라미터 위치에서 쓰이면 rest parameter로, 구조분해 할당에서 쓰이면, rest elements로 불립니다.
            - 객체 구조분해할당에서 쓰이는 `...`은 rest properties라고 하는데, 이는 `ES9`에서 도입된 기능입니다.


## 구조분해 할당

- **구조분해할당**이란, 배열 혹은 객체의 값이나 프로퍼티를 분리하여, 그 값을 별도의 변수에다가 담을 수 있도록 하는 표현식입니다.
    
- 배열일 때는 `[](대괄호)`를, 객체일 때는 `{}(중괄호)`를 사용할 수 있습니다.
    
    - 할당받는 쪽의 마지막 위치에는 **rest parameter**를 사용하여, 대응되지 않은 나머지 인수를 묶어 받아들일 수 있습니다.
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


#### 2. Rest parameter (`...`)

- **역할**: 함수나 배열, 객체의 나머지 요소를 하나의 변수로 묶어 받는 역할을 한다.
- **사용 사례**:
    - 함수의 매개변수에서, 정해지지 않은 개수의 인자를 배열로 받아 처리할 때
    - 객체나 배열에서 일부 요소를 분리한 후, 나머지 요소들을 모아 처리할 때

```ts
// 함수에서 사용
function sum(...numbers) {
  return numbers.reduce((acc, cur) => acc + cur, 0);
}
console.log(sum(1, 2, 3, 4));  // 10

// 배열 구조분해에서 사용
const [first, ...rest] = [1, 2, 3, 4];
console.log(first);  // 1
console.log(rest);   // [2, 3, 4]

// 객체 구조분해에서 사용
const { a, ...others } = { a: 1, b: 2, c: 3 };
console.log(a);       // 1
console.log(others);  // { b: 2, c: 3 }
```


#### 3. 주요 차이점

1. **Spread operator**는 값을 "펼쳐서" 새로운 배열, 객체, 함수 인자 등으로 확장할 때 사용된다.
	- iterable한 객체에 대해. 기존 요소를 건드리지 않고, 값들을 펼쳐내어 새로운 객체나 배열을 만드는 데에 사용한다.
	- 또한, spread operator로 풀어낸 요소들은 함수의 인자로 전달할 수도 있다.

 
2. **Rest parameter**는 함수의 인자나 배열/객체에서 "나머지" 값을 묶어 배열이나 객체로 받을 때 사용된다.
	- 함수의 파라미터가 몇 개인지 확실치 않은 상황에서 사용할 수 있다. 
		- **Rest parameter**는 파라미터를 받는 맨 뒤의 위치에서 요소들을 받는 경우에 사용한다.
	
	- 또한 객체나 배열요소에 대해 구조분해 할당을 할 경우에도 사용할 수 있다.
        - `...`는 파라미터 위치에서 쓰이면 **Rest parameter**로, 구조분해 할당에서 쓰이면, **Rest elements**로 불린다.
		- **구조 분해 할당** 시 사용하는 **rest elements(...)** 는, 배열이나 객체에서 일부 값을 추출한 후 나머지를 관리할 때 유용하다.

```js
// 1. object.prop 값이 변수 varName에 할당된다.
// 2. object 안에 prop이 없으면 (undefined), deafult가 varName에 할당된다.
// 3. 연결할 변수가 없는 나머지 object의 프로퍼티들은 객체 rest에 복사된다.
let {prop : varName = default, ...rest} = object

// ----------

let obj1 = { prop: undefined, other: 2 };
let obj2 = { other: 2 };

// 첫 번째 경우: object.prop이 undefined인 경우
let { prop: varName1 = 'default', ...rest1 } = obj1;
console.log(varName1);  // 'default'
console.log(rest1);     // { other: 2 }

// 두 번째 경우: object에 prop이 없는 경우
let { prop: varName2 = 'default', ...rest2 } = obj2;
console.log(varName2);  // 'default'
console.log(rest2);     // { other: 2 }

```

```js
// 1. array의 1번째 요소는 item에, 2번째 요소는 item2에 할당된다.
// 2. 만약, item1이 undefined이면 'default'라는 값이 저장된다.
// 2. 이어지는 나머지 요소는 배열 rest에 저장된다.
let array = [undefined, 2, 3, 4]; 
let [item1 = 'default', item2, ...rest] = array; 

console.log(item1); // 'default' 
console.log(item2); // 2 
console.log(rest); // [3, 4]
```
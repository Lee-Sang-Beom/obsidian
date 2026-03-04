- JavaScript에서 `Iterable`은 반복 가능한 객체를 의미하며, `for...of` 루프나 전개 연산자(`...`) 등과 같은 반복문에서 사용할 수 있는 객체이다.
- 반복 가능한 객체는 `Symbol.iterator()` 메소드를 구현하고 있어야 하며, 이 메소드는 객체가 어떻게 반복될지 정의한다.


#### 1. `Symbol.iterator()`

- `Iterable` 객체는 `Symbol.iterator`라는 키를 가진 메소드를 가지고 있다. 
	- 이 메소드는 `Iterator` 객체를 반환해야 한다.

- `Iterator` 객체는 `next()` 메서드를 제공하며, `next()` 메서드는 객체의 다음 값을 반환하는데, 반환 값은 아래의 형태를 가진다.

> `{ value: <값>, done: <boolean> }` 
- `value`: 현재 순회에서 반환된 값.
- `done`: 반복이 끝났는지를 나타내는 `boolean` 값이다. `true`이면 반복이 끝났다는 의미이다.


#### 2. iterator

- 반복 작업을 제어하는 객체로, 반복되는 시퀀스의 값을 하나씩 반환할 수 있다.
    - `next()` 메서드를 호출할 때마다 순차적으로 값을 반환하며, 반복이 끝나면 `done: true`가 반환된다.

- **배열**, **문자열**, **Set**, **Map** 등은 기본적으로 `Iterable`을 구현하고 있다. 즉, `for...of` 구문으로 순회를 진행할 수 있다는 뜻이다.


#### 3. 예시 1. 배열 반복

- 배열은 기본적으로 `Iterable`이기 때문에, 내부적으로 `Symbol.iterator` 메서드를 가지고 있어 `for...of`로 순회할 수 있다.
```js
const arr = [1, 2, 3];
for (const value of arr) {
  console.log(value); // 1, 2, 3 순서대로 출력
}
```


#### 4. 예시 2. 직접적인 Iterator 사용

- 배열은 기본적으로 `Iterable`이기 때문에 `Symbol.iterator` 메서드를 사용할 수 있으며, 이를 통해 명시적으로 `Iterator` 객체를 만들고, `next()`를 호출하여 값을 하나씩 가져올 수 있다.

```js
const arr = [1, 2, 3];
const iterator = arr[Symbol.iterator]();

console.log(iterator.next()); // { value: 1, done: false }
console.log(iterator.next()); // { value: 2, done: false }
console.log(iterator.next()); // { value: 3, done: false }
console.log(iterator.next()); // { value: undefined, done: true }
```


#### 5. Iterator을 사용하여 구현된 다른 예시

> **전개 연산자**
- 사용 예시
```tsx
const arr = [1, 2, 3]; 
const newArr = [...arr, 4, 5]; // [1, 2, 3, 4, 5]
```

- 실제 코드 사용 예시
```tsx
useEffect(() => {
	setMarkerPagingSearchOption((prev) => {
	  return {
		...prev,
		page: 0,
	  };
	});
}, [selectMapDepth3SubMenuState]);
```

> **Array.from()**
- 사용 예시
```tsx
const iterable = new Set([1, 2, 3]);
const arr = Array.from(iterable); // [1, 2, 3]
```

- 실제 코드 사용 예시
```tsx
{Array.from({ length: 5 }).map((_, idx: number) => {
  return (
	<tr key={`인기검색어 순위 : ${idx+1}위, ${srchStatList[idx].srchWord}`}>
	  <td>{idx + 1}</td>
	  <td>
		{srchStatList &&
		srchStatList.length > idx &&
		srchStatList[idx]
		  ? srchStatList[idx].srchWord
		  : ""}
	  </td>
	  <td>
		{srchCrollingOpenDataList &&
		srchCrollingOpenDataList.length > idx &&
		srchCrollingOpenDataList[idx]
		  ? srchCrollingOpenDataList[idx].srchWord
		  : ""}
	  </td>
	  <td>
		{srchCrollingKosisList &&
		srchCrollingKosisList.length > idx &&
		srchCrollingKosisList[idx]
		  ? srchCrollingKosisList[idx].srchWord
		  : ""}
	  </td>
	</tr>
  );
})}
```
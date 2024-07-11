
- 자바스크립트에서는 객체나 배열을 복사할 때, **깊은 복사(Deep Copy)** 와 **얕은 복사(Shallow Copy)** 라는 개념이 있다.
	- 과연 이들의 **차이**는 무엇일까?

#### 1. 깊은 복사

- **깊은 복사(Deep Copy)** 는 '실제 값'을 새로운 메모리 공간에 복사하는 것을 의미한다.
	- 즉, 원본 객체의 모든 값을 복사하면서, 내부의 객체나 배열도 재귀적으로 복사하여 새로운 객체를 생성하는 복사 방식을 의미하는 것이다.
	- 복사된 객체는 원본 객체와 완전히 분리된 별도의 객체로 볼 수 있다.
```ts
let originalObject = { a: 1, b: { c: 2 } };

// 깊은 복사 -> 값을 그대로 복사한 새로운 객체를 가져오기 위해, JSON stringify와 parse 메소드를 이용함
let deepCopy = JSON.parse(JSON.stringify(originalObject));

// 원본 객체와 깊은 복사된 객체 비교
console.log(originalObject === deepCopy); // false
console.log(originalObject.b === deepCopy.b); // false (내부 객체도 새로운 복사본을 가짐)
```

-   **1depth 까지의 얕은 비교용 깊이**에서의 깊은 복사는 `Object.assign(), spread operator` 등을 활용할 수 있다.
    - 그러나, 그 이상의 완벽한 깊은 복사는 `JSON.parse()`와 `JSON.stringify()` 등의 방법을 사용할 수 있다.


#### 2. 얕은 복사

- 하지만, **얕은 복사(Shallow Copy)** 는 '주소 값'을 복사하기 때문에, 서로 다른 식별자여도 참조하고 있는 실제값은 같다.
	- 즉, 얕은 복사는 복사된 객체가 원본 객체의 구조와 같지만 내부의 객체나 배열 등의 참조형 데이터는 동일한 참조를 가지는 복사 방식임을 의미한다.
	- 즉, 복사된 객체와 원본 객체는 같은 내부 객체를 참조하게 된다.
```ts
let originalArray = [1, 2, [3, 4]];

// 얕은 복사
let shallowCopy = [...originalArray];

// 원본 배열과 얕은 복사된 배열 비교
console.log(originalArray === shallowCopy); // false
console.log(originalArray[2] === shallowCopy[2]); // true (내부 배열은 같은 참조를 가짐)
```

- 위 예제에서 `shallowCopy`는 `originalArray`의 구조는 같지만, 내부의 배열 `[3, 4]`는 동일한 참조를 공유한다. 
	- 따라서 `originalArray`의 내부 배열을 수정하면 `shallowCopy`에도 반영되게 된다.

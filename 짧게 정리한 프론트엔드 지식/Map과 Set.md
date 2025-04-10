
#### 1. 정의

- 자바스크립트에서 `Map` 객체와 `Set` 객체는 각각 `key-value` 쌍과 고유한 값의 집합을 관리하기 위한 데이터 구조이다.
- 이들은 ES6(ECMAScript 2015)에서 추가된 기능으로, 기존의 객체(`Object`)와 배열(`Array`)보다 더 효율적으로 특정 작업을 수행할 수 있도록 설계되었다.


#### 2. Map

- Map은 객체처럼 key가 있는 데이터를 저장한다.

- 객체(Object)는 key를 문자형 데이터로 변환하는 과정을 거쳐, 문자형 데이터만 허용하는 특징이 있지만, Map은 키의 타입을 변환하지 않고 유지하기 때문에 다양한 자료형을 키로써 사용할 수 있다.

```js
let map = new Map();

map.set('name', 'John');
map.set(42, 'The answer');
map.set({ foo: 'bar' }, 'baz');

console.log(map.get('name')); // "John"
console.log(map.get(42)); // "The answer"
console.log(map.size); // 3

```
- Map에는 아래와 같은 메소드, 프로퍼티가 존재한다.
    - `new Map()` : 새로운 Map 객체 요소를 만든다.
    - `map.set(key, value)` : key를 이용해 value를 저장한다.
    - `map.get(key)` : key에 해당하는 값을 반환합니다. key가 존재하지 않으면 `undefined`를 반환한다.
    - `map.has(key)` : key가 존재하면 `true`, 존재하지 않으면 `false`를 반환한다.
    - `map.delete(key)` : key에 해당하는 값을 삭제한다.
    - `map.clear()` : Map 객체 안의 모든 요소를 제거한다.
    - `map.size` : 요소의 개수를 반환한다.

- Map을 사용하면서 주의할 점은 Map 객체 요소에 접근 및 수정을 가할 때, 전용 메소드인 `.set()` 및 `.get()`를 사용해야 한다는 점이다.
    - 일반 객체처럼 취급하여 값에 접근할 경우, 여러 제약이 생기기 때문이다.



#### 2. Set

- Set은 Map과 다르게 중복을 허용하지 않는 값을 모아두며, 키를 따로 지정하지 않는다.
    - 즉, 중복되지 않는 값을 기록하기 위한 용도의 자료구조를 사용해야 할 때 적합한 고유한 값의 집합을 저장하는 컬렉션이다.

- Set에는 아래와 같은 메소드, 프로퍼티가 존재한다.
    - `new Set(iterable)` : 새로운 Set 객체 요소를 만든다. iterable 객체를 전달받으면, 그 안의 값을 복사해 Set에 넣어준다.
    - `set.add(value)`: 값을 추가하고 Set 객체 자신을 반환한다.
    - `set.delete(value)` : 값을 제거한다. 호출 시점에 Set 객체 내에 값이 있어서 제거에 성공하면`true`, 아니면 `false`를 반환한다.
    - `set.has(value)` : Set 객체 내에 값이 존재하면 `true`, 아니면 `false`를 반환한다.
    - `set.clear()` : Set 객체를 비운다.
    - `set.size` :  요소의 개수를 반환한다.
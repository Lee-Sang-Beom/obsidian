## Map

- Map은 객체처럼 key가 있는 데이터를 저장합니다.
- 객체는 key를 문자형 데이터로 변환하는 과정을 거쳐, 문자형 데이터만 허용하는 특징이 있지만, Map은 키의 타입을 변환하지 않고 유지하기 때문에 다양한 자료형을 키로써 사용할 수 있습니다.
- Map에는 아래와 같은 메소드, 프로퍼티가 존재합니다.
    - new Map() : map 요소를 만듭니다.
    - map.set(key, value) : key를 이용해 value를 저장합니다.
    - map.get(key) : key에 해당하는 값을 반환합니다. key가 존재하지 않으면 undefined를 반환합니다.
    - map.has(key) : key가 존재하면 true, 존재하지 않으면 false를 반환합니다.
    - map.delete(key) : key에 해당하는 값을 삭제합니다.
    - map.clear() : 맵 안의 모든 요소를 제거합니다.
    - map.size : 요소의 개수를 반환합니다.

- Map을 사용하면서 주의할 점은 Map 요소에 수정 및 접근할 때, 전용 메소드인 set() 및 get()를 사용해야 한다는 점입니다.
    - 일반 객체처럼 취급하여 값에 접근할 경우, 여러 제약이 생기기 때문입니다.

---

## Set

- Set은 Map과 다르게 중복을 허용하지 않는 값을 모아두며, 키를 따로 지정하지 않습니다. 
    - 즉, 중복되지 않는 값을 기록하기 위한 용도의 자료구조를 사용해야 할 때, 적합합니다.

- Set에는 아래와 같은 메소드, 프로퍼티가 존재합니다.
    - new Set(iterable) : Set 요소를 만듭니다. iterable 객체를 전달받으면, 그 안의 값을 복사해 Set에 넣어줍니다.
    - set.add(value) : 값을 추가하고 Set 자신을 반환합니다.
    - set.delete(value) : 값을 제거합니다. 호출 시점에 Set 내에 값이 있어서 제거에 성공하면 true, 아니면 false를 반환합니다.
    - set.has(value) : Set 내에 값이 존재하면 true, 아니면 false를 반환합니다.
    - set.clear() : Set을 비웁니다.
    - set.size : Set에 몇 개의 값이 있는지 세줍니다.

---

### 참고자료 : https://ko.javascript.info/map-set

## 공통

```
const obj = {name: 'tom'};
const say = function(city){
  console.log(`hello ${this.name}, this is ${city}`);
}

say('seoul'); // hello undefined, this is seoul
```

- 자바스크립트의 함수는 각자 this라는 것을 보유합니다. 하지만, **상황에 따라 함수 내 this를 변경**해야 할 수 있습니다.

- call, bind, apply 함수 모두, 특정 함수가 가지는 this를 특정하여 지정하기 위해 사용한다는 공통점이 있습니다.

---

## call

```
const obj = {name: 'tom'};
const say = function(city){
  console.log(`hello ${this.name}, this is ${city}`);
};

say.call(obj, 'seoul'); // hello tom, this is seoul

---

function foo(a, b, c) {
    console.log(a + b + c);
    console.log(this);
};


foo.call(obj, 1, 2, 3); // 6, { name: 'tom' }
```

- call함수는, 변경할 this를 대체할 대상을 1번째 인자로 전송합니다.
- 그리고, 함수가 필요로하는 파라미터가 있다면, **2번째 인자부터 차례로 넣어 전달해주는 방식**을 사용합니다.

---

## apply

```
const obj = {name: 'tom'};
const say = function(city){
  console.log(`hello ${this.name}, this is ${city}`);
};

say.apply(obj, ['seoul']) // hello tom, this is seoul

---

function foo(a, b, c) {
    console.log(a + b + c);
    console.log(this);
};
  
foo.apply(obj, [1, 2, 3]); // 6, { name: 'tom' }
```

- apply의 첫 번째 인자는 call함수와 똑같이 this를 대체할 대상을 넣습니다. 
- apply는 call 함수와는 다르게, 두 번째 인자부터는 **모두 배열에 넣어 전달해주어야 한다**는 특징을 가집니다.

---

## bind

```
const obj = {name: 'tom'};
const say = function(city){
  console.log(`hello ${this.name}, this is ${city}`);
};

const boundSay = say.bind(obj);
boundSay("busan"); // hello tom, this is busan
```

- bind함수는 call, apply와 다르게 **this가 대체된, 바인딩된 함수를 리턴**합니다.
- 이 함수는 전달한 값을 this로 갖고 있습니다. 즉, 함수를 가지고 있다가 나중에 함수를 사용할 수 있습니다.
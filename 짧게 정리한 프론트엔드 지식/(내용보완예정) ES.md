
## ES

[](https://github.com/Lee-Sang-Beom/Short_web_concept_summary/blob/main/1.%20%EC%9B%B9%20%EA%B0%9C%EB%B0%9C%20%EA%B8%B0%EC%B4%88%EA%B0%9C%EB%85%90/ES%EB%9E%80.md#es)

- **ES(ECMA Script)**는 모든 브라우저에서 동일하게 의도한 기능이 등장하지 않는 **크로스 브라우징 이슈** 를 계기로, **자바스크립트를 표준화**하기 위해 등장했습니다.
    
- ECMA Script는 **자바스크립트 문법의 규격 및 표준**이라 말씀드릴 수 있습니다.
    

---

## ES6?

[](https://github.com/Lee-Sang-Beom/Short_web_concept_summary/blob/main/1.%20%EC%9B%B9%20%EA%B0%9C%EB%B0%9C%20%EA%B8%B0%EC%B4%88%EA%B0%9C%EB%85%90/ES%EB%9E%80.md#es6)

- ES6란 2015년 6월에 개정된, ES의 6번째 버전을 의미합니다.
    
    - ES6에는 복잡한 응용 프로그램을 작성하기 위한 새로운 문법들이 많이 추가되었고, 이는 코드의 가독성과 유지보수성을 향상시켰습니다.
        
    - 가장 핵심적인 변화가 있었고 대부분의 상황에 대해 안정적인 표준이 된 버전이 ES6이기 때문에, ES6의 출시는 의미가 깊다고 생각할 수 있습니다.
        
- ES6에 출시된 문법은 아래와 같은 것들이 있습니다.
    
    1. spread operator
        
        - iterable 객체에 적용하여, 요소를 풀어 전개할 수 있는 문법입니다.
        - Object 요소를 꺼낼 때는 `{}`를, Array 요소를 꺼낼 때는 `[]`를 이용하는 것을 기본으로 합니다.
            - Object 복사의 경우, key값이 같은 경우 뒤에오는 요소가 앞의 요소를 덮어씌우므로 주의해야 합니다.
    2. arrow function
        
    3. let과 const
        
    4. import 와 export
        
    5. Map과 Set
        
    6. template literal
        
        - 백틱(`) 기호안에 문자열을 나열하고 $와 {} 기호 안에 변수를 넣어 출력할 수 있도록 합니다.
    7. 객체 리터럴에서,
        
        - 객체의 키를 대괄호로 동적 생성이 가능합니다
        - 객체 내 메소드 생성의 간소화가 가능합니다. (key,function 키워드 생략)
        - `Shorthand property names` : name, value 문자열이 같은 경우에는 단축하여 표기가 가능합니다.
    8. for of 문법
        
        - iterable 객체에 대해 내부적으로 `next()`메소드를 사용하며 값을 살펴봅니다.
        - index가 아니라 값을 추출하는 특징이 있습니다.
    9. default parameter
        
        - 함수 실행 시, 전달한 값이 없거나 undefined가 전달될 때, 함수 내부에서 매개변수를 기본값으로 초기화합니다
    10. 제너레이터
        
    11. rest parameter - spread operator가 iterable 객체에 `...`을 붙여 요소를 전개시킨다면, - rest parameter는 함수 파라미터를 받는 위치에 쓰여, 따로 정의하지 않은 남는 요소를 배열로 받아주는 역할을 합니다.
        
    12. Destructuring Assignment
        
    
    - 배열 혹은 객체의 값이나 프로퍼티를 분리하여, 그 값을 별도의 변수에다가 담을 수 있도록 하는 표현식입니다.
    
    13. Ternary Operator
    
    - 한 줄로 조건문을 기술하는 방법입니다. `ex) const value = isTrue ? 100 : 200`
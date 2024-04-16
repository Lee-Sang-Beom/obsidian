
## spread operator와 rest parameter의 차이

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
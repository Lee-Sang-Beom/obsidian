
#### 1. `useReducer`란?

- `useReducer`는 `useState`를 대체할 수 있는 함수이다.
	- `reducer`를 사용하여 컴포넌트의 상태 로직을 관리하는 데 사용하며, 주로 `useState`보다 복잡한 상태 로직을 처리해야할 때 사용한다고 한다.
	
- 작성자 본인은 평소 습관적으로 `useState`를 사용하다가 `useReducer`에 대한 내용을 살펴보면 당장에 잘 이해가 되지 않았다.
	- 본인은 몇 개의 프로젝트를 진행하면서, **많은 입력 Form을 관리**해야 하는 상황이 있다면, `react-hook-form`을 사용했었기 때문에 `useReducer`의 필요성을 느끼지 못했었다.
	- 그래도 기회가 되면 나중에 다시 공부해보고 실 프로젝트에 적용해보고싶다.


#### 2. `useReducer` 사용하기

- `useReducer`의 구성요소는 크게 3가지가 있다.
	1. `reducer`: `state`를 업데이트시켜주는 역할의 **함수**
	2. `dispatch`: `reducer`에게 보내는 일련의 **요구**
	3. `action`: `reducer`에게 보내는 일련의 요구에 대한 **내용**

- `useReducer`의 초기 정의는 컴포넌트 최상단에서 다음과 같이 이루어진다.
	- `const [stateName, dispatch function] = useReducer(reducer func, state 초기값);`
	
	- **첫 번째 인자**: `useReducer`는 배열을 반환하는데, 첫 번째 인자는 새로 만들어진 `state`이다.
		- `useReducer`라고 별반 `useState`와 다르진 않으나, 이 `state`는 `reducer`함수를 통해 **변경**하여야 한다.

	- **두 번째 인자**: `useReducer`가 만들어준 `dispatch`함수이다.
		- `useState`의 `setState dispatch`와 다르게 `reducer` **함수를 실행시킨다는 점**에서 차이가 있다.


#### 3. 예제 1 (예금 및 출금)

```jsx

// next.js 14버전에서 테스트한 내용이라 지시어가 필요해서 넣음
"use client";

import { useReducer, useState } from "react";

// 상수값 정의
const ACTION_TYPES = {
  deposit: "deposit",
  withdraw: "withdraw",
};

/**
 * @reducer : useReducer에서 정의하는 데에 사용하고, dispatch로 인해 호출되는 함수
 * @param state : 변경 이전의 기존 state값
 * @param action : dispatch로 전달한 요소
 * @returns : 변경된 state 값
 */
const reducer = (state, action) => {
  switch (action.type) {
    case ACTION_TYPES.deposit:
      return state + action.payload;

    case ACTION_TYPES.withdraw:
      return state - action.payload;

    default:
      return state;
  }
};

export default function Page() {
  const [num, setNum] = useState(0);

  // 호출될 reducer 함수 및 초기값을 설정하여 useReducer hook 사용
  const [money, dispatch] = useReducer(reducer, 0);

  return (
    <div>
      <h2>useReducer 은행예제</h2>
      <p>잔고: {money}원</p>
      <input
        type="number"
        value={num}
        onChange={(e) => setNum(parseInt(e.target.value))}
        step="1000"
      />
      <button
        onClick={() => {
          /**
           * @dispatch : 실행 시, reducer함수 호출
           *
           * @type : 어떤 성격의 호출인가? (본 예제에서는 예금인지 출금인지)
           * @payload : 사용자가 입력한 금액
           */
          dispatch({ type: ACTION_TYPES.deposit, payload: num });
        }}
      >
        예금
      </button>
      <button
        onClick={() => {
          /**
           * @dispatch : 실행 시, reducer함수 호출
           *
           * @type : 어떤 성격의 호출인가? (본 예제에서는 예금인지 출금인지)
           * @payload : 사용자가 입력한 금액
           */
          dispatch({ type: ACTION_TYPES.withdraw, payload: num });
        }}
      >
        출금
      </button>
    </div>
  );
}

```


#### 예제 2 (학생 관리)

- `page.tsx`
```tsx
"use client";
import { useReducer, useState } from "react";
import Student from "./Student";

interface StudentType {
  id: number;
  name: string;
  isHere: boolean;
}

interface StateType {
  count: number;
  students: StudentType[];
}

interface ActionType {
  type: "ADD" | "CHECK" | "DEL";
  id?: number;
  data?: string;
}

const reducer = (state: StateType, action: ActionType) => {
  switch (action.type) {
    case "ADD":
      const name = action.data;
      const newStudent: StudentType = {
        id: Date.now(),
        name: name!,
        isHere: false,
      };

      /**
       * @return : 새로운 state
       * @count : 기존 state.count(학생수) + 1
       * @students : 새로 만든 newStudent를 기존 유저리스트 뒤에 추가
       */
      return {
        count: state.count + 1,
        students: [...state.students, newStudent],
      };

    case "DEL":
      /**
       * @return : 새로운 state
       * @count : 기존 state.count(학생수) - 1
       * @students : action에 전달된 id와 저장된 student들의 각 id를 비교해, 일치하지 않는 id를 가진 학생들만 반환하는 새로운 배열을 만들어 저장
       */
      return {
        count: state.count - 1,
        students: state.students.filter(
          (student: StudentType) => student.id !== action.id
        ),
      };

    case "CHECK":
      /**
       * @return : 새로운 state
       * @count : 기존 state.count(학생수 유지
       * @students : action에 전달된 id와 저장된 student들의 각 id를 비교해, 일치하는 student의 isHere을 토글시킴
       */
      return {
        count: state.count,
        students: state.students.map((student: StudentType) => {
          if (student.id === action.id) {
            return { ...student, isHere: !student.isHere };
          }

          return student;
        }),
      };

    default:
      return state;
  }
};

export default function Page() {
  const [name, setName] = useState("");
  const initState: StateType = {
    count: 0,
    students: [],
  };

  const [studentState, dispatch] = useReducer(reducer, initState);

  return (
    <div>
      <h1>출석부</h1>
      <p>총 학생 수 : {studentState.count}</p>
      <input
        type="text"
        placeholder="이름 입력"
        value={name}
        onChange={(e) => setName(e.target.value)}
      />
      <button
        onClick={() => {
          // reducer 함수 내, action.type === "ADD"인 로직 실행
          dispatch({ type: "ADD", data: name });
        }}
      >
        추가
      </button>

      <br />

      {/* 학생 각각을 배열로 map함수로 돌리면서 element list를 반환 */}
      {studentState.students.map((student: StudentType) => (
        <Student
          key={student.id}
          name={student.name}
          id={student.id}
          dispatch={dispatch}
          isHere={student.isHere}
        />
      ))}
    </div>
  );
}
```

- `Student.tsx(<Student ... />`
```tsx
import React, { Dispatch } from "react";

interface IProps {
  key: number;
  id: number;

  name: string;
  dispatch: Dispatch<any>;
  isHere: boolean;
}
const Student = ({ key, name, dispatch, id, isHere }: IProps) => {
  return (
    <div key={key}>
      <span
        style={{
          textDecoration: isHere ? "line-through" : "none",
          color: isHere ? "gray" : "black",
        }}
        onClick={() => {
          // reducer 함수 내, action.type === "CHECK"인 로직 실행
          dispatch({ type: "CHECK", id: id });
        }}
      >
        {name}
      </span>
      <button
        onClick={() => {
          // reducer 함수 내, action.type === "DEL"인 로직 실행
          dispatch({ type: "DEL", id: id });
        }}
      >
        삭제
      </button>
    </div>
  );
};

export default Student;

```

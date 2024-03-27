
- https://c17an.netlify.app/blog/React/useEffect-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0/article/
```js
import React, {useEffect} from 'react'

  useEffect(
    (이펙트 함수)
    return {
      (클린업 함수)
    }
  }, [의존값]);

  // 이펙트 함수 호출시점 : 컴포넌트가 처음 마운트될 때, 의존값으로 주어진 값이 변경될 때
  // 클린업 함수 호출시점 : 이펙트 함수가 호출되기 전, 컴포넌트가 언마운트될때 각각 클린업이 호출된다.

  // 의존값 배열이 비어 있으면 최초 마운트 시에만 이펙트를 호출한다.
  // 의존값이 존재하지 않으면 컴포넌트가 렌더링될 때마다 이펙트를 호출한다.
```
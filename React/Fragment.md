
#### 1. 사용배경

- React에서는 하나의 컴포넌트가 여러 개의 `element`를 반환한다. 

- 하지만, React 컴포넌트를 return하는 부분에서 사용하는 `jsx`, `tsx`문법을 사용할 때는 최상위 태그가 반드시 1개로만 구성되어 있어야 한다.
	- React는 하나의 컴포넌트만을 return할 수 있기 때문이다.


#### 2. 사용 방법

- 기본적으로는 `<React.Fragment></React.Fragment>` 혹은 `<></>`로 사용할 수 있다.

- 대표적으로 아래와 같은 table이 `React Fragment`의 사용 예시로 등장한다.
```jsx
// ...

// 해결 전
function Table(){
	return (
		{/* 불필요한 div 사용 */}
		<div>
			<td>Hello</td>
			<td>Fragment</td>
		</div>
	)
}

// 해결 후
function Table(){
	return (
		<>
			<td>Hello</td>
			<td>Fragment</td>
		</>
	)
}

function App(){
	return (
	<div className="App">
		<table>
			<tr>
				<TableBody/>
			</tr>
		</table>
	</div>
	)
}
```


#### 3. 주의사항

- 배열에서 사용할 수 있는 `map()`함수와 같이 사용할 경우 각별한 주의가 필요하다.
```tsx
"use client";

import style from "./subTop.module.scss";
import { GrFormNext, GrHomeRounded } from "react-icons/gr";
import { menuByDepth } from "@/utils/utils";
import { usePathname } from "next/navigation";
import React from "react";

interface subTopProps {
  title: string;
  flowChildMenuList: string[];
  requireContainer?: boolean;
}

export default function SubTop({
  title,
  flowChildMenuList,
  requireContainer,
}: subTopProps) {
  const pathname = usePathname();
  return (
    <>
    {/* ... */}
    {flowChildMenuList.map((menuName: string) => {
                  return (
                    <React.Fragment key={menuName}>
                      <GrFormNext
                        role="img"
                        aria-label="오른쪽 화살표 아이콘"
                        className={style.ico}
                      />
                      <p>{menuName}</p>
                    </React.Fragment>
                  );
                })}
    </>
  );
}

```
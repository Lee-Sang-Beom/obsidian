
- `clsx`는, 각 컴포넌트마다 해당되는 css 클래스 이름을 조건부로 결합하는 라이브러리이다.
	- 즉, **단일 혹은 묶음의 css 클래스 이름을 한 로직 안에서 관리할 수 있도록 도와주는 라이브러리**이다. 

#### 1. before (clsx 사용 이전)
```tsx
"use client";

import { useEffect, useState } from "react";
import { useRecoilState } from "recoil";
import "./AutoAlert.scss";
import {
  BsXCircleFill,
  BsExclamationTriangleFill,
  BsCheckCircleFill,
  BsInfoCircleFill,
} from "react-icons/bs";
import { StatusType } from "@/types/common/commonType";
import {
  openAlertState,
  openAlertStatus,
  openAlertText,
} from "@/recoilstate/autoAlert";
  
export default function AutoAlert() {
  const [openState] = useRecoilState(openAlertState);
  const [text] = useRecoilState(openAlertText);
  const [status] = useRecoilState<StatusType>(openAlertStatus);

  return (
  // 길다!!
    <div
      className={`alert ${
        status === "error"
          ? "error"
          : status === "info"
          ? "info"
          : status === "success"
          ? "success"
          : status === "warning"
          ? "warning"
          : "disabled"
      } ${openState ? "animation_wrap" : ""}`}
    >
      <p className={openState ? "animation_p" : ""}>
        {status === "error" ? (
          <BsXCircleFill color="var(--r01)" size={18} />
        ) : status === "info" ? (
          <BsInfoCircleFill color="var(--m-blue02)" size={18} />
        ) : status === "success" ? (
          <BsCheckCircleFill color="var(--sc)" size={18} />
        ) : (
          <BsExclamationTriangleFill color="var(--wn)" size={18} />
        )}
        <span>{text}</span>
      </p>
    </div>
  );
}
```


#### 2. after (clsx 사용 이후)

- className을 부여하는 코드가 보기 좋게 변경되었다.
```tsx
"use client";

import { useEffect, useState } from "react";
import { useRecoilState } from "recoil";
import "./AutoAlert.scss";
import {
  BsXCircleFill,
  BsExclamationTriangleFill,
  BsCheckCircleFill,
  BsInfoCircleFill,
} from "react-icons/bs";
import { StatusType } from "@/types/common/commonType";
import {
  openAlertState,
  openAlertStatus,
  openAlertText,
} from "@/recoilstate/autoAlert";
import clsx from "clsx";


export default function AutoAlert() {
  const [openState] = useRecoilState(openAlertState);
  const [text] = useRecoilState(openAlertText);
  const [status] = useRecoilState<StatusType>(openAlertStatus);

  // 보기 편하다!
  const alertStatusClassName = clsx({
    alert: true,
    animation_wrap: openState,
    error: status === "error",
    info: status === "info",
    success: status === "success",
    warning: status === "warning",
  });

  return (
  // 여기!!
    <div className={alertStatusClassName}>
      <p className={openState ? "animation_p" : ""}>
        {status === "error" ? (
          <BsXCircleFill color="var(--r01)" size={18} />
        ) : status === "info" ? (
          <BsInfoCircleFill color="var(--m-blue02)" size={18} />
        ) : status === "success" ? (
          <BsCheckCircleFill color="var(--sc)" size={18} />
        ) : (
          <BsExclamationTriangleFill color="var(--wn)" size={18} />
        )}
        <span>{text}</span>
      </p>
    </div>
  );
}
```

#### before (clsx 사용 이전)
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


#### after (clsx 사용 이후)
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
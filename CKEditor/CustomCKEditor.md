
#### 1. CustomeCKEditor.tsx
```tsx

"use client";

import React, { useRef } from "react";
import ClassicEditor from "@ckeditor/ckeditor5-build-classic";
import { CKEditor } from "@ckeditor/ckeditor5-react";

/**
* 에디터 작성 시점에서, 사용자에게 보여줄 스타일 정의파
*/
import "./Editor.scss";

const CustomCKEditor = ({ content, onContentChange }: CustomCKEditorProps) => {
  /**
   * 필요 func 구성
   */
  return (
    <>
      <CKEditor
        editor={ClassicEditor}
        data={content}
        onChange={(e, editor) => {
          const value = editor.getData();
          onContentChange(value);
        }}
        config={{
          // upload img 관리
          extraPlugins: [uploadPlugin],

          // toolbar 관리 func 제거
          removePlugins: ["Indent"],

          // oembed -> iframe으로 링크 영상이 출력되게끔 구성
          mediaEmbed: {
            previewsInData: true,
          },
        }}
      />
    </>
  );
};

export default CustomCKEditor;


```
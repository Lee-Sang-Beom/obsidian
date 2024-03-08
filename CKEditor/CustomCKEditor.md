※ 주의: `className`이나 `variable`명은 프로젝트에 맞게 변경해야함

#### 1. CustomCKEditor.tsx

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

#### 2. Editor.scss (CustomCKEditor.tsx에서 import하는 css 파일)

```css
// style.text (이미지 잘 나오면 굳이 없어도 됨)
img {
  width: auto;
}

// ckEditor root classNm : 버전에 따라 이름 및 하위 구성요소 다를 수 있음
.ck {
  margin: 0;
  font-weight: 400;
  font-style: normal;

  h2 {
    font-size: 32px;
  }
  h3 {
    font-size: 26px;
  }
  h4 {
    font-size: 22px;
  }
  p {
    font-size: 16px;
    text-align: left;
  }

  // Bold
  strong {
    font-weight: bold;
  }

  // Italic
  i {
    font-style: italic;
  }

  * a {
    display: inline-block;
    color: var(--m-blue02);
    font-weight: 400;
    cursor: pointer;

    &:hover {
      text-decoration: underline;
      font-weight: 500;
    }
  }

  // content: ol/ul list type and margin (list type set)
  &.ck-content {
    ol {
      margin-left: 20px;
      list-style: decimal;
    }
    ul {
      margin-left: 20px;
      list-style: circle;
    }
  }
}
```

#### 3. Editor.scss (Editor 내용이 실제로 보여질 위치에서 적용할 css)

```css
// 에디터가 출력되는 화면에서 정의할 유일한 이름의 className이나 id
// 에디터 버전에 따라 구조가 달라질 수 있음
.editor-wrap-box {
  width: 100%;
  border-bottom: 1px solid var(--gray-400);
  padding: 2rem 1.2rem;
  box-sizing: border-box;

  // style.text
  img {
    width: auto;
  }

  h2 {
    font-size: 32px;
  }
  h3 {
    font-size: 26px;
  }
  h4 {
    font-size: 22px;
  }
  p {
    font-size: 16px;
    text-align: left;
  }

  * a {
    display: inline-block;
    color: var(--m-blue02);
    font-weight: 400;

    &:hover {
      text-decoration: underline;
      font-weight: 500;
    }
  }

  // Bold
  strong {
    font-weight: bold;
  }
  // Italic
  i {
    font-style: italic;
  }

  // content: ol/ul list type and margin (list type set)
  ol {
    margin-left: 20px;
    list-style: decimal;
  }
  ul {
    margin-left: 20px;
    list-style: circle;
  }

  .table_detail .text {
    border-bottom: 1px solid var(--gray-400);
    padding: 2rem 1.2rem;
    box-sizing: border-box;
    min-height: 500px;
  }

  // left
  figure {
    width: 100%;
    height: 100%;
    position: relative;

    display: flex;
    align-items: flex-start;
    flex-direction: column;
    justify-content: center;

    figcaption {
      position: absolute;
      bottom: -20px;
      font-size: 14px;
    }
  }
  // center
  figure.image {
    justify-content: center;
    align-items: center;
  }
  // right
  figure.image-style-side {
    justify-content: center;
    align-items: flex-end;
  }

  figure.media > div[data-oembed-url] {
    position: relative;
    width: 100%;
    padding-bottom: 56.2493%; /* 16:9 비율 */
    height: 0;
    overflow: hidden;
  }

  figure.media > div[data-oembed-url] > div {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
  }

  figure.media > div[data-oembed-url] > div > iframe {
    width: 100%;
    height: 100%;
    border: 0;
  }
}
```

- 에디터 내용을 출력하는 컴포넌트의 태그는 아래와 같음

```tsx
{
  /* 디테일 내용 */
}
<div className="editor-wrap-box">
  <div
    dangerouslySetInnerHTML={{
      __html: detailText,
    }}
  ></div>
</div>;
```

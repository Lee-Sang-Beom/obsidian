
#### 1. mapContext.ts 생성

- React 컴포넌트 간에, 값을 공유하기 위한 context 파일을 생성한다. 
```tsx
"use client";
import React from "react";

// context 사용을 위한 createContext
const MapContext = React.createContext({});
export default MapContext;
```

- 본인이 진행한 프로젝트의 `layout.tsx` 하위 페이지에는 총 4개의 `map object`가 필요했기 때문에, 총 4개의 context를 생성했다.
	- `selectMapContext`, `fireMapContext`, `explMapContext`, `dfsnMapContext`


#### 2. MapComponent.tsx 생성

- 지도를 만드는 Map 객체를 생성한다. 
	- React의 `useContext hook`을 사용하기위한 `Provider`가 return 된다. 
```tsx
"use client";
import React, { useState, useEffect } from "react";
import MapContext from "./mapContext";
import "ol/ol.css";
import { Map, View } from "ol";
import { defaults } from "ol/control";
import { fromLonLat, get } from "ol/proj";
import { Tile } from "ol/layer";
import { OSM } from "ol/source";

const MapComponent = ({ children }: any) => {
  const [mapObj, setMapObj] = useState({});
  
  useEffect(() => {
    const map = new Map({
      controls: defaults({ zoom: true, rotate: false }).extend([]),
      layers: [
        new Tile({
          source: new OSM(),
        }),
      ],

      //target: "map",
      target: document.getElementById("map")!,
      view: new View({
        projection: get("EPSG:3857")!,
        center: fromLonLat([128.6491542, 35.2402561], get("EPSG:3857")!),
        zoom: 14,
        showFullExtent: true,
      }),
    });
    setMapObj({ map });
    return () => map.setTarget(undefined);
  }, []);

  // ✨ MapContext.Provider에 객체 저장
  return (
    <MapContext.Provider value={{ mapObj }}>{children}</MapContext.Provider>
  );
};

  

export default MapComponent;
```

- 마찬가지로, 본인이 진행한 프로젝트의 `layout.tsx` 하위 페이지에는 총 4개의 `map object`가 필요했기 때문에, 총 4개의 context에 해당하는 `Component`를 생성했다.
	- `selectMapComponent`, `fireMapComponent`, `explMapComponent`, `dfsnMapComponent`
	- `fireMapComponent`내용은 아래와 같다.
```tsx
"use client";
import React, { useState, useEffect } from "react";
import MapContext from "@/utils/map/mapContext";
import "ol/ol.css";
import { Map, View } from "ol";
import { defaults } from "ol/control";
import { fromLonLat, get } from "ol/proj";
import { OSM } from "ol/source";
import TileLayer from "ol/layer/Tile";
import FireMapContext from "@/utils/map/FireMapContext";

const FireMapComponent = ({ children }: any) => {
  const [mapObj, setMapObj] = useState({});

  useEffect(() => {
    const map = new Map({
      /**
       * @controls : https://openlayers.org/en/latest/apidoc/module-ol_control_Control-Control.html
       * 컨트롤은 화면의 고정 위치에 DOM 요소가 있는 표시되는 위젯이다. 사용자 입력(버튼)이 포함될 수도 있고 정보 제공용일 수도 있다.
       * 위치는 CSS를 사용하여 결정된다. 기본적으로 이는 CSS 클래스 이름으로 컨테이너에 배치되지만 ol-overlaycontainer-stopevent외부 DOM 요소를 사용할 수 있다.
       *
       * 아래는 zoom,rotate 비활성화에 더해 빈 배열을 확장하여, 추가적으로 어떠한 컨트롤 요소도 사용하지 않음을 의미.
       */
      controls: defaults({ zoom: false, rotate: false }).extend([]),
      layers: [
        /**
         * @layers : https://openlayers.org/en/latest/apidoc/module-ol_layer_Tile-TileLayer.html
         *
         * @TileLayer : 특정 해상도에 대한 확대/축소 수준으로 구성된 그리드에 사전 렌더링된 타일 이미지를 제공하는 레이어
         * - 옵션에 설정된 모든 속성은 BaseObject Layer 개체의 속성으로 설정된다.
         */
        new TileLayer({
          source: new OSM(),
          className: "base_osm",

          // 오류 발생 시, 임시 타일 사용
          useInterimTilesOnError: true,
        }),
      ],

      /**
       * @target : 지도가 표시될 DOM의 요소 설정
       */
      target: document.getElementById("firemap")!,
      //target: "map",

      /**
       * @View :  https://openlayers.org/en/latest/apidoc/module-ol_View-View.html
         - View 객체는 지도의 간단한 2D 출력을 담당하며, 이는 지도의 중심, 해상도, 회전을 변경하기 위해 작동하는 개체이다. 
         - View는 투영(Projection)을 갖는다. 이 투영은 중심의 좌표 시스템을 결정하며, 그 단위는 해상도의 단위를 결정한다.(해상도 당 투영 단위). 
         - 기본 투영은 웹 메르카토르(Web Mercator, EPSG:3857)이다.

         - 뷰 상태
            뷰(View)는 중심(center), 해상도(resolution) 및 회전(rotation) 세 가지 상태로 결정된다.
            각 상태에는 해당하는 게터(getter)와 세터(setter)가 있다. 예를 들어, 중심 상태의 경우 getCenter와 setCenter가 있다.

         - 줌 상태
            줌(zoom) 상태는 사실 뷰에 저장되지 않는다. 내부적으로 모든 계산은 해상도 상태를 사용한다. 그럼에도 불구하고 setZoom 및 getZoom 메서드가 있다. 또한, 한 시스템에서 다른 시스템으로 전환하기 위한 getResolutionForZoom 및 getZoomForResolution도 사용 가능하다.

         - 제약 사항
            setCenter, setResolution 및 setRotation은 뷰의 상태를 변경하는 데 사용될 수 있지만, 생성자에서 정의된 제약이 적용될 것이다.
            View 객체는 해상도 제약, 회전 제약 및 중심 제약을 가질 수 있다.

            해상도 제약은 일반적으로 최소/최대 값 제한 및 특정 해상도에 스냅하는데, 다음 옵션으로 결정된다: resolutions, maxResolution, maxZoom 및 zoomFactor.
            
            resolutions이 설정된 경우, 다른 세 개의 옵션이 무시된다. 기본적으로 뷰는 최소/최대 제약만 있으며, 예를 들어 핀치 줌으로 중간 줌 레벨을 허용한다.

            회전 제약은 특정 각도에 스냅하며, 다음 옵션으로 결정된다: enableRotation 및 constrainRotation. 기본적으로 회전은 허용되고, 수평에 접근할 때 값이 제로에 스냅된다.

            중심 제약은 extent 옵션에 의해 결정된다. 기본적으로 뷰 중심은 전혀 제약되지 않는다.
       */
      view: new View({
        projection: get("EPSG:3857")!,
        center: fromLonLat([128.6491542, 35.2402561], get("EPSG:3857")!),
        zoom: 14,
        showFullExtent: true,
      }),
    });

    setMapObj({ map });
    return () => map.setTarget(undefined);
  }, []);

  // ✨ MapContext.Provider에 객체 저장
  return (
    <FireMapContext.Provider value={{ mapObj }}>
      {children}
    </FireMapContext.Provider>
  );
};
export default FireMapComponent;

```


#### 3. `layout.tsx`에 Provider 연결하기

- `layout.tsx` 하위 컴포넌트들이 react에서 제공하는 `useContext hook`을 사용할 수 있도록 `children` 요소들을 `Provider`로 감싸주어야 한다.
	- 각 Map Component들이 `Provider`를 반환하므로, 다음과 같이 작성하면 된다.

```tsx
export const dynamic = "force-dynamic";

// provider import
import FireMapComponent from "@/components/common/Map/FireMapComponent";
import ExplMapComponent from "@/components/common/Map/ExplMapComponent";
import DfsnMapComponent from "@/components/common/Map/DfsnMapComponent";
import SelectMapComponent from "@/components/common/Map/SelectMapComponent";

export default async function SubLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <div className="s_container">
      <div>
        <SelectMapComponent>
          <FireMapComponent>
            <ExplMapComponent>
              <DfsnMapComponent>{children}</DfsnMapComponent>
            </ExplMapComponent>
          </FireMapComponent>
        </SelectMapComponent>
      </div>
    </div>
  );
```
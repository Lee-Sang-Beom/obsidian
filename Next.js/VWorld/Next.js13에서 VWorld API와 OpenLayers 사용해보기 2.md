
#### mapContext.ts 생성

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

#### MapComponent.tsx 생성

- 지도를 만드는 Map 객체를 생성한다. 
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

```
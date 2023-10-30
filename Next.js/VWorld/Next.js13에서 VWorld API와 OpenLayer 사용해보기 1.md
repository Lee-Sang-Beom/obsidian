
##### mapContext.ts 생성

- React 컴포넌트 간에, 값을 공유하기 위한 context 파일을 생성한다.
```tsx
"use client";
import React from "react";

// context 사용을 위한 createContext
const MapContext = React.createContext({});
export default MapContext;
```

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
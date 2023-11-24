
- 데이터 생성
- 맵 그리기
- 이벤트 처리
```tsx
"use client";

  

import styles from "./ChemiMap.module.scss";

import MapContext from "@/utils/map/mapContext";

import { useContext, useEffect, useState } from "react";

  

import { ShelterDetailResponse } from "@/types/sub/Sm/SysShelter";

  

import { useAutoAlert } from "@/hooks/Alert/useAutoAlert";

import { commonSelectType } from "@/components/common/CommonSelect/CommonSelect";

  

import { Point } from "ol/geom";

  

import { useShelterListForMap } from "@/hooks/sub2/Ci/ChemiMap/useShelterListForMap";

  

import { getVWorldLayer } from "@/types/common/map/Layer";

import {

  SearchShelterForMapCommand,

  returnSettingRadiusShelterMarkerVectorLayer,

} from "./TestRadiusMap";

  

export default function RadiusMap() {

  const { setIsChange, setText, setStatus } = useAutoAlert();

  const { mapObj } = useContext<any>(MapContext);

  const { map } = mapObj;

  

  const [isMounted, setIsMounted] = useState(false);

  

  const [shelterSearchKeyObj, setShelterSearchKeyObj] =

    useState<SearchShelterForMapCommand>({

      page: 0,

      size: 5,

      shelterForMapSearchType: "ALL",

      searchKeyWord: "",

      orderBy: "DESC",

      sort: "shelterNm",

    });

  const shelterQuery = useShelterListForMap(shelterSearchKeyObj);

  const [shelterMarkerList, setShelterMarkerList] = useState<

    ShelterDetailResponse[]

  >([]);

  

  // 4-3)

  useEffect(() => {

    if (shelterQuery && shelterQuery.data && shelterQuery.data.content) {

      setShelterMarkerList(shelterQuery.data.content);

    } else {

      setShelterMarkerList([]);

    }

  }, [shelterSearchKeyObj, shelterQuery.data]);

  

  // 4-4)

  useEffect(() => {

    // 초기화작업

    setIsMounted(true);

  

    // 최초 로드 시, BaseLayer 추가

    const vworld = getVWorldLayer();

  

    if (map) {

      // 팝업 띄우기

  

      // 기존 맵 객체 존재 시, default로 작동할 BaseLayer를 map에 add

      map.addLayer(vworld);

      map.getAllLayers().map((m: any) => {

        if (

          m.get("name") &&

          (m.get("name") === "base-vworld-Satellite" ||

            m.get("name") === "base-vworld-hybrid")

        ) {

          map.removeLayer(m);

        }

        return;

      });

  

      map.on("pointermove", function (e: any) {

        const pixel = map.getEventPixel(e.originalEvent);

        const hit = map.hasFeatureAtPixel(pixel);

        map.getTarget().style.cursor = hit ? "pointer" : "";

        map.getTarget().style.zIndex = 9999;

      });

    }

  }, [map]);

  

  // 4-5)

  useEffect(() => {

    if (!isMounted || !map) {

      return;

    }

  

    // 함수 정의

    const updateMapWithShelterMarkers = () => {

      // 레이어 추가 전, 모든 레이어 제거

      map.getAllLayers().forEach((layer: any) => {

        if (layer.get("name") && layer.get("name") === "shelter") {

          map.removeLayer(layer);

        }

      });

  

      if (shelterMarkerList[0]) {

        const newShelterMarkerVectorLayerList =

          returnSettingRadiusShelterMarkerVectorLayer(

            shelterMarkerList,

            "shelter",

            map

          );

  

        map.addLayer(newShelterMarkerVectorLayerList);

      }

    };

  

    // 레이어 추가 전, 모든 레이어 제거

    map.getAllLayers().map((m: any) => {

      if (m.get("name") && m.get("name") === "shelter") {

        map.removeLayer(m);

      }

    });

  

    if (shelterMarkerList[0]) {

      // 초기 로드 시와 shelterQuery.data가 변경될 때 실행

      updateMapWithShelterMarkers();

  

      map.getView().on("change:resolution", () => {

        console.log("잉?");

        updateMapWithShelterMarkers();

      });

  

      // 데이터의 맨 첫번째 요소로 자동 포커스이동!!

      map.getView().setCenter(

        new Point([

          Number(shelterMarkerList[0].lot),

          Number(shelterMarkerList[0].lat),

        ])

          .transform("EPSG:4326", "EPSG:3857")

          // @ts-ignore

          .getCoordinates()

      );

    } else {

    }

  

    return () => {

      // 컴포넌트 언마운트 시 이벤트 핸들러 제거

      map.getView().un("change:resolution", updateMapWithShelterMarkers);

    };

  }, [isMounted, shelterMarkerList]);

  

  return (

    <div className={styles.chemimap_wrap}>

      <div id="map" className={styles.map_el}></div>

    </div>

  );

}
```
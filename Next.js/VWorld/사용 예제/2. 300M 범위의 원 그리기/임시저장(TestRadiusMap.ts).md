
- 300m범위의 영역 스타일 생성

```tsx
import { Style, Text, Icon, Fill, Stroke, Circle } from "ol/style";

import VectorSource from "ol/source/Vector.js";

import Point from "ol/geom/Point.js";

import Feature from "ol/Feature.js";

import { fromLonLat, getPointResolution } from "ol/proj";

import CircleStyle from "ol/style/Circle";

import { Collection } from "ol";

import { Geometry } from "ol/geom";

import VectorLayer from "ol/layer/Vector";

import { ShelterDetailResponse } from "@/types/sub/Sm/SysShelter";

import { CompanyChemResponse } from "@/types/sub/Sm/SysCompany";

  

// 300m 반경의 원을 그리기 위한 함수

function createCircleStyle(center: any, resolution: any) {

  const radius = 300; // 300m 반경

  

  const circle = new CircleStyle({

    radius: radius / resolution,

    fill: new Fill({

      color: "rgba(255, 0, 0, 0.2)",

    }),

  });

  

  return new Style({

    image: circle,

  });

}

  

// 테스트

// 대피소마커반환

export const returnSettingRadiusShelterMarkerVectorLayer = (

  markerDataList: ShelterDetailResponse[],

  layerName: string,

  map: any

) => {

  return new VectorLayer({

    className: "shelterMarkerVectorLayerList",

    properties: { name: layerName },

    source: new VectorSource({

      features: new Collection<any>(

        markerDataList.map((data: ShelterDetailResponse, idx: number) => {

          const view = map.getView();

          const resolution = getPointResolution(

            view.getProjection(),

            view.getResolution(),

            [Number(data.lot), Number(data.lat)]

          );

  

          console.log("resolution : ", resolution);

          const feature = new Feature<Geometry>({

            geometry: new Point(

              fromLonLat([Number(data.lot), Number(data.lat)])

            ),

            geometryName: data.shelterNm,

          });

  

          feature.setStyle(

            createCircleStyle([Number(data.lot), Number(data.lat)], resolution)

          );

  

          return feature;

        })

      ),

    }),

    zIndex: 999,

  });

};

  

/**

 * 인터페이스 영역

 */

// 대피소

export interface SearchShelterForMapCommand {

  /**

   * SHELTER_NM, ROAD_NM_ADDR, ALL

   */

  shelterForMapSearchType?: string;

  

  /**

   * 검색어

   */

  searchKeyWord?: string;

  /**

   * 정렬기준

   */

  sort?: string;

  /**

   * 정렬순서

   */

  orderBy?: string;

  

  page?: number;

  size?: number;

}

  

export interface ShelterResponse {

  rowId: number;

  /**

   * 시퀀스

   */

  shelterSeq: number;

  /**

   *대피소명

   */

  shelterNm: string;

  /**

   *세부위치명

   */

  dtlPstnNm: string;

  /**

   *수용인원수

   */

  accpTnope: number;

  /**

   *도로명주소

   */

  roadNmAddr: string;

  /**

   *위도

   */

  lat: string;

  /**

   *경도

   */

  lot: string;

  /**

   *설치년도

   */

  instlYr: string;

  /**

   *설치형태

   */

  instlForm: string;

  /**

   *관리기관명

   */

  mngInstNm: string;

  /**

   *관리기관전화번호

   */

  mngInstTelno: string;

  /**

   *데이터기준일자

   */

  crtrYmd: string;

  /**

   *삭제여부

   */

  delYn: string;

  /**

   *등록자아이디

   */

  rgtrId: string;

  /**

   *등록일

   */

  regDt: string;

  /**

   *수정자아이디

   */

  mdfrId: string;

  /**

   *수정일

   */

  mdfcnDt: string;

}
```
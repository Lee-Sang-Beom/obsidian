##### OpenLayers

- [OpenLayers](https://openlayers.org/)는 지도 API 중 하나로, 웹 페이지에서 동적인 지도기반 서비스를 개발할 수 있도록 도와주는 오픈 소스 JavaScript 라이브러리이다.
  
- OpenLayers는 **OSM**이라는 것을 기본으로 제공하는데, 이것을 통해 별다른 **API KEY**나 호출 과정 없이 간단히 웹 상에 지도를 띄울 수 있다.



#### 구조

- OpenLayers에서, 지도서비스를 사용하기 위해서는 **Map** 객체가 반드시 필요하다.
	- Map 객체는 지도화면에 배경지도(이번에 사용할 VWorld이나, OSM, Naver, Kakao, Google Map)를 호출하기 위해 필요하다.


![[Openlayers 구조.png]]
[이미지 출처 : 알파카님의 개발 포스트](https://blog.itcode.dev/projects/2022/03/19/gis-guide-for-programmer-10#open-street-map)

- Map 객체를 사용하여 지도를 화면에 표시하려면, **Layer, View, Interaction 등**과 같은 요소가 필요하다. 
	- Layer: 배경지도 또는 레이어를 출력하기 위해 사용하는 객체이다. 하나 이상의 Layer가 필요하며, Map에서 배열로 사용된다.
	- Source: Layer 데이터의 원천(Source)으로, Feature의 집합과 같다. 
	- Feature: 점, 선, 면과 같은 벡터 요소(벡터 Layer 한정)
	- View: 해상도, 화면 레벨, 좌표 등 지도의 시각적 효과에 대한 변수들을 정의한다.
	- Interaction: 마우스, 키보드 등의 이벤트 효과 등의 맵의 상호작용 요소를 관리한다. (Zoom in, Zoom out 등)
	- Overlay: Map에 표시할 요소
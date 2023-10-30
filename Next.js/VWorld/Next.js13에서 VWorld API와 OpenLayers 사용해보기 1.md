##### OpenLayers

- [OpenLayers](https://openlayers.org/)는 지도 API 중 하나로, 웹 페이지에서 동적인 지도기반 서비스를 개발할 수 있도록 도와주는 오픈 소스 JavaScript 라이브러리이다.
  
- OpenLayers는 **OSM**이라는 것을 기본으로 제공하는데, 이것을 통해 별다른 **API KEY**나 호출 과정 없이 간단히 웹 상에 지도를 띄울 수 있다.


#### 구조

- OpenLayers에서, 지도서비스를 사용하기 위해서는 **Map** 객체가 반드시 필요하다.
	- Map 객체는 지도화면에 배경지도(이번에 사용할 VWorld이나, OSM, Naver, Kakao, Google Map)를 호출하기 위해 필요하다.

- Map 객체를 사용하여 지도를 화면에 표시하려면, **Layer, View, Interaction, Controls**와 같은 요소가 필요하다.
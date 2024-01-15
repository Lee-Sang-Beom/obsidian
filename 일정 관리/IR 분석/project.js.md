#### 경로
- `src/main/webapp/res/lib/project.js`

#### 목적
- 프로젝트 내에서 주로 사용하는 파일자원 매핑 `(CSS, HTML, JS)`
- API, PopupUrl과 같은 경로 정의
- UserRole 정의
- key-value 형식의 변수 정의

#### API 정의 
- 해당 프로젝트에서는, `Requester.a`는 사용하지 않은 것으로 보임. 
- API 요청 시, `Request.awb` 만 사용

```javascript
Lia.Requester.prototype.ajax = function (url, parameterMap, onResponse, object, noExecuteOnResponseWhenError, q, xhrFields) { 
	
    var request = {  
        url: url,  
        noExecuteOnResponseWhenError: noExecuteOnResponseWhenError,  
        parameterMap: parameterMap,  
        onResponse: onResponse,  
        object: object,  
        sync: false,  
        q: q,  
        xhrFields: xhrFields  
    };  
  
    this.request(request);  
};
```

- 전달 인자
	- `url`: 1. 서버에 요청할 URL
	- `newParameterMap`: AJAX 요청 시 함께 전송할 매개변수 (파라미터)  
	- `onResponse`: 서버 응답을 처리할 콜백 함수  
	- `object`: 콜백 함수 내에서 사용할 객체 (옵션)  
	- `noExecuteOnResponseWhenError`: 5. 에러 발생 시 응답 콜백을 실행하지 않을지 여부 (옵션)  
	- `q`: (deprecated) 쿼리스트링 파라미터 (옵션)  
	- `xhrFields`: `XMLHttpRequest` 객체의 필드를 설정하는 데 사용할 객체 (옵션)


#### API 사용 예시

```javascript

Requester.awb(ProjectApiUrl.Project.GET_KEY_PERFORMANCE_INDICATOR, {year : year,dashBoard : 1}, function(status, data) {  
  
    if(status != Requester.Status.SUCCESS){  
        return;  
    }  
  
    var body = Lia.p(data, 'body');  
    var list = Lia.p(body, 'list');  
  
    var number = '①②③④⑤⑥⑦⑧⑨⑩⑪⑫⑬⑭⑮⑯⑰⑱⑲⑳㉑㉒';  
    var rowNumber = 0;  
    var listNoticePage = 0;  
    
    if(list == undefined){
	    ...  
    }else {  
	    ...
    }  
});
```
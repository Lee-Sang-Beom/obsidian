#### 1. Lia.p
- **extractProperty**(추출) 형식
	- `argument[0]`: 전달할 data
	- `argument[1, ...]`: 전달할 data에서 **특정** value를 반환받기 위한 **object key** 
		- `body` 하위 `list` property 접근 시, `Lia.p(data, 'body', 'list')` 사용

```javascript
var body = Lia.p(data, 'body');  
var list = Lia.p(body, 'list');
```


#### 2. Lia.pcd
- **extractProperty**(추출)에서 기본값을 추가한 형식
	- `argument[0]`: 기본값
	- `argument[1]`: 전달할 data
	- `argument[2]`: 전달할 data에서 **특정** value를 반환받기 위한 **object key** 

```javascript
var body = Lia.p(data, 'body')  

// body값이 undefined || null || "" 이면, 기본값 출력
var standardFieldTrainingSemester = Lia.pcd('0', body, 'standard_field_training_semester')
```


#### 3. Lia.extractFileExt
- 파일 확장자 추출
	- `argument[0]`: 파일

```javascript
var path = Lia.p(data, 'body', 'result_file_path');

new Triton.FlatButton({  
    appendTo: buttonSection,  
    content: '다운로드',  
    theme: Triton.FlatButton.Theme.Normal,  
    css: {'margin-bottom': '20px'},  
    onClick: function (e) {  
  
        var ext = Lia.extractFileExt(path); // 여기  
        if (String.isBlank(ext)) {  
            ext = '';  
        }  
  
        var newFilename  = fileName;  
        if ( String.isNotBlank(destFileNumber) ) {  
            newFilename += '_' + destFileNumber;  
        }  
  
        Requester.open(PathHelper.getFileUrl(path, newFilename + ext));  
        popup.hide();  
    }  
});
```


#### 4. Lia.extractFilename
- 파일명 추출
	- `argument[0]`: 파일


#### 5. Lia.extractGetParameterMapFromUrl
- URL 기준으로 들어온 파라미터 추출
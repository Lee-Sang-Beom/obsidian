#### 1. Lia.p
- **extractProperty**(추출) 형식
	- `argument[0]`: 전달할 data
	- `argument[1]`: 전달할 data에서 **특정** value를 반환받기 위한 **object key** 

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
var standardFieldTrainingSemester = Lia.pcd('0',body,'standard_field_training_semester')
```
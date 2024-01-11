
#### 작업 경로
- `wisdomir/src/main/resources/META-INF/resources.res.carina.js/constants.js`

#### 목적
- `logoImageUrl`, `MessageType`, `BannerType` 등 고정되어 규격화되는 항목 모음

#### 사용 예시

```javascript
// page 내에서의 page_login_logo class명을 가진, element의 src 지정
page.find('.page_login_logo').attr('src', ProjectSettings.Logo.logoImageUrl);
```


##### API 경로
- `carina-core-1.00.09.RELEASE.jar/META-INF/resources/res/carina/js/apis.js`

```javascript

Requester.awb(ApiUrl.User.LOGIN, {  
    id: id,  
    password: pw,  
    stayLoggedIn: 1  
}, function (status, data) {  
  
    if (status != Requester.Status.SUCCESS) {  
        PopupManager.alertByResponse(data);  
        return;  
    }  
  
    if (page.find('.image_button.check').buttonPressed()) {  
        CookieHelper.set("login_saved_inst_id", instId);  
        CookieHelper.set("login_saved_id", id);  
    } else {  
        CookieHelper.remove("login_saved_inst_id");  
        CookieHelper.remove("login_saved_id");  
    }  
    page.goDefault();  
}, {  
    autoPopup: false  
});

```
## 啥都没干的代码
```js
require.async(["common:zepto"] , function($) {
    var fuc = {
        init: function() {
            //this.initCss();
        },
        initCss: function() {
        }
    }
    fuc.init();
});
```

## 格式规范
```css
/* 表单 */
.address_form { padding: 20px; box-sizing: border-box;}
.address_form li { margin-bottom: 20px; font-size: 16px;}
.address_form .img_01 { text-align: center;}
.address_form .img_01 img { width: 73%;}
.address_form .prompt {
    background-color: rgba(255, 255, 255, 0.5); font-size: 12px;
    text-align: center; color: #fff; padding: 10px 0;
}
.address_form input {
    height: 45px; line-height: 45px; box-sizing: border-box;
    padding-left: 10px; width: 100%; border: 0; font-size: 1em;
    
}
.address_form .submit {
    width: 100%; background-color: #ffcd19; color: #939; margin-top: 20px;
    line-height: 45px; border-radius: 5px; text-align: center;
}
```

## bad responsive code
```js
initCss: function() {
    if (window.screen.availHeight > 600 && window.screen.availHeight < 700) {
        $("html").css("font-size", '14px');
    } else if (window.screen.availHeight > 700) {
        $("html").css("font-size", '15px');
    }
}
```
用media query更加合适

## jQuery 使用
```js
bindRuleShow: function() {
    $("#ruleBtn").click(function() {
        $("#ruleList").toggleClass("hide");
        if ($(this).hasClass("hide")) {
            $(this).find("i").text("收起");
            $(this).removeClass("hide");
            window.location.href = "#ruleBtn";
        } else {
            $(this).find("i").text("展开");
            $(this).addClass("hide");
            document.body.scrollTop = 0;
        }
    })
}
```
选择器缓存

链式书写

##代码严谨性

```js
validAddress: function(){
    var name = $("#name").val(),
        address = $("#address").val(),
        mobile = $("#mobile").val();

    if(name=="" || address == "" || mobile == "") {
        this.setAddressInputError("输入框不能为空");
    } else {
        // this.setAddressInputSuccess();
        return true;
    }
}
```
空格也能通过验证建议用trim

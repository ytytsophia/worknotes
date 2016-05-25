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

##代码组织

```js
function eventMove(e){                 
    e.preventDefault();                 
    if(mousedown && !stop){                     
        if(e.changedTouches){                         
            e=e.changedTouches[e.changedTouches.length-1];                     
        }
        var canvasX =  e.clientX + document.body.scrollLeft || e.pageX; 
        var canvasY =  e.clientY + document.body.scrollTop || e.pageY;                  
        var x = canvasX - offsetX || 0;
        var y = canvasY - offsetY || 0;

        console.log("x:" + x + "\n" + "y:" + y)
        if(y >20 && y < 62) {
            if(x > 60 && x < 135 && !shotArea1) {
                shotNum++;
                shotArea1 = true;
            }
            if(x > 135 && x < 210 && !shotArea2) {
                shotNum++;
                shotArea2 = true;
            }
        }
        if(y >62 && y < 104) {
            if(x > 60 && x < 135 && !shotArea3) {
                shotNum++;
                shotArea3 = true;
            }
            if(x > 135 && x < 210 && !shotArea4) {
                shotNum++;
                shotArea4 = true;
            }
        }
        if(shotNum == 4 && !shot) {
            shot = true;
            setTimeout(function() {
                stop = true;
                fuc.activeAward();
            },1000)
        }                 
        with(ctx){                    
            beginPath()                     
            arc(x, y, 35, 0, Math.PI * 2);
            fill();                     
        }                
    }             
}               
canvas.width=w;             
canvas.height=h;             
canvas.style.backgroundImage='url('+img.src+')';             
ctx=canvas.getContext('2d');             
ctx.fillStyle='transparent';             
ctx.fillRect(0, 0, w, h);             
layer(ctx);                
ctx.globalCompositeOperation = 'destination-out';               
canvas.addEventListener('touchstart', eventDown);             
canvas.addEventListener('touchend', eventUp);             
canvas.addEventListener('touchmove', eventMove);             
canvas.addEventListener('mousedown', eventDown);             
canvas.addEventListener('mouseup', eventUp);             
canvas.addEventListener('mousemove', eventMove);     
```
代码本身没什么问题，但是在feather-mobile-event里面一样的代码看到了至少三次。

全是复制粘贴的，建议抽出来做组件。

适当添加下注释。

##　jquery选择器优化

```js
if(data.data.iRemainNum == 0) {
    $("#goNoRewardGua img").hide();
    $("#goNoRewardGua img.disabled").show();
    $("#goNoRewardGua").addClass('disabled');
}
```

优化：
```js
$("#goNoRewardGua").addClass('disabled')
    .find('img')
    .hide()
    .find('.disabled')
    .show();
```
1.jquery中最快的选择器是id选择器和元素标签选择器

2.使用.find()更快，因为没有使用sizzle选择器引擎而是直接调用浏览器的原生方法

3.总而言之在父元素中选择子元素最快的方式是$parent.find('.child')

4.使用链式写法，缓存每一步的结果可以提升大概25%的性能

```js
$('a').click(function(){
　　　　alert($(this).attr('id'));
});
```
优化
```js
$('a').click(function(){
　　　　alert(this.id);
});
```
1.点击a元素后，弹出该元素的id属性。为了获取这个属性，必须连续两次调用jQuery，第一次是$(this)，第二次是attr('id')。

2.使用jquery时要注意不要过度依赖，有原生js方法可以使用的场合，尽量避免使用jquery。
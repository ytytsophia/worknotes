## 1.长参数列表 
```js
	/*if(hasMoreData){
            _this.getListData(myIncomeType, _this.userId, _this.userRole, _this.pageSize, _this.page, function(){});
        }*/

    }).one('tap', function(){
        _this.getListData(curIncomeType, _this.userId, _this.userRole, _this.pageSize, _this.page, function(){});
    });

    this.$loan_made.on("tap", function() {
        $(this).addClass("curStatus");
        _this.$loan_applying.removeClass("curStatus");
        var myIncomeType = $("#loan_made").val();
        curIncomeType = myIncomeType;
        _this.$content_loan_made.css('display', 'none');
        _this.$content_loan_made2.css('display', 'block');
        /*if(hasMoreData2){
            _this.getListData(myIncomeType, _this.userId, _this.userRole, _this.pageSize, _this.page, function(){});
        }*/
    }).one('tap', function(){
        _this.getListData(curIncomeType, _this.userId, _this.userRole, _this.pageSize, _this.page2, function(){});
    });

    $(window).on("scroll", function() {
        if(curPage == 'profit'){
            var doMore = curIncomeType == 1 ? hasMoreData : hasMoreData2;
            if ($("body").height() - $(this).scrollTop() - $(this).height() == 0 && doMore) {
                //_this.waterfullList();
                var page = curIncomeType == 1 ? ++_this.page : ++_this.page2;
                _this.getListData(curIncomeType, _this.userId, _this.userRole, _this.pageSize, page, function() {});
            }
        }
    });

},

getListData: function(myIncomeType, userId, userRole, pageSize, page, success) {
    var _this = this, hasTitle = page == 1 ? true : false;
```
-------------------------------------------------------------------------------------------
出了问题的方法被标记出来很明显可以看到getListData这个方法被调用了几次，代码难以阅读的罪魁祸首就是这几次调用传入的**长参数列表**。

问题：

* 参数过长，影响可读性。
* 可维护性差，当getListData方法的参数个数需要增减时需要相应更改每一次调用的传参，减少参数时还好，如果是增加参数的话只会导致调用方法的代码行越来越长。
* 从性能角度上来说，调用方法时每次获取参数的值都回去查找一次上下文，费时费力。

重构方案：
将相关参数组织成对象来替换长参列表（回调函数除外），对象可以提前缓存，回调函数除外。

-------------------------------------------------------------------------------------------
## 2.误用注释
```js
/*var _mask = new Mask({
		autoOpen: false,
		color: '#fff',
		opacity: 0
	})*/

	var fuc = {
		init: function(){
			var self = this;
			self.initDialog();
			self.bindEvent();
		},

		bindEvent: function(){
			var self = this;

			$('#J_adopt').on('tap', function(){
				// var $this = $(this);
				// if(!$this.hasClass('dis')){
				// 	$this.addClass('dis');
					// if(window.iOrderStatus == 1){
					// 	$J_mask.css('display', 'block');
					// 	$J_sp_ad_dialog.css('display', 'block');
					// 	$this.removeClass('dis');
					// }else{
					// 	var url = $this.attr('ajaxurl');
					// 	self.doAdopt(url, $this);
					// }
					$J_mask.css('display', 'block');
					$J_sp_ad_title.html(d_title);
					$J_sp_ad_content.html(d_content);
					$J_sp_ad_dialog.css('display', 'block');
				// }
```
问题：

* 注释符号用于遮盖错误或无用的代码
* 用注释符号来调试代码并带到了线上
* 后期维护成问题

------------------------------------------------------------------------------------------
##3.格式问题
```js
getInfo:function(){
	var pra={},
	name=$.trim($("#name").val()),
	cellphone=$.trim($("#cellphone").val()),
	houseNumber=$.trim($("#houseNumber").val());

		if(name==""){
		Tips.error("姓名不能为空！");
		return false;
	}

	if(cellphone==""){
		Tips.error("手机号不能为空！");
		return false;
	}else{
    	if(!cellphone.match(/^\d{11}$/)){
    		Tips.error("手机号输入有误！");
    		return false;
    	}
	}

	if(houseNumber==""){
		Tips.error("房源号不能为空！");
		return false;
	}

	pra={
		"name":name,
		"cellphone":cellphone,
		"houseNumber":houseNumber
	}

	return pra;
}
```
问题：

* 代码过于紧凑，没有空格

建议：

* 左侧大括号前面留一个空格
* 在控制语句中（if, while etc），左括号之前留一个空格。
* 使用空白来分隔运算符

------------------------------------------------------------------------------------------
##4.提炼函数

## 1.提炼函数
```js
//整批通过
sMultiApproveURL: function(iAutoID, success) {
	var _this = this;
	$.ajax({
		url: dpageConfig.sMultiApproveURL,
		type: 'POST',
		dataType: "json",
		data: {
			iAutoID: iAutoID
		},
		success: function(res) {
			if (res.status == "1") {
				success && success(res);
			} else {
				alert(res.errmsg);
			}
		}
	}).always(function() {
		$("#sMultiApprove").removeAttr("disabled");
	});
},
//整批驳回
sMultiRejectURL: function(iAutoID,sRejMsg, success) {
	var _this = this;
	$.ajax({
		url: dpageConfig.sMultiRejectURL,
		type: 'POST',
		dataType: "json",
		data: {
			iAutoID: iAutoID,
			sRejMsg:sRejMsg
		},
		success: function(res) {
			if (res.status == "1") {
				success && success(res);
			} else {
				alert(res.errmsg);
			}
		}
	});
},
//经办通过
sApproveURL: function(iDate, iAutoIDs, success) {
	var _this = this;
	$.ajax({
		url: dpageConfig.sApproveURL,
		type: 'POST',
		dataType: "json",
		data: {
			iDate: iDate,
			iAutoIDs: iAutoIDs
		},
		success: function(res) {
			if (res.status == "1") {
				success && success(res);
			} else {
				alert(res.errmsg);
			}
		}
	}).always(function() {
		$("#auditBtn").removeAttr("disabled");
	});
},
//经办拒绝
sRejectURL: function(iDate,iAutoIDs, sRejMsg, success) {
	var _this = this;
	$.ajax({
		url: dpageConfig.sRejectURL,
		type: 'POST',
		dataType: "json",
		data: {
			iDate:iDate,
			iAutoIDs: iAutoIDs,
			sRejMsg: sRejMsg
		},
		success: function(res) {
			if (res.status == "1") {
				success && success(res);
			} else {
				alert(res.errmsg);
			}
		}
	});
},
//打包转复核
sReAuditUrl: function(iAutoID, success) {
	var _this = this;
	$.ajax({
		url: dpageConfig.sReAuditUrl,
		type: 'POST',
		dataType: "json",
		data: {
			iAutoID: iAutoID
		},
		success: function(res) {
			if (res.status == "1") {
				success && success(res);
			} else {
				alert(res.errmsg);
			}
		}
	});
}
```
这些方法可以提炼成一个方法，增加参数： url，data，always。

$.ajax()可以简化成$.post()

------------------------------------------------------------------------------------------

## 2.选择器缓存
```js
$("#tableListWrap input[name=auditSelect]").on("click", function() {
	var checkedNum = $("#tableListWrap input[name=auditSelect]:checked").size();
	if (checkedNum > 1) {
		$("#rejectBtn").hide();
	} else {
		$("#rejectBtn").show();
	}
});
$("#auditSelectFlag").on("change", function() {
	auditSltFlag == 0 ? auditSltFlag = 1 : auditSltFlag = 0;
	if (auditSltFlag) {
		$("#tableListWrap input[name=auditSelect]").prop("checked", "checked");
		$("#rejectBtn").hide();
	} else {
		$("#tableListWrap input[name=auditSelect]").removeAttr("checked", "checked");
		$("#rejectBtn").show();
	}
});
```
多次查找相同的选择器浪费性能，建议用变量缓存。

------------------------------------------------------------------------------------------
##3.代码结构杂糅，超长函数

```js
getListDataAudit: function() {
	var _this = this;
	this.$dtotalListWrap.hide();
	this.$dloading.show();
	this.sQueryUrl(this.iAutoID,"1",this.dlistInfoTotal.iPage, function(data) {
		var iAutoIDs = [],
			iAutoIDs,
			auditSltFlag = 0;
		_this.$dtotalListWrap.show();
		_this.createAuditList(data.data.aList);
		_this.createRuleListPageAudit(data.data);
		if (pageConfig.iOperate == "0") {
			$("#detailOptWrap").hide();
			$("#dtotalListWrap tr th").eq(0).hide();
			$("#dtotalListWrap tbody tr").each(function(){
				$(this).find("td").eq(0).hide();
			});
			return;
		};
		function checkedSlect() {
			iAutoIDs.splice(0, iAutoIDs.length);
			$("#tableListWrap input[name=auditSelect]:checked").each(function() {
				var curSltID = $(this).parent().next().html();
				iAutoIDs.push(curSltID);
			});
		};
		$("#tableListWrap input[name=auditSelect]").on("click", function() {
			var checkedNum = $("#tableListWrap input[name=auditSelect]:checked").size();
			if (checkedNum > 1) {
				$("#rejectBtn").hide();
			} else {
				$("#rejectBtn").show();
			}
		});
		$("#auditSelectFlag").on("change", function() {
			auditSltFlag == 0 ? auditSltFlag = 1 : auditSltFlag = 0;
			if (auditSltFlag) {
				$("#tableListWrap input[name=auditSelect]").prop("checked", "checked");
				$("#rejectBtn").hide();
			} else {
				$("#tableListWrap input[name=auditSelect]").removeAttr("checked", "checked");
				$("#rejectBtn").show();
			}
		});
		$("#auditBtn").on("click", function() {
			var self = $(this);
			checkedSlect();
			if (iAutoIDs.length == "0") {
				alert("请先选择要复核的订单项。")
				return;
			};
			$(this).attr("disabled", "disabled");
			_this.sApproveUrl(_this.iAutoID,iAutoIDs, function() {
				alert("复核通过成功！");
				self.removeAttr("disabled");
				location.replace(location);
			});
		});
		var rejmsgValid = new FormValid({
			dom: '#srejmsgTop',
			rules: {
				sRejMsg: [{
					rule: function(value) {
						return value.length != 0;
					},
					errorText: '请填写拒绝理由'
				}]
			}
		});
		$("#rejectBtn").on("click", function() {
			var self = $(this);
			checkedSlect();
			if (iAutoIDs.length == "0") {
				alert("请先选择要拒绝的订单项。")
				return;
			}
			var rejectnDialog = new Dialog2({
				esc: true,
				title: '驳回理由',
				autoOpen: true,
				width: 450,
				buttons: {
					'提交': {
						events: {
							click: function() {
								if (!rejmsgValid.check()) {
									return;
								};
								var sRejMsg = $("#rejectText").val();
								_this.sRejectURL(_this.iAutoID,iAutoIDs, sRejMsg, function() {
									alert("提交成功！");
									location.replace(location);
								});
								this.close();										
							}
						},
						className: "btn reject_btn"
					}
				},
				close:function(){
					$("#rejectText").val("");
				}
			});
			$("#srejmsgTop").show();
			rejectnDialog.setContent($("#srejmsgTop"));
		});
	});
}
```
多个事件绑定代码嵌在方法里，方法每执行一次事件绑定一次，应该提出去。

结构混乱，看着难受。

------------------------------------------------------------------------------------------

```js
getListDataAudit: function() {
    var _this = this;
    this.$auDitListWrap.hide();
	this.$paycollectionWrap.hide();
	this.$auDitDetailListWrap.hide();
	$("#goBackBtn").hide();
    var iCurrAuditStatus = $("#statusSlt").val(),
		iBusinessType = $("#sInfoSlt").val(),
		iAuditAgent = pageConfig.iAuditAgent,
        startDate = $("[name = startDate]").val(),
        bStartDate = Date.parse(startDate),
        iStartTime = startDate.replace(/[^0-9]/ig, ""),
        endDate = $("[name = endDate]").val(),
        bEndDate = Date.parse(endDate),
        iEndTime = endDate.replace(/[^0-9]/ig, "");
	if (iBusinessType == "0") {
		alert("请选择业务类型");
		return;
	};
    if ((bEndDate - bStartDate) < 0) {
        alert("截止时间不能小于开始时间");
    } else if ((bEndDate - bStartDate) / 86400000 > 45) {
        alert("间隔时间不能超过45天");
    } else {
		$("#preauditSearch").button('loading');
        this.getAuditList(iCurrAuditStatus,iBusinessType,iAuditAgent,iStartTime, iEndTime, this.listInfoTotal.iPage, function(data) {					
            if (data.data.iCount != "0") {
				var iAutoIDArray = [],
					iAutoIDs,
					auditSltFlag = 0,
					rejectDialog;
                _this.$auDitListWrap.show();
                _this.createAuditList(data.data);
                _this.createRuleListPageAudit(data.data);
				if ($("#statusSlt").val() != "1" && $("#statusSlt").val() != "4") {
					$("#tableListWrap tr").find("th").eq(0).hide();
					$("#tableListWrap tbody tr").each(function(){
						$(this).find("td").eq(0).hide();
					});
				}
				$("#downAuditList").css("display","inline-block").on("click", function() {
					var downAuditUrl = pageConfig.downAuditList + "?iCurrAuditStatus=" + iCurrAuditStatus + "&iBusinessType=" + iBusinessType + "&iAuditAgent=" + iAuditAgent
						+ "&iStartTime=" + iStartTime + "&iEndTime=" + iEndTime;
					$(this).attr("href",downAuditUrl);
				});
				function checkedSlect() {
					iAutoIDArray.splice(0,iAutoIDArray.length);
					$("#tableListWrap input[name=auditSelect]:checked").each(function(){
						var curSltID = $(this).parent().next().html();
						iAutoIDArray.push(curSltID);
					});
					iAutoIDs = iAutoIDArray.join(",");
				};					
				$("#tableListWrap input[name=auditSelect]").on("click",function(){
					var checkedNum = $("#tableListWrap input[name=auditSelect]:checked").size();
					if (checkedNum > 1) {
						$("#rejectBtn").hide();
					}else{
						$("#rejectBtn").show();
					}
				});
				$("#auditSelectFlag").on("change", function() {
					auditSltFlag == 0 ? auditSltFlag = 1 : auditSltFlag = 0;
					if (auditSltFlag) {
						$("#tableListWrap input[name=auditSelect]").prop("checked","checked");
						$("#rejectBtn").hide();								
					} else {
						$("#tableListWrap input[name=auditSelect]").removeAttr("checked","checked");
						$("#rejectBtn").show();								
					}
				});
				if (iCurrAuditStatus == "1") {
					$("#detailOpt").show();
				}else{
					$("#detailOpt").hide();
				};
				if (iCurrAuditStatus == "4") {
					$("#reAuditSure").show();
					$("#reAuditSureBtn").on("click", function() {
						checkedSlect();
						if (iAutoIDArray.length == "0") {
							alert("请先选择要确认的订单项。")
							return;
						}							
						_this.batchAuditComfirm(iAutoIDs,'1',function(){
							alert("复核驳回确认成功！");
							$("#preauditSearch").trigger("click");
						});
					}); 
				}else{
					$("#reAuditSure").hide();
				};
				$("#auditBtn").on("click", function() {
					var self = $(this);
					$("#rejectText").val("");
					checkedSlect();
					if (iAutoIDArray.length == "0") {
						alert("请先选择要经办通过的订单项。")
						return;
					};
					$(this).attr("disabled","disabled");
					_this.batchupdateAuditDetail(iAutoIDs,iAuditAgent,'2',"",function(){
						alert("经办通过成功!");
						self.removeAttr("disabled");
						$("#preauditSearch").trigger("click");
					});
				});
				$("#rejectBtn").on("click", function() {
					var self = $(this);
					$(".ui-formvalid-field").hide();
					$("#rejectText").val("");
					checkedSlect();
					if (iAutoIDArray.length == "0") {
						alert("请先选择要经办驳回的订单项。")
						return;
					}							
					rejectDialog = new Dialog2({
						esc:true,
						title:'提示',
						autoOpen:true,
						width:450,
					});
					$("#rejectTextWrap").show();
					rejectDialog.setContent($("#rejectTextWrap"));
				});
				$("#rejectSure").off("click").on("click", function() {
					var sRejectDesc = $("#rejectText").val();
					var rejectValid = new FormValid({
						dom: '#rejectTextWrap',
						rules: {
							rejectText:[     
								{
									rule: function(value){
										return value.length != 0;
									},
									errorText: '驳回理由不可为空!'
								}
							]
						}
					});
					if (!rejectValid.check()) {
						return;
					};
					_this.batchupdateAuditDetail(iAutoIDs,iAuditAgent,'3',sRejectDesc,function(){
						alert("经办驳回成功!");
						rejectDialog.close();
						$("#preauditSearch").trigger("click");
					});										
				});
				$(".order_opt").on("click",function(){
					var iAutoID = $(this).closest("tr").attr("iautoid");						
					_this.getAuditListDetail(iAutoID,iBusinessType,iAuditAgent,function(data){
						var detailIautoID = data.data.iAutoID;
						_this.$auDitListWrap.hide();
						_this.$paycollectionWrap.hide();
						_this.$auDitDetailListWrap.show();
						_this.createAuditDetailList(data.data.aList);
						if (iCurrAuditStatus == "1") {
							$("#inDetailOpt").show();
						}else{
							$("#inDetailOpt").hide();
						};
						$("#orderDetailData table").prev().hide();
						$("#auditDetailBtn").on("click",function(){
							var self = $(this);
							$(this).attr("disabled","disabled");
							_this.batchupdateAuditDetail(detailIautoID,iAuditAgent,'2',"",function(){
								alert("经办通过成功!");
								self.removeAttr("disabled");
								$("#preauditSearch").trigger("click");
							});
						});
						$("#rejectDetailBtn").on("click",function(){
							rejectDialog = new Dialog2({
								esc:true,
								title:'提示',
								autoOpen:true,
								width:450,
							});
							$("#rejectTextWrap").show();
							rejectDialog.setContent($("#rejectTextWrap"));
							$("#rejectSure").off("click").on("click", function() {
								var sRejectDesc = $("#rejectText").val();
								var rejectValid = new FormValid({
									dom: '#rejectTextWrap',
									rules: {
										rejectText:[     
											{
												rule: function(value){
													return value.length != 0;
												},
												errorText: '驳回理由不可为空!'
											}
										]
									}
								});
								if (!rejectValid.check()) {
									return;
								};
								_this.batchupdateAuditDetail(detailIautoID,iAuditAgent,'3',sRejectDesc,function(){
									alert("经办驳回成功!");
									rejectDialog.close();
									$("#preauditSearch").trigger("click");
								});										
							});
						});
						$("#goBackBtn").show().on("click",function(){
							_this.$auDitDetailListWrap.hide();
							_this.$auDitListWrap.show();
							$(this).hide();
						});
					});
				});
				$(".hfb_participater").on("click",function(){
					var releateTrIndex = $(this).closest("tr").index();
						_this.sBusinessOrderID = data.data.aList[releateTrIndex][1];
						_this.getListDataPay(_this.sBusinessOrderID);
				});
            } else {
            	$("#downAuditList").hide();
                alert("暂无数据,请更改查询条件重试");
            };
        });
    }
}
```
没有最长，只有更长，流水账式写法。

fin里面有很多这样的代码，从一份js里copy到另一个js，很难看。

------------------------------------------------------------------------------------------

## 4.选择器合并
```js
mainListDisplay: function(status) {
    var _this = this;
    if (status == "search") {
        _this.$detailListWrap.hide();
        _this.$moreDetailListWrap.hide();
        _this.$mainListWrap.hide();
        _this.$mainTplBtn.hide();
        _this.$BackBtn.hide();
        _this.$loading.show()
        _this.$downloadBtn.hide();
        _this.$searchResult.hide();
        _this.$downloadDetailBtn.hide();
        _this.$closeDetailBtn.hide();
        _this.$confirmBtn.hide();
        return;
    } else if (status == "noData") {
        _this.$mainTplBtn.hide();
        _this.$mainListWrap.hide();
        _this.$downloadBtn.hide();
        _this.$searchResult.show();
        _this.$closeDetailBtn.hide();
        _this.$confirmBtn.hide();
        return;
    } else {
        _this.$mainListWrap.show();
        _this.$downloadBtn.show();
        _this.$searchResult.hide();
        if ($("#iCurrAuditStatus").val() == "1") {
            _this.$mainTplBtn.show();
        } else if ($("#iCurrAuditStatus").val() == "4") {
            _this.$confirmBtn.show();
        }
    }
}
```
选择器适当合并写，代码量会少很多。

-----------------------------------------------------------------------------------------

## 5.有些事不该js做

```js
//日期初始化
dateInit: function() {
    var nowDate = new Date(),
        nowYear = nowDate.getFullYear(),
        initYear = 2014,
        options = "";
    for (var i = initYear; i <= nowYear; i++) {
        if (i == nowYear) {
            $("<option selected>" + i + "</option>").appendTo("#StartYear");
            $("<option selected>" + i + "</option>").appendTo("#EndYear");
        } else {
            $("<option>" + i + "</option>").appendTo("#StartYear");
            $("<option>" + i + "</option>").appendTo("#EndYear");
        }
    }
}
```

用php init 这个日期列表更加适合，毕竟用js操作dom真的很慢。

而且js拿到的当前时间是客户端时间，拿服务器时间应更为准确。

------------------------------------------------------------------------------------------

## 6.代码格式

```js
addEvents:function(){
    var _this = this;
    $('#totalSearch').on('click', function() {
        _this.iPageNum = 1;
        $(this).button('loading');
        $('#noData').hide();
        _this.getResultWrap();
    });
    $('#iPurposeSlt,#iBusinessTypeSlt').change(function(event) {
        $('#emailListWrap').hide();
    });
    $('#addEmail').on('click', function() {
        $("#dialog .ui2-formvalid-field").hide();
        _this.successmsg = '邮箱添加成功！';
        $('#hideID').remove(); 
        _this.queryLink = pageConfig.addEmail ;
        $('#sEmail').val('');
        $('#j-func-text').val('');
        $('#dialog').dialog('instance').setTitle('添加邮箱');
		$('#dialog').dialog('open');
    });
    $('#emailListWrap').delegate('.J_editItem', 'click', function(e) {
         $("#dialog .ui2-formvalid-field").hide();
        _this.successmsg = '邮箱修改成功！';
        _this.queryLink = pageConfig.editEmail;
        var line = $(e.target).closest("tr"),
            iAutoID = line.find('.iAutoID').eq(0).html(),
            beforePurposeVal = line.find('.categoryName').eq(0).attr('data-value'),
            beforeBusinessVal = line.find('.sTypeName').eq(0).attr('data-value');
        $('#j-func-text').val('').val(line.find('.sDes').eq(0).html());
        $('#sEmail').val(line.find('.sEmail').eq(0).html())
        $("#iPurpose option").each(function(i){
            var curPurposeVal = $("#iPurpose option").eq(i).attr("value");
            if (beforePurposeVal == curPurposeVal) {
                $("#iPurpose option").eq(i).prop("selected","selected").change();
            }                               
        }); 
        $("#iBusinessType option").each(function(i){
            var curBusinessVal = $("#iBusinessType option").eq(i).attr("value");
            if (beforeBusinessVal == curBusinessVal ) {
                $("#iBusinessType option").eq(i).attr("selected","selected");
            }                               
        }); 
        $('#hideID').remove();
        $('#addForm').append('<input type="hidden" name="iAutoID" value='+iAutoID+' id="hideID">');
        $('#dialog').dialog('instance').setTitle('修改邮箱');
		$('#dialog').dialog('open');
    }).delegate('.J_delItem', 'click', function(e) {
        var line = $(e.target).closest("tr"),
            iAutoID = line.find('.iAutoID').eq(0).html();
        if (confirm("确定要删除该邮箱配置？")) {
            _this.sendQuery(pageConfig.deleteEmail,{'iAutoID':iAutoID},function(data){
                alert('删除成功！');
                _this.getResultWrap();
            });
        }
    });;
}
```
适当添加空行，代码阅读起来会舒服些。

jquery链式操作较长时换行写。

两个each方法可以合并，获取当前遍历到的item，不需要纠结地用eq(i),.each()第二个参数就是，用this也行。

------------------------------------------------------------------------------------------

## 7.缓存对象

```js
$(".checkOpt a").on("click",function(){
	var that = $(this),
		releateTrIndex = $(this).closest("tr").index(),
		iAutoID = data.data.aList[releateTrIndex].iAutoID,
		iAccChkStatus = data.data.aList[releateTrIndex].iAccChkStatus,
		iDate = data.data.aList[releateTrIndex].iPrimaryDate,
		iChannelID = data.data.aList[releateTrIndex].iPrimaryChannelID,
		sAccountID = data.data.aList[releateTrIndex].sAccountID,
		iCheckStatus = $(this).attr("iCheckStatus");
		 _this.updateAccountHandle(iCheckStatus, iAutoID,iAccChkStatus, iDate, iChannelID, sAccountID, function(){
			 var isSure = window.confirm("确认" + that.html());
			 if (!isSure){return;};
			 $("#btnQuery").trigger("click");
		 });									
});
```
data.data.aList[releateTrIndex]一样的对象拿了n遍，缓存起来吧。

如果把下面的updateAccountHandle长参数列表改成对象，根本就不用写这么多代码吧。--，--

------------------------------------------------------------------------------------------
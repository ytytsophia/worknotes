## 1.提炼函数 
```js
/* 是否显示 "本人纳税或社保证明" */
toggleDuty: function() {

	var pass = toggleRule[ajd_cityPy].Duty();

	toggle($J_duty_b, 'select', pass);

	return this;
},

/* 是否显示 "配偶户籍" */
togglePeiou: function() {

	var pass = toggleRule[ajd_cityPy].Peiou();

	toggle($J_peiou_b, 'input', pass);

	return this;
},

/* 是否显示 "配偶纳税或社保证明" */
togglePeiouDuty: function() {

	var pass = toggleRule[ajd_cityPy].PeiouDuty();

	toggle($J_peiouDuty_b, 'select', pass);

	return this;
},

/* 是否显示 "公积金" */
toggleGjj: function() {

	var pass = toggleRule[ajd_cityPy].Gjj();

	toggle($J_gjj_b, 'input', pass);

	return this;
},

/* 是否显示 "未结清贷款所在地" */
toggleMorLoc: function() {

	var pass = toggleRule[ajd_cityPy].MorLoc();

	toggle($J_szd_b, 'select', pass);

	return this;
},

togglePropSts: function() {

	var pass = toggleRule[ajd_cityPy].PropSts();

	toggle($J_propSts_b, 'select', pass);

	return this;
}
```
问题：

* 多个函数做同一类型的事
* 函数嵌套的没有道理，这里函数仅仅是简单的集合代码而不是重用代码。

解决方案：
* 这五个函数可以去掉 没有意义
* pass 可以在toggle函数里取值



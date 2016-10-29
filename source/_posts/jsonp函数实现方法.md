---
title: jsonp函数实现方法
date: 2016-10-08 15:47:44
categories: web前端
tags: 
  -- jsonp
  -- jsonp函数实现方法
  -- javascript实现jsonp函数
---

## jsonp
jsonp是一种跨域的请求方式,可以从其他域名获取数据,即跨域读取数据。



## jsonp函数的实现方法

```
/*
* @Author: accord
* @Date:   2016-10-08 11:17:21
* @Last Modified by:   accord
* @Last Modified time: 2016-10-08 15:46:18
*/

'use strict';

(function(window,document){
	var jsonp = function(url,data,callback){

		/*随机一个callback函数名 */
		var callbackFuncName = 'Callback_' + Math.random().toString().replace('.','');

		/*添加回调函数*/
		window[callbackFuncName] = callback;

		/*分解data对象,拼接成url字符串形式*/

		/* {id:1,name:'taoyage'} => ?id=1&name=taoyage& */
		var querystring = url.indexOf('?')== -1 ? '?':'&';

		for(var key in data){
			querystring += key + '=' + data[key] + '&';
		}

		/*进行拼接*/
		/* ?id=1&name=taoyage&callback=jsonCallback_1231313 */
		querystring += 'callback=' + callbackFuncName;

		/*创建script标签*/
		var script = document.createElement('script');
		script.src = url + querystring;
		document.body.appendChild(script);
	}

	window.$jsonp = jsonp;

})(window,document);
```

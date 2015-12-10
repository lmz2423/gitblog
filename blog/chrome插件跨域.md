[chrome plugin, stock]


chrome插件开发中，**XMLHttpRequest ** 对象的[same origin policy](https://en.wikipedia.org/wiki/Same_origin_policy)(同源策略),在chrome插件中没有受到限制。
### Extension origin 
每一个插件都在自己单独的安全隔离源运行（separate security origin） 。如果没有设置其他权限的话，插件能够使用XMLHttpRequest获取安装包内的内容。现在举一个例子。假如一个插件包含一个名叫config.json的配置文件，该文件在config_resources文件夹下。我们可以使用下面的方式获取config.json的信息。
``` javascript
	var xhr = new XMLHttpRequest();
	xhr.onreadystatechange = handleStateChange;// Implemented elsewhere.
	xhr.open("GET", chrome.extension.getURL('/config_resources/config.json'),true);
	xhr.send();

```
如果插件想使用一个安全源，而不是内容安装包内的文件，如 http://www.google.com, 浏览器不会允许扩展向该地址发送请求，除非该地址添加到允许的请求源中。
### 请求跨域允许(Requesting cross-origin permissions)
  往manifest文件的permissions属性中添加对应的地址，插件就能向远程服务发送请求。
```  json
{
	"name": "My extension",
	...
	"permissions":[
	 "http://www.google.com/"
	]
	...
}   
```
permissions的值的写法如有两种，一种是全值，一种是匹配模式的写法。
全值 ： "http://www.baidu.com/"
模式匹配："http://*.baidu.com/"

### 安全考虑(Security considerations)
通过XMLHttpRequest 来获取到对应的信息时，我们应该注意[cross-site scripting](http://en.wikipedia.org/wiki/Cross-site_scripting)。一定要注意不要使用危险发的API.就像下面的例子
```  javascript
var xhr = new XMLHttpRequest();
xhr.open("GET", "http://api.example.com/data.json", true);
xhr.onreadystatechange = function(){
	if (xhr.readyState == 4) {
	   //如果返回的数据是脚本话会运行脚本
		var resp = eval("(" + xhr.responseText + ")");
		...
	}
}
``` 
``` javascript
var xhr = new XMLHttpRequest();
xhr.open("GET", "http://api.example.com/data.json", true);
xhr.onreadystateChange = function() {
	if (xhr.readyState == 4) {
		// 可能会注入恶意的脚本
		document.getElementById("reps").innerHtml = xhr.reponseText;
	}
	...
}
xhr.send();
```
我们应该使用安全的API(不会运行脚本)
``` javascript
var xhr = new XMLHttpRequest();
xhr.open("GET", "http://api.example.com/data.json". true);
xhr.onreadystateChange = function() {
	if (xhr.readyState == 4) {
		var resp = JSON.parse(xhr.responseText);
	}
}
xhr.send();
```
``` javascript
var xhr = new XMLHttpRequest();
xhr.open("GET", "http://api.example.com/dat.json", true);
xhr.onreadystatechange = function() {
	if (xhr.readyState == 4) {
	  // innerText does not let the attacker inject HTML elements.
	  documemt.getElementById("resp").innerText = xhr.responseText;
	}
}
xhr.send();
``` 
此外一定要注意通过HTTP协议接收到的数据，如果我们的插件处于一个恶意的网络中，网络攻击者可以篡改HTTP响应，（这种攻击叫中间人攻击）。为了防止此类攻击，我们更应该使用HTTPS协议代替HTTP









---                                                                                                                                                             
layout: post
title: "【Node.js】node学习"
description: "node学习"
category: 'node.js'
---

node学习 
一 下载安装
1.安装依赖包 
sudo apt-get install g++ curl libssl-dev apache2-utils 
sudo apt-get install git-core 

2.克隆源代码

先进入/home为自己建一个目录：
mkdir song

并在下边建立node目录用于下载：
mkdir node

克隆源代码：
git clone git://github.com/ry/node.git 

3.安装
node安装采用configure/make的方法，使用configure程序扫描系统查找依赖路径
./configure
运行make来编译
make 
为系统下全部用户安装
sudo make install 

二 入门
让我们来做一个超复杂的hello world的程序。首先创建一个文件叫做base.js,其中实现命名空间，类继承等。以便实现传统(java)的设计模式。
<div class="highlighter-rouge"><pre class="highlight"><code> 
if (typeof Song == "undefined" || !Song) {
	var Song = {
		'version' : '1.1.0',
		'author' : 'songxiaoyin',
		'email' : 'xiaoyinsong@gmail.com'
	};
}
Song.namespace = function() {
	var a = arguments, o = null, i, j, d;
	for (i = 0; i &lt; a.length; i = i + 1) {
		d = ("" + a[i]).split(".");
		o = Song;
		for (j = (d[0] == "Song") ? 1 : 0; j &lt; d.length; j = j + 1) {
			o[d[j]] = o[d[j]] || {};
			o = o[d[j]];
		}
	}
	return o;
};
(function(lib){
var Class = function(properties) {
	var klass = function() {

		return (arguments[0] !== null && this.initialize && $type(this.initialize) == 'function') ? this.initialize
				.apply(this, arguments)
				: this;
	};
	$extend(klass, this);
	klass.prototype = properties;
	klass.constructor = Class;

	return klass;
};
Class.prototype = {
	extend : function(properties) {

		var proto = new this(null);

		for ( var property in properties) {
			var pp = proto[property];
			proto[property] = Class.Merge(pp, properties[property]);
		}
		return new Class(proto);
	},
	implement : function() {
		for ( var i = 0, l = arguments.length; i &lt; l; i++) {
			$extend(this.prototype, arguments[i]);
		}
	}
};
Class.Merge = function(previous, current) {
	if (previous && previous != current) {
		var type = $type(current);
		if (type != $type(previous))
			return current;
		switch (type) {
		case 'function':
			var merged = function() {
				this.parent = arguments.callee.parent;
				return current.apply(this, arguments);
			};
			merged.parent = previous;
			return merged;
		case 'object':
			return $merge(previous, current);
		}
	}
	return current;
};
function $merge() {
	var mix = {};
	for ( var i = 0; i &lt; arguments.length; i++) {
		for ( var property in arguments[i]) {
			var ap = arguments[i][property];
			var mp = mix[property];
			if (mp && $type(ap) == 'object' && $type(mp) == 'object')
				mix[property] = $merge(mp, ap);
			else
				mix[property] = ap;
		}
	}
	return mix;
}
function $type(obj) {
	if (!$defined(obj))
		return false;
	if (obj.htmlElement)
		return 'element';
	var type = typeof obj;
	if (type == 'object' && obj.nodeName) {
		switch (obj.nodeType) {
		case 1:
			return 'element';
		case 3:
			return (/\S/).test(obj.nodeValue) ? 'textnode' : 'whitespace';
		}
	}
	if (type == 'object' || type == 'function') {
		switch (obj.constructor) {
		case Array:
			return 'array';
		case RegExp:
			return 'regexp';
		case Class:
			return 'class';
		}
		if (typeof obj.length == 'number') {
			if (obj.item)
				return 'collection';
			if (obj.callee)
				return 'arguments';
		}
	}
	return type;
};
function $defined(obj) {
	return (obj != undefined);
};
var $extend = function() {
	var args = arguments;
	if (!args[1])
		args = [ this, args[0] ];

	for ( var property in args[1])
		args[0][property] = args[1][property];

	return args[0];
};
var Each = function(list, fun) {
	for ( var i = 0, len = list.length; i &lt; len; i++) {
		fun(list[i], i);
	}
};
function StringBuffer(){
	this._strings_=new  Array;
}
StringBuffer.prototype.append=function (str){
	this._strings_.push(str);
};
StringBuffer.prototype.toString=function(){
	return this._strings_.join("");
};
String.prototype.Trim = function(){
	return this.replace(/(^\s*)|(\s*$)/g,""); 
};
var randomChar=function()  {
	  var l=7;
    var  x="qwertyuioplkjhgfdsazxcvbnm";
    var  tmp=[];
    var re;
    for(var  i=0;i&lt;  l;i++)  {
         tmp.push(x.charAt(Math.ceil(Math.random()*100000000)%x.length));
    }
    re=tmp.join("");
    return re;
};
Song.Class = Class;
var slice = Array.prototype.slice;
var bind = function(thisp, fun) {
	return function() {
		return fun.apply(thisp,slice.call(arguments));
	};
};

lib.randomChar=function(){
	return randomChar;
};
lib.getStringBuffer=function(){
	return StringBuffer;
};
var BaseClass = new Class( {
	extend : function(destination, source) {
		for ( var property in source) {
			destination[property] = source[property];
		}
		return destination;
	},
	initialize : function(options) {
		this.setOptionsValue();
		this.setOptions(options);

	},
	setOptionsValue : function() {
		this.options = {};
	},
	setOptions : function(options) {
		this.extend(this.options, options || {});
	}
});
Song.BaseClass =BaseClass;

var StandardFormat = BaseClass.extend( {
	Manager:{
        push:function(obj){
            this.list.push(obj);
        },
        initialize : function() {
    	    this.list=[];
            this.sign = false;
        },		
        sign:true
    },
    response:function(req,res){
    	console.log("start");
    	res.writeHead(200,{'Content-Type':'text/plain'});
    	res.end(this.options.name);
    	console.log("end");
    },
    event:function(){
    	this._response=bind(this,this.response);
    	this.http.createServer(this._response).listen(8124);
    },
	create:function(){
		this.http=require('http');
   
	},
	initialize : function(options) {
		this.parent(options);
		
		if (this.Manager.sign) {
			this.Manager.initialize();
		}
		this.create();
		this.event();
		console.log("Server running at http://127.0.0.1:8124");
	},
	setOptionsValue : function(options) {
		this.options = {
				name:"songxiaoyin"
		};
	}
});
new StandardFormat({
	name:"hello"
});
})(Song); 
</code></pre></div>
好了打开浏览器，访问http://127.0.0.1:8124，显示出hello。
到此说明我之前写页面的js方式都可以用。

三 创建一个聊天室

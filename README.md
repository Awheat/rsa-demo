# rsa-demo
RSA前端非对称数据加密传输，服务端解密示例 ！

### 需求
最近项目当中需要用到数据加密传输，于是就花了一些时间来看了一下非对称加密，看了不少资料，市面上各种加密的库倒是不少，于是动手实践，在实践过程中遇到一个问题就是
配套的库好像很少， 常遇到的一个情况就是数据加密传输给服务端了，可是解不了。不过最后皇天不负有心人，终于找到了一个配套的库，并且经过实践后是没有任何问题的，前端
数据加密，后端解密后的内容也是正确的，于是就此做一个小小的笔记。

### 所用到的库

- - -

#### 服务端：

* express
* node-rsa

#### 客户端：

* jquery
* jsencrypt

### 服务端代码：

```
var express = require('express');
var bodyParser = require('body-parser');
var NodeRSA = require('node-rsa');
var app = express();

var key = new NodeRSA({b:512});

设置解密的方式
key.setOptions({encryptionScheme: 'pkcs1'});

公钥和私钥
var publicDer=key.exportKey('pkcs8-public');
var privateDer=key.exportKey('pkcs8-private');

app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));

app.get('/getPublicKey',function(req, res){
	res.send({"key":publicDer});
});

app.post('/postMsg',function(req, res){
	var result = req.body;
	var msg = result.msg;
	console.log(msg);
	console.log(key.decrypt(msg,'utf-8'));
	res.send({"code":"0"});
});

var server = app.listen(3000, function () {
  var host = server.address().address;
  var port = server.address().port;

  console.log('Example app listening at http://%s:%s', host, port);
  console.log(publicDer);
  console.log(privateDer);
});
```

### 客户端代码：

```
let jsencrypt = new JSEncrypt();

获取公钥
function getPublicKey(){
    $.get('xxxxxxxx',function(res){
        jsencrypt.setPublicKey(res.json().key);
    });
}

加密传输
function postMessage(){
    let params = {};
    params.id = "110";
    params.msg = jsencrypt.encrypt('这个就是加密传输的内容....');
    $.post('xxxxxx',params,function(res){
        console.log(res);
    });
}

```

### 最后
重要代码都贴出来了，整个过程就是这样子，前端加密传输，后端解密也是没有问题了！


# Node-lesson
Node学习笔记

###**node lesson 笔记**
####*lesson01*
一个最简单的node服务器
```npm init``` : 生成一个package.json
```npm install express --save``` : --save是下载好包后自动添加进package.json的dependencies里面 
```
//app.js程序实现
var express = require('express');
var app = express();
app.get('/',function(req,res){
	res.send('Hello~');
});
app.listen(3000,function(){
	console.log('app is listening...');
})
//打开命令窗口运行 node app.js
//在浏览器地址栏输入localhost:3000/
```

####*lesson02*
学习使用外部模块 以及 req.query用法
```
//app.js程序实现
//引入依赖
var express = require('express');
var utility = require('utility'); //实现字符串加密的库
//建立express实例
var app = express();

app.get('/',function(req,res){
// 从 req.query 中取出我们的 q 参数。
// 如果是 post 传来的 body 数据，则是在 req.body 里面，不过 express 默认不处理 body 中的信息，需要引入 https://github.com/expressjs/body-parser 这个中间件才会处理
	var q = req.query.q;
	var md5str = utility.md5(q);
	res.send(md5str);
});

app.listen(3000,function(){
	console.log('app is running at port 3000..');
});
//打开命令窗口运行 node app.js
//访问http://localhost:3000/?q=huanghuiqi  （别忘了带参数）
```

####*lesson03*
使用 superagent 与 cheerio 完成简单爬虫   
当在浏览器中访问 http://localhost:3000/ 时，输出 CNode(https://cnodejs.org/ ) 社区首页的所有帖子标题和链接，以 json 的形式。    
- superagent(http://visionmedia.github.io/superagent/ ) 是个 http 方面的库，可以发起 get 或 post 请求。

- cheerio(https://github.com/cheeriojs/cheerio ) 大家可以理解成一个 Node.js 版的 jquery，用来从网页中以 css selector 取数据，使用方式跟 jquery 一样一样的。
```
//app.js实现代码
app.get('/', function (req, res, next) {
  // 用 superagent 去抓取 https://cnodejs.org/ 的内容
  superagent.get('https://cnodejs.org/')
    .end(function (err, sres) {
      // 常规的错误处理
      if (err) {
        return next(err);
      }
      // sres.text 里面存储着网页的 html 内容，将它传给 cheerio.load 之后
      // 就可以得到一个实现了 jquery 接口的变量，我们习惯性地将它命名为 `$`
      // 剩下就都是 jquery 的内容了
      var $ = cheerio.load(sres.text);
      var items = [];
      $('#topic_list .topic_title').each(function (idx, element) {
        var $element = $(element);
        items.push({
          title: $element.attr('title'),
          href: $element.attr('href')
        });
      });
      res.send(items);
    });
});
```

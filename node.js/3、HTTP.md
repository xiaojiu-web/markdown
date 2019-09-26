### 查看 HTTP 请求，从中提取出请求的 URL 以及 GET/POST 参数

### 为了解析这些数据，我们需要额外的 Node.JS 模块，它们分别是 url 和 querystring 模块。



+ 入门

  ```javascript
  //引入http                 //本网页内容
  let http = require("http");
  ///创建服务器
  let server = http.createServer((req, res) => {
      res.writeHead(200, {"Content-type": "text/html;charset=UTF-8"});//头部文件  200请求成功状态码
      res.write("hollow world");
      res.end("like666");
  });
  //监听
  server.listen(80, "127.0.0.1");
  ```

+ 访问该网站其他页面

  ```javascript
  //引入http
  let http = require("http");
  let fs = require("fs");
  ///创建服务器
  let server = http.createServer((req, res) => {
      if (req.url === "/index1.html") {
          fs.readFile("./index1.html", (err, data) => {
              if (!err) {
                  res.writeHead(200, {"Content-type": "text/html;charset=UTF-8"});
                  res.end(data);
              }
          })
      }
     /*
     else if (req.url === "/css/index.css") {
          fs.readFile("./css/index.css", (err, data) => {
              if (!err) {
                  res.writeHead(200, {"Content-type": "text/css"});   //引入css文件
                  res.end(data);
              }
          })
      }
      */
     /*
     else if (req.url === "/img/1.PNG") {
          fs.readFile("./img/1.PNG", (err, data) => {             //引入img文件
              if (!err) {
                  res.writeHead(200, {"Content-type": "image/PNG"});
                  res.end(data);
              }
          })
      }
      */
      else{
          res.writeHead(200, {"Content-type": "text/html;charset=UTF-8"});
          res.end("找不到该文件");
      }
  });
  server.listen(80,"127.0.0.1")
  ```

  

  + URL  :  解析 URL 字符串

    + ```javascript
      const url = require('url');
      let server = http.createServer((res, req) => {
           let myURL =url.parse(res.url);
      }
      ```

  

  ##  GET请求

  + 在html页面 

       ```html
         <form action="http://127.0.0.1:80/" method="get">
       ```
  
       
  
  + js页面
  
     ```javascript
    let http = require("http");
    let url = require("url");
    let server = http.createServer((res, req) => {
        let myUrl = url.parse(res.url, true); //  parse 将完整的url分解为多个单独个体，便于获取
        let query = myUrl.query;  // query: ?后面的部分
        console.log(query.name);
        req.end("hollow world");
    });
    server.listen(80, "127.0.0.1");
    ```
  
    
  
  ## POST请求
  
  + html界面
  
      ```html
    <form action="http://127.0.0.1:80/postmsg" method="post" enctype="multipart/form-data">
    ```
  
  + js 界面
  
    
  
    ```javascript
  let http = require("http");
  let querystring=require("querystring");  // 用于解析和格式化 URL 查询字符串的工具
  let server = http.createServer((res, req) => {
      if (res.url === "/postmsg" && res.method.toLowerCase() === "post") {
    //1、变量                           //method.toLowerCase() 转化为小写
          let Alldata="";
  //2、接受小段数据
          res.on("data",(brr)=>{
              Alldata+=brr;
          });
  //3、所有的数据传输完毕
          res.once("end",()=>{
              req.end("数据传输完毕");
              let dataobj=querystring.parse(Alldata);  //将字符串 'foo=bar&abc=xyz&abc=123' 解析为：对象；{foo: 'bar', abc: ['xyz', '123']}  键值对
              console.log(dataobj);
          });
      }
  });
    
    ```
  
  
  
  ### 将图片/视频文件放到服务器中
  
  + 借助第三方框架来做： **formidable**
    + 安装： npm i -S formidable
  
  
  
  + 案例
  
     ```javascript
    let http = require("http");
    let fs = require("fs");
    let formidable = require("formidable");
    let util = require("util");  //将obj转化为字符串
    let path = require("path");
    let server = http.createServer((req, res) => {
    if (req.url === "/postmsg" && req.method.toLowerCase() === "post") {
            if (req.url == "/postmsg" && req.method.toLowerCase() == 'post') {
                //实例化对象
             let form = new formidable.IncomingForm();
                //设置上传的文件类路径
             form.uploadDir = "./upload"; //要保存到那个文件夹下
                // 3. 获取表单的内容
             form.parse(req, (err, fields, files) => {
                 res.writeHead(200, {'content-type': 'text/html;charset=UTF-8'});
                 res.write("写入成功");
                 res.end(util.inspect({fields: fields, files: files}))
        });
    ```
  
    
    
    + 此方法获取的只是图片前面部分，没有后缀，不可查看
  
  
  
  
  
  
  
  ### 加上图片后缀
  
   ```javascript
  let http = require("http");
  let fs = require("fs");；
  let formidable = require("formidable");///cmd:  npm i -S formidable  ///Github
  let util = require("util");//将obj转化为字符串
  let uuidv1 = require('uuid/v1');///uuid:随机生成时间戳，  cmd:  npm install uuid
  let path = require("path");///path：获取路径中最后一个 . 以后的内容 如 254af.jpg ;获取" .jpg"
  let server = http.createServer((req, res) => {
   
          if (req.url == "/postmsg" && req.method.toLowerCase() == 'post') {
              //实例化对象
              let form = new formidable.IncomingForm();
              //设置上传的文件类路径
              form.uploadDir = "./upload";
              // 3. 获取表单的内容
              form.parse(req, (err, fields, files) => {
                  // 3.1 生成随机的名称
                  let name = uuidv1();
                  // 3.2 获取上传文件的后缀
                  let extName = path.extname(files.photo.name);
                  // 3.3 设置路径
                  let oldPath = __dirname + "/" + files.photo.path;
                  let newPath = __dirname + "/upload/" + name + extName;
                  // 3.4 改名
                  fs.rename(oldPath, newPath, (err) => {  ///fs.rename()：用新路径将老路径替换掉
                      if (!err) {
                          res.writeHead(200, {'content-type': 'text/html;charset=UTF-8'});
                          res.write("写入成功");
                          res.end(util.inspect({fields: fields, files: files}));
                      } else {
                          throw  err;
                      }
                  });
              });
  
          }
      }
  });
  server.listen(80, "127.0.0.1");
   ```
  
  
  
  
  
  
  
  
  
  
## mongodb是一个基于分布式文件存储的数据库；是非关系数据库当中功能最丰富，最像关系数据库的

+ 安装：

  ```npm
  $ npm install mongodb
  ```
  
+ 借助可视化工具
  
  + NoSQL Manager for MongoDB
  
    +  下载地址
  
      + <a href="https://www.mongodbmanager.com/">可视化工具</a>
  
          
  
+ 可视化工具使用方法
  
  +  创建数据库： use moon；  进入数据库 db.moon ;
  
   + 手动创建文档 
  
    +  入集合插入文档内容   db.moon.insert({data})；//插入一条；； insertmany（）插入多条；
  
    +  查看文档：db.moon.find();
  
    +  update()：文档更新
  
    + 文档内容替换：   
  
        db.moon.update({data},{$set：{要替换的内容}})；//默认只修改一条：想修改多条加上：{$set：{要替换的内容}}，{multi：true};
  
    + 文档内容删除
  
        db.moon.update({data},{$unset：{删除内容}})；//unset:删除内容
  
    + 删除文档：remove()；
  
        + db.moon.remove({data});  ({data},true);只删除第一个：
  
        +  instor :1 删除；  0；留下
  
    + 添加内容：：db.moon.update({data}, $push:{data}}
  
    + 删除内容：：db.moon.update({data}, $pop:{}}
  
## mongoose

  - mongoose：nodeJS提供连接 mongodb的一个库
  - npm install mongoose --save

  

  ### 引入模块：
  ```javascript
   let mongoose=require("mongoose");
   mongoose.connect("mongodb://localhost/moon");//必须在可视化工具中创建moon文档
  ```
#### 判断是否链接成功

```javascript
let db = mongoose.connection; ///`connect()` 返回一个状态待定（pending）的连接
db.on('error',()=>{
    console.log("链接失败");
});
db.once('open', function() {
    console.log("链接成功");
});

db.once('close', function() {
    console.log("链接断开");
});
```

###   Schema   : 模式对象

+ Mongoose 的一切始于 Schema。每个 schema 都会映射到一个 MongoDB
  collection ，并定义这个collection里的文档的构成。

  + 定义方法

    + ```javascript
      let Schema=mongoose.Schema;
      new Schema();
      ```

    + 案例

      + ```javascript
        let Schema=mongoose.Schema;
        let personSchema=new Schema({
            name:String,
            age:Number,
            sex:String,
            chat:String,
        });
        ```

###   Model 对象

  ```javascript
let personModel=mongoose.model("person",personSchema);
  ```

+ 案例：

   ```javascript
  let personModel=mongoose.model("person",personSchema);
  //插入文档
  personModel.create({
      name:"谢霆锋",
      age:56,
      chat:"12345679哈哈哈",
  },(err)=>{
      if(!err){
          console.log("插入成功");
      }else {
          throw err;
      }
  });
  ```

## 增删改查

+ 增加

   ```javascript
  personModel.create([{name:"chenqi"}]，(err)=>{
                      if(!err){
                         console.log("插入成功");
                          }else {        
                          throw err;
                                 }
                        });
  ```

  

+ 查找

   ```javascript
  personModel.find({}，(err，doc)=>{// {}从全部里面查找
            if(!err){
                         console.log(doc);//将查到的内容输出
                          }else {        
                          throw err;
                                 }
                        });
  ```

+ 修改

   ```javascript
  personModel.update({name:"王菲4"},{$set:{name:"张桓",age:66}},{multi:true},(err)=>{
      if(!err){
          console.log("修改成功");
      }else {
          throw err;
      }
  });
  ```

+ 删除

   ```javascript
  personModel.remove({name:"谢霆锋"},(err)=>{
      if(!err){
          console.log("删除成功");
      }else {
          throw err;
      }
  })
  ```

+  查询文档个数

  ```javascript
  personModel.count({},(err,count)=>{
      if(!err){
          console.log(count);
      } else {
          throw err;
      }
  })
  ```

  


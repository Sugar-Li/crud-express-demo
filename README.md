# 使用express创建一个增删改差的页面

## 前言：

​	这个demo有两个版本，第一个是将数据存储到文件中，直接调取。由于没有相关的数据调用api，所以需要自己封装关于数据操作的异步api

​	第二个版本是使用mongodb来操作数据，直接调用相关api

​	第一个版本主要是为了操作掌握node关于异步函数和回调函数的使用，属于node中的核心基础，在很多地方都会用到

​	第二个是在实际操作过程使用的方法。

## 学习目的

+ 学习掌握使用express框架

+ 学习模块单一职责思想

+ 封装异步Api

+ 持久化保存，线保存到文件

+ 再学习使用MongoDB

  

## 问题与重点

1. `post` 和 `get` 的区别

- `post`: 指的是 HTTP [POST 方法](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html#sec9.5) ; 表单数据会包含在表单体内然后发送给服务器.
- `get`: 指的是 HTTP [GET 方法](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html#sec9.3); 表单数据会附加在 `action` 属性的URI中，并以 '?' 作为分隔符, 然后这样得到的 URI 再发送给服务器. 当这样做（数据暴露在URI里面）没什么副作用，或者表单仅包含ASCII字符时，再使用这种方法吧。



## 1. 基础设置

+ 新建package.json

```shell
npm init -y
```

+ 下载express

```shell
//新版本默认会保存到package.json中，所以可以忽略 -save
npm i express
```

+ 新增app.js作为入口，调用express并测试

```html
//声明express
var express=require("express")

//调用express方法
var app=express()

app.get('/',function (req,res) {
    //在express模板中，默认是使用utf-8编译的，不需要手动设置
    res.send('你好，世界')
})

// 监听，端口号为3000
app.listen(3000,function () {
    console.log('running...')
})
```

+ 创建views——公共文件
+ 创建public——调用资源

- 

## 2. 配置使用art-template

安装:

```shell
npm i -S art-template
npm i -S express-art-template
```

配置：

```shell
#在配置了 express 后
app.engine('html',require('express-art-template'))
```

使用:

```javascript
//调用art-template，自动在根目录下寻找到views文件夹
app.get('/index',function (req,res) {
    res.render('index.html')
})
```





## 3. 布置首页样式

### 使用Bootstrap获取基本框架

+ 在bootstrap网页上获取一个基本框架，写入到index.html中，除去逻辑层的js代码
+ 获取css代码，写入到public/css/main.css中
+ 开放node_modules和public文件

+ 下载BootStrap
+ 修改bootstrap样式的位置



### 修改基本框子样式，得到我们相应的首页样式

+ 修改主体内容
+ 根据写入的数据显示的页面



## 4.根据数据在视图层显示


+ 在index.html中读取

```javascript
  <!--表头-->
                    <thead>
                    <tr>
                        <th>#</th>
                        <th>姓名</th>
                        <th>性别</th>
                        <th>年龄</th>
                        <th>爱好</th>
                    </tr>
                    </thead>
                    <!--表单具体内容-->
                    <tbody>
                        {{ each students}}
                        <tr>
                            <td>{{$value.id}}</td>
                            <td>{{$value.name}}</td>
                            <td>{{$value.gender}}</td>
                            <td>{{$value.age}}</td>
                            <td>{{$value.hobbies}}</td>
                        </tr>
                        {{/each}}
                    </tbody>
```

+ 在app.js中，直接写入一个对象，

```javascript
//调用art-template，自动在根目录下寻找到views文件夹
app.get('/index',function (req,res) {
        res.render('index.html',{
            fruits:[
                '苹果',
                '香蕉',
                '西瓜'
            ],
            students:[
                {"id":1,"name":"张三","gender":1,"age":18,"hobbies":"吃饭，睡觉，打农药"},
                {"id":2,"name":"张三","gender":2,"age":18,"hobbies":"吃饭，睡觉，打农药"},
                {"id":3,"name":"张三","gender":1,"age":18,"hobbies":"吃饭，睡觉，打农药"},
                {"id":4,"name":"张三","gender":2,"age":18,"hobbies":"吃饭，睡觉，打农药"},
                {"id":5,"name":"张三","gender":1,"age":18,"hobbies":"吃饭，睡觉，打农药"},
                {"id":6,"name":"张三","gender":2,"age":18,"hobbies":"吃饭，睡觉，打农药"},
                {"id":7,"name":"张三","gender":1,"age":18,"hobbies":"吃饭，睡觉，打农药"},
                {"id":8,"name":"张三","gender":1,"age":18,"hobbies":"吃饭，睡觉，打农药"},
                {"id":9,"name":"张三","gender":1,"age":18,"hobbies":"吃饭，睡觉，打农药"}
            ]
        })
    })
   
})
```



## 5.创建文件保存数据,并从从文件中读取数据

目的：

+ 将数据从逻辑层中独立出来
+ 数据存储在单独文件便于处理，后期会将这个文件省略，直接将数据砖到数据库中



应用：

1. 在根目录创建一个db.json文件，用来保存文件

```json
{
  "students":[
    {"id":1,"name":"张三","gender":1,"age":18,"hobbies":"吃饭，睡觉，打农药"},
    {"id":2,"name":"张三","gender":2,"age":18,"hobbies":"吃饭，睡觉，打农药"},
    {"id":3,"name":"张三","gender":1,"age":18,"hobbies":"吃饭，睡觉，打农药"},
    {"id":4,"name":"张三","gender":2,"age":18,"hobbies":"吃饭，睡觉，打农药"},
    {"id":5,"name":"张三","gender":1,"age":18,"hobbies":"吃饭，睡觉，打农药"},
    {"id":6,"name":"张三","gender":2,"age":18,"hobbies":"吃饭，睡觉，打农药"},
    {"id":7,"name":"张三","gender":1,"age":18,"hobbies":"吃饭，睡觉，打农药"},
    {"id":8,"name":"张三","gender":1,"age":18,"hobbies":"吃饭，睡觉，打农药"},
    {"id":9,"name":"张三","gender":1,"age":18,"hobbies":"吃饭，睡觉，打农药"}
  ]
}
```

2. 在app.js中

+ 调用核心模块——文件模块fs
+ 调用fs.readFile
  + 加入第2个参数 `utf8`既可直接按照 utf-8 编码读取的页面
  + 读取到的数据data是一个字符串，但是我们希望获取到的是文件中的对象
    + 使用 `JSON.parse()` 将字符串转化成 `JSON` 对象
    + 调用 `JSON.parse().students` 获得数组

```javascript
//调用art-template，自动在根目录下寻找到views文件夹
app.get('/index',function (req,res) {
    //使用 utf8 可以使得读取的文件按照 utf-8 编码
    fs.readFile('./db.json','utf8',function (err,data) {
        if(err){
            return res.status(500).send('Server error')
        }
         //从文件中读取到的数据必然是字符串
         //所以必须手动转换成对象
        var students=JSON.parse(data).students
        //data是字符串
        // console.log(typeof data)
        res.render('index.html',{
            fruits:[
                '苹果',
                '香蕉',
                '西瓜'
            ],
            students:students
        })
    })
})
```



## 6.路由设计与提取

| 请求方法 | 请求路径         | get 参数 | post参数                 | 作用             |
| -------- | ---------------- | -------- | ------------------------ | ---------------- |
| GET      | /students        |          |                          | 渲染首页         |
| GET      | /students/new    |          |                          | 渲染添加学生页面 |
| POST     | /students/new    |          | name,age,gender,hobby    | 处理添加学生请求 |
| GET      | /students/edit   | id       |                          | 渲染编辑页面     |
| POST     | /student/edit    |          | id,name,age,gender,hobby | 处理编辑请求     |
| EGT      | /students/delete | id       |                          | 处理删除请求     |

由于路由代码数量种类多，为了便于管理，单独划分一个js文件进行存储



+ 创建router.js文件

```javascript
/*
* 路由模块
*   职责：
*       处理路由
*       根据不同的请求方法+请求路径设置具体的请求函数
*
* */

//声明
var express=require('express')
var Student=require('./student')

//1.调用express方法，创建一个路由容器
var router = express.Router()


//2.将路由都挂载到 router 路由容器中
// 渲染首页
router.get('/students',function (req,res) {
    //使用 utf8 可以使得读取的文件按照 utf-8 编码
    // fs.readFile('./db.json','utf8',function (err,data) {
    //     if(err){
    //         return res.status(500).send('Server error')
    //     }
    //
    //     //data是字符串
    //     // console.log(typeof data)
    //     var students=JSON.parse(data).students
    //     //调用art-template，自动在根目录下寻找到views文件夹中的index.html
    //     res.render('index.html',{
    //         fruits:[
    //             '苹果',
    //             '香蕉',
    //             '西瓜'
    //         ],
    //         students:students
    //     })
    // })

    Student.find(function (err,students) {
        if (err){
            return res.status(500).send('Server error')
        }
        res.render('index.html',{
            fruits:[
                '苹果',
                '香蕉',
                '西瓜'
            ],
            students:students
        })
    })

})

//渲染添加学生页面
router.get('/students/new',function (req,res) {
    res.render('new.html')
})

//处理添加学生请求
router.post('/students/new',function (req,res) {
    // console.log(req.body)
    Student.save(req.body,function (err) {
        if (err){
            return res.status(500).send('Server error')
        }
        res.redirect('/students')
    })
})

//渲染编辑页面
router.get('/students/edit',function (req,res) {
//    1. 在客户端的列表页中处理链接问题（需要有id参数）
//    2.获取需要编辑的学生的id
//    3.渲染编辑页面
//      根据id获取学生信息
//       使用模板引擎渲染页面
    Student.findById(parseInt(req.query.id),function (err,student) {
        if (err){
            return res.status(500).send('Server error')
        }
        res.render('edit.html',{
            student:student
        })
    })
})

// 处理编辑请求
router.post('/students/edit',function (req,res) {
//    1.获取表单数据
//    2.更新
//    3.发送响应

    Student.updateById(req.body,function (err) {
        if (err){
            return res.status(500).send('Server error')
        }
        res.redirect('/students')
    })
})

//处理删除请求
router.get('/students/delete',function (req,res) {
    console.log(req.query.id)
    Student.deleteById(req.query.id,function (err) {
        if (err){
            return res.status(500).send('Server error')
        }
        res.redirect('/students')
    })
})

//将路由导出
module.exports=router
```



+ 关联到app.js接口文件中
  + app.js始终是入口文件，所以不能在app.js中导出 `module.exports=app`——这样我们将来执行的就是router.js了。
  + 在router.js中调用一个关联路由的方法
    + 声明express
    + 1.创建一个路由容器 router
    + 2.将路由都挂载到 router 路由容器中
    + 3.把路由导出
  + 在app.js中调用router.js
    + 声明
    + 把路由容器挂载到app服务中

```javascript
//router.js

//声明
var express=require('express')

//1.调用express方法，创建一个路由容器
var router = express.Router()

//2.将路由都挂载到 router 路由容器中
// 渲染首页
router.get('/student',function (req,res) {

})

//渲染添加学生页面
router.get('/students/new',function (req,res) {

})

//处理添加学生请求
router.post('/students/new',function (req,res) {

})

//渲染编辑页面
router.get('/students/edit',function (req,res) {

})

// 处理编辑请求
router.post('/students/edit',function (req,res) {

})

//处理删除请求
router.get('/students/delete',function (req,res) {

})

//将路由导出
module.exports=router
```



```javascript
//app.js

//声明
var router=require('./router')

//把路由容器挂载到app 服务中
app.use(router)

```



## 7. 处理添加页面及配置body-parser中间件

- 在express中获取表单post请求体数据
  - 在 Express 中没有内置获取表单 POST 请求体的API, 这里我们需要一个第三方包： body-parse

安装：

    npm i -S body-parser

配置

```javascript

//0. 先引包
var bodyParser = require('body-parser')
//配置 body-parser
//只要加入这个配置，则在 req 中多处一个 body 属性
//也就是说可以通过 req.body 来获取表单 post 请求体数据
// parse application/x-www-form-urlencoded
app.use(bodyParser.urlencoded({ extended: false }))
// parse application/json
app.use(bodyParser.json())
```



+ 在views配置new.html
  + 复制index页面，删除右侧主体内容，在bootstrap中寻找表单组件
  + 根据我们要获取的数据来配置new.html
  + 根据表单的action 和 method 调用post请求方法

```html
<!--右侧内容-->
        <div class="col-sm-9 col-sm-offset-3 col-md-10 col-md-offset-2 main">
            <h1 class="page-header">添加学生</h1>
            <!--添加表单-->
            <form action="/students/new" method="post">
                <!--姓名-->
                <div class="form-group">
                    <label for="name">姓名</label>
                    <input type="text" class="form-control" id="name" 
                           placeholder="请输入姓名。。。">
                </div>

                <!--性别，单选-->
                <label for="">性别</label>
                <div class="radio">

                    <label>
                        <input type="radio" name="gender" id="" value="1" checked>
                        男
                    </label>

                    <label>
                        <input type="radio" name="gender" id="" value="2">
                        女
                    </label>
                    <label class="disabled">
                        <input type="radio" name="gender" id="" value="0" disabled>
                       未知
                    </label>
                </div>

                <!--年龄-->
                <div class="form-group">
                    <label for="age">年龄</label>
                    <input type="text" class="form-control" id="age" 
                           placeholder="请输入年龄。。。">
                </div>

                <!--爱好-->
                <div class="form-group">
                    <label for="hobbies">爱好</label>
                    <input type="text" class="form-control" id="hobbies" 
                           placeholder="请输入爱好。。。">
                </div>

                <button type="submit" class="btn btn-default">提交</button>
            </form>
        </div>


```

+ 处理添加学生请求

  + 获取表单数据
    + 注意：获取表单的数据的表单必须要用name属性
  		处理-——将数据保存到db.json文件中用以持久化	
    + 因为文件是字符串，读取处理的是对象
    + 所以先读取文件
    + 将文件转化成对象
    + 将数据push到文件对象中
    + 将对象转回字符串
    + 保存回文件中
  + 发送响应

```javascript
//处理添加学生请求
router.post('/students/new',function (req,res) {
    // console.log(req.body)
    Student.save(req.body,function (err) {
        if (err){
            return res.status(500).send('Server error')
        }
        res.redirect('/students')
    })
})
```





## 8. 封装student模块

​	因为在路由处理函数中需要不断获取，操作，保存students里面的数组，重复调用导致重复代码堆积，所以封装一个student模块，让处理函数直接调用student里面api即可，这样即减少代码，也符合模块单一职责的思想

​	这个模块只关心数据，不关心业务，主要包含以下api



+ 获取全部数据 `find`
  + 因为fs.readFile()方法属于异步函数，所以需要使用回调函数来获取异步函数中的结果
  + 在router中调用Student.find(callback)时
    + 首先将整个callback当成参数，先读取数据
    + 如果数据读取失败，则会调用callback（err）这个函数，此时err是一个失败的对象，也就是说err是存在的
      + 所以在回调函数callback==function(err,student){}中，err存在，所以返回系统错误信息--end
    + 如果数据读取成功，则会调用callback（null，JSON.parse(data).students），
      + 此时err为null，所以在callback==function(err,student){}中，只会触发 `students:students`，其中students==JSON.parse(data).students

```javascript
//student.js

var dbPath='./db.json'
/*
* 获取全部学生
* 返回的是一个列表[]
* callback中的参数
*   第一个参数是 err
*       成功时null
*       失败时就是错误对象——所以才会在回调函数触发 if（err）
*
*   第二个参数是 结果
*       成功是 数组
*       失败是 undefined
*
* */
exports.find=function (callback) {
    fs.readFile(dbPath,function (err,data) {
        if (err){
            return callback(err)
        }
        callback(null,JSON.parse(data).students)
    })
}
```

```javascript
//router.js


    Student.find(function (err,students) {
        if (err){
            return res.status(500).send('Server error')
        }
            students:students
        })
    })

```



+ 获取指定单一学生 
+ 添加保存学生 `save`

```javascript
//添加保存学生(传入两个参数，一个是要保存的学生对象，一个是回调函数)
exports.save=function(student,callback){
    fs.readFile(dbPath,function (err,data) {
        if (err){
            return callback(err)
        }
        //将字符串转换成对象，然后获取数据数组
        var students=JSON.parse(data).students

        //处理id唯一的，不重复
        student.id=students[students.length-1].id+1

        // 将对象保存到数组中
        students.push(student)

        //数组转回对象，在转会符串
        var fileData=JSON.stringify({
            students:students
        })

        // 保存文件
        fs.writeFile(dbPath,fileData,function (err) {
            if (err){
                //失败就吧错误对象返回
                return callback(err)
            }
            // 成功则返回一个null
            return callback(null)
        })
    })
}
```



+ 更新学生 `updateById`
  + 读取文件，获取数组
  + 根据id获取想要更新的对象
    + 使用find（）方法，接收一个函数作为参数，返回的是符合函数要求的对象（如果是对象数组时）
  + 循环，将传进来的对象的对应value值替换到符合要求（id相同）的对象的value中
  + 写回文件中

```javascript
//更新学生
exports.updateById=function(student,callback){
    fs.readFile(dbPath,function (err,data) {
        if (err){
            return callback(err)
        }
        //将字符串转换成对象，然后获取数据数组
        var students=JSON.parse(data).students

        //调用find遍历students数组，当找到对像中的id和给定对象student的id一致时，返回这个对象
        var stu=students.find(function (item) {
            return item.id===student.id
        })

        //通过循环的遍历对象的方法，将student更改的信息传到对应的stu中
        for (key in student){
            stu[key]=student[key]
        }

        //数组转回对象，在转回字符串
        var fileData=JSON.stringify({
            students:students
        })

        // 保存文件
        fs.writeFile(dbPath,fileData,function (err) {
            if (err){
                //失败就吧错误对象返回
                return callback(err)
            }
            // 成功则返回一个null
            return callback(null)
        })
    })
}
```



+ 删除学生 `delete`



## 9. 渲染编辑页面

+ 在客户端的列表页中处理链接问题（需要有id参数）
+ 获取需要编辑的学生的id
+ 渲染编辑页面
  + 根据id获取学生信息——调用student的api
    + 根据id获取学生信息（这里的id是字符串，需要转换成数字）
  +  使用模板引擎渲染页面
    + 在name，age等表单中中添加value

```javascript
//router.js

//渲染编辑页面
router.get('/students/edit',function (req,res) {
//    1. 在客户端的列表页中处理链接问题（需要有id参数）
//    2.获取需要编辑的学生的id
//    3.渲染编辑页面
//      根据id获取学生信息
//       使用模板引擎渲染页面
    Student.findById(parseInt(req.query.id),function (err,student) {
        if (err){
            return res.status(500).send('Server error')
        }
        res.render('edit.html',{
            student:student
        })
    })
})
```



## 10.处理编辑页面

```javascript
// 处理编辑请求
router.post('/students/edit',function (req,res) {
//    1.获取表单数据
//    2.更新
//    3.发送响应

    Student.updateById(req.body,function (err) {
        if (err){
            return res.status(500).send('Server error')
        }
        res.redirect('/students')
    })
})
```



11.删除项

```javascript
//处理删除请求
router.get('/students/delete',function (req,res) {
    console.log(req.query.id)
    Student.deleteById(req.query.id,function (err) {
        if (err){
            return res.status(500).send('Server error')
        }
        res.redirect('/students')
    })
})
```





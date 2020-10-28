# node-server
1、简单创建一个静态服务

> 使用require来载入http模块，并将实例化的HTTP赋值给变量http
    var http = require("HTTP")
  
> 创建服务器，使用htttp.createServer()方法创建服务器，并使用listen（）方法来绑定端口
    //req(request)用来接收数据,res(response)返回数据
    http.createServer(function(req, res){
      res.writeHead(200,{'Content-Type':'text/html;charset=utf8'})
      res.write('hello,this is a test page!')
      res.end()
    })
  
  2. 实现处理路由，支持静态目录的 server
  
  >第一步：先创建一个服务器
  >第二步：实现路由
      - 获取请求地址
      - 将请求地址与具体要返回数据的地址连接起来
      - 通过路径读取文件，处理读取文件时报错或者读取成功时返回给客户端的信息
 > 第三步：用listen()方法绑定端口
        
      var http = require("http")
      var fs = require("fs")
      var url = require("url")
      var path = require("path")

      //创建服务器，使用http.createServer()方法创建服务器
      http.createServer(function(req,res){
          //获取请求地址
          var pathObj = url.parse(req.url,true)
          //设置默认打开的文件为demo.html
          if(pathObj.pathname === '/'){
            pathObj.pathname += 'demo.html'
          }
          //设定用户只能请求sample目录下的文件。服务器会将路径信息映射到assets目录。(作用是用于连接路径，得到所有请求文件的具体路径)
          var realUrl = "sample" + pathObj.pathname

          //判断realUrl在本地服务中是否存在，如果不存在就直接返回404。
          //如果文件存在则调用fs.readFile方法读取文件。如果发生错误，我们响应给客户端500错误，表明存在内部错误。正常状态下则发送读取到的文件给客户端，表明200状态。
          fs.exists(realUrl,function(exists){
            if(!exists){
              res.writeHead(404,{
                'Content-Type' : 'text/plain'
              });

              res.write('404您访问的文件已丢失~~~')
              res.end()
            }else{
              fs.readFile(realUrl, 'binary', function(err, file){
                if(err){
                  res.writeHead(500,{
                    'Content-Type':'text/plain'
                  })

                  res.write('文件内部错误~~')
                  res.end(err)
                }else{
                  res.writeHead(200,{
                    'Content-Type':'text/html'
                  })

                  res.write(file, "binary");
                            res.end();
                }
              })
            }
          })
      }).listen(8080)

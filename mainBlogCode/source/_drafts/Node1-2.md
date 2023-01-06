---
title: Node即学即用-手写Twitter
date: 2023-01-06 15:46:19
tags: Node
---
<meta name="referrer" content="no-referrer"/>


让我们用 Node 来创建一个类似 Twitter 的 Web 应用。

## 安装 Express 模块

终端执行代码：

```
$ npm install express
```

## 使用 Express 的基本 Web 服务器

终端执行代码：

```

var express = require('express')
var app = express.createServer()
app.listen(8000)
var tweets = []
app.get('/', function(req, res) {
    res.send('Welcome to Node Twitter')
})
app.post('/send', express.bodyParser(), function(req, res) {
    if (req.body && req.body.tweet) {
        tweets.push(req.body.tweet)
        res.send({status:"ok", message:"Tweet received"})
    } else {
        // 没有 tweet ？
        res.send({status:"nok", message:"No tweet received"})
    }
})
app.get('/tweets', function(req,res) {
    res.send(tweets)
})
```

**代码解析**

1) app.listen() 函数调用是异步的，因为绑定 TCP 端口也需要花时间，而其他（通过app.get() 和 app.post() 指定的）事件监听器则是同步的。

2)中间件`express.bodyParser` 为 req 对 象 添 加 了 新 的 属 性， 称 为 req.body。 这 个 属性（如果它存在的话）包含了 POST 数据对应的对象。express.bodyParser 中间件`只`能够处理 POST 方法的数据，而且要求 HTTP 头的 content-type 属性是`application/x-www-form-urlencoded` 或 `application/json`。这两种数据格式都能够很容易地解析成 key/value 值，并保存到 req.body 对象中。





## 编写Node.js服务器程序

```
var http = require('http');
http.createServer(function (req, res) {
res.writeHead(200, {'Content-Type': 'text/plain'});
res.end('Hello World\n');
}).listen(8124, "127.0.0.1");
console.log('Server running at http://127.0.0.1:8124/');
```

在浏览器中访问[http://127.0.0.1:8124/](http://127.0.0.1:8124/)可以看到页面中显示`Hello World`



1）先通过 require 方法把 HTTP 库包含到程序中来，HTTP 库所具有的功能已经赋给了 http 对象。

2）通过调用 HTTP 模块的一个`createServer`方法来创建新的 HTTP 服务器.并传入一个匿名函数作为参数，此函数绑定在
新创建服务器的事件监听器上进行 request 事件处理。每当一个新的访问请求到达 Web 服务器，它都将调用我们指定的函数方法来处理。我们称这类方法为回调（callback）。

举例： 每当通过浏览器访问[http://127.0.0.1:8124/](http://127.0.0.1:8124/)，都会通过匿名函数进行处理请求头，返回`hello world`。

3）首先必须调用 res.writeHead 方法来设置 HTTP 响应头，否则就不能返回真实内容给客户端。设置状态代码为 200（表示 HTTP 状态代码“200 OK”），并且传入一段 HTTP 头描述。在本例中，只指定了 Content-type。

4)end 方法将在把正文内容发送给客户端后进行关闭连接。



## 创建一个聊天服务器

#### 1.1创建新的 TCP 服务器


首先，调用 node 命令并带上文件名来启动服务器：

1）终端1输入
```
$ node
```

2）终端1继续输入：

```
var chatServer = net.createServer()
chatServer.on('connection', function(client) {
 client.write('Hi!\n');
 client.write('Bye!\n');
 client.end()
 })
chatServer.listen(9000)
```

**代码解析**



1） 加载了net 模块，net模块包含了 Node 需要的所有 TCP 功能。
2）调用 net.createServer() 方法来创建一个新的 TCP 服务器。
3）调用 on() 方法来添加一个事件监听器：每当有新的客户端通过网络连接接入服务器，就会触发 connection 事件，事件监听器就会调用匿名函数。
4）调用 client.write()，就能发送信息给该客户端。
5）调用 client.end() 方法来关闭连接。
6）调用listen() 函数，好让 Node 知道监听哪个端口。让我们马上测试一下吧。


#### 1.2通过telnet进行链接远端服务器

然后，打开 Telnet 连接到localhost 的 9000 端口（这是我们在 Node 程序中指定的端口）：

1）终端2输入
```
$ $ telnet 127.0.0.1 9000
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
Hi!
Bye!
Connection closed by foreign host.
```
到目前为止，我们创建了一个服务器，它能够接受客户端的连接，并且在断开连接
前发送了一小段内容。
下面我们再来添加几个功能吧。首先，需要能收到客户端发送的消息。

#### 2.1监听所有的连接请求

终端1输入：

```
var net = require('net')
var chatServer = net.createServer()
chatServer.on('connection', function(client) {
    client.write('Hi!\n');
    client.on('data', function(data) {
        console.log(data)
    })
})
chatServer.listen(9000)
```
注意，我们是在connection 回调函数的作用域中添加的这个事件监听器，这样就可以访问到连接事件所对应的 client 对象。新监听器关注的是 data 事件，每当 client 发送数据给服务器时，这一事件都会被触发。
接着要删掉 client.end() 这一行。如果关闭了和客户端的连接，又如何获得新的数据呢？

#### 2.2从 Telnet 发送数据到服务器
终端2输入：
```
$ telnet 127.0.0.1 9000
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
Hi!
```
终端2继续输入:

```
Hello, yourself
```
可以看到终端1中Node 吐出了一堆你从来没有见过的看似无用的数据：
![Telnet.png](https://upload-images.jianshu.io/upload_images/11846892-88e8cb6a10caca40.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

打印的字符信息实际是`十六进制字节数据`。每个字节对应着字符串“Hello, yourself”中的一个字母或字符。因为 JavaScript 无法很好地处理二进制数据，所以 Node 特地增加了一个 Buffer 库来帮助服务器。Node 并不知道 Telnet 发送的是什么类型的数据，所以在我们告诉它该用什么编码前，Node 只能保存原始的二进制格式。如果需要，可以调用 toString() 方法来把 Buffer 数据翻译为可读的字符串格式；不需要的话，也可以保持二进制格式，因为 TCP 和 Telnet 都能处理它。

#### 3.1客户端之间的通信
现在我们能够接收客户端发送的消息了，接下来要做的事情是让它们互相发送消息。要完成此功能，需要让它们互相通信。之前我们采用的是 client.write() 方法，可惜它只能和一个客户端通信，而我们需要照顾到所有客户端。为此可以创建一个列表，然后把希望与之通信的客户端都添加进去。当一个新的客户端出现时，就把它添加到列表中，然后利用此列表实现客户端之间的通信。
终端1输入：
```
var net = require('net')
var chatServer = net.createServer(),
clientList = []
chatServer.on('connection', function(client) {
    client.write('Hi!\n');
    clientList.push(client)
    client.on('data', function(data) {
        for(var i=0;i<clientList.length;i+=1) {
            // 把数据发送给所有客户端
            clientList[i].write(data)
        }
    })
})
chatServer.listen(9000)
```

#### 3.2客户端互相发消息
现在可以连接多个客户端到服务器上，看看它们是如何互相发
同时打开两个终端进行连接到服务器上,输入：
```
$ telnet 127.0.0.1 9000
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
Hi!
```
终端2继续输入：
```
Hello, yourself
```

这次，服务器没有记录它收到的任何消息，而是把列表中的每个客户端都轮询一遍，并把消息转发出去。值得注意的是，当终端 2 发送消息后，消息转发到了终端3 的 Telnet 客户端上，同时也发回给终端 2 的 Telnet 客户端。这是因为我们在发送消息的时候，并没有检查发送者是谁，只是简单地把消息转发给所有客户端。而且Telnet 客户端也无法区分哪些消息是自己发送的，哪些消息是别人发送的。我们需要改进一下。

#### 4.1改进消息发送
终端1输入：
```
var net = require('net')
var chatServer = net.createServer(),
clientList = []
chatServer.on('connection', function(client) {
    client.name = client.remoteAddress + ':' + client.remotePort
    client.write('Hi ' + client.name + '!\n');
    clientList.push(client)
    client.on('data', function(data) {
        broadcast(data, client)
    })
})
function broadcast(message, client) {
    for(var i=0;i<clientList.length;i+=1) {
        if(client !== clientList[i]) {
            clientList[i].write(client.name + " says " + message)
        }
    }
}
chatServer.listen(9000)
```

**代码解析**

1)为每个 client 对象增加 name 属性:因为闭包绑定了每个 client 对象和相应的请求。于是，在闭包内就可以利用 client.remoteAddress 和 client.remotePort来创建 client 的 name 属性.
* client.remoteAddress 是客户端所在的 IP地址
* client.remotePort 是客户端接收从服务器返回数据的 TCP 端口。
当不同的客户端从同一个 IP 发起连接时，它们各自会有唯一的 remotePort。以后再向client 发送消息时，我们就能用此唯一标识来找到它。

2)把处理 data 的事件监听器代码抽离到了 broadcast 函数中。这样，通过调用 broadcast 函数就可以把消息发送给所有客户端。这一次，我们把发起消息（data）的 client 对象也传递进去，以便于把它从接收消息的客户端列表中排除掉。我们还把 client.name 加到了要发送的消息上，好让其他客户端清楚消息来源。

#### 4.2 运行改进后的聊天服务器

终端2输入：
```
$ telnet 127.0.0.1 9000
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
Hi ::ffff:127.0.0.1:51170!
```
终端3输入：
```
$ telnet 127.0.0.1 9000
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
Hi ::ffff:127.0.0.1:51179!
```

我们的服务器有一个致命的缺陷。 如果其中一个客户端断开了，服务器就会出大问题。
**缺陷复现步骤**
1）终端 1先启动服务器、终端2连接客户端1，终端3连接客户端2
2）当终端 2中的客户端断开连接时，如果终端 3 再发送消息，即调用broadcast() 的时候，服务器会往一个已经断开的客户端写入数据。当终端 2 的
客户端断开的时候，它对应的 socket 已经无法写入或读取了。而对已经关闭的socket 进行 write() 操作时，Node 程序会抛出异常。这将导致其他所有客户端掉线。

**解决思路**
1）首先，必须保证在一个客户端断开的时候，要把它从客户端列表中移除，防止它再调用 write() 方法。V8 引擎也会把相应的 socket 对
象作为垃圾回收，并释放相应的内存。
2）其次，要采用更保险的方式调用 write() 方法。我们要确保 socket 从上次被写入到现在，没有发生任何阻碍我们调用 write()方法的事情。好在用 Node 很容易做到这两点。

#### 5.1把聊天服务器改造得更加健壮

终端1输入：
```
var net = require('net')
var chatServer = net.createServer(),
clientList = []
chatServer.on('connection', function(client) {
    client.name = client.remoteAddress + ':' + client.remotePort
    client.write('Hi ' + client.name + '!\n');
    clientList.push(client)
    client.on('data', function(data) {
        broadcast(data, client)
    })
    client.on('end', function() {
        clientList.splice(clientList.indexOf(client), 1)
    })
    client.on('error', function(e) {
        console.log(e)
    })
})

function broadcast(message, client) {
    var cleanup = []
    for(var i=0;i<clientList.length;i+=1) {
        if(client !== clientList[i]) {
            if(clientList[i].writable) {
                clientList[i].write(client.name + " says " + message)
            } else {
                cleanup.push(clientList[i])
                clientList[i].destroy()
            }
        }
    }
    // 在写入循环中删除死节点，消除垃圾索引
    for(i=0;i<cleanup.length;i+=1) {
        clientList.splice(clientList.indexOf(cleanup[i]), 1)
    }
}

chatServer.listen(9000)
```
**代码分析**

1)我们先处理断开连接的客户端。当一个客户端断开时，要把它从客户端列表中移除。这可以利用 end 事件来完成。一个 socket 断开连接时会触发 end 事件，表示它要关闭。
2）调用 Array.splice() 将客户端从 clientList 列表中移除。Array.indexOf() 方法用于找到客户端在列表中的位置，然后 splice() 把它从列表中移除。在此之后，下一个客户端调用 broadcast 方法时，已经断开的客户端将不会再出现在列表中了。
3)调用 broadcast 函数的时候，检查一下 socket 是否可写，以确保不会因为任何一个不可写的 socket 导致异常。不仅如此，发现任何不可写的 socket 后，还要通 过 Socket.destroy() 方 法 将 其 关 闭 并 从 clientList 中 移 除。 注 意， 遍 历clientList 的过程中并没有移除 socket，因为我们不想在遍历过程中出现任何未知的副作用。现在我们的服务器更加健壮了。
4)为 client 对象的 error 事件添加了 console.log() 调用后，可以确保客户端发生的任何错误都会被记录下来。而之前增加的代码，则能够确保在客户端抛出错误的时候，不会因为异常而导致服务器停止。

这样初级的实时聊天系统就完成了！
---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️


参考链接:
* [Node即学即用](https://awesome-programming-books.github.io/nodejs/Node%E5%8D%B3%E5%AD%A6%E5%8D%B3%E7%94%A8.pdf)


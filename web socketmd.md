# WebSocket 和HTTP的区别及原理

http协议是用在应用层的协议，他是基于tcp协议的，http协议建立链接也必须要有三次握手才能发送信息。http链接分为短链接，长链接，短链接是每次请求都要三次握手才能发送自己的信息。即每一个request对应一个response。长链接是在一定的期限内保持链接。保持TCP连接不断开。客户端与服务器通信，必须要有客户端发起然后服务器返回结果。客户端是主动的，服务器是被动的。

WebSocket他是为了解决客户端发起多个http请求到服务器，浏览器必须要经过长时间的轮训问题而生的，他实现了多路复用，他是全双工通信。在webSocket协议下客服端和浏览器可以同时发送信息。

建立了WenSocket之后服务器不必在浏览器发送request请求之后才能发送信息到浏览器。这时的服务器已有主动权想什么时候发就可以发送信息到服务器。而且信息当中不必在带有head的部分信息了与http的长链接通信来说，这种方式，不仅能降低服务器的压力。而且信息当中也减少了部分多余的信息。

# HTTP的短/长连接与websocket的持久连接

**HTTP 长连接**：

即在一定的期限内保持链接，客户端会需要在短时间内向服务端请求大量的资源，保持TCP连接不断开。客户端与服务器通信，必须要有客户端发起然后服务器返回结果。客户端是主动的，服务器是被动的。

在一个TCP连接上可以传输多个Request/Response消息对，所以本质上还是Request/Response消息对，仍然会造成资源的浪费、实时性不强等问题。

**HTTP 短连接**：

如果不是持续连接，即短连接，那么每个资源都要建立一个新的连接，HTTP底层使用的是TCP，那么每次都要使用三次握手建立TCP连接，即每一个request对应一个response，将造成极大的资源浪费。

**长轮询**：

即客户端发送一个超时，时间很长的Request，服务器hold住这个连接，在有新数据到达时返回Response。

**websocket的持久连接**：

只需建立一次Request/Response消息对，之后都是TCP连接，避免了需要多次建立Request/Response消息对而产生的冗余头部信息。

web socket的特点及好处
==============================

Web socket 支持 bi-directional communication between a server and a client. 
Web socket 方法一旦建立连接，可以在客户端不发送请求的情况下，主动发送“消息”给客户端。多个请求可以是通过一个连接完成的。
WebSocket 是为了满足基于 Web 的日益增长的实时通信需求而产生的。在传统的 Web 中，要实现实时通信，通用的方式是采用 HTTP 协议不断发送请求。但这种方式即浪费带宽（HTTP HEAD 是比较大的），又消耗服务器 CPU 占用（没有信息也要接受请求）。

Web socket 比 HTTP快，如果处理大量请求的话。Websockets do better when high loads are present.

More about the [Web Technologies Comparison](https://medium.com/platform-engineer/web-api-design-35df8167460)

# Web Sockets – Events

There are four main Web Socket API events −

Open
Message
Close
Error

Each of the events are handled by implementing the functions like **onopen, onmessage**, **onclose** and **onerror** functions respectively. It can also be implemented with the help of addEventListener method.

**Open**

Once a connection has been established between the client and the server, the open event is fired/triggered by web socket instance. The event is called onopen.

**onopen** refers to the initial handshake between client and the server which has lead to the first deal and the web application is ready to transmit the data.

The following code snippet describes opening the connection of Web Socket protocol −

```javascript
// Create a new WebSocket.
var socket = new WebSocket('ws://echo.websocket.org');
socket.onopen = function(event) {
   console.log(“Connection established”);
   // Display user friendly messages for the successful establishment of connection
   var label = document.getElementById(“status”);
   label.innerHTML = ”Connection established”;
}
```

## Example

Building up the client-HTML5 file.

```
<!DOCTYPE html>
<html>
   <meta charset = "utf-8" />
   <title>WebSocket Test</title>

   <script language = "javascript" type = "text/javascript">
      var wsUri = "ws://echo.websocket.org/";
      var output;
	
      function init() {
         output = document.getElementById("output");
         testWebSocket();
      }
	
      function testWebSocket() {
         websocket = new WebSocket(wsUri);
			
         websocket.onopen = function(evt) {
            onOpen(evt)
         };
      }
	
      function onOpen(evt) {
         writeToScreen("CONNECTED");
      }
	
      window.addEventListener("load", init, false);
   
   </script>

   <h2>WebSocket Test</h2>
   <div id = "output"></div>

</html>
```



**Handling Errors**

The **onerror** event is fired when something wrong occurs between the communications. The event **onerror** is followed by a connection termination, which is a **close** event.

A good practice is to always inform the user about the unexpected error and try to reconnect them.

```javascript
socket.onclose = function (event) {
   // Connection closed.
   // Firstly, check the reason.
	
   if (event.code != 1000) {
      // Error code 1000 means that the connection was closed normally.
      // Try to reconnect.
		
      if (!navigator.onLine) {
         alert("You are offline. Please connect to the Internet and try again.");
      }
   }
}
```

**Example**

The following program explains how to show error messages using Web Sockets −

```javascript
<!DOCTYPE html>
<html>
   <meta charset = "utf-8" />
   <title>WebSocket Test</title>

   <script language = "javascript" type = "text/javascript">
      var wsUri = "ws://echo.websocket.org/";
      var output;
		
      function init() {
         output = document.getElementById("output");
         testWebSocket();
      }
		
      function testWebSocket() {
         websocket = new WebSocket(wsUri);
			
         websocket.onopen = function(evt) {
            onOpen(evt)
         };
			
         websocket.onclose = function(evt) {
            onClose(evt)
         };
			
         websocket.onerror = function(evt) {
            onError(evt)
         };
      }
		
      function onOpen(evt) {
         writeToScreen("CONNECTED");
         doSend("WebSocket rocks");
      }
		
      function onClose(evt) {
         writeToScreen("DISCONNECTED");
      }
		
      function onError(evt) {
         writeToScreen('<span style = "color: red;">ERROR:</span> ' + evt.data);
      } 
		
      function doSend(message) {
         writeToScreen("SENT: " + message); websocket.send(message);
      }
		
      function writeToScreen(message) {
         var pre = document.createElement("p"); 
         pre.style.wordWrap = "break-word"; 
         pre.innerHTML = message; output.appendChild(pre);
      }
		
      window.addEventListener("load", init, false);
   </script>
	
   <h2>WebSocket Test</h2>
   <div id = "output"></div>
	
</html>
```

**Message**

The **Message** event takes place usually when the server sends some data. Messages sent by the server to the client can include plain text messages, binary data, or images. Whenever data is sent, the **onmessage** function is fired.

```
connection.onmessage = function(e){
   var server_message = e.data;
   console.log(server_message);
}
```

It is also necessary to take into account what kinds of data can be transferred with the help of Web Sockets. Web socket protocol supports text and binary data. In terms of Javascript, **text** refers to as a string, while binary data is represented like **ArrayBuffer**.

Web sockets support only one binary format at a time. The declaration of binary data is done explicitly as follows −

```
socket.binaryType = ”arrayBuffer”;
socket.binaryType = ”blob”;
```

## Strings

Strings are considered to be useful, dealing with human readable formats such as XML and JSON. Whenever **onmessage** event is raised, client needs to check the data type and act accordingly.

The code snippet for determining the data type as String is mentioned below −

```
socket.onmessage = function(event){

   if(typeOf event.data === String ) {
      console.log(“Received data string”);
   }
}
```

## JSON (JavaScript Object Notation)

It is a lightweight format for transferring human-readable data between the computers. The structure of JSON consists of key-value pairs.

### Example

```
{
   name: “James Devilson”,
   message: “Hello World!”
}
```

The following code shows how to handle a JSON object and extract its properties −

```
socket.onmessage = function(event) {
   if(typeOf event.data === String ){
      //create a JSON object
      var jsonObject = JSON.parse(event.data);
      var username = jsonObject.name;
      var message = jsonObject.message;
		
      console.log(“Received data string”);
   }
}
```

## XML

Parsing in XML is not difficult, though the techniques differ from browser to browser. The best method is to parse using third party library like jQuery.

In both XML and JSON, the server responds as a string, which is being parsed at the client end.

## ArrayBuffer

It consists of a structured binary data. The enclosed bits are given in an order so that the position can be easily tracked. ArrayBuffers are handy to store the image files.

Receiving data using ArrayBuffers is fairly simple. The operator **instanceOf** is used instead of equal operator.

The following code shows how to handle and receive an ArrayBuffer object −

```
socket.onmessage = function(event) {
   if(event.data instanceof ArrayBuffer ){
      var buffer = event.data;
      console.log(“Received arraybuffer”);
   }
}
```

**Example**

```
<!DOCTYPE html>
<html>
   <meta charset = "utf-8" />
   <title>WebSocket Test</title>

   <script language = "javascript" type = "text/javascript">
      var wsUri = "ws://echo.websocket.org/";
      var output;
		
      function init() {
         output = document.getElementById("output");
         testWebSocket();
      }
		
      function testWebSocket() {
         websocket = new WebSocket(wsUri);
			
         websocket.onopen = function(evt) {
            onOpen(evt)
         };
		
         websocket.onmessage = function(evt) {
            onMessage(evt)
         };
		
         websocket.onerror = function(evt) {
            onError(evt)
         };
      }
		
      function onOpen(evt) {
         writeToScreen("CONNECTED");
         doSend("WebSocket rocks");
      }
		
      function onMessage(evt) {
         writeToScreen('<span style = "color: blue;">RESPONSE: ' +
            evt.data+'</span>'); websocket.close();
      }

      function onError(evt) {
         writeToScreen('<span style="color: red;">ERROR:</span> ' + evt.data);
      }
		
      function doSend(message) {
         writeToScreen("SENT: " + message); websocket.send(message);
      }
		
      function writeToScreen(message) {
         var pre = document.createElement("p"); 
         pre.style.wordWrap = "break-word"; 
         pre.innerHTML = message; output.appendChild(pre);
      }
		
      window.addEventListener("load", init, false);
		
   </script>
	
   <h2>WebSocket Test</h2>
   <div id = "output"></div> 
	
</html>
```

**Close**

The **close()** method stands for **goodbye handshake**. It terminates the connection and no data can be exchanged unless the connection opens again. We call the **close()** method when the user clicks on the second button.

```
var textView = document.getElementById("text-view");
var buttonStop = document.getElementById("stop-button");

buttonStop.onclick = function() {
   // Close the connection, if open.
   if (socket.readyState === WebSocket.OPEN) {
      socket.close();
   }
}
```

It is also possible to pass the code and reason parameters we mentioned earlier as shown below.

```
socket.close(1000, "Deliberate disconnection");
```

**Example**

```
<!DOCTYPE html>
<html>
   <meta charset = "utf-8" />
   <title>WebSocket Test</title>

   <script language = "javascript" type = "text/javascript">
      var wsUri = "ws://echo.websocket.org/";
      var output;
	
      function init() {
         output = document.getElementById("output");
         testWebSocket();
      }
	
      function testWebSocket() {
         websocket = new WebSocket(wsUri);
		
         websocket.onopen = function(evt) {
            onOpen(evt)
         };
		
         websocket.onclose = function(evt) {
            onClose(evt)
         };
		
         websocket.onmessage = function(evt) {
            onMessage(evt)
         };
		
         websocket.onerror = function(evt) {
            onError(evt)
         };
      }
	
      function onOpen(evt) {
         writeToScreen("CONNECTED");
         doSend("WebSocket rocks");
      }
	
      function onClose(evt) {
         writeToScreen("DISCONNECTED");
      }
	
      function onMessage(evt) {
         writeToScreen('<span style = "color: blue;">RESPONSE: ' + 
            evt.data+'</span>'); websocket.close();
            //assume server sent something back
      }
	
      function onError(evt) {
         writeToScreen('<span style = "color: red;">ERROR:</span> '
            + evt.data);
      } 
	
      function doSend(message) {
         writeToScreen("SENT: " + message); websocket.send(message);
      }
	
      function writeToScreen(message) {
         var pre = document.createElement("p"); 
         pre.style.wordWrap = "break-word"; 
         pre.innerHTML = message; 
         output.appendChild(pre);
      }
	
      window.addEventListener("load", init, false);
   </script>
	
   <h2>WebSocket Test</h2>
   <div id = "output"></div>
	
</html>
```

## Web Sockets – Actions

Events are usually triggered when something happens. On the other hand, actions are taken when a user wants something to happen. Actions are made by explicit calls using functions by users.

The Web Socket protocol supports two main actions, namely −

- send( )
- close( )

### send ( )

This action is usually preferred for some communication with the server, which includes sending messages, which includes text files, binary data or images.

A chat message, which is sent with the help of send() action, is as follows −

```
// get text view and button for submitting the message
var textsend = document.getElementById(“text-view”);
var submitMsg = document.getElementById(“tsend-button”);

//Handling the click event
submitMsg.onclick = function ( ) {
   // Send the data
   socket.send( textsend.value);
}
```

**Note** − Sending the messages is only possible if the connection is open.

### close ( )

This method stands for goodbye handshake. It terminates the connection completely and no data can be transferred until the connection is re-established.

```
var textsend = document.getElementById(“text-view”);
var buttonStop = document.getElementById(“stop-button”);

//Handling the click event
buttonStop.onclick = function ( ) {
   // Close the connection if open
   if (socket.readyState === WebSocket.OPEN){
      socket.close( );
   }
}
```

It is also possible to close the connection deliberately with the help of following code snippet −

```
socket.close(1000,”Deliberate Connection”);
```

#  JavaScript Application Example

The following program code describes the working of a chat application using JavaScript and Web Socket protocol.

```
<!DOCTYPE html>
<html lang = "en">

   <head>
      <meta charset = utf-8>
      <title>HTML5 Chat</title>
		
      <body>
		
         <section id = "wrapper">
			
            <header>
               <h1>HTML5 Chat</h1>
            </header>
				
            <style>
               #chat { width: 97%; }
               .message { font-weight: bold; }
               .message:before { content: ' '; color: #bbb; font-size: 14px; }
					
               #log {
                  overflow: auto;
                  max-height: 300px;
                  list-style: none;
                  padding: 0;
               }
					
               #log li {
                  border-top: 1px solid #ccc;
                  margin: 0;
                  padding: 10px 0;
               }
					
               body {
                  font: normal 16px/20px "Helvetica Neue", Helvetica, sans-serif;
                  background: rgb(237, 237, 236);
                  margin: 0;
                  margin-top: 40px;
                  padding: 0;
               }
					
               section, header {
                  display: block;
               }
					
               #wrapper {
                  width: 600px;
                  margin: 0 auto;
                  background: #fff;
                  border-radius: 10px;
                  border-top: 1px solid #fff;
                  padding-bottom: 16px;
               }
					
               h1 {
                  padding-top: 10px;
               }
					
               h2 {
                  font-size: 100%;
                  font-style: italic;
               }
					
               header, article > * {
                  margin: 20px;
               }
					
               #status {
                  padding: 5px;
                  color: #fff;
                  background: #ccc;
               }
					
               #status.fail {
                  background: #c00;
               }
					
               #status.success {
                  background: #0c0;
               }
					
               #status.offline {
                  background: #c00;
               }
					
               #status.online {
                  background: #0c0;
               }
					
               #html5badge {
                  margin-left: -30px;
                  border: 0;
               }
					
               #html5badge img {
                  border: 0;
               }
            </style>
				
            <article>
				
               <form onsubmit = "addMessage(); return false;">
                  <input type = "text" id = "chat" placeholder = "type and press 
                  enter to chat" />
               </form>
					
               <p id = "status">Not connected</p>
               <p>Users connected: <span id = "connected">0
                  </span></p>
               <ul id = "log"></ul>
					
            </article>
				
            <script>
               connected = document.getElementById("connected");
               log = document.getElementById("log");
               chat = document.getElementById("chat");
               form = chat.form;
               state = document.getElementById("status");
					
               if (window.WebSocket === undefined) {
                  state.innerHTML = "sockets not supported";
                  state.className = "fail";
               }else {
                  if (typeof String.prototype.startsWith != "function") {
                     String.prototype.startsWith = function (str) {
                        return this.indexOf(str) == 0;
                     };
                  }
						
                  window.addEventListener("load", onLoad, false);
               }
					
               function onLoad() {
                  var wsUri = "ws://127.0.0.1:7777";
                  websocket = new WebSocket(wsUri);
                  websocket.onopen = function(evt) { onOpen(evt) };
                  websocket.onclose = function(evt) { onClose(evt) };
                  websocket.onmessage = function(evt) { onMessage(evt) };
                  websocket.onerror = function(evt) { onError(evt) };
               }
					
               function onOpen(evt) {
                  state.className = "success";
                  state.innerHTML = "Connected to server";
               }
					
               function onClose(evt) {
                  state.className = "fail";
                  state.innerHTML = "Not connected";
                  connected.innerHTML = "0";
               }
					
               function onMessage(evt) {
                  // There are two types of messages:
                  // 1. a chat participant message itself
                  // 2. a message with a number of connected chat participants
                  var message = evt.data;
						
                  if (message.startsWith("log:")) {
                     message = message.slice("log:".length);
                     log.innerHTML = '<li class = "message">' + 
                        message + "</li>" + log.innerHTML;
                  }else if (message.startsWith("connected:")) {
                     message = message.slice("connected:".length);
                     connected.innerHTML = message;
                  }
               }
					
               function onError(evt) {
                  state.className = "fail";
                  state.innerHTML = "Communication error";
               }
					
               function addMessage() {
                  var message = chat.value;
                  chat.value = "";
                  websocket.send(message);
               }
					
            </script>
				
         </section>
			
      </body>
		
   </head>	
	
</html>
```

The key features and the output of the chat application are discussed below −

To test, open the two windows with Web Socket support, type a message above and press return. This would enable the feature of chat application.

If the connection is not established, the output is available as shown below.
































































































































































Web Sockets 背景之Network角度
==============================
![](C:\Users\pxiao\Desktop\New folder\TCP-IP.gif)



在这里，我们只需要知道，HTTP、WebSocket 等协议都是处于 OSI 模型的最高层： **应用层** 。而 IP 协议工作在网络层（第3层），TCP 协议工作在传输层（第4层）。

至于 OSI 模型的各个层次都有什么系统和它们对应，这里有篇很好的文章可以满足大家的求知欲：[OSI七层模型详解](http://blog.csdn.net/yaopeng_2005/article/details/7064869) 。

**WebSocket、HTTP 与 TCP**

从上面的图中可以看出，HTTP、WebSocket 等应用层协议，都是基于 TCP 协议来传输数据的。我们可以把这些高级协议理解成对 TCP 的封装。

既然大家都使用 TCP 协议，那么大家的连接和断开，都要遵循 [TCP 协议中的三次握手和四次握手](http://blog.csdn.net/whuslei/article/details/6667471) ，只是在连接之后发送的内容不同，或者是断开的时间不同。

更详细内容可阅读：[wireshark抓包图解 TCP三次握手/四次挥手详解](http://www.seanyxie.com/wireshark%E6%8A%93%E5%8C%85%E5%9B%BE%E8%A7%A3-tcp%E4%B8%89%E6%AC%A1%E6%8F%A1%E6%89%8B%E5%9B%9B%E6%AC%A1%E6%8C%A5%E6%89%8B%E8%AF%A6%E8%A7%A3/)

对于 WebSocket 来说，它必须依赖 [HTTP 协议进行一次握手](http://tools.ietf.org/html/rfc6455#section-4) ，握手成功后，数据就直接从 TCP 通道传输，与 HTTP 无关了。

# Socket 与 WebScoket

[Socket](http://en.wikipedia.org/wiki/Network_socket) 其实并不是一个协议。它工作在 OSI 模型会话层（第5层），是为了方便大家直接使用更底层协议（一般是 [TCP](http://en.wikipedia.org/wiki/Transmission_Control_Protocol) 或 [UDP](http://en.wikipedia.org/wiki/User_Datagram_Protocol) ）而存在的一个抽象层。

最早的一套 Socket API 是 [Berkeley sockets](http://en.wikipedia.org/wiki/Berkeley_sockets) ，采用 C 语言实现。它是 Socket 的事实标准，POSIX sockets 是基于它构建的，多种编程语言都遵循这套 API，在 JAVA、Python 中都能看到这套 API 的影子。

下面摘录一段更容易理解的文字（来自 [http和socket之长连接和短连接区别](http://www.a8z8.com/html/2012/tech_1214/133.html)）：

> Socket是应用层与TCP/IP协议族通信的中间软件抽象层，它是一组接口。在设计模式中，Socket其实就是一个门面模式，它把复杂的TCP/IP协议族隐藏在Socket接口后面，对用户来说，一组简单的接口就是全部，让Socket去组织数据，以符合指定的协议。





![](C:\Users\pxiao\Desktop\New folder\socket.gif)



[WebSocket --为什么引入WebSocket协议](https://www.cnblogs.com/jiangzhaowei/p/8781629.html)

Browser已经支持http协议，为什么还要开发一种新的WebSocket协议呢？我们知道http协议是一种单向的网络协议，在建立连接后，它只允许Browser/UA（UserAgent）向WebServer发出请求资源后，WebServer才能返回相应的数据。而WebServer不能主动的推送数据给Browser/UA，当初这么设计http协议也是有原因的，假设WebServer能主动的推送数据给Browser/UA，那Browser/UA就太容易受到攻击，一些广告商也会主动的把一些广告信息在不经意间强行的传输给客户端，这不能不说是一个灾难。那么单向的http协议给现在的网站或Web应用程序开发带来了哪些问题呢？

让我们来看一个案例，现在假设我们想开发一个基于Web的应用程序去获取当前Web服务器的实时数据，例如股票的实时行情，火车票的剩余票数等等，这就需要Browser/UA与WebServer端之间反复的进行http通信，Browser不断的发送Get请求，去获取当前的实时数据。下面介绍几种常见的方式：

1. Polling

![img](https://img-blog.csdn.net/20130517151509160)

这种方式就是通过Browser/UA定时的向Web服务器发送http的Get请求，服务器收到请求后，就把最新的数据发回给客户端（Browser/UA），Browser/UA得到数据后，就将其显示出来，然后再定期的重复这一过程。虽然这样可以满足需求，但是也仍然存在一些问题，例如在某段时间内Web服务器端没有更新的数据，但是Browser/UA仍然需要定时的发送Get请求过来询问，那么Web服务器就把以前的老数据再传送过来，Browser/UA把这些没有变化的数据再显示出来，这样显然既浪费了网络带宽，又浪费了CPU的利用率。如果说把Browser发送Get请求的周期调大一些，就可以缓解这一问题，但是如果在Web服务器端的数据更新很快时，这样又不能保证Web应用程序获取数据的实时性。

2. Long Polling

![img](https://img-blog.csdn.net/20130517151612871)

上面介绍了Polling遇到的问题，现在介绍一下LongPolling，它是对Polling的一种改进。

Browser/UA发送Get请求到Web服务器，这时Web服务器可以做两件事情，第一，如果服务器端有新的数据需要传送，就立即把数据发回给Browser/UA，Browser/UA收到数据后，立即再发送Get请求给Web Server；第二，如果服务器端没有新的数据需要发送，这里与Polling方法不同的是，服务器不是立即发送回应给Browser/UA，而是把这个请求保持住，等待有新的数据到来时，再来响应这个请求；当然了，如果服务器的数据长期没有更新，一段时间后，这个Get请求就会超时，Browser/UA收到超时消息后，再立即发送一个新的Get请求给服务器。然后依次循环这个过程。

这种方式虽然在某种程度上减小了网络带宽和CPU利用率等问题，但是仍然存在缺陷，例如假设服务器端的数据更新速率较快，服务器在传送一个数据包给Browser后必须等待Browser的下一个Get请求到来，才能传递第二个更新的数据包给Browser，那么这样的话，Browser显示实时数据最快的时间为2×RTT（往返时间），另外在网络拥塞的情况下，这个应该是不能让用户接受的。另外，由于http数据包的头部数据量往往很大（通常有400多个字节），但是真正被服务器需要的数据却很少（有时只有10个字节左右），这样的数据包在网络上周期性的传输，难免对网络带宽是一种浪费。

通过上面的分析可知，要是在Browser能有一种新的网络协议，能支持客户端和服务器端的双向通信，而且协议的头部又不那么庞大就好了。WebSocket就是肩负这样一个使命登上舞台的。

 

　　WebSocket协议是一种双向通信协议，它建立在TCP之上，同http一样通过TCP来传输数据，但是它和http最大的不同有两点：1.WebSocket是一种双向通信协议，在建立连接后，WebSocket服务器和Browser/UA都能主动的向对方发送或接收数据，就像Socket一样，不同的是WebSocket是一种建立在Web基础上的一种简单模拟Socket的协议；2.WebSocket需要通过握手连接，类似于TCP它也需要客户端和服务器端进行握手连接，连接成功后才能相互通信。

下面是一个简单的建立握手的时序图：

![img](http://hi.csdn.net/attachment/201202/27/0_133032795020RR.gif)

 

这里简单说明一下WebSocket握手的过程。

当Web应用程序调用new WebSocket(url)接口时，Browser就开始了与地址为url的WebServer建立握手连接的过程。

1. Browser与WebSocket服务器通过TCP三次握手建立连接，如果这个建立连接失败，那么后面的过程就不会执行，Web应用程序将收到错误消息通知。

2. 在TCP建立连接成功后，Browser/UA通过http协议传送WebSocket支持的版本号，协议的字版本号，原始地址，主机地址等等一些列字段给服务器端。

例如：

 

[html]

view plain

 [copy](https://blog.csdn.net/yl02520/article/details/7298461#)

 

1. GET /chat HTTP/1.1  
2. Host: server.example.com  
3. Upgrade: websocket  
4. Connection: Upgrade  
5. Sec-WebSocket-Key:dGhlIHNhbXBsZSBub25jZQ==  
6. Origin: http://example.com  
7. Sec-WebSocket-Protocol: chat,superchat  
8. Sec-WebSocket-Version: 13  

 

 

3. WebSocket服务器收到Browser/UA发送来的握手请求后，如果数据包数据和格式正确，客户端和服务器端的协议版本号匹配等等，就接受本次握手连接，并给出相应的数据回复，同样回复的数据包也是采用http协议传输。

 

[html]

view plain

 [copy](https://blog.csdn.net/yl02520/article/details/7298461#)

 

1. HTTP/1.1 101 Switching Protocols  
2. Upgrade: websocket  
3. Connection: Upgrade  
4. Sec-WebSocket-Accept:s3pPLMBiTxaQ9kYGzzhZRbK+xOo=  
5. Sec-WebSocket-Protocol: chat  



4. Browser收到服务器回复的数据包后，如果数据包内容、格式都没有问题的话，就表示本次连接成功，触发onopen消息，此时Web开发者就可以在此时通过send接口想服务器发送数据。否则，握手连接失败，Web应用程序会收到onerror消息，并且能知道连接失败的原因。

 

　　WebSocket与http协议一样都是基于TCP的，所以他们都是可靠的协议，Web开发者调用的WebSocket的send函数在browser的实现中最终都是通过TCP的系统接口进行传输的。WebSocket和Http协议一样都属于应用层的协议，那么他们之间有没有什么关系呢？答案是肯定的，WebSocket在建立握手连接时，数据是通过http协议传输的，正如我们上一节所看到的“GET/chat HTTP/1.1”，这里面用到的只是http协议一些简单的字段。但是在建立连接之后，真正的数据传输阶段是不需要http协议参与的。

具体关系可以参考下图：

![img](http://hi.csdn.net/attachment/201202/27/0_1330330261W6TS.gif)

 

# TCP、UDP、HTTP、SOCKET、WebSocket之间的区别

TCP/IP协议栈主要分为四层:应用层、传输层、网络层、数据链路层, 
每层都有相应的协议，如下图

![img](https://images.cnblogs.com/cnblogs_com/goodcandle/socket2.jpg)

## IP：

网络层协议；（高速公路）

## TCP和UDP：

传输层协议；（卡车）

## HTTP：

应用层协议；（货物）。HTTP(超文本传输协议)是利用TCP在两台电脑(通常是Web服务器和客户端)之间传输信息的协议。客户端使用Web浏览器发起HTTP请求给Web服务器，Web服务器发送被请求的信息给客户端。

## SOCKET：

套接字，TCP/IP网络的API。(港口码头/车站)Socket是应用层与TCP/IP协议族通信的中间软件抽象层，它是一组接口。socket是在应用层和传输层之间的一个抽象层，它把TCP/IP层复杂的操作抽象为几个简单的接口供应用层调用已实现进程在网络中通信。

## TCP/IP：

代表传输控制协议/网际协议，指的是一系列协议，TCP/IP 模型在 OSI 模型的基础上进行了简化，变成了四层，从下到上分别为：网络接口层、网络层、传输层、应用层。与 OSI 体系结构对比如下： 
![img](https://img-blog.csdn.net/20170604181256498?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTI0MDg3Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## TCP/UDP区别：

## TCP

（传输控制协议，Transmission Control Protocol）：(类似打电话) 
面向连接、传输可靠（保证数据正确性）、有序（保证数据顺序）、传输大量数据（流模式）、速度慢、对系统资源的要求多，程序结构较复杂， 
每一条TCP连接只能是点到点的， 
TCP首部开销20字节。

## UDP

(用户数据报协议，User Data Protocol)：（类似发短信） 
面向非连接 、传输不可靠（可能丢包）、无序、传输少量数据（数据报模式）、速度快，对系统资源的要求少，程序结构较简单 ， 
UDP支持一对一，一对多，多对一和多对多的交互通信， 
UDP的首部开销小，只有8个字节。

## tcp三次握手建立连接：

![img](http://cricode.qiniudn.com/tcp-ip-handshark.png)

第一次握手：客户端发送syn包(seq=x)到服务器，并进入SYN_SEND状态，等待服务器确认； 
第二次握手：服务器收到syn包，必须确认客户的SYN（ack=x+1），同时自己也发送一个SYN包（seq=y），即SYN+ACK包，此时服务器进入SYN_RECV状态； 
第三次握手：客户端收到服务器的SYN＋ACK包，向服务器发送确认包ACK(ack=y+1)，此包发送完毕，客户端和服务器进入ESTABLISHED状态，完成三次握手。 
握手过程中传送的包里不包含数据，三次握手完毕后，客户端与服务器才正式开始传送数据。理想状态下，TCP连接一旦建立，在通信双方中的任何一方主动关闭连接之前，TCP 连接都将被一直保持下去。

主机A向主机B发出连接请求数据包：“我想给你发数据，可以吗？”，这是第一次对话； 
主机B向主机A发送同意连接和要求同步（同步就是两台主机一个在发送，一个在接收，协调工作）的数据包：“可以，你什么时候发？”，这是第二次对话； 
主机A再发出一个数据包确认主机B的要求同步：“我现在就发，你接着吧！”，这是第三次对话。 
三次“对话”的目的是使数据包的发送和接收同步，经过三次“对话”之后，主机A才向主机B正式发送数据。

## Websocket

Websocket协议解决了服务器与客户端全双工通信的问题。

注:什么是单工、半双工、全工通信？ 
信息只能单向传送为单工； 
信息能双向传送但不能同时双向传送称为半双工； 
信息能够同时双向传送则称为全双工。

websocket协议解析 
wensocket协议包含两部分:一部分是“握手”，一部分是“数据传输”。

## WebSocket和Socket区别

可以把WebSocket想象成HTTP(应用层)，HTTP和Socket什么关系，WebSocket和Socket就是什么关系。

HTTP 协议有一个缺陷：通信只能由客户端发起，做不到服务器主动向客户端推送信息。

WebSocket 协议在2008年诞生，2011年成为国际标准。所有浏览器都已经支持了。 
它的最大特点就是，服务器可以主动向客户端推送信息，客户端也可以主动向服务器发送信息，是真正的双向平等对话，属于服务器推送技术的一种。

## 参考：

[揭开Socket编程的面纱](http://goodcandle.cnblogs.com/archive/2005/12/10/294652.aspx)

[图解TCP-IP协议](http://blog.csdn.net/wind19/article/details/39393843)

[Android 进阶12：进程通信之 Socket （顺便回顾 TCP UDP）](http://blog.csdn.net/u011240877/article/details/72860483)

[WebSocket与TCP/IP](https://zhuanlan.zhihu.com/p/27021102?refer=dreawer)

[websocket 历史及使用详解](https://juejin.im/post/58de433c61ff4b00615e6d90)

[WebSocket 教程](http://www.ruanyifeng.com/blog/2017/05/websocket.html)

[web communication technologies](https://medium.com/platform-engineer/web-api-design-35df8167460)
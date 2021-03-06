# websocket-sharp #

**websocket-sharp** is a C# implementation of WebSocket protocol client & server.

## Usage ##

### WebSocket Client ###

#### Step 1 ####

Required namespaces.

```cs
using WebSocketSharp;
using WebSocketSharp.Frame;
```

`WebSocket` class exists in `WebSocketSharp` namespace, WebSocket data frame resources (e.g. `WsFrame` class) exist in `WebSocketSharp.Frame` namespace.

#### Step 2 ####

Creating a instance of `WebSocket` class.

```cs
using (WebSocket ws = new WebSocket("ws://example.com"))
{
  ...
}
```

`WebSocket` class inherits `IDisposable` interface, so you can use `using` statement.

#### Step 3 ####

Setting `WebSocket` event handlers.

##### WebSocket.OnOpen event #####

`WebSocket.OnOpen` event is emitted immediately after WebSocket connection has been established.

```cs
ws.OnOpen += (sender, e) =>
{
  ...
};
```

`e` has come across as `EventArgs.Empty`, so there is no operation on `e`.

##### WebSocket.OnMessage event #####

`WebSocket.OnMessage` event is emitted each time WebSocket data frame is received.

```cs
ws.OnMessage += (sender, e) =>
{
  ...
};
```

**Frame type** of received WebSocket data frame is stored in `e.Type` (`WebSocketSharp.MessageEventArgs.Type`, its type is `WebSocketSharp.Frame.Opcode`), so you check it out and you determine which item you should operate.

```cs
switch (e.Type)
{
  case Opcode.TEXT:
    ...
    break;
  case Opcode.BINARY:
    ...
    break;
  default:
    break;
}
```

If `e.Type` is `Opcode.TEXT`, you operate `e.Data` (`WebSocketSharp.MessageEventArgs.Data`, its type is `string`).

If `e.Type` is `Opcode.BINARY`, you operate `e.RawData` (`WebSocketSharp.MessageEventArgs.RawData`, its type is `byte[]`).

##### WebSocket.OnError event #####

`WebSocket.OnError` event is emitted when some error is occurred.

```cs
ws.OnError += (sender, e) =>
{
  ...
};
```
Error message is stored in `e.Message` (`WebSocketSharp.ErrorEventArgs.Message`, its type is `string`), so you operate it.

##### WebSocket.OnClose event #####

`WebSocket.OnClose` event is emitted when WebSocket connection is closed.

```cs
ws.OnClose += (sender, e) =>
{
  ...
};
```

Close status code is stored in `e.Code` (`WebSocketSharp.CloseEventArgs.Code`, its type is `WebSocketSharp.Frame.CloseStatusCode`) and reason of close is stored in `e.Reason` (`WebSocketSharp.CloseEventArgs.Reason`, its type is `string`), so you operate them.

#### Step 4 ####

Connecting to server using WebSocket.

```cs
ws.Connect();
```

#### Step 5 ####

Sending data.

```cs
ws.Send(data);
```

`WebSocket.Send` method is overloaded.

`data` types are `string`, `byte[]` and `FileInfo` class.

#### Step 6 ####

Closing WebSocket connection.

```cs
ws.Close(code, reason);
```

If you want to close WebSocket connection explicitly, you can use `Close` method.

Type of `code` is `WebSocketSharp.Frame.CloseStatusCode`, type of `reason` is `string`.

`WebSocket.Close` method is overloaded (In addition `Close()` and `Close(code)` exist).

### WebSocket Server ###

#### Step 1 ####

Required namespace.

```cs
using WebSocketSharp.Server;
```

`WebSocketServer<T>` class and `WebSocketService` class exist in `WebSocketSharp.Server` namespace.

#### Step 2 ####

Creating a class that inherits `WebSocketService` class.

For example, if you want to provide the echo service,

```cs
using System;
using WebSocketSharp;
using WebSocketSharp.Server;

public class Echo : WebSocketService
{
  protected override void onMessage(object sender, MessageEventArgs e)
  {
    Send(e.Data);
  }
}
```

For example, if you want to provide the chat service,

```cs
using System;
using WebSocketSharp;
using WebSocketSharp.Server;

public class Chat : WebSocketService
{
  protected override void onMessage(object sender, MessageEventArgs e)
  {
    Publish(e.Data);
  }
}
```

If you override `onMessage` method, it is bound to server side `WebSocket.OnMessage` event.

In addition, if you override `onOpen`, `onError` and `onClose` methods, each of them is bound to `WebSocket.OnOpen`, `WebSocket.OnError` and `WebSocket.OnClose` events.

#### Step 3 ####

Creating a instance of `WebSocketServer<T>` class.

```cs
var wssv = new WebSocketServer<Echo>("ws://example.com:4649");
```

Type of `T` inherits `WebSocketService` class, so you can use a class that was created in **Step 2**.

If you set WebSocket url without port number, `WebSocketServer<T>` set **80** or **443** to port number automatically.
So it is necessary to run with root permission.

    $ sudo mono example2.exe

#### Step 4 ####

Setting WebSocketServer event handler.

##### WebSocketServer<T>.OnError event #####

`WebSocketServer<T>.OnError` event is emitted when some error is occurred.

```cs
wssv.OnError += (sender, e) =>
{
  ...
};
```

Error message is stored in `e.Message` (`WebSocketSharp.ErrorEventArgs.Message`, its type is `string`), so you operate it.

#### Step 5 ####

Starting server.

```cs
wssv.Start();
```

#### Step 6 ####

Stopping server.

```cs
wssv.Stop();
```

## Examples ##

Examples of using **websocket-sharp**.

### Example ###

[Example] connects to the [Echo server] using the WebSocket.

### Example1 ###

[Example1] connects to the [Audio Data delivery server] using the WebSocket ([Example1] is only implemented a chat feature, still unfinished).

[Example1] uses [Json.NET].

### Example2 ###

[Example2] starts WebSocket server.

## Supported WebSocket Protocol ##

**websocket-sharp** supports **[RFC 6455]**.

- @**[branch: hybi-00]** supports older draft-ietf-hybi-thewebsocketprotocol-00 (**[hybi-00]**).
- @**[branch: draft75]** supports even more old draft-hixie-thewebsocketprotocol-75 (**[hixie-75]**).

## Reference ##

- **[The WebSocket Protocol]**
- **[The WebSocket API]**

Thx for translating to japanese.

- **[The WebSocket Protocol 日本語訳]**
- **[The WebSocket API 日本語訳]**

## License ##

Copyright &copy; 2010 - 2012 sta.blockhead

Licensed under the **[MIT License]**.


[Audio Data delivery server]: http://agektmr.node-ninja.com:3000/
[branch: draft75]: https://github.com/sta/websocket-sharp/tree/draft75
[branch: hybi-00]: https://github.com/sta/websocket-sharp/tree/hybi-00
[Echo server]: http://www.websocket.org/echo.html
[Example]: https://github.com/sta/websocket-sharp/tree/master/Example
[Example1]: https://github.com/sta/websocket-sharp/tree/master/Example1
[Example2]: https://github.com/sta/websocket-sharp/tree/master/Example2
[hixie-75]: http://tools.ietf.org/html/draft-hixie-thewebsocketprotocol-75
[hybi-00]: http://tools.ietf.org/html/draft-ietf-hybi-thewebsocketprotocol-00
[Json.NET]: http://james.newtonking.com/projects/json-net.aspx
[MIT License]: http://www.opensource.org/licenses/mit-license.php
[RFC 6455]: http://tools.ietf.org/html/rfc6455
[The WebSocket API]: http://dev.w3.org/html5/websockets
[The WebSocket API 日本語訳]: http://www.hcn.zaq.ne.jp/___/WEB/WebSocket-ja.html
[The WebSocket Protocol]: http://tools.ietf.org/html/rfc6455
[The WebSocket Protocol 日本語訳]: http://www.hcn.zaq.ne.jp/___/WEB/RFC6455-ja.html

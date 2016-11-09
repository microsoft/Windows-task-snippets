<!---
  category: NetworkingAndWebServices 
  language: cs
-->

# Sending and receiving a message over TCP

These methods provide simple string-based communication over a TCP socket connection:

* [Sending a message](#sending-a-message)  
* [Receiving a message](#receiving-a-message)   

## Sending a message

The remoteHost and remotePort parameter values must match the device that is
listening for incoming connections and will be receiving the message.

```C#
using System;
using System.IO;
using System.Threading.Tasks;
using Windows.Networking;
using Windows.Networking.Sockets;

public static async Task SendStringTcpAsync(HostName remoteHost, 
    string remotePort, string message)
{
    using (var socket = new StreamSocket())
    {
        await socket.ConnectAsync(remoteHost, remotePort);
        using (var writer = new StreamWriter(
            socket.OutputStream.AsStreamForWrite()))
        {
            await writer.WriteLineAsync(message);
            await writer.FlushAsync();
        }
    }
}
```

### Usage

```C#
try
{
    await SendStringTcpAsync(new HostName("192.168.0.5"), 
        "56789", "Hello, world!");
}
catch (Exception)
{
    // Handle relevant errors. 
}
```

## Receiving a message

The specified callback is called every time a message is received.

The return value is a cleanup action that you should call when you are finished receiving messages. 

```C#
using System;
using System.IO;
using System.Threading.Tasks;
using Windows.Foundation;
using Windows.Networking.Sockets;

public static async Task<Action> ReceiveStringTcpAsync(string port,
    Action<string, StreamSocketInformation> callback)
{
    var socket = new StreamSocketListener();
    TypedEventHandler<StreamSocketListener, 
        StreamSocketListenerConnectionReceivedEventArgs> handler = 
        async (sender, args) => 
        {
            using (var reader = new StreamReader(
                args.Socket.InputStream.AsStreamForRead()))
            {
                string message = await reader.ReadLineAsync();
                callback(message, args.Socket.Information);
            }
        };

    socket.ConnectionReceived += handler;
    await socket.BindServiceNameAsync(port);

    Action cleanup = () =>
    {
        socket.ConnectionReceived -= handler;
        socket.Dispose();
    };
    return cleanup;
}
```

### Usage

You can bind to any port that is not already being
used by the device. If the port is already in use, the ReceiveStringTcpAsync
method will throw an exception. 

```C#
Action cleanup = null;
try
{
    cleanup = await ReceiveStringTcpAsync("56789", (message, information) =>
    {
        System.Diagnostics.Debug.WriteLine(
            $"\"{message}\" was received from {information.RemoteAddress}.");
    });
}
catch (Exception)
{
    // Handle relevant errors.
}

// Clean up resources at the appropriate place in your code.  
if (cleanup != null) cleanup();
```

## See also

[StreamSocket class](https://msdn.microsoft.com/library/windows/apps/windows.networking.sockets.streamsocket.aspx)  
[StreamSocketListener class](https://msdn.microsoft.com/library/windows/apps/windows.networking.sockets.streamsocketlistener.aspx)  
[Exceptions in Windows.Networking.Sockets](https://msdn.microsoft.com/library/windows/apps/mt280234.aspx#Exceptions_in_Windows.Networking.Sockets)

[Lambda expressions](https://msdn.microsoft.com/library/bb397687.aspx) (anonymous methods using the "=>" syntax)  
[Interpolated strings](https://msdn.microsoft.com/library/dn961160.aspx) (strings with a $ prefix)  

For code that handles more complex TCP socket communication scenarios, see the 
[P2PHelper](https://github.com/Microsoft/Windows-appsample-quizgame/tree/master/P2PHelper) project in the 
[QuizGame sample](https://github.com/Microsoft/Windows-appsample-quizgame).  
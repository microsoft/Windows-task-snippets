<!---
  category: NetworkingAndWebServices 
  language: cs
-->

# Sending and receiving a message over UDP

These methods provide simple string-based communication over a UDP socket connection:

* [Sending a message](#sending-a-message)  
* [Receiving a message](#receiving-a-message)   

## Sending a message

The remoteHost and remotePort parameter values must match the device that is listening
for incoming connections and will be receiving the message.

```C#
using System;
using System.IO;
using System.Threading.Tasks;
using Windows.Networking;
using Windows.Networking.Sockets;

public static async Task SendStringUdpAsync(HostName remoteHost, 
    string remotePort, string message)
{
    using (var socket = new DatagramSocket())
    {
        var stream = (await socket.GetOutputStreamAsync(
            remoteHost, remotePort)).AsStreamForWrite();
        using (var writer = new StreamWriter(stream))
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
    await SendStringUdpAsync(new HostName("192.168.0.5"), 
        "56789", "Hello, world!");
}
catch (Exception)
{
    // Handle appropriate errors.
}
```

## Receiving a message

The specified callback is called everytime a message is received.

The return value is a cleanup action that you should call when you are finished receiving messages. 

```C#
using System;
using System.IO;
using System.Threading.Tasks;
using Windows.Foundation;
using Windows.Networking.Sockets;

public static async Task<Action> ReceiveStringUdpAsync(string port,
    Action<string, DatagramSocketMessageReceivedEventArgs> callback)
{
    var socket = new DatagramSocket();
    TypedEventHandler<DatagramSocket, 
        DatagramSocketMessageReceivedEventArgs> handler =
        async (sender, args) =>
        {
            using (var reader = new StreamReader(
                args.GetDataStream().AsStreamForRead()))
            {
                string message = await reader.ReadLineAsync();
                callback(message, args);
            }
        };

    socket.MessageReceived += handler;
    await socket.BindServiceNameAsync(port);

    Action cleanup = () =>
    {
        socket.MessageReceived -= handler;
        socket.Dispose();
    };
    return cleanup;
}
```

### Usage

You can bind to any port that is not already being used by the device. 
If the port is already in use, the ReceiveStringUdpAsync method will throw an exception.

```C#
Action cleanup = null;
try
{
    cleanup = await ReceiveStringUdpAsync("56789", (message, args) =>
    {
        System.Diagnostics.Debug.WriteLine(
            $"\"{message}\" was received from {args.RemoteAddress}.");
    });
}
catch (Exception)
{
    // Handle appropriate errors.
}

// Clean up resources at the appropriate place in your code.  
if (cleanup != null) cleanup();
```

## See also

[DatagramSocket class](https://msdn.microsoft.com/library/windows/apps/windows.networking.sockets.datagramsocket.aspx)  
[Exceptions in Windows.Networking.Sockets](https://msdn.microsoft.com/library/windows/apps/mt280234.aspx#Exceptions_in_Windows.Networking.Sockets)  

[Lambda expressions](https://msdn.microsoft.com/library/bb397687.aspx) (anonymous methods using the "=>" syntax)  
[Interpolated strings](https://msdn.microsoft.com/library/dn961160.aspx) (strings with a $ prefix)  

For code that handles more complex UDP socket communication scenarios, see the 
[P2PHelper](https://github.com/Microsoft/Windows-appsample-quizgame/tree/master/P2PHelper) project in the 
[QuizGame sample](https://github.com/Microsoft/Windows-appsample-quizgame).  

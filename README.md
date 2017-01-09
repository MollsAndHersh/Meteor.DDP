# Meteor.DDP

A simple implementation of Meteor DDP integration with .NET/C#.

Runtime needs a platform that provides `System.Net.WebSockets`, in practice, Windows 8 and above.  The only external dependencies are Newton.Json a.k.a. [Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/) for quick dynamic-json serialization and  Nito.AsyncEx a.k.a [Async and Task Helpers ](https://www.nuget.org/packages/Nito.AsyncEx/).

The code uses dynamics and C# events. Very simple usage:

### NuGet package

https://www.nuget.org/packages/Meteor.DDP/

### Connection
```
using(DdpClient client = new DdpClient("localhost:3000"))
{
   client.ConnectAsync().Wait();
   // Subscribe or publish then...
}
```

### Subscriptions

#### On Meteor side:

```
Meteor.publish('MeteorPublished', function(){
    return SomeCollection.find({});
});
```
#### On .NET Side:
```
void Subscribe()
{
  client.Message += OnMessage;
  client.Subscribe(Guid.NewGuid().ToString("N"), "MeteorPublished");
}

void OnMessage(object sender, DdpMessageEventArgs e)
{
    Console.WriteLine("Message from Meteor! Item {0}::{1}:  {2}\n{3}", e.Collection, e.Id, e.Method, e.Data);
}
```

### Publications

#### On Meteor side:

```
Meteor.methods({
    MeteorMethod: function (message){
      console.log("I'm called from .NET! The message: " + message.data);
    }
  });
```

#### On .NET side:

```
client.Publish(callId, "MeteorMethod", new
{
    hello = "hello",
    data = "Hello, Meteor!!!"
});
```

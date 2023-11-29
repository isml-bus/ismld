# ismld
A simple, secure, brokerless, polygot event bus that can be consumed on the edge written in C# inspired by NSQ.io  

To run:  
` $> ismld daemon up --bind=192.168.0.1:31685`  

To add client users:  
```
$> ismld clients add foo 
ismld: New client with login='foo' created. Keep generated client secret:

6PtO6ppUlpO5feSAgGwydf14iqaYNM1qZ0lpnNX65uA5S2FganISH3NJqckSDlHHKnDpj1Q9hR71CnhRtwcKNC2HFhMO6vlsyE8aK5mTjVWooBmXelt36IEP8vBWTRlutqipWSvgdGLzh6CaNWEpApW1ujuuF1Bei6qwmGriWgXLfDyL1XndNLYmKT3ORUtgdKmFCKtzYIGaZmg3nfEPI1kjj59L1GZhH69FbTk5t141sYDvazrhfw0CFYiYlD01tOwxNVASqhL5hqKj04NWh4OjSBgn6k0KEXTbN5zLxE01G7E6vG851wynbsinaoaeZF2tzy55iOirDYpcCZPwjqVoTtcZNdQDaMdRPTaxxoE4FCmXR6IRoHMBhuXUnaM2A0jDHXX8tkvgkHGziW11RUB5AA7WQMlp1yVlydS2voSTWKFb0TfccqGw5XAkRdt5SNwPzk38l21bldUcdWgRGLxI6dAUN0hw1zeGbxVbtc6d8KkNtSiNcfJOAmJ7IHa1
```  

Client usage example (C#):
```
var connection = new IsmlConnection(
    options: new IsmlConnectionOptions {
        Host = "127.0.0.1:31685",
        ClientCredentials = new {
            Login = "foo",
            Secret = "some-super-secret-key",
            ClientId = "an-optional-but-unique-client-id"
        },
        NamePrefix = "[DEV]", // prepend all topic, channel names with it. 
        HeartbeatInterval = TimeSpan.FromSeconds(30),
        BackoffDuration = TimeSpan.FromSeconds(5)
        MaxAttempts = 3
    }, 
    cancellationToken: cancellationToken
);

// publish a message
var messagePayload = new OnPingSentMessage { Message = "Hello World" };
var metadata = connection.Publish(topic: "OnPingSent", payload: messagePayload);
Console.WriteLine($"msgId={metadata.MsgId}");

// subscribe to a channel
var subscriber = connection.Subscribe(
	options: {
    	Topic: "OnPingSent", 
        Channel: "OnPingReceivedHandler",
        HandlerTimeout = TimeSpan.FromMinutes(1),
        MaxInFlight = 3 // process 3 unique messages concurrently
    }, 
    handler: async (message) => {
      var payload = await message.Payload.CastTo<OnPingSentMessage>();
      var progress = message.Checkpoint.CastTo<HandlerProgressData>() ?? new HandlerProgressData
      {
          Counter = 0;
      };

      while (progress.Counter < 3)
      {
          // terminate work if cancellation is requested
          if (message.CancellationToken.IsCancellationRequested)
          {
              // save progress state for use when the message
              // is redispatched for re-execution later
              // to resume where it left off
              message.Shelve(state: progress);
              return;
          }

          // simulate some work
          await Task.Delay(TimeSpan.FromSeconds(5));

          // increment counter
          progress.Counter += 1;

          // let the bus know that we're still working on it
          // otherwise message will timeout and the bus will 
          // send it again for a retry
          await message.Acknowledge(state: progress);
      }

      Console.WriteLine(payload.Message);

      // let the bus know that we're done with the message
      // to get the next item from the queue
      await message.Finish(state: progress);
});

Console.WriteLine($"subsId={subscriber.Id}");
Console.WriteLine($"Full Channel Name: {subscriber.FullChannelName}"); // [DEV]OnPingReceivedHandler@OnPingSent

// start listening for incoming messages
subscriber.Start();
```

# Objectives:
* Become the simplest event bus implementation for Dotnet (C#) and Javascript
* Run as a sidecar to the main producer/subscriber apps. 
* Implement in ASPNETCore, C#
* Brokerless
* support for both publish-subscribe, request-repsponse
* Produce via JSON or GRPC over HTTP
* Subscribe via Websockets
* Publish to topics, subscribe to channels

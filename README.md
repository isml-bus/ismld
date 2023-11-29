# ismld
A simple, secure, brokerless, polygot event bus that can be consumed on the edge written in C# inspired by NSQ.io  

To run:  
` $> ismld up --bind=192.168.0.1:31685`  

To add client users:  
` $> ismld clients add foo `
`ismld: New client with login='foo' created. Keep generated client secret:`
`6PtO6ppUlpO5feSAgGwydf14iqaYNM1qZ0lpnNX65uA5S2FganISH3NJqckSDlHHKnDpj1Q9hR71CnhRtwcKNC2HFhMO6vlsyE8aK5mTjVWooBmXelt36IEP8vBWTRlutqipWSvgdGLzh6CaNWEpApW1ujuuF1Bei6qwmGriWgXLfDyL1XndNLYmKT3ORUtgdKmFCKtzYIGaZmg3nfEPI1kjj59L1GZhH69FbTk5t141sYDvazrhfw0CFYiYlD01tOwxNVASqhL5hqKj04NWh4OjSBgn6k0KEXTbN5zLxE01G7E6vG851wynbsinaoaeZF2tzy55iOirDYpcCZPwjqVoTtcZNdQDaMdRPTaxxoE4FCmXR6IRoHMBhuXUnaM2A0jDHXX8tkvgkHGziW11RUB5AA7WQMlp1yVlydS2voSTWKFb0TfccqGw5XAkRdt5SNwPzk38l21bldUcdWgRGLxI6dAUN0hw1zeGbxVbtc6d8KkNtSiNcfJOAmJ7IHa1`

# Objectives:
* Become the simplest event bus implementation for Dotnet (C#) and Javascript
* Run as a sidecar to the main producer/subscriber apps. 
* Implement in ASPNETCore, C#
* Brokerless
* support for both publish-subscribe, request-repsponse
* Produce via JSON or GRPC over HTTP
* Subscribe via Websockets
* Publish to topics, subscribe to channels

# ismld
A simple, brokerless, polygot event bus that can be consumed on the edge written in C# inspired by NSQ.io  

To run:  
` $> ismld --bind=192.168.0.1:31685`

# Objectives:
* Become the simplest event bus implementation for Dotnet (C#) and Javascript
* Run as a sidecar to the main producer/subscriber apps. 
* Implement in ASPNETCore, C#
* Brokerless
* support for both publish-subscribe, request-repsponse
* Produce via JSON or GRPC over HTTP
* Subscribe via Websockets
* Publish to topics, subscribe to channels

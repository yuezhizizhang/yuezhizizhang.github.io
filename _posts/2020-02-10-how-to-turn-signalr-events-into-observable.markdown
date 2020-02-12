---
layout: post
title:  "How to turn SignalR events into Observable"
categories: "asp.net signalr websocket"
---

[ASP.NET Core SignalR](https://github.com/dotnet/aspnetcore/tree/master/src/SignalR) is a library for ASP.NET Core developers that makes it simple to add real-time web functionality to your applications. 

SignalR uses WebSocket where available but falls back to older transport like long polling where necessary.

I used to work with WebSocket for a long time, so the interface of SignalR client looks familiar to me. It resembles the WebSocket interface very much.

Our project uses @aspnet/signalr 1.1.0. The latest version of TypeScript SignalR package is renamed to @microsoft/signalr.

The steps to start a Hub Connection and listen to a method events stream are: 

```typescript
// Establishes a Hub Connection with specified url.
const conn: HubConnection = new HubConnectionBuilder()
    .withUrl(url)
    .build();

// On reciving an event when the hub method with the specified method name is invoked.
conn.on("methodName", (...args: any[]) => {
});

// When the connection is closed.
conn.onclose((err?: Error) => {
});

// Starts the connection.
conn.start();
```

The benefits of turning the message events stream into an RxJS Subject are:

1. Subject can be subscribed to easily.
2. Subject can multicast to multiple observers simultaneously.

Thus, multiple UI components are able to subscribe to a single Hub Connection method events stream simultaneously. 

```typescript
signalRObservable(url: string, method: string): Observable<any> {
    // Establishes a Hub Connection with specified url.
    const conn: HubConnection = new HubConnectionBuilder()
        .withUrl(url)
        .build();

    const subject: Subject<any> = new Subject<any>();

    // On reciving an event when the hub method with the specified method name is invoked
    conn.on(method, (...args: any[]) => {
        // Multicast the event.
        subject.next(args);
    });

    // When the connection is closed.
    conn.onclose((err?: Error) => {
        if (err) {
            // An error occurs
            subject.error(err); 
        } else {
            // No more events to be sent.
            subject.complete();
        }
    });

    // Starts the connection.
    conn.start();

    // To be subscribed to by multiple components
    return subject;
}
```
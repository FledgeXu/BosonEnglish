# How minecraft works

This section is very important in that you must build up a model image of Minecraft running in your own brain, which will help you understand the concepts covered later.

In this section, I'll go over how Minecraft works in general, and a very important concept: the "sides".

Minecraft generally belongs to "C/S (Client/Server) Architecture". So what is "server" and what is "client"?

The name actually gives a general idea of what it means, the "server" is used to provide the service, and the "client" is used directly by the user. So how are these two sides represented in Minecraft?

In Minecraft the division of duties between the two sides is as follows.

- server side

  Responsible for the game's logic, reading and writing of data.

- client side

  It accepts input and output from the user and renders the game screen based on the data from the server.

It's worth noting that the distinction between client and server here is only logical. In fact if you are in single player mode, there will be both a server and a client on your computer, and they are in different threads[^1]. But when you connect to a server, only the client exists on your computer and the server is moved to a remote server.

The following diagram roughly explains how Minecraft works.

![How_minecraft_work](how-minecraft-works.assets/How_minecraft_work.svg)

When you see this picture, you may wonder why the client has a data model when the server is responsible for the game logic. In fact, the "client-side data model" here is just a copy of the "server-side data model", although they have separate game ticks and share a lot of the same code, but the final logic is still the server-side prevails.

As we mentioned before, the client and server are independent, but they inevitably need to synchronize data, and in Minecraft, all client and server data synchronization is done through network packets. In most of the cases, the original version has already implemented a method to synchronize the data, we just need to call the method that has already been implemented, but in some cases, the original version does not implement the corresponding function, or it is not suitable to use the function provided by the original version, we have to create and send network packets to complete the data synchronization.

So the next question is, how do we distinguish in our code whether we are on the client side or the server side?

Minecraft's `World` has an `isRemote` field, which is `true` when on the client and `false` when on the server.

---

[^1]: Thread is one of the units of program scheduling, being in different threads means that the logic and data of these two are independent of each other and can only be synchronized by specific methods. Specifically, the server is in the "Server thread" and the client is in the "Render thread", if you've ever watched the output log when Minecraft starts, you should see these two words.


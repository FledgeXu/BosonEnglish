# How minecraft works

This section is very important in that you must build up a model image of Minecraft running in your own brain, which will help you understand the concepts covered later.

In this section, I'll go over how Minecraft works in general, and a very important concept: the "sides".

Minecraft generally belongs to "C/S (Client/Server) Architecture". So what is "server" and what is "client"?

The name actually gives a general idea of what it means, the "server" is used to provide the service, and the "client" is used directly by the user. So how are these two sides represented in Minecraft?

Actually, there are four sides in Minecraft.

> When we say “client” or “server”, it usually follows with a fairly intuitive understanding of what part of the game we’re talking about. After all, a client is what the user interacts with, and a server is where the user connects for a multiplayer game. Easy, right?
>
> As it turns out, there can be some ambiguity even with two such terms. Here we disambiguate the four possible meanings of “client” and “server”:
>
> - Physical client - The *physical client* is the entire program that runs whenever you launch Minecraft from the launcher. All threads, processes, and services that run during the game’s graphical, interactable lifetime are part of the physical client.
> - Physical server - Often known as the dedicated server, the *physical server* is the entire program that runs whenever you launch any sort of `minecraft_server.jar` that does not bring up a playable GUI.
> - Logical server - The *logical server* is what runs game logic: mob spawning, weather, updating inventories, health, AI, and all other game mechanics. The logical server is present within the physical server, but is also can run inside a physical client together with a logical client, as a single player world. The logical server always runs in a thread named the `Server Thread`.
> - Logical client - The *logical client* is what accepts input from the player and relays it to the logical server. In addition, it also receives information from the logical server and makes it available graphically to the player. The logical client runs in the `Client Thread`, though often several other threads are spawned to handle things like audio and chunk render batching.
>
> In the Minecraft codebase, the physical side is represented by an enum called `Dist`, while the logical side is represented by an enum called `LogicalSide`.
>
> — [Forge Doc](https://mcforge.readthedocs.io/en/1.14.x/concepts/sides/)

It's worth noting that the distinction between client and server here is only logical. In fact if you are in single player mode, there will be both a server and a client on your computer, and they are in different threads[^1]. But when you connect to a server, only the client exists on your computer and the server is moved to a remote server.

The following diagram roughly explains how Minecraft works.

![How_minecraft_work](how-minecraft-works.assets/How_minecraft_work.svg)

When you see this picture, you may wonder why the client has a data model when the server is responsible for the game logic. In fact, the "client-side data model" here is just a copy of the "server-side data model", although they have separate game ticks and share a lot of the same code, but the final logic is still the server-side prevails.

As we mentioned before, the client and server are independent, but they inevitably need to synchronize data, and in Minecraft, all client and server data synchronization is done through network packets. In most of the cases, the original version has already implemented a method to synchronize the data, we just need to call the method that has already been implemented, but in some cases, the original version does not implement the corresponding function, or it is not suitable to use the function provided by the original version, we have to create and send network packets to complete the data synchronization.

So the next question is, how do we distinguish in our code whether we are on the client side or the server side?

> ### `world.isRemote`
>
> This boolean check will be your most used way to check sides. Querying this field on a `World` object establishes the **logical** side the world belongs to. That is, if this field is `true`, the world is currently running on the logical client. If the field is `false`, the world is running on the logical server. It follows that the physical server will always contain `false` in this field, but we cannot assume that `false` implies a physical server, since this field can also be `false` for the logical server inside a physical client (in other words, a single player world).
>
> Use this check whenever you need to determine if game logic and other mechanics should be run. For example, if you want to damage the player every time they click your block, or have your machine process dirt into diamonds, you should only do so after ensuring `world.isRemote` is `false`. Applying game logic to the logical client can cause desynchronization (ghost entities, desynchronized stats, etc.) in the lightest case, and crashes in the worst case.
>
> This check should be used as your go-to default. Aside from `DistExecutor`, rarely will you need the other ways of determining side and adjusting behavior.
>
> ### `DistExecutor`
>
> Considering the use of a single “universal” jar for client and server mods, and the separation of the physical sides into two jars, an important question comes to mind: How do we use code that is only present on one physical side? All code in `net.minecraft.client` is only present on the physical client, and all code in `net.minecraft.server.dedicated` is only present on the physical server. If any class you write references those names in any way, they will crash the game when that respective class is loaded in an environment where those names do not exist. A very common mistake in beginners is to call `Minecraft.getMinecraft().<doStuff>()` in block or tile entity classes, which will crash any physical server as soon as the class is loaded.
>
> How do we resolve this? Luckily, FML has `DistExecutor`, which provides various methods to run different methods on different physical sides, or a single method only on one side.
>
> Note
>
> It is important to understand that FML checks based on the **physical** side. A single player world (logical server + logical client within a physical client) will always use `Dist.CLIENT`!
>
> ### Thread Groups
>
> If `Thread.currentThread().getThreadGroup() == SidedThreadGroups.SERVER` is true, it is likely the current thread is on the logical server. Otherwise, it is likely on the logical client. This is useful to retrieve the **logical** side when you do not have access to a `World` object to check `isRemote`. It *guesses* which logical side you are on by looking at the group of the currently running thread. Because it is a guess, this method should only be used when other options have been exhausted. In nearly every case, you should prefer checking `world.isRemote` to this check.
>
> ### `FMLEnvironment.dist` and `@OnlyIn`
>
> `FMLEnvironment.dist` holds the **physical** side your code is running on. Since it is determined at startup, it does not rely on guessing to return its result. The number of use cases for this is limited, however.
>
> Annotating a method or field with the `@OnlyIn(Dist)` annotation indicates to the loader that the respective member should be completely stripped out of the definition not on the specified **physical** side. Usually, these are only seen when browsing through the decompiled Minecraft code, indicating methods that the Mojang obfuscator stripped out. There is little to no reason for using this annotation directly. Only use it if you are overriding a vanilla method that already has `@OnlyIn` defined. In most other cases where you need to dispatch behavior based on physical sides, use `DistExecutor` or a check on `FMLEnvironment.dist` instead.
>
> — [Forge Dock](https://mcforge.readthedocs.io/en/1.14.x/concepts/sides/#performing-side-specific-operations)

---

[^1]: Thread is one of the units of program scheduling, being in different threads means that the logic and data of these two are independent of each other and can only be synchronized by specific methods. Specifically, the server is in the "Server thread" and the client is in the "Render thread", if you've ever watched the output log when Minecraft starts, you should see these two words.


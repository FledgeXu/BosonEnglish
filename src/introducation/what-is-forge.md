# What is Forge?

This tutorial is a mod development tutorial based on Forge, so it's natural to answer the question, "What is Forge?

At first glance, this doesn't seem like a question at all, “Forge? Isn't Forge just Forge?” The first thought that comes to your inner mind when you see this question is probably this.

But it's necessary to answer this question, and I'm going to talk a little bit about what Forge is, and the history of Forge. This may seem unrelated to our tutorial, but it is actually the "Lore" of mod development, and learning it will help you communicate with others better.

We have to start with Minecraft itself, but first we have to make it clear that Minecraft is a commercial software written in Java. This means two things: first, Minecraft is relatively easy to modify, and second, the code itself is not open source and is obfuscated. Early on in Minecraft's history, because Mojang never provided an official API[^1] for Minecraft, the "Mod Coder Pack" project was born (hereinafter referred to as MCP).

Remember what I said earlier about the two features of Minecraft that MCP uses to implement a set of tools that allow developers to modify the content of Minecraft jar packs directly?

So `srg name`, `notch name` and `mcp name` were born.

So what are these three?

The first is the `notch name`, which is a name that Minecraft has directly decompiled and obfuscated, and is usually a meaningless alphanumeric combination. You can tell from the name Notch that it comes directly from Minecraft (~~and the grudge against Notch~~), for example `u` is a typical `notch name`.

There is a example about `notch name`

```java
public class cbw extends cbq {
   public int u() {
      return 0;
   }

   public int t() {
      return 127;
   }
}
```

Next is the `srg name`, which is a one-to-one correspondence with the `notch name`, and `srg name` will not change from one version to the next, the reason why it is called `srg name`(`srg` meas Searge) is to commemorate [the leader](https://twitter.com/SeargeDP) of the MCP project, Searge. But there are corresponding prefixes and suffixes to distinguish them. Take the above `u` as an example, its `srg name` is `func_214968_u`.

```java
public class NetherGenSettings extends GenerationSettings {
   public int func_214968_u() {
      return 0;
   }

   public int func_214967_t() {
      return 127;
   }
}
```

Finally, there's the `mcp name`, which is also the name we come across most in mod development, where the code is already readable. In `mcp name`, the code is already readable. It's the same as the names we use in our normal java programs. But the `mcp name` is subject to change. For example, the `func_214968_u` above has an `mcp name` of `getBedrockFloorHeight`. The class name in `mcp name` is the same as the class name in `srg name`.

```java
public class NetherGenSettings extends GenerationSettings {
   public int getBedrockFloorHeight() {
      return 0;
   }

   public int getBedrockRoofHeight() {
      return 127;
   }
}
```

As time went on, mod developers realized that modding Jar files directly to write mods was too crude, and that mod-to-mod compatibility was virtually non-existent. So Forge was born.

Forge was actually a set of third-party APIs implemented by modifying the Minecraft way, and as time went on, MCP is now dead (some of MCP's tools are still alive). In addition to the Forge set of APIs, Fabric is also in the limelight, and Forge itself underwent a rewrite after Minecraft 1.13 arrived, introducing a number of APIs for functional programming.

So how did Forge use the three names we mentioned earlier?

After you install Forge, everything in the game will be decompiled into `srg names` to run, and your compiled mods will also be obfuscated into `srg names` to make sure it runs properly.

---

[^1]: API (Application programming interface) is a mechanism provided by a program that allows a third party to modify or add functionality.


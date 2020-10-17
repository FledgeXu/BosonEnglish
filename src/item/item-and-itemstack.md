# Item and ItemStack
Here, I want to talk about the distinction between `Item` and `ItemStack`. Let's start with `ItemStack` and think step by step about why they need to be distinguished.

As the name implies, `ItemStack` is an Item Stack. In fact, in the game, all the items in the item slots are individual `ItemStacks`.

![image-20200428080226198](item-and-itemstack.assets/image-20200428080226198.png)

For example, in this case, there are three `ItemStack`s.

But this begs the question, although one set of apples is different in number from the second set, that number doesn't actually affect their actual performance. They can also be eaten, and the effect of the reply is the same when eaten.

These are the same as `Properties` or `Default Behavior`, and the same logic should be extracted from them, which is called `Item`.

Again, there are only two kinds of `Items` here: apples and iron swords.

You can imagine that `ItemStack` is a wrapper for `Item`, which provides additional attributes such as quantity, NBT tags, etc. 

It's worth noting here that just because `ItemStack` is zero, it doesn't mean it's null, so you have to use the `isEmpty()` method under `ItemStack` to determine if it's null.

`ItemStack` contains `Item` is actually the same instance, the reason is very simple, if it is not the same instance, it will pointlessly produce many of the same instance, for optimization purposes, of course, is appropriate to share an instance, which also means that you can use`result.getItem() == Items` to detect which `Item` is stored in ItemStack.


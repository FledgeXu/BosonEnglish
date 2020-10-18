# Sword

In this section, we'll explain how to create a new sword, and here we'll use the Obsidian Sword as an example.

First we have to create an `ItemTier`. The `ItemTier` is what we call tool levels, such as `Diamond Level`, `Iron Level` .

```java
public enum ModItemTier implements IItemTier {

    OBSIDIAN(3, 2000, 10.0F, 4.0F, 30, () -> {
        return Ingredient.fromItems(ItemRegistry.obsidianIngot.get());
    });

    private final int harvestLevel;
    private final int maxUses;
    private final float efficiency;
    private final float attackDamage;
    private final int enchantability;
    private final LazyValue<Ingredient> repairMaterial;

     ModItemTier(int harvestLevelIn, int maxUsesIn, float efficiencyIn, float attackDamageIn, int enchantabilityIn, Supplier<Ingredient> repairMaterialIn) {
        this.harvestLevel = harvestLevelIn;
        this.maxUses = maxUsesIn;
        this.efficiency = efficiencyIn;
        this.attackDamage = attackDamageIn;
        this.enchantability = enchantabilityIn;
        this.repairMaterial = new LazyValue<>(repairMaterialIn);
    }

    @Override
    public int getMaxUses() {
        return this.maxUses;
    }

    @Override
    public float getEfficiency() {
        return this.efficiency;
    }

    @Override
    public float getAttackDamage() {
        return this.attackDamage;
    }

    @Override
    public int getHarvestLevel() {
        return this.harvestLevel;
    }

    @Override
    public int getEnchantability() {
        return this.enchantability;
    }

    @Override
    public Ingredient getRepairMaterial() {
        return this.repairMaterial.getValue();
    }

}
```



Again, this may seem like an awful lot of content, but it's not as complicated as you might think.

Here we create a `ModItemTier` class and inherit the `IItemTier` interface, the reason is that the original `net.minecraft.item.ItemTier` is implemented in enum, we can't add anything to it ourselves, so we have to implement it ourselves, we basically just copy it here. The original implementation, with all the properties of the original, is also in this class, so you can refer to it.

As for the various methods in this enum, I'm not going to explain them here, but with the experience of several previous items, I'm sure the reader reading this will have the ability to guess the function's function by its name.

Similarly, we create an `ObsidianSword`, but this time the inheritance class is a little different, this time we directly inherit the original `SwordItem` class, if you look at the inheritance diagram, you can see that `SwordItem` is a subclass of `Item`.

![image-20200928181500005](sword.assets/image-20200928181500005.png)

Implementation:

```java
public class ObsidianSword extends SwordItem {
    public ObsidianSword() {
        super(ModItemTier.OBSIDIAN, 3, -2.4F, new Item.Properties().group(ItemGroup.COMBAT));
    }
}
```

Please also refer to the original implementation here for `3` and `-2.4F`, where all item registrations are in `net.minecraft.item.Items`.

Then register the item.

```java
public static RegistryObject<Item> obsidianSword = ITEMS.register("obsidian_sword", ObsidianSword::new);
```

Adding model files.

```json
{
  "parent": "item/generated",
  "textures": {
    "layer0": "boson:item/obsidian_sword"
  }
}
```

and textures:

<img src="sword.assets/obsidian_sword.png" alt="obsidian_sword" style="zoom:300%;" />

Once you're done creating it, open the game and see.

![image-20200427184918516](sword.assets/image-20200427184918516.png)

[Source Code](https://github.com/FledgeXu/BosonSourceCode/tree/master/src/main/java/com/tutorial/boson/melee_weapons)


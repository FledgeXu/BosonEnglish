# Item Property Override

Item Property Override is a mechanism of the original Minecraft, used to achieve, for example, the bow and arrow display different bow models according to different draw times. We use the model file of the original `bow.json` to explain what is item attribute coverage. For more information, please refer to the original [Item models](https://minecraft.gamepedia.com/Model#Item_models) ’s overrides.

`bow.json`

```json
{ 
    ……Omit……
    "overrides": [
        {
            "predicate": {
                "pulling": 1
            },
            "model": "item/bow_pulling_0"
        },
        {
            "predicate": {
                "pulling": 1,
                "pull": 0.65
            },
            "model": "item/bow_pulling_1"
        },
        {
            "predicate": {
                "pulling": 1,
                "pull": 0.9
            },
            "model": "item/bow_pulling_2"
        }
    ]
}
```

You can see that a configuration element called `overrides` appeared in `bower.json`, and a pair of pairs appeared in this configuration element:

```json
{
  "predicate": {
    "pulling": 1
  },
  "model": "item/bow_pulling_0"
}
```

The content here is very easy to understand. The `predicate` is the "condition". When the value of `predicate` (defined in the game will dynamically change) in the condition is greater than or equal to 1, load the corresponding model, which is `item/bow_pulling_0`.

And we are also about to implement a similar function, in this section we are going to implement a "magic ingot" that dynamically changes the model according to the number of stacked items.

The first is the items, which is very simple.

```java
public class MagicIngot extends Item {
    public MagicIngot() {
        super(new Properties().group(ModGroup.itemGroup));
    }
}
```

Registered items:

```java
public static final RegistryObject<Item> magicIngot = ITEMS.register("magic_ingot", MagicIngot::new);
```

The next step is to add "Item Property Override" to the item we created.

```java
@Mod.EventBusSubscriber(bus = Mod.EventBusSubscriber.Bus.MOD, value = Dist.CLIENT)
public class PropertyRegistry {
    @SubscribeEvent
    public static void propertyOverrideRegistry(FMLClientSetupEvent event) {
        event.enqueueWork(() -> {
            ItemModelsProperties.registerProperty(ItemRegistry.magicIngot.get(), new ResourceLocation(Utils.MOD_ID, "size"), (itemStack, clientWorld, livingEntity) -> itemStack.getCount());
        });
    }
}
```

As you can see, in the `FMLClientSetupEvent` event, we called the `ItemModelsProperties.registerProperty` method to add an `overrides` to our item. The meaning of the three parameters here is that the first one is the one you want to add `overrides` Item, the second is the name of this `overrides`, which is the name you will write in the `predicate` of the model file later, and the third is the dynamically changing value. Here we directly call `itemStack.getCount( )`The function returns the number of stacked items.

Next look at our model file: `magic_ingot.json`:

```java
{
  "parent": "item/generated",
  "textures": {
    "layer0": "item/iron_ingot"
  },
  "overrides": [
    {
      "predicate": {
        "boson:size": 16
      },
      "model": "item/gold_ingot"
    }
  ]
}
```

You can see here that we have set it to display the texture of the iron ingot by default, and then display the texture of the gold ingot when the number of items is greater than or equal to 16.

You can see this "magic ingot" when you open the game.

![image-20201009195911814](item-property-override.assets/image-20201009195911814.png)

![image-20201009195924247](item-property-override.assets/image-20201009195924247.png)

[Source Code](https://github.com/FledgeXu/BosonSourceCode/tree/master/src/main/java/com/tutorial/boson/item_property_overrides)
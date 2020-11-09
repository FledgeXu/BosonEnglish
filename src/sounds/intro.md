# Sounds

In this section, we'll talk about learning how to add sound effects to my mods, and it's very simple, so let me get started.

```java
public class SoundEventRegistry {
    public static final DeferredRegister<SoundEvent> SOUNDS = DeferredRegister.create(ForgeRegistries.SOUND_EVENTS, Utils.MOD_ID);
    public static final RegistryObject<SoundEvent> meaSound = SOUNDS.register("mea", () -> new SoundEvent(new ResourceLocation(Utils.MOD_ID, "mea")));
}
```

With those two sentences, you can register our sound effects.

Next, let's add sound effects and create the following files and directories in your `Resource` directory.

```
resources
├── META-INF
│   └── mods.toml
├── assets
│   └── boson
│       ├── blockstates
│       ├── lang
│       ├── models
│       ├── sounds
│       ├── sounds.json
│       └── textures
├── data
└── pack.mcmeta
```

Here we have created a `sounds.json`, please refer to the wiki for the format of this file.

It reads as follows

```json
{
  "mea": {
    "subtitle": "mea",
    "replace": "true",
    "sounds": [
      {
        "name": "boson:mea",
        "steam": true
      }
    ]
  }
}
```

Note that the outermost key name should be the same as the second parameter in your previous `ResourceLocation`. We then specify the specific audio file in `name`. Please note that Minecraft only allows loading of audio files in ogg format.

Next, we place the finished audio file in the `sounds` file.

```
resources
├── META-INF
│   └── mods.toml
├── assets
│   └── boson
│       ├── blockstates
│       ├── lang
│       ├── models
│       ├── sounds
│       │   └── mea.ogg
│       ├── sounds.json
│       └── textures
├── data
└── pack.mcmeta
```

Place the directory as above.

Next you can create objects to use with our sound effects!

```java
public class SoundTestItem extends Item {
    public SoundTestItem() {
        super(new Properties().group(ModGroup.itemGroup));
    }

    @Override
    public ActionResult<ItemStack> onItemRightClick(World worldIn, PlayerEntity playerIn, Hand handIn) {
        if (worldIn.isRemote) {
            worldIn.playSound(playerIn, playerIn.getPosition(), SoundEventRegistry.meaSound.get(), SoundCategory.AMBIENT, 10f, 1f);
        }
        return super.onItemRightClick(worldIn, playerIn, handIn);
    }
}
```

Open the game and try it out, we should have successfully added sound effects.

[Source Code](https://github.com/FledgeXu/BosonSourceCode/tree/master/src/main/java/com/tutorial/boson/sounds)


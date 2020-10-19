# Data synchronization built into the block entity

In this section, we will learn about the data synchronization function built into the block entity.

Remember the data synchronization between the server and the client we talked about before? Fortunately, TileEntity has built-in two sets of data synchronization methods for us. Unfortunately, these two sets of methods can only synchronize data from the server to the client, and can only synchronize a small amount of data.

In this section, we will learn how to make a box that plays the roar of the zombie every few seconds. Let's start with the box;

`ObsidianZombieBlock`

```java
public class ObsidianZombieBlock extends Block {
    public ObsidianZombieBlock() {
        super(Properties.create(Material.ROCK).hardnessAndResistance(5));
    }

    @Override
    public boolean hasTileEntity(BlockState state) {
        return true;
    }

    @Nullable
    @Override
    public TileEntity createTileEntity(BlockState state, IBlockReader world) {
        return new ObsidianZombieTileEntity();
    }
}
```

Then there is `ObsidianZombieTileEntity`.

```java
public class ObsidianZombieTileEntity extends TileEntity implements ITickableTileEntity {
    private boolean flag = false;
    private int MAX_TIME = 5 * 20;
    private int timer = 0;


    public ObsidianZombieTileEntity() {
        super(TileEntityTypeRegistry.obsidianZombieTileEntity.get());
    }

    @Override
    public void tick() {
        if (world.isRemote && flag) {
            PlayerEntity player = world.getClosestPlayer(pos.getX(), pos.getY(), pos.getZ(), 10, false);
            this.world.playSound(player, pos, SoundEvents.ENTITY_ZOMBIE_AMBIENT, SoundCategory.AMBIENT, 1.0f, 1.0f);
            flag = false;
        }
        if (!world.isRemote) {
            if (timer == MAX_TIME) {
                flag = true;
                world.notifyBlockUpdate(pos, getBlockState(), getBlockState(), Constants.BlockFlags.BLOCK_UPDATE);
                flag = true;
                timer = 0;
            }
            timer++;
        }
    }

    @Nullable
    @Override
    public SUpdateTileEntityPacket getUpdatePacket() {
        return new SUpdateTileEntityPacket(pos, 1, getUpdateTag());
    }

    @Override
    public void onDataPacket(NetworkManager net, SUpdateTileEntityPacket pkt) {
        handleUpdateTag(world.getBlockState(pkt.getPos()), pkt.getNbtCompound());
    }

    @Override
    public CompoundNBT getUpdateTag() {
        CompoundNBT compoundNBT = super.getUpdateTag();
        compoundNBT.putBoolean("flag", flag);
        return compoundNBT;
    }

    @Override
    public void handleUpdateTag(BlockState state, CompoundNBT tag) {
        flag = tag.getBoolean("flag");
    }
}
```

First, let's explain the method used to synchronize the two sets of data.

- `getUpdatePacket`
- `onDataPacket`

These two methods are data synchronization methods that will be used during normal games. Their names may be a little confusing, because `getUpdatePacket` is the method used by the server to send data packets, and `onDataPacket` is the client accepts Data packet method.

Next is:

- `getUpdateTag`
- `handleUpdateTag`

These two methods are called when the block is just loaded. The reason why these two methods exist is because some decorative block entities do not need to synchronize data frequently, such as billboards. Synchronize once when the block is loaded.

In order for your block entity to automatically synchronize data when the game is loaded, you need to implement `getUpdateTag` and `handleUpdateTag` first, and then call them in `getUpdatePacket` and `onDataPacket`.

If you need to trigger data synchronization, you need to call the `world.notifyBlockUpdate` method on the server.

Of course, just like data storage, the default serialization and deserialization methods of these methods are NBT tags.

```java
@Override
public CompoundNBT getUpdateTag() {
  CompoundNBT compoundNBT = super.getUpdateTag();
  compoundNBT.putBoolean("flag", flag);
  return compoundNBT;
}

@Override
public void handleUpdateTag(BlockState state, CompoundNBT tag) {
  flag = tag.getBoolean("flag");
}
```

As you can see, the data is transmitted with the NBT tag.

```java
@Nullable
@Override
public SUpdateTileEntityPacket getUpdatePacket() {
  return new SUpdateTileEntityPacket(pos, 1, getUpdateTag());
}

@Override
public void onDataPacket(NetworkManager net, SUpdateTileEntityPacket pkt) {
  handleUpdateTag(world.getBlockState(pkt.getPos()), pkt.getNbtCompound());
}
```

Here we simply call the previous two methods. You can fill in the serial number value of the second parameter of `SUpdateTileEntityPacket`.

Then there is our main logic `tick` method:

```java
@Override
public void tick() {
  if (world.isRemote && flag) {
    PlayerEntity player = world.getClosestPlayer(pos.getX(), pos.getY(), pos.getZ(), 10, false);
    this.world.playSound(player, pos, SoundEvents.ENTITY_ZOMBIE_AMBIENT, SoundCategory.AMBIENT, 1.0f, 1.0f);
    flag = false;
  }
  if (!world.isRemote) {
    if (timer == MAX_TIME) {
      flag = true;
      world.notifyBlockUpdate(pos, getBlockState(), getBlockState(), Constants.BlockFlags.BLOCK_UPDATE);
      flag = true;
      timer = 0;
    }
    timer++;
  }
}
```

For your convenience, I have used two `if` statements here to distinguish the logic between the client and the server

The first is the client:

```java
if (world.isRemote && flag) {
  PlayerEntity player = world.getClosestPlayer(pos.getX(), pos.getY(), pos.getZ(), 10, false);
  this.world.playSound(player, pos, SoundEvents.ENTITY_ZOMBIE_AMBIENT, SoundCategory.AMBIENT, 1.0f, 1.0f);
  flag = false;
}
```

First, we judge whether it is on the client side, and whether the value of flag is true (true means to play the sound), and then get the nearest player, use the `this.world.playSound` method to play the sound, please pay attention, play the sound It can be executed on the client and the server at the same time. If you execute it on the server, a data packet will be sent to the client automatically and the client will play the sound.

The first and second variables of `playSound` are easy to understand. The third variable is the sound to be played, and the third variable determines which sound control category controls the size of the sound.

Next is the server:

```java
if (!world.isRemote) {
  if (timer == MAX_TIME) {
    flag = true;
    world.notifyBlockUpdate(pos, getBlockState(), getBlockState(), Constants.BlockFlags.BLOCK_UPDATE);
    flag = true;
    timer = 0;
  }
  timer++;
}
```

The server is basically a counter as before. The only special thing is that we call the `world.notifyBlockUpdate` method, because we don't need to update the `blockstate`, so the second and third parameters are the same. The last parameter What level of update is needed, Forge provides us with the `Constants` class, which has detailed instructions.

Then there are things like adding textures and models, and interested readers can read the source code for themselves.

After opening the game and placing the blocks, wait a while and you should be able to hear the roar of the zombies.

[Source Code](https://github.com/FledgeXu/BosonSourceCode/tree/master/src/main/java/com/tutorial/boson/tileentitydatasync)


# Block's Model and Texture

We have successfully created a Block, but this Block is still ugly, just a black and purple block. In this section, we are going to add models and textures to this block.

First, we create some folders, and the created directories are displayed as follows:

```
resources
├── META-INF
│   └── mods.toml
├── assets
│   └── boson
│       ├── blockstates
│       ├── lang
│       ├── models
│       │   ├── block
│       │   └── item
│       └── textures
│           ├── block
│           └── item
└── pack.mcmeta
```

Then we create a json file with the same name as the registered name of your item under the `blockstates` folder, here we are going to create `obsidian_block.json`

The content is as follows:

```json
{
  "variants": {
    "": { "model": "boson:block/obsidian_block_model" }
  }
}
```

This file is actually a mapping table of the blockstate and the specific model to be used. If you are still not sure what the blockstate is, please read it forward.

Here we have no blockstate, so we wrote `"": {"model": "boson:block/obsidian_block_model" }`, setting the default model to `obsidian_block_model.json`.

Next we create `obsidian_block_model.json` under `models/block` with the following content:

```json
{
  "parent": "block/cube_all",
  "textures": {
    "all": "boson:block/obsidian_block_texture"
  }
}
```

As you can see, compared with the item model, the inherited stuff is different. As for the format of the specific model file, please refer to [Wiki](https://minecraft.gamepedia.com/Model#Block_models). In the model, we called `obsidian_block_texture.png` as our material.

Next, let's add our material under `textures/block`, also please note that the ratio of the material file is 1:1, and it is best not to be larger than 32x32 pixels.

<img src="block-model-and-texture.assets/obsdian_block.png" alt="obsdian_block" style="zoom:300%;" />



Start the game at this time, and you should be able to see that our blocks have corresponding textures and models.

![image-20200428181816541](block-model-and-texture.assets/image-20200428181816541.png)

**The whole loading process is: get the state of the block in the game, get the model in the mapping table corresponding to Blockstates, and load the material according to the model. **

But at this time you will find that the item corresponding to our block does not have a texture yet, and we will solve this problem next.

Create a json file with the same name as our BlockItem registered name under `models/item`, here is `obsidian_block.json`

```json
{
  "parent": "boson:block/obsidian_block_model"
}
```

Yes, you read that right, just one sentence, we can directly inherit the corresponding block model.

![image-20200428182406212](block-model-and-texture.assets/image-20200428182406212.png)


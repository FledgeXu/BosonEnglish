# Core Conception

In this subsection, I will cover a few concepts that are not difficult to understand but are very important.

## register

If you want to add some content to Minecraft, then one of the things you have to do is to register. Registration is a mechanism that tells the game itself which things are available. Things you need to register can basically be broken down into two parts: a registration name and an instance.

## ResourceLocation

You can think of ResourceLocation as a specially formatted string that looks something like this: `minecraft:textures/block/stone.png`, a ResouceLocation specifies a particular file under the resource bundle. For example, this ResourceLocation represents material image of the stone in the original resource pack. There will be one or more domains. Second half after colon corresponds one-to-one directory structure within `assets` folder. In a way, ResourceLocation is a special URL.

## Models and Textures

In game 3d objects have their models and those models and textures together define specific look of the object. Model is equivalent of bones, and material is equivalent of skin. For the most part, your materials are png images, make sure your material background is opaque, and secondly don't use semi-transparent pixels in your materials, it can be unpredictable and problematic.


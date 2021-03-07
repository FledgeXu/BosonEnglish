# Core Conception

In this subsection, I will cover a few concepts that are not difficult to understand but are very important.

## register

If you want to add some content to Minecraft, then one of the things you have to do is register. Registration is a mechanism that tells the game itself which things are available. The things you need to register can basically be broken down into two parts: a registration name and an instance.

## ResourceLocation

You can think of ResourceLocation as a specially formatted string that looks something like this: `minecraft:textures/block/stone.png`, a ResouceLocation specifies a specific file under the resource bundle. For example, this ResourceLocation represents the material image of the stone in the original resource pack. There will be one or more domains. The second half of the colon corresponds one-to-one with the directory structure within the `assets` folder. In a way, ResourceLocation is a special URL.

## Models and Textures

In the game 3d objects basically have their model, the model and textures together define the specific look of an object. The model is the equivalent of bones, and the material is the equivalent of skin. For the most part, your materials are png images, make sure your material background is opaque, and secondly don't use semi-transparent pixels in your materials, it can be unpredictable and problematic.


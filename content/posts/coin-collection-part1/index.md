---
title         : "Coin Collection Part 1"
description   : ""
toc           : true
cover         : "amazon-branches-dawn-975771.jpg"
coverAlt      : "A simple Coin Collection tutorial for Torque3D beginners."
tags          : [ game, beginner, coin-collection, torquescript, t3d-4 ]
---

## What is Torque 3D?
Torque is a game engine, it is not based on graphical drag 'n' drop elements 
like Unity thus it is not as easy to get into and understand. There is no 'make 
game button' in Torque; you need to have an understanding of how to code, so 
if you are an absolute beginner at programming and have never written even a 
simple program then this might not be the right guide to start with.

## What can Torque do out of the box?

Apart from the advanced deferred rendering model and the physics and all the other 
great stuff Torque can 'do'. What you are thinking of here is more likely what can 
you do if you load up the engine and start walking around? Actually, T3D has a lot 
of gameplay features out of the box. You can try the full template and you will be 
able to run around, shoot, mount vehicles, throw mines etc without making a single 
change to the engine. There is a lot of FPS features already implemented as well, 
including multiplayer support! So if you want to make a FPS you can probably (at 
first) treat the development as modding a game. A good bet for you would be to 
start at the FPS tutorial (see link at the end of the tutorial)

## What do I need to get started with developing for Torque?
### Coding
_(The following steps is only necessary if you are not satisfied with just scripting 
and at some point want more than that. If you are only here for the scripting, the 
binary files in the repo will be enough.)_ 

**Before you** [download the repo](http://github.com/GarageGames/Torque3D) you should 
be sure to setup your environment correctly! **First** do you have an IDE (Integrated 
Development Environment)? No? If you are running Windows, you should go ahead and 
download [Visual Studio](https://visualstudio.microsoft.com/vs/). **Now**, you need to 
download the [Windows 10 SDK](https://developer.microsoft.com/en-US/windows/downloads/windows-10-sdk/) 
or else you can't compile the engine. If you plan to be using the physics, you will need 
the [PhysX SDK](http://www.nvidia.com/object/physx_downloads.html) as well.

### Scripting
Nothing special is necessary in order to script with Torque3D, if the engine is 
running you should be good to go. However you might want a 
[scripting IDE](http://wiki.torque3d.org/introduction:scripting-ides).

## What is TorqueScript?
TorqueScript (TS) is a C-like language. It is very basic. There are no 'types' in 
TorqueScript, everything is handled as strings or ints. TorqueScript is not object 
oriented - you can treat it as such but it wasn't made with object-oriented programming in 
mind. One of the most interesting things about scripting in TS is that it is event driven. 
The engine runs the game and sends the necessary callbacks to the Script interface.

TS is a (at the time of writing this) a pretty slow scripting language so you should avoid 
having all your core features in script. Personally one of the things I use TS for is triggering 
spell casts and spawn emitters, these are commands that aren't triggered a lot of times and 
extremely handy to have in script. I wouldn't use scripts for minds for the AI players, for 
prototyping it is okay but not for the release version of your game.

## Introduction
To me there is no better way of learning something than to throw yourself into it and get 
dirty. So we will start right away with the first project. I will not go in depth with how 
the editors works in this tutorial but I will link you to places where you can get help to a 
specific task.

## The Module system
Modules were introduced in Torque3D 4.0, by using modules you can easily isolate your changes
in a dedicated package. _TBD - extensible description of module system_.

Before we can begin writing our game scripts, we need to set up a module. The process
is simple, create a new folder `data/CoinCollectionModule` and inside that folder add two files.

**data/CoinCollection/CoinCollectionModule.module**

{{< highlight xml "linenos=table,hl_lines=5-8" >}}
<ModuleDefinition
    ModuleId="CoinCollectionModule"
    VersionId="1"
    Description="Starter module for CoinCollection gameplay."
    scriptFile="CoinCollectionModule.cs"
    CreateFunction="onCreate"
    DestroyFunction="onDestroy"
    Group="Game"
    Dependencies="UI=1">
    <DeclaredAssets
        canSave="true"
        canSaveDynamicFields="true"
        Extension="asset.taml"
        Recurse="true" />
</ModuleDefinition>
{{< / highlight >}}

Most of this, you don't need to concern yourself with at this moment. It's mainly metadata about 
the module. The most significant pieces right now is that the `Group` is set to `Game`, this 
means that it will be automatically by our `main.cs` file, the game's entrypoinnt.

Furthermore, the `scriptFile`, `DestroyFunction` and `CreateFunction` specify how our module 
is initialized. Let's go ahead and add this file now:

**data/CoinCollection/CoinCollectionModule.cs**
{{< highlight torquescript >}}
/// Module life-cycle

function CoinCollectionModule::onCreate(%this)
{
}

function CoinCollectionModule::onDestroy(%this)
{
}

/// Server life-cycle

function CoinCollectionModule::initServer(%this)
{
}

function CoinCollectionModule::onCreateGameServer(%this)
{
}

function CoinCollectionModule::onDestroyGameServer(%this)
{
}

/// Client life-cycle

function CoinCollectionModule::initClient(%this)
{
}

function CoinCollectionModule::onCreateClientConnection(%this)
{
}

function CoinCollectionModule::onDestroyClientConnection(%this)
{
}

{{< / highlight >}}

This is all the life-cycle hooks we get for our module, the _Module life-cycle_ hooks 
are general for all modules, while the _Server_ and _Client life-cycle_ hooks are
specific for `Game` modules.

That is actually the basis of creating a module, before we move further we want an empty 
level to work with.

## Creating an empty level
Launch Torque3D, and click the "Open World Editor" button, you can also access this editor from in-game.
If you don't know how to launch the World Editor, take a look at 
[this guide](https://torque-3d.readthedocs.io/en/latest/world/overview.html#how-to-launch-the-world-editor).

Once inside the editor, play around with the terrain editing tools. 
[Here's a guide to creating your first terrain](https://torque-3d.readthedocs.io/en/latest/world/terrainblock.html).

But we don't need anything fancy, a flat terrain is fine.

**One little thing:** 

Then click `File>Save Level As`.



## Place some objects
For learning how to place objects in the world, [you have to use the library tab](https://torque-3d.readthedocs.io/en/latest/world/interface.html#library-tab).
But before we do that, we need to create a new interactable type of object! _First_ go into 
`Scripts/Server/ScriptExec.cs`, at the end of the file add a new line:

{{< highlight tscript >}}
exec("./Coin.cs");
{{< / highlight >}}

This is absolutely vital! If you don't execute your scripts with a `exec("");"` call, they wont be 
loaded. You have to execute scripts to use them. Now create a new file in `Scripts/Server` called `Coin.cs` 
**Notice! Here I am not following the conventions on where to place datablocks.** The reason I do this 
is because
 
  1. I wanted to keep it as simple as possible in the first tutorial
  2. I like to go against the convention; in some cases I prefer having the datablock with the scripts.

**Put the following code in the Coin.cs file:**

{{< highlight tscript >}}
datablock StaticShapeData( Coin )
{
   category = "TutorialObjects";
   shapeFile = "art/shapes/items/kit/healthkit.dts";
};
{{< / highlight >}}

`datablock` this is very similar to a struct, basically in a datablock you dene a set of default values which will get 
copied to the spawned objects upon creation.

`StaticShapeData( Coin )` here we state that we want to create a datablock of the type StaticShapeData, for creating new 
StaticShapes. We call this new datablock Coin.

`category` the category attribute is (AFAIK) only for telling the world editor where the new object can be found. (Which
is why the Coin object showed up in the 'TutorialObjects' folder)

`shapeFile` this string defines where the 3D model which the static shape should use is located.
_Granted_, it doesn't really look like a coin. But use your imagination!

**Alright**, to spawn your new object load up the **world editor**_(F11 in game)_ make sure you are in the **object 
editor**_(F1)_ and in the **scene tree menu**_(to the right)_ press the _'Library'_ tab.

Here you will notice that under the _'Scripted'_ tab a new folder has appeared, the _'TutorialObjects'_ folder. Inside 
this folder an object called Coin has appeared. Double click it and your first coin will get spawned.

## onCollision Callback
{{< highlight tscript >}}
function Coin::onCollision(%this, %obj, %col, %vec, %len)
{
   %obj.delete();
}
{{< / highlight >}}
**Now what can we make of this?** For all static shapes that are using the datablock Coin, hence all our Coin objects, 
we define a function for the onCollision callback. The engine calls this callback when two objects collide. The 
parameters it gets is:

`%this` refers to the datablock.

`%obj` refers to the coin object.

`%col` refers to the object colliding with the coin.

`%vec` is the direction of the impact vector, which tells you the direction of the impact itself.

`%len` is the length of the impact vector, hence the force of the impact.

When an object collides with the coin, it deletes itself. That was pretty easy huh? This is one of the benets of an 
event driven language, it can be incredibly easy to create something cool!

## SimGroups
Remember how to spawn those coins? Well lets get back to that. We want a way to manage how many coins are left in the 
game, so when there is no more coins, the game is done. We will do this by using a `SimGroup`. A `SimGroup` is a 
collection of objects. If you delete a `Simgroup` all objects inside it will be deleted as well.

Again head to the _'Library'_ tab in the **scene tree menu**. Now i want you to find the _'Level'_ tab. Inside the 
_'Level'_ tab go to the _'System'_ folder. Inside of this folder there will be a new object called `SimGroup`, which has 
an icon similar to a folder. Double click on it to spawn a new `SimGroup`. Make sure you have created at least 5 
`Coin` objects and 1 `SimGroup`. You can check this by clicking on the **Scene tab** under the **Scene Tree** menu and 
check the list of objects there.

Now you noticed how the `SimGroup` looked like a folder? This is because it is used to store other objects! You can drag 
and drop the `Coin` objects onto the newly created `SimGroup` and they will be stored there. Do so now. Make sure that 
you have only stored `Coin` objects in the `SimGroup`. In the _'Inspector'_ panel under the **Scene Tree menu** you will 
find a list of attributes you can set for the selected object. Select the `SimGroup` and rename it to _"Coins"_ do the 
same for all of the coin object, rename them to: _"Coin1", "Coin2" "Coin3"_... Etc.

## Victory Conditions
Did you read the guide to variables? if not then you should go ahead and 
[do it now](http://wiki.torque3d.org/scripter:variables). Now we want to add some sort of victory condition, lets say 
that when all the coins are gone, then we will stop the game. Change the onCollision function in `Coin.cs` to this:
{{< highlight tscript >}}
function Coin::onCollision(%this, %obj, %col, %vec, %len)
{
   %obj.delete();
   if(Coins.getCount() <= 0)
   {
      commandToClient(%col.client, 'ShowVictory');
   }
}
{{< / highlight >}}

`if(Coins.getCount() <= 0)` Very simple, if there is less or equal to 0 coins left in the game, then we should shut the 
game down.

`commandToClient(%col.client,'ShowVictory');` this function sends a command to the client, telling it to show the 
victory message.

[You should read the networking commands guide.](http://wiki.torque3d.org/scripter:server-and-client-communication).

Now as you have read in the networking commands guide, we will need a `clientCmd` function. We will define this in a 
`commands.cs` file. First, we need to add an `exec` call so our new script will get loaded. We will do this in 
`Scripts/client/init.cs`. Around line 80 after the:

{{< highlight tscript >}}
exec("./serverConnection.cs");
{{< / highlight >}}

Add:

{{< highlight tscript >}}
exec("./commands.cs");
{{< / highlight >}}

Now create a new file called `commands.cs` in `Scripts/client` and write the following function in it:

{{< highlight tscript >}}
function clientCmdShowVictory(%score)
{
   MessageBoxOK("You Win!",
      "Congratulation you found" SPC %score SPC "coins!",
      "disconnect();" );
}
{{< / highlight >}}

[You should read the guide about string concatenation in Torque.](http://wiki.torque3d.org/scripter:string-concatenation) 

This function simply shows a message box telling the client that he has won, and the amount of coins he collected. When 
he presses ok, the game ends and he returns to the mainmenu. You will notice that we never passed the score to this 
command from the server. That is what we are going to fix now.

## Point System
Now we are going to make use of some global variables to keep track of the amount of coins we've collected. 
Back in `Coins.cs` add
{{< highlight tscript >}}
$CoinsFound = 0;
{{< / highlight >}}

To the top of the file. And now change the `onCollision` callback to this:

{{< highlight tscript >}}
function Coin::onCollision(%this, %obj, %col, %vec, %len)
{
   %obj.delete();
   $CoinsFound++;
   if(Coins.getCount() <= 0)
   {
      commandToClient(%col.client, 'ShowVictory', $CoinsFound);
   }
}
{{< / highlight >}}

Now, everytime we pickup a coin the `CoinFound` global will raise by one. When there there is no more coins in the world 
the client will win the game when a pop-up shows him how many coins he gathered.

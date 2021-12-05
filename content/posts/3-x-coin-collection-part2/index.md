---
title         : "3.X Coin Collection Part 2"
description   : ""
toc           : true
cover         : "amazon-branches-dawn-975771.jpg"
coverAlt      : "A simple Coin Collection tutorial for Torque3D beginners."
tags          : [ game, beginner, coin-collection, torquescript, t3d-3 ]
---

## Visual effects
**As you may have noticed**, I love particles. So we will work with particles and 
emitters in this section where I will cover dynamic instancing of objects.
What we will create is a small explosion of particles when you pick up a coin.
First we want to define a new datablock for the particle emitter we will be using later.
In `art/datablocks` create a new file `CoinDatablock.cs`
To execute datablocks you should do it in the `datablockExec.cs` file in the same folder.

The CoinDatablock.cs file should have the following code:


{{< highlight tscript >}}
datablock ParticleData(CoinParticle : DefaultParticle)
{
};

datablock ParticleEmitterData(CoinEmitter : DefaultEmitter)
{
   particles = DefaultParticle;
};

datablock ParticleEmitterNodeData(CoinNode  : DefaultEmitterNodeData)
{
   timeMultiple = 1.0;
};
{{< / highlight >}}

As you can see we define two new **datablocks** in this file, a 
`ParticleEmitterDatablock` for creating a new _particle emitter_. 
_Particle emitters_ does not have a place in the world by themselves 
tho, they only emit particles they need a node to know where to emit 
particles from. Therefore we need a datablock for a _particle emitter 
node_ aswell. What you probably will notice here is when we define the 
name of the datablocks, we write a colon and then another name. **What 
does this mean?**

This means that our new datablock, inherits values from another datablock. 
It makes a copy of the other datablock and lets you edit the values which 
will only affect the new datablock.

**Another thing that is important to note**, is that `CoinEmitter` references 
`CoinParticle`. Which is why it is important that `CoinParticle` is defined 
before `CoinEmitter`.

## Datablocks

You have already seen an example of how a datablock is written. But I wrote 
the `CoinParticle` for you anyway:

{{< highlight tscript >}}
datablock ParticleData(CoinParticle : DefaultParticle)
{
   lifetimeMS = 1000;
   gravityCoefficient = 0;
   dragCoefficient = "2";
   
   sizes[0] = 1;
   sizes[1] = 1;
   sizes[2] = 1;
   sizes[3] = 1;
   inheritedVelFactor = "0";
};
{{< /highlight >}}

You can copy and paste that to the `CoinDatablock.cs`.
**Now a little task for you**, I want you to create your own emitter. You 
can either use the `ParticleEditor` in the World Editor or try different 
values by writing them in script and then see how it looks in-game.

If you decide to script it in hand, then I wrote a small list of 
important `ParticleEmitter` field values.
If you think this is a waste of time, you can find my `ParticleEmitterDatablock` 
below, however I can ensure you, it is not a waste of time.

{{< highlight tscript >}}
datablock ParticleEmitterData(CoinEmitter : DefaultEmitter)
{
   particles = CoinParticle;
   ejectionPeriodMS = "10";
   ejectionVelocity = "4.167";
   ejectionOffset = "0.625";
   thetaMax = "180";
   softnessDistance = "1";
   lifetimeMS = "200";
};
{{< /highlight >}}

An important thing to note here is that I set the `softnessDistance` to 1.
It defaults to `1000`, so it is important to set this down to something 
reasonable, or else your particles will look transparent when the background 
is not kilometres away.

## On-the-fly instancing
Lets put these new datablocks to good use. We want some visual feedback to 
tell us that we have picked up a coin.

To spawn a new Emitter we will use the `new` operator. It works like this:

{{< highlight tscript >}}
   %emitterNode = new ParticleEmitterNode(){
   datablock = CoinNode;
   emitter = CoinEmitter;
};
{{< /highlight >}}

Remember it is the node not emitter we want to spawn, then we set the emitter 
inside the "constructor". What i call the constructor is the variable 
definitions inside the two brackets { and }.

This is where we define what datablock to use, the emitter and anything else 
we want to do with the newly created object.

We need to give this new object a position in the world.
To get the position of an object you would call

{{< highlight tscript >}}
%obj.getPosition();
{{< /highlight >}}

And to set the position of an object you would assign it, like this

{{< highlight tscript >}}
%obj.position = "x y z";
{{< /highlight >}}

Can you figure out where to spawn the new emitter? And how to set its position 
(one answer can be found below[^hint]).

_Hint: look in Coin.cs_

[^hint]: Hint {{< highlight tscript >}}
function Coin::onCollision(%this, %obj, %col, %vec, %len)
{
   %emitterNode =  new ParticleEmitterNode(){
      datablock = CoinNode;
      emitter = CoinEmitter;
      position = %obj.getPosition();
   };
   %obj.delete();
   
   $CoinsFound++;
   if(Coins.getCount() <= 0)
   {
      commandToClient(%col.client,'ShowVictory',$CoinsFound);
   }
}
{{< /highlight >}}
Simply create the new emitter, and give it the same position as the coin.

### Schedules and cleanup
If you run into a couple of Coins, and it is all working properly, 
then if you open the world editor you will notice that the emitters 
is still there even tho they stopped emitting particles (given that 
you gave the `ParticleEmitter` a lifetime) if you didn't you will 
see that they keep emitting particles.

We want to fix that! So I will introduce you to a very important feature in TorqueScript. 
[Schedules](http://wiki.torque3d.org/scripter:schedules-and-timers).

You can use a schedule to delete the emitter after some time.

The schedule syntax is:
{{< highlight tscript >}}
%obj.schedule(int timeMS, string method, string args...);
{{< /highlight >}}

Or if you are not calling it on an object:
{{< highlight tscript >}}
schedule(int timeMS, int objectID, string method, string args...);
{{< /highlight >}}

We can use this to delete the emitter after we spawn it:
{{< highlight tscript >}}
%emitterNode =  new ParticleEmitterNode(){
   datablock = CoinNode;
   emitter = CoinEmitter;
   position = obj.getPosition();
};
%emitterNode.schedule(200, "delete");
{{< /highlight >}}

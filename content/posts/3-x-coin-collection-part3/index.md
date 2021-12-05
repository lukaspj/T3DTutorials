---
title         : "3.X Coin Collection Part 3"
description   : ""
toc           : true
cover         : "amazon-branches-dawn-975771.jpg"
coverAlt      : "A simple Coin Collection tutorial for Torque3D beginners."
tags          : [ game, beginner, coin-collection, torquescript, t3d-3 ]
---

## Gearing the game for multiplayer
So you want to play this brand new game with your friends? Well then there is a 
few things we need to fix. **Unfortunately**, I have deliberately made the game 
non-multiplayer compatible. Now lets see how this can be. You remember the 
`$CoinsFound` global which kept track of the player score? This global was 
defined in the `Coins.cs` file which resided in the `Scripts/server` directory, 
and was executed by the `scriptExec.cs` in the server folder. The `Coins.cs` is 
actually set on the server and the `onCollision` trigger, increasing the score 
is also happening on the server. So if there were more than one client they would 
have the same score! Maybe that is what you want but that is not what I would want 
for my awesome coin collecting game. So lets change this code.

Now lets begin, first I will tell you something about dynamic variables. 
TorqueScript has something called dynamic variables which is a very simple but very 
smart concept. Basically you can define any variable on any object by assigning it 
to a value.

{{< highlight tscript >}}
%obj.somedynVar = 2;
echo(%obj.somedynVar); //outputs "2"
%obj.TSisSpecial = "Is it now?";
echo(%obj.TSisSpecial); //outputs "Is it now?"
echo(%obj.tsisspecial); //outputs "Is it now?"
//(TorqueScript is not case sensitive either.)
{{< /highlight >}}

We can use this to keep track on how many coins each client has picked up!

{{< highlight tscript >}}
%col.client.coinsfound++; // Automatically starts at 0
{{< /highlight >}}

So what is this ClientGroup I talked about? The ClientGroup is a collection 
of all the clients who have joined the game. We can use this group to access 
all the clients and send global messages or iterate through them to find the 
winner.

## Implementing multiplayer support

Lets start with the simple things. A little task. Remember the 
`Scripts/client/commands.cs`? We need to edit this file and add a 
`clientCmdShowDefeat`. Create this function, you can use the `clientCmdShowVictory` 
as a template. We also need to edit the `Coin.cs` file. Change the `onCollision` 
function to something like this:

{{< highlight tscript >}}
function Coin::onCollision(%this, %obj, %col, %vec, %len)
{
   %emitterNode =  new ParticleEmitterNode(){
      dataBlock = CoinNode;
      emitter = CoinEmitter;
      position = %obj.getPosition();
   };
   %emitterNode.schedule(200,"delete");
   %obj.delete();
   
   %col.client.coinsfound++;
   if(Coins.getCount() <= 0)
   {
      %winnerClient = %col.client;
      foreach(%idxClient in ClientGroup)
      {
         if(%idxClient.coinsfound > %winnerClient.coinsfound) {
            commandToClient(%winnerClient, 'ShowDefeat', %winnerClient.coinsfound);
            %winnerClient = %idxClient;
         } else {
            commandToClient(%idxClient, 'ShowDefeat', %idxClient.coinsfound);
         }
      }
      commandToClient(%winnerClient,
      'ShowVictory',%idxClient.coinsfound);
   }
}
{{< /highlight >}}

The first thing you should notice is that we swapped the `$CoinsFound` out 
with `%col.client.coinsfound` so that now the score is on a per-client 
basis. Then we changed the `wincondition` out with a new iterative loop where 
we find the client with the highest amount of coins found and put him in the 
first place. All the clients who did not come in first place will get a 
defeat message. If someone has higher score than the former first place he 
will get a defeat message.

Now your first multiplayer game should actually be working! Try opening two 
instances of Torque, in one of the instances you press **"play"** then you tick 
the **"host"** check box to the left of the `Go` button. In the other instance 
you press `join`, `"Query LAN"` select the server that comes forth and join 
the game. Now you can compete with yourself about collecting most coins! Even 
better, you can host a LAN and let all your friends play your coin collection 
game with you! Give it a cool name and brag about it a little!

**But what can you improve?** Use your imagination! I will give you one last 
task tho, in the above code, there can only be one winner. What happens if two 
persons have an equal amount of coins collected?
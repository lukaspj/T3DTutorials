---
title         : "Coin Collection Part 2"
description   : ""
toc           : true
cover         : "amazon-branches-dawn-975771.jpg"
coverAlt      : "A simple Coin Collection tutorial for Torque3D beginners."
tags          : [ game, beginner, coin-collection, torquescript ]
---

## What is Torque 3D?
Torque is a game engine, it is not based on graphical drag 'n' drop elements 
like Unity thus it is not as easy to get into and understand. There is no 'make 
game button' in Torque; you need to have an understanding of how to code, so 
if you are an absolute beginner at programming and have never written even a 
simple program then this might not be the right guide to start with.

## What can Torque do out of the box?

{{< highlight tscript >}}
// Test
/* test */

// exec("./serverConnection.cs");

%a;

datablock StaticShapeData( Coin )
{
   category = "TutorialObjects";
   shapeFile = "art/shapes/items/kit/healthkit.dts";
};

function Coin::onCollision(%this, %obj, %col, %vec, %len)
{
   %obj.delete();
   $CoinsFound++;
   if(Coins.getCount() <= 0)
   {
      commandToClient(%col.client, 'ShowVictory', $CoinsFound);
   }
}

function clientCmdShowVictory(%score)
{
   MessageBoxOK("You Win!",
      "Congratulation you found" SPC %score SPC "coins!",
      "disconnect();" );
}
{{< / highlight >}}

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

function clientCmdShowVictory(%score)
{
   MessageBoxOK("You Win!",
      "Congratulation you found" SPC %score SPC "coins!",
      "disconnect();" );
}
{{< / highlight >}}

{{< highlight tscript >}}
function Coin::onCollision(%this, %obj, %col, %vec, %len)
{
   %obj.delete();
   $CoinsFound++;
}
function Coin::onCollision(%this, %obj, %col, %vec, %len)
{
   %obj.delete();
   $CoinsFound++;
}

function clientCmdShowVictory(%score)
{
   MessageBoxOK("You Win!",
      "Congratulation you found" SPC %score SPC "coins!",
      "disconnect();" );
}

function clientCmdShowVictory(%score)
{
   MessageBoxOK("You Win!",
      "Congratulation you found" SPC %score SPC "coins!",
      "disconnect();",
       "disconnect();",
    "echo(%a + %b);",
     "disconnect();" );
}
{{< / highlight >}}

{{< highlight tscript >}}
if (%a < %b) {
  echo(%a);
}
if (%a < %b) {
  echo(%a);
}
{{< / highlight >}}

{{< highlight go "linenos=table,hl_lines=8 15-17,linenostart=199" >}}
// GetTitleFunc returns a func that can be used to transform a string to
// title case.
//
// The supported styles are
//
// - "Go" (strings.Title)
// - "AP" (see https://www.apstylebook.com/)
// - "Chicago" (see https://www.chicagomanualofstyle.org/home.html)
//
// If an unknown or empty style is provided, AP style is what you get.
func GetTitleFunc(style string) func(s string) string {
  switch strings.ToLower(style) {
  case "go":
    return strings.Title
  case "chicago":
    return transform.NewTitleConverter(transform.ChicagoStyle)
  default:
    return transform.NewTitleConverter(transform.APStyle)
  }
}
{{< / highlight >}}
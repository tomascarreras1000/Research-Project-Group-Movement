# Personal Research: Group Movement


[The web page](https://tomascarreras1000.github.io/Research-Project-Group-Movement/) and the [GitHub repository](https://github.com/tomascarreras1000/Research-Project-Group-Movement) where it is created from are dedicated to how to generate Group Movement for an RTS game.

***


## Who am I?

I am [Tomás Carreras](https://www.linkedin.com/in/tom%C3%A1s-carreras-a96a99177/) (GitHub username [*tomascarreras1000*](https://github.com/tomascarreras1000)), student of the [Bachelor's Degree in Video Games by UPC at CITM-TTC](https://www.citm.upc.edu/ing/estudis/grau-videojocs-bcn/). This content is generated for the second year's subject Project 2, under supervision of lecturer [Ramon Santamaria](https://www.linkedin.com/in/raysan/).


***


## What is included in this research?


 - [What should you know about *Group Movement* before starting?](#what-should-you-know-about-group-movement-before-starting)
 - [Have you seen it in games?](#have-you-seen-it-in-games)
 - [What is the approach to this problem in this research?](#what-is-the-approach-to-this-problem-in-this-research)
 - [Can it be improved?](#can-it-be-improved)
 - [How can YOU do it?](#how-can-you-do-it)
 - [How can you continue improving?](#how-can-you-continue-improving)
 - [Links to documentation](#links-to-documentation)


***


## What should you know about *Group Movement* before starting?

### Introduction

In games you usually have to control a unit, being a single character or multiple units, which means they have to move from point A to point B. In the case of RTS games, you have to move one or more units, which may be the same or different ones, across the map. Every unit has to react to other units, buildings, enemies, resources, etc. in order to move coherently and at the same time follow the player's orders. However, there may be some problems we have to be aware of, like units overlapping or pathfinding algorithms. These problems can have different approaches, which will be dealt with later in this research, what methods are popular in different games and we will genereate our own code so we can develop a group movement which can be applied to an RTS game.

### Objective

The objetive of this research is to implement group pathfinding. To reach this we'll analyse different group movement implented in RTS games and decide which one would work best for us and work around it. 


***


## Have you seen it in games?

### Age of Empires II

[![Age_of_Empires](https://user-images.githubusercontent.com/45202069/82712049-6d825f00-9c87-11ea-8071-9fe832140cfe.png)](https://www.youtube.com/watch?v=tbqvXp5WRdE)

In Age of Empires II, troops move to the position where the player has sent them by using A* algorithm. However, this movement depends of various factors, such as the number of units, the type of units, and (for some of them) the formation selected.
The moving function isn't exactly alike for all units: military units form up in a group in the formation the player specifies and, once in formation, they will move to their destination, unlike the villagers who just go to where they were sent to.
The speed of the group is also something that can vary depending on the speed of the individual units on the group (a group will move slower if it contains an elephant due to this unit's slow movement speed). Units avoid buildings and other obstacles but they can collide with other units, at least during a movement, not at the end of it.

### StarCraft and StarCraft II

[![StarCraft](https://user-images.githubusercontent.com/45202069/82712051-6fe4b900-9c87-11ea-8160-50bfe2efb1a8.png)](https://www.youtube.com/watch?v=0oJPPCaQeD4)

In StarCraft, units constantly check whether they can advance through their path, if at some point they can't, after a while it will recalculate a new path having in mind the where it got stuck last time and avoiding that.

[![StarCraft_II](https://user-images.githubusercontent.com/45202069/82712054-707d4f80-9c87-11ea-98e4-75c5b90fc562.png)](https://www.youtube.com/watch?v=vgkCx-1VUtU)

In StarCraft II, the units will collide with each other preventing overlapping and will each go to the specified location.

***

## What is the approach to this problem in this research?

### First Idea

As we implied in the introduction, moving troops in groups can be a real challenge challenge, and many games have to deal with it, specially in RTS, where there are many units that may have the same destination or that have to re-calculate this destination midways. Then, how do we make units cooperate with each other so they move coherently? The answer, of course, is pathfinding. Pathfinding is the technique of finding the best path from point A to point B. If we implement it correctly, together with individual unit movement and their behaviours related with the other entities, we can achive a good, coordinated group movement.

### Movement

First we need to get a unit from A to B when ordered to do so. Pathfinding, as said before, enables us to calculate it. We will use a tile-based greedy algorithm: A*. We will use a basic implementation of it.

A* gives us a path from A to B, but it takes a good amount of time for it to retrieve this path. When dealing with only one unit is not a problem, but as we icrease the number of entities, it becomes more complex, and it may take too much time. Therefore, we would need some optimizations to reduce the CPU load. However, due to the scope of this research and it is not the main topic, I will not go into detail. Just be aware that this is a problem you may have.

### Group Movement

When we move a single unit, we just have to give this entity the order of moving from point A to point B. However, when we have move more than one, this complicates a little bit, since only one of the units will be moving from A to B, the others will start from a different point of origin and should end in a position around B, but not exactly it to avoid overlapping. Nevertheless, bear in mind that all of them will be receiving the same instruction!

So a simple approach to group momevemnt is to select all the entities and then calculate how to get from A to B for the first one in the list. The others, however, will take into account that this spot has already been taken and will get as their destiny a surrounding tile, if walkable. And like this from this unit onwards. This will be done using pathfinding. This will keep the group together, with the same destination, but without overlaps in the end so the player can continue treating them as individuals.

### Approach

Now that we have a basic idea of the most important concepts, we are going to look deeply into the matter of discussion.

In group movement we could have 3 different approaches:

 - **Flow-based:** The crowd as a whole is the main focus, not each unit. Individuals are equal and behavioural factors are heavily reduced.
 - **Entity-based:** Every movement is determined by some global laws, which are enforced to the individuals of the group.
 - **Agent-based:** Autonomous intelligent individuals. Action-reaction is local to each individual based on a set of rules.

However, all of them refer to the same concept: **Steering behaviour**. In 1986 a computer model was developed to simulate coordinated animal motion, such as the movement of bird flocks. 

### Steering

First, it is important to give credit to the developer of this idea, Craig Reynolds, a software engineer,expert in artificial life and computer graphics. You can check his web page [here](https://www.red3d.com/cwr/).

When talking about steering, it is important to also introduce the concept of *Boids*, which is this  artificial life program that simulates the flocking behaviour of birds and is the short version of "bord-oid object". The rules applied in the simplest Boids world are as follows:

 - **Separation:** steer to avoid crowding local flockmates.
 
 ![Rule_separation](https://user-images.githubusercontent.com/45202069/82712602-a66f0380-9c88-11ea-9e9b-c83ade043935.png)
 
 - **Aalignment:** steer towards the average heading of local flockmates.
 
 ![Rule_alignment](https://user-images.githubusercontent.com/45202069/82712605-a66f0380-9c88-11ea-9592-ff3bb067d341.png)
 
 - **Cohesion:** steer to move towards the average position (center of mass) of local flockmates.
 
 ![Rule_cohesion](https://user-images.githubusercontent.com/45202069/82712606-a7079a00-9c88-11ea-8d21-c3e989a0ae07.png)
 
More complex rules can be added, such as obstacle avoidance and goal seeking.

The boids also have a region on which they are influenced by neighboring boids, this region is defined by an angle and distance, building a spherical field around each boid.

![boid](https://user-images.githubusercontent.com/45202069/82712764-13829900-9c89-11ea-8377-15c1bd051084.png)

To summarize, this concept could be defined as a set of rules that regulate the relationship between individuals of a group.

#### Behaviours

As said by Craig Reynolds himself, we can set mmany differen rules to keep our units moving in a structured way:

 - Simple behaviors for individuals and pairs:
   + Seek and Flee
   + Pursue and Evade
   + Wander
   + Arrival
   + Obstacle Avoidance
   + Containment 
   + Wall Following
   + Path Following
   + Flow Field Following
   
 - Combined behaviors and groups:
   + Crowd Path Following
   + Leader Following
   + Unaligned Collision Avoidance
   + Queuing (at a doorway)
   + Flocking (combining: separation, alignment, cohesion)
   
However, as you can see, this by itself could be a single topic, so I won't spend a lot of time on it: explaining each behaviour would be too much information for the scope of this project. But I recommend checking out the following [link](https://gamedevelopment.tutsplus.com/tutorials/understanding-steering-behaviors-leader-following--gamedev-10810) on the topic.

### Pathfinding

#### A*

Many games use as a baseline for pathfinding implementations A*, a tile-based algorithm. These games did not have to deal with as many individuals as later sequels and new ips, so it was not much of a problem.

#### Flow Field



***

## Can it be improved?

One of the ways to improve the code is to change A* for a more modern pathfinder. A* is very basic, and has a high impact on performance. Flow Fields is a modern pathfinding tech for large groups of units, used in SC2 for example. So we implementing a solution in this direction would make the code more optimized. Making our pathfinding efficient is key in order to have groups in movement, A* is only to do basic code.

Also, since I have talked about steering behaviours, they would improve the code a lot. If no steering behaviours are used, the movement could be heavily improved when implementing some of them, both in terms of life-like feeling and performance. What if the way we move our groups enables us to only make a single path request? And on the contrary, what if our pathfinding tech enables us to move any number of units with the behaviours we want? As you can see, we can improve on A to improve B, and the other way too.



***


## How can YOU do it?

*UNDER CONSTRUCTION*

***


## How can you continue improving?

*UNDER CONSTRUCTION*

***


## Links to documentation

Here are the links and references where I got all the information I used to create this research.

- [Gamasutra: *Coordinated Unit Movement*](https://www.gamasutra.com/view/feature/131720/coordinated_unit_movement.php?page=1)
- [Gamasutra: *Implementing Coordinateed Movement*](https://www.gamasutra.com/view/feature/131721/implementing_coordinated_movement.php?page=1)
- [Github repository](https://github.com/Danny0ner/Pathfinding-Optimization) and [web page](https://danny0ner.github.io/Pathfinding-Optimization/) on Pathfinding Optimizations by [Danny0ner](https://github.com/Danny0ner)
- [Hindawi: *Hierarchical Pathfinding and AI-Based Learning Approach in Strategy Game Design*](https://www.hindawi.com/journals/ijcgt/2008/873913/)
- [Lazy Foo' Productions: *Circular Collision Detection*](http://lazyfoo.net/tutorials/SDL/29_circular_collision_detection/index.php)
- [Introduction To Game Studies: *“Age of Empires II: Age of Kings” – Grouping and Formations*](http://blog.ocad.ca/wordpress/vism2b15-fw2011-01/2011/10/%E2%80%9Cage-of-empires-ii-age-of-kings%E2%80%9D-grouping-and-formations/?doing_wp_cron=1489083347.2696259021759033203125)
- [Gamasutra: *Group Pathfinding & Movement in RTS Style Games*](https://www.gamasutra.com/blogs/AndrewErridge/20180522/318413/Group_Pathfinding__Movement_in_RTS_Style_Games.php)
- [GDC Vault: *AI Navigation: It's Not a Solved Problem - Yet*](https://www.gdcvault.com/play/1014514/AI-Navigation-It-s-Not)
- [REYNOLDS engineering & design: *Research*](http://www.red3d.com/research.html)
- [Red Blob Games: *Introduction to the A* Algorithm*](https://www.redblobgames.com/pathfinding/a-star/introduction.html)
- [Jeremiah Warm - Game Design: *Coordinated Movement*](http://www.jeremiahwarm.com/coordinated-movement.php)
- [Crowd Flows](http://grail.cs.washington.edu/projects/crowd-flows/)
- [Reddit: *How do RTS games such as Starcraft 2 or Warcraft 3 handle multi-unit movement?*](https://www.reddit.com/r/Unity3D/comments/7v62t2/how_do_rts_games_such_as_starcraft_2_or_warcraft/)
- [Wikipedia: Crowd simulation](https://en.wikipedia.org/wiki/Crowd_simulation)
- [Game Ai Pro: *Crowd Pathfinding and Steering Using Flow Field Tiles*](http://www.gameaipro.com/GameAIPro/GameAIPro_Chapter23_Crowd_Pathfinding_and_Steering_Using_Flow_Field_Tiles.pdf)
- [Leif Node: *Flow Field Pathfinding*](https://leifnode.com/2013/12/flow-field-pathfinding/)
- [How to RTS](https://howtorts.github.io/)
- [Envato tuts+: *Understanding Steering Behaviors*](https://gamedevelopment.tutsplus.com/series/understanding-steering-behaviors--gamedev-12732)
- [Journal of Computing and Information Technology: *Path Finding and Collision Avoidance in Crowd Simulation*](https://pdfs.semanticscholar.org/3cb8/5d6c5db83af451637607c01b4c9190decf61.pdf)
- [Game Ai Pro: *Hierarchical Architecture for Group Navigation Behaviors*](http://www.gameaipro.com/GameAIPro2/GameAIPro2_Chapter20_Hierarchical_Architecture_for_Group_Navigation_Behaviors.pdf)
- [REYNOLDS engineering & design: *Boids*](https://www.red3d.com/cwr/boids/)
- [Envato tuts+: *3 Simple Rules of Flocking Behaviors: Alignment, Cohesion, and Separation*](https://gamedevelopment.tutsplus.com/tutorials/3-simple-rules-of-flocking-behaviors-alignment-cohesion-and-separation--gamedev-3444)
- [TL.net: *The Mechanics of Starcraft 2*](https://tl.net/forum/starcraft-2/132171-the-mechanics-of-sc2-part-1)
- [Strike Tactics: *Starcraft 1 Pathfinding: A technical analysis*](http://striketactics.net/devblog/starcraft-1-pathfinding-technical-analysis)
- [Code of Honor: *More Band-Aids: path-finding in StarCraft*](https://www.codeofhonor.com/blog/tough-times-on-the-road-to-starcraft#path-finding)
- [Code of Honor: *The StarCraft path-finding hack*](https://www.codeofhonor.com/blog/the-starcraft-path-finding-hack)
- [Gamedev.net: *Formations in RTS (A* pathfinding)*](https://www.gamedev.net/forums/topic/672751-formations-in-rts-a-pathfinding/)
- [Unity: *Unit local avoidance in RTS type games*](https://answers.unity.com/questions/790857/unit-local-avoidance-in-rts-type-games.html)
- [*StarCraft Unit Motion: Analysis and Search Enhancements* by Douglas Schneider and Michael Buro (link to download the article)](https://www.aaai.org/ocs/index.php/AIIDE/AIIDE15/paper/download/11573/11384)
- [Wikipedia: *Boids*](https://en.wikipedia.org/wiki/Boids)
- [Wikipedia: *Navigation mesh*](https://en.wikipedia.org/wiki/Navigation_mesh)
- [Rich Geldreich's Tech Blog: *On Age DE's pathing/movement*](http://richg42.blogspot.com/2018/02/on-age-des-pathingmovement.html)
- [ResearchGate: *Flocking behaviour of group movement in real strategy games*](https://www.researchgate.net/publication/282379016_Flocking_behaviour_of_group_movement_in_real_strategy_games)
- [O'Reilly: *Chapter 4. Flocking*](https://www.oreilly.com/library/view/ai-for-game/0596005555/ch04.html)
- [*Visibility graphs* by Haarika Koneru (presentation)](http://www.cs.kent.edu/~dragan/ST-Spring2016/visibility%20graphs.pdf)
- [Warhammer Guide: *Dawn of War: Stances*](http://warhammer-guide.ru/wiki/Dawn_of_War_Stances.html)

Other Researches:

- Group Movement in RTS by Albert Garcia
     - [GitHub repository](https://github.com/Ap011y0n/Group-Movement)
     - [Web page](https://ap011y0n.github.io/Group-Movement/)
 - RTS Group Movement by Sandra Alvarez
     - [GitHub repository](https://github.com/Sandruski/RTS-Group-Movement)
     - [Web page](https://sandruski.github.io/RTS-Group-Movement/)
- Group Movement by Aitor Simona
     - [GitHub repository](https://github.com/AitorSimona/Research_GroupMovement)
     - [Web page](https://aitorsimona.github.io/Research_GroupMovement/)
- Group Movement in a RTS game by Marc Latorre
     - [GitHub repository](https://github.com/marclafr/Research-Group-Movement-RTS-)
     - [Web page](https://marclafr.github.io/Research-Group-Movement-RTS-/)
- Group Movement by Yessica Servin
     - [GitHub repository](https://github.com/YessicaSD/CITM_2_Research_GroupMovement)
     - [Web page](https://yessicasd.github.io/CITM_2_Research_GroupMovement/)


# Ecstasy

Sparse-set based ECS for Unity. Simple, flexible and fast.

Core part of architecture that I'm developing for all our future games, **of any scale**.

[![twitter](https://img.shields.io/twitter/follow/_neonage?style=social)](https://twitter.com/_neonage)\
[![discord online](https://img.shields.io/discord/830405926078644254?label=Open%20Labs&logo=discord&style=social)](https://discord.gg/uF3sJFMA2j)

## Features
* Burst + Jobs direct support
* Stupendously simple architecture
* As simple as for-loop iteration
* Data is tightly packed, contiguously per-type
* No Sync-Points, No Events, No Bitmasks, Barebones!
* Total control and freedom

Can be used outside of Unity, with a slight modification.

### Downfalls
No multiple components of the same type on a single entity.\
Interfaces and polymorphism (`world.Pool<IBase>`) is not yet supported, I've got an idea and working on it.

The underlying architecture uses pointers to access and change shared data directly.\
While it is super fast and works perfectly on main-thread,\
In Jobs, this might circumvent the Safety System and cause ***Race Conditions*** when systems read-write on the same data.\
We need to extensively test this and find a solution.\
https://docs.unity3d.com/Manual/JobSystemSafetySystem.html
>Job System solves this by sending each job a copy of the data it needs to operate on, rather than a reference to the data in the control thread. This copy isolates the data, which eliminates the race condition.

## Examples
https://user-images.githubusercontent.com/29812914/167914219-6b81ab7f-ecee-4e79-ba83-fa063a8b5672.mp4

## Motivation? Why yet another ECS!
Every existing implementation I've seen is utterly overcomplicated, heavy, hard to understand and work with.

Unity's ECS is based on the most difficult architecture — Archetypes.\
The learning barrier is **colossal**, with tons of pitfalls, and so many core game features missing and never released.\
It just doesn't suit ***existing*** Unity Ecosystem (unless remade as a brand-new DOTS engine).

We, instead, simply use **Sparse-sets** for iterating over contiguous data array:\
![Sparse Sets](https://i.imgur.com/Cy2TC4s.png)

It fits perfectly within Unity, works with any components and familiar workflows.

Our refreshing approach might unlock a true game-design potential, with endless amount of new opportunities — technical and gameplay-wise. The ideas that I've never thought of before, now ~ flow to me naturally. It is a joy to program with :)

\**being too ecstatic*\*

Still, I need to proof this concept on a real project and find all the downfalls of this implementation, as there is no one-fit silver bullet!

## Notice
During development of `Open Labs Core` systems, this tool won't be shipped as a UPM package.\
Use it as a pluggable asset, and modify it for your project needs.\
Get .unitypackage on Releases page.\
Pull Requests, Ideas and Discussions are very welcomed!

## Usage & Quirks
Components are refered to as **Data**

```csharp
var world = World.Default;
     
// Get the required data pools:
var gravityPool = world.Pool<CustomGravity>();
// Loop over smallest pool when querying for multiple types
var (trsPool, bodyPool) = world.Pool<TRS, SimpleRigidbody>(out var smallestPool);

// foreach (var ID in smallestPool)
for (int i = 0; i < smallestPool.count; i++) 
{ 
    var id = smallestPool.GetEntityID(i);
    ref var trs = ref trsPool[id]; // get by ref!
    // ...
}
```
```csharp
var entity = world.CreateEntity(); // or world.GetEntity(gameObject)
world.AddData(entity, new TRS(transform), new SimpleRigidbody());
```

You can optionally use `ushorts` for IDs with `ENTITY_SHORT_ID` define,\
We use 0 value instead of -1 for unassigned entities, thus - the [0] data slot is reserved to null entity.

Use `GameObjectEntity` to auto-link attached MonoBehaviours:\
![GameObjectEntity](https://i.imgur.com/jMPy9vM.png)

Optional: inherit from `SystemBehaviour` and attach it on game-object. It'll use a coroutine update loop.

## What's Next?
Make actual games with it.\
I'm extremely interested in researching complex logic and deep behaviour systems — \
Emergent & engaging AI, bosses, game flow, world interactions, dynamic soundscape, \
Characters ala Mario Odyssey, Zelda BOTW, Sekiro, Jak, It Takes Two.. just to name a few :)

So yeah, I'm eagering to make a 3D Platformer now.\
I will be sharing the progress on [our discord channel](https://discord.gg/uF3sJFMA2j).

## Research (**help wanted!**)
We need to design a struct-based, ***visual data-oriented*** \
"State Machine/Behaviour Tree/HTN/GOAP/Planner/**Flow Graph**" hybrid that works with **Jobs**!

The idea is to separate Design from Code, provide visual debugging and runtime graph editing (in Editor and Player builds!).\
In the same fashion as Ecstasy — simple stupid and powerful.\
Here's a concept mockup:
![image](https://user-images.githubusercontent.com/29812914/174079744-697efd9f-c80a-443d-bbf9-1af160cce264.png)


## Community
More info and discussions on our server\
[![join discord](https://user-images.githubusercontent.com/29812914/121816656-0cb93080-cca7-11eb-954a-344cfd31f530.png)](https://discord.gg/uF3sJFMA2j)

## TODO
* Test for Job Safety System and Race Conditions
* Test interfaces in Jobs
* Implement Entity **Versioning?** (**!**)
* Support scenes loading
* More Examples & Showcases

## Roadmap
* Entity Debugger
* Runtime Debugging in Build
* Any-Data Authoring in Inspector
* Multiple Worlds + Serialization

## Futures Future
* Battle-test in real world production
* Streamable Game Worlds
* Data & Commands Journaling
* Networking via Unity Transport Layer (a secret prototype!)
* Determenistic Simulation Rollback & Replays (ala Overwatch)


## References
https://david-colson.com/2020/02/09/making-a-simple-ecs.html \
https://github.com/SanderMertens/ecs-faq \
https://geeksforgeeks.org/sparse-set/

Trick to making a NativeArray view of a managed array (or any pointer)\
https://github.com/stella3d/SharedArray


## Mentions
**Slug Glove** - the funkiest devlog channel on action-platformer games\
https://youtube.com/c/SlugGlove/

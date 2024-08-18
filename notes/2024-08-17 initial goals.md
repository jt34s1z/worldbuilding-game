Initial Goals
=============

I won't focus on the graphical side to start. I want to create a representation for
everything first, and then work on how best to render after the "world" already exists
in code. My interest and motivation is on the implementation of the world and simulating
it efficiently. I've played games like Minecraft and Vintage Story, but the concept of
events only occurring in "chunks" nearby the player is not the route I want to go on,
and I also want to support fast-forwarding the simulation, so that a rich and deep world
history can be simulated; it might even be part of the player game play, where the
player is absent from the world for some period, and the world changes and they jump
ahead to some point in the future. I'll need to experiment a lot to figure out a good
way to implement this all.

*********

In the past, I considered splitting the simulation of actors (NPCs) into two ways: 1) if
they are near the player (being rendered), and 2) if they are not being rendered. If the
world contains a large number of actors, most of them won't be rendered, so we don't need
to simulate them traveling along a route for example. The idea is for non-rendered actors
to be assigned travel routes, tasks, and other events for a given period (a day), and only
compute the outcome instead of simulating them in real time, unless they need to be
rendered. This seems straight forward in principle, but the actual implementation will
vary depending on the types of events and interactions between actors that need to be
supported.

For example, suppose a world contains a city, and a camp of bandits, how do we handle
simulating an event where the bandits attack the city, disrupting the normal outcome
for the simulated period? One possible way of handling this is to fallback to simulating
in real time, as if they are being rendered, and running performance tests to see if
this is viable. There might be a third type of simulation for this category, because
the actors don't need to be rendered, but the outcome of the events and interactions
isn't known ahead of time. There might be some way to "optimize" the simulation since
it's off screen, to use less resources than if it was being rendered.

*********

My goal is to support simulating a massive world, with hundreds of thousands of actors,
simulating macro events like interactions between populations, and a full economy with
actors trading and producing goods, instead of shops and resources simply being "spawned
in" for the player to utilize.

*********

To start out, actor behavior will be simple: performing a set of tasks each day,
traveling along a route to specific locations for each task. To simplify path finding,
actors will simply follow existing paths whenever possible. In a rural setting, this
could simply be compacted soil clear of foliage. There might be a mechanic for compacted
dirt paths being created as actors travel over them, or they might be intentionally
created in the world by actors. In a city setting, paths will be mostly static, as paved
road ways surrounding buildings.

For "routine" behaviors like daily chores, only need to be simulated once, and the
outcome can be cached, so non-rendered simulation simply applies the cached outcome.
These "routine" behaviors are "deterministic events", which can be simulated quickly.
However, to handle random, non-deterministic events, either from random actor behaviors,
needing to be rendered, or from a player interaction, the routes of actors must be
tracked and queryable, to flag the actor to be simulated in real time or in finer
detail, instead of using the cached behavior outcome.

This would also apply to random scenarios like apply illness or injury; before an actor
"starts" their day, an "RNG check" would be made if some deviation to their "routine"
will occur, which will also flag the actor for fine-detail simulation, to account for
the "RNG" even. In addition, the RNG should be "deterministic", in the sense that it's
all based off a seed that evolves each day, so doing two simulations from the same saved
initial state will result the same final output state.

In summary, an actor in a rural setting will usually follow a daily routine set of
behaviors, the output of which can be cached and quickly simulated each day. However,
a number of things might interrupt this process, like: 1) being rendered or interacting
with the player 2) interacting with another actor's random event 3) a random scenario
affecting the actor like illness. In case 2, it might even be part of the other actor's
"routine", for example a traveling merchant doing trading.

One thing that this approach doesn't handle is a chaotic setting like a city. Such a
large density of actors, with overlapping routes and "routines", it makes sense to
handle crowded areas with special logic. For many actors traveling on roads and paths,
maybe we could compute a rough crowd density over time, and apply a slow down in travel
speed as it gets higher; that way, if the crowd needs to be rendered when the player is
nearby, the slower speed could be visualized by people slowing down to avoid bumping
into each other. Or maybe that doesn't make sense. Correctly handling a chaotic setting
might work if we build a real time simulation first, and try to work out how to improve
efficiency by caching routines and behaviors second.

I envisioned cities as being quite chaotic; random events like crime interrupting the
normal flow. I had an idea for implementing some sort of "noise" mechanic that operates
over a certain distance. If an actor yelled out or an explosion occurred, that might
attract other actors within a certain radius, maybe causing them to investigate. The
problem I envision with such a system is the overall world simulation performing quickly,
but then the latency randomly spiking when some random event occurs in the city, changing
the behaviors of many actors at once. In normal game play, this might not be that huge of
an issue, since the simulation only needs to run at "real time" with the player, and this
slow down would only be an issue when trying to simulating in "fast forward" mode, which
might be acceptable.

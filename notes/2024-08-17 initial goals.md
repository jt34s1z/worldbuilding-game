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

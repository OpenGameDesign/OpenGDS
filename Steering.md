# OpenGDS Steering 0.1.0

The specification which defines steering behaviors and how they can be used to create intelligent autonomous characters. This specification depends on the Space, Path, and Core specifications.

### Steering in the Real World
A good steering behavior system focuses on the agent - the thing you are trying to give autonomy to. That agent could be a vehicle, a bird, a person, or an ameoba. A nieve steering behavior system provides vectors to the agents to adjust the acceleration by, but it may ignore the turning speed of the agent, the agent's ability to move in one than more direction - or any direction! A vehicle like a tank has two inputs, one for each tread. A bird has a forward motion basically constantly but can alter its path by turning depending on how fast it's going. A person depending on how fast they're moving can turn and also move to the side. Possibly stopping quickly, and once stopped moving in any direction. An ameoba in water can change it's direction at will, it doesn't face a certain way. 

### Steering in the Virtual World
The scenarios above can be described simply by defining a function which determines how much an agent can change their direction based on their current velocity. An ideal function defines more than one available direction change so the steering behavior system can determine which one results in a more intelligent move.


### Simulating Human Agents
Humans can focus on one thing at a time. We can quickly toggle between a few things for a short amount of time when those things are really important. Think about avoiding a crash on the highway... you want to avoid the vehicle in front of you but also try to not turn into cars beside you. In the virtual world we could set an importance level where the agent simply has to evaluate all targets and do the most important one at that moment for a small amount of time before re-evaluating the target. Humans also deal with sunk cost - when we focus on one target it may be hard to switch from that target, unless another target is that important we need to completely switch away. In the virtual world this could be described with a scalar and addend for flexibilty (when s=1,a=0.0001 the new target simply needs to be slightly more important).

### Splitting or Joining Targets
In a highly dynamic environment you might want to treat all targets equally. For example if you're in a gokart trying to get items and avoid obstacles you want to make a movement decision based on all the targets you see - in this scenario all targets are looked at individually. Conversely you might want to have a hierarchy for the targets. A hierarchy could allow you to define good and bad targets, and that you want to avoid bad targets at all cost and only look at good targets when you're safe. The joining of targets would output a single target. Either one target was selected as the most important, or the targets are averaged in such a way that agent decisions are holistically a good idea.

### Steering Behavior System
This system in the simplest form would tell the agent how to modify their movement to best achieve their goals. A nieve implementation defines the force/acceleration the agent should use to update it's velocity. Alternatively the system could define the target direction and target velocity magnitude. Ideally the agent has a function which takes the current state and generates a set of possible ranged moves. The agent can also take one of these ranged moves and apply it to it's state and also provide insight on how these moves will change the state of the agent so we know which move best suits the agent's goals.

### Goals
In the end the goal is to define a flexible system that is highly reusable for any type of agent. The way an agent moves is defined by the user and the system is given enough information about the agent, it's goals, and it's surroundings so that it can make a move.

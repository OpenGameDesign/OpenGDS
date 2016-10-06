# OpenGDS

OpenGDS (or Open Game Design Specifications) is a collection of specifications which provide generic solutions in design and implementation to several problems faced in the game programming world.

Knowledge you gain from learning the specifications will be transferable as implementations for various languages are created. The specifications will be used as documentation for the implementing projects, provide the API definition, and the equations and algorithms necessary for implementation.

### Goals

To offer `generic`, `forward compatible`, and `powerful` solutions to problems in game programming and provide `designs` which can be used across languages for easy `transitioning` between languages and codebases.

In more detail:

- Provide design solutions that can work on existing & future codebases.
- Provide design solutions that are powerful - requires the user to only specify the parameters - and have the solutions do all the heavy lifting.
- Provide specifications that are ready to be implemented.
  - They start with enough high-level explanation that a non-programmer can learn the topic.
  - In the middle a contract (API) is developed and documented.
  - They end with enough low-level details (equations, basic algorithms) so there isn't anything left unsolved for implementing libraries.
- Provide a unified design which allows a single documentation source that multiple libraries across different languages can all share.

### Concerns

- These specifications provide powerful & generic solutions which inevitably adds overhead when compared to implementing a minimal solution to your problems.

### Structure

A specification has the following structure:

- **Title**
  - Short description of the topic which should be as universally understood as possible.
- **Introduction**
  - Sets specification expectations.
- **Definition**
  - Long description of the topic, includes other names and related topics.
- **Problem Statement**
  - List of challenges to be solved.
- **Goals**
  - The features for the specification and a set of solutions to the problems.
- **Concepts**
  - Words important to understanding how the specification is going to solve the problem.
- **Design**
  - Using the concepts, define structures and algorithms necessary to meet the goals.
- **API**
  - A language-agnostic definition of classes and services in the spec.
- **Additional API**
  - Possible functionality to be added to the implementations.
- **Implementation**
  - A detailed list of all equations and algorithms necessary to implement the spec.

### Contributing

You can contribute to defining these specifications by catching up with the current state of the specification and then filing an issue in the correct project. These issues will be where the feature evolves and is integrated into the specification.

### Specifications

#### Math [v0.1.0](Math.md)

The math specification which defines data types, their calculators, and other foundational mathematical structures.

#### Easing

The specification which defines easing functions and their types: static, procedural, decorated, and scaled.

#### Path

The specification which defines paths as functions for generating data points based on other data points. This specification is dependent on the Math specification.

#### Space

The specification which defines how entities in space can be organized and searched. This specification is dependent on the Math specification.

#### Steering

The specification which defines steering behaviors and how they can be used to create intelligent autonomous characters. This specification depends on the Space, Path, and Math specifications.

#### Animation

The specification which defines how to animate any attribute on any object using the traditional animation functions like playing, queueing, pausing, resuming, and transitioning. This specification depends on the Path and Math specifications.

#### Particle

The specification which defines the ways to define systems responsible for generating and animating particles over their lifetime. This specification depends on the Path and Math specifications.

#### Networking

This specification defines the structures and services involved in communicating, synchronizing, and interpolating game state (events and character updates) in any game type. This specification depends on the Path and Math specifications.

### Possible Specifications

#### StateMachine

A specification which defines how to manage game states, the conditions which cause a transition, and the events that are triggered when a state is entered, is active, and is left.

#### PathFinding

A specification which defines ways of finding a path from one point to another in a world. A simple interface to the world defines the nodes, which travelers can be on the nodes, the moves available from one node to another, how much it "cost" to move from one node to another, and other variables on a node or move that may affect its selection in the path. Different algorithms and heuristics are defined to allow the user to use which pathfinding algorithm is most suited for their game. Other features include caching paths for quick reuse, setting limits on how much time or resources are used to find a path, allowing the user to modify or cancel a path, simplifying paths into fewer nodes/moves, provide a  prioritized queue to handle large pathfinding requests.

#### Input

A specification which defines ways to handle, track, and utilize user input. This specification would aim to provide a generic implementation for input, combinations (^^vv<><>BA), cumulative (useful for networking), and state transforming (useful for replays and networking).

#### Views

A specification which defines ways to keep your rendering code separate from your game logic. This simplifies networking code (through decoupling), enables degradation in implementation based on whats supported by their machine, and allows you to reuse your code with a different graphics library.

#### UI

A specification which describes how user interface elements work (events), how to render them, how to create themes, manage their state, and provides definitions of commonly used elements and how they behave.

#### Tasks

A specification which describes how to offload game logic or background rendering to threads. These tasks are small items which can trigger other tasks.

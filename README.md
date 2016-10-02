# OpenGDS

OpenGDS (or Open Game Design Specifications) is a collection of specifications which provide generic solutions in design and implementation to several problems faced in the game programming world. These specifications are on topics unrelated to graphics & rendering - as there are existing specifications around them.

Knowledge you gain from learning the specifications will be transferable as implementations for various languages are created. The specifications will be used as documentation for the implementing projects as well as provide the API definition - and the necessary equations and algorithms.

There exists a **core** spec which the others rely on - this spec merely defines concepts necessary for the other specs to operate generically. Much like a library has a dependency to fulfill same basic utility, OpenGDS has a core spec which lays the groundwork for the other specs.

### Goals

- To provide specifications that are ready to be implemented. 
  - They start with enough high-level explanation that a non-programmer can learn the topic.
  - In the middle a contract (API) is developed and documentated.
  - They end with enough low-level details (equations, basic algorithms) so there isn't anything left unsolved for implementing libraries.
- To provide a unified design which allows a single documentation source that multiple libraries across different languages can all share.


### Structure

A specification has the following structure:

- **Title**
  - Short description of the topic which should be as universally understood as possible.
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

#### Math

The math specification which defines data types, their calculators, and other foundational mathematical structures.

#### Path

The specification which defines paths as functions for generating data points based on other data points. This specification is dependent on the Math specification.

#### Easing

The specification which defines easing functions and their types: static, procedural, decorated, and scaled.

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

# OpenGDS

OpenGDS (or Open Game Design Specifications) is a collection of specifications which provide generic solutions in design and implementation to several problems faced in the game programming world.

Knowledge you gain from learning the specifications will be transferable as implementations for various languages are created.

There exists a core spec which the others rely on - this spec merely defines concepts necessary for the other specs to operate generically. Much like a library has a dependency to fulfill same basic utility, OpenGDS has a core spec which lays the groundwork for the other specs.

### Goals

- To provide specifications that are ready to be implemented. 
  - They start with enough high-level explanation that a non-programmer can learn the topic.
  - They end with enough low-level details (equations, basic algorithms) that there isn't anything left unsolved for implementing libraries.
- To provide a unified 


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
- **Design & Implementation** 
  - Using the concepts, define structures and algorithms necessary to meet the goals.

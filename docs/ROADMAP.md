# Ludus Roadmap

Ludus is being developed through incremental milestones. Each milestone establishes new capabilities while maintaining the framework's goals of modularity, extensibility, and clear architectural boundaries.

## Milestone 0.1.0 — Foundation

**Purpose:** Establish the architecture, design principles, and foundation of the framework.

**Goals:**

* define core domain concepts
* establish module structure
* document design principles
* create initial APIs

## Milestone 0.2.0 — Core Engine

**Purpose:** Create the minimal framework required to run a game.

**Goals:**

* define game lifecycle
* introduce game state representation
* implement turn management
* implement action validation
* define player interfaces
* establish state transition model

**Outcome:**

Ludus should be able to run a simple turn-based game without a UI.

## Milestone 0.3.0 — Game Modules

**Purpose:** Prove the framework supports independent game implementations.

**Goals:**

* create first example games
* demonstrate separate game implementations
* validate extension points
* improve developer experience

**Potential examples:**

* Tic-Tac-Toe — simple framework validation
* Checkers — board movement and rules
* Chess — complex rules and state management

## Milestone 0.4.0 — Rendering and Interaction

**Purpose:** Provide ways to visualize and interact with games.

**Goals:**

* define rendering abstractions
* create console renderer
* create GUI renderer
* support human input
* provide game visualization examples

## Milestone 0.5.0 — AI Framework

**Purpose:** Make agents first-class components.

**Goals:**

* define agent interfaces
* implement decision-making pipeline
* add search-based agents
* support evaluation functions
* create AI benchmarking tools

## Milestone 0.6.0 — Competition Platform

**Purpose:** Support AI competitions and tournaments.

**Goals:**

* agent versus agent matches
* tournament engine
* match statistics
* replay systems
* performance evaluation

## Milestone 1.0.0 — Stable Framework

**Purpose:** Provide a publicly usable framework.

**Goals:**

* stable APIs
* complete documentation
* example projects
* versioned releases
* contribution guidelines


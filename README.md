# Ludus

A modular Java framework for building turn-based board games and AI agents.

## Overview

Ludus is a reusable game framework designed to provide a common platform for building and experimenting with turn-based games.

The framework supports:

- multiple board games on a shared platform
- independent game implementations
- human and AI players
- rendering systems
- AI competitions and tournaments

Ludus provides the infrastructure. Individual games are built on top of the framework as separate modules.

## Vision

Ludus is designed around the idea of a shared game platform where different games, players, and agents can interact through common abstractions.

Conceptually:

```text
Ludus Framework
    |
    +-- Chess
    |
    +-- Checkers
    |
    +-- Classroom Games
    |
    +-- AI Agents
```

## Design Principles

Ludus follows these principles:

- separation of concerns
- composition over inheritance
- immutable game states
- engine-controlled state transitions
- clear API boundaries
- modular game development

## Project Structure

```text
ludus/
│
├── docs/              Design Documentation
│
├── ludus-core/        Core Framework
│
├── games/             Game Implementations
│
├── agents/            AI Players
│
└── ludus-examples/    Example Applications
```

## Development Status

Ludus is currently in early development.

**Current milestone:** 0.1.0 — Foundation

The current focus is:

- defining core domain concepts
- establishing module structure
- documenting design principles
- creating initial APIs
- setting up the development foundation

See the full development plan in the [Roadmap](docs/ROADMAP.md).

## Documentation

Project documentation is available in the [documentation directory](docs/).

Key documents:

- [Roadmap](docs/ROADMAP.md)
- [Development Guide](docs/DEVELOPMENT_GUIDE.md)
- [Domain Model](docs/DOMAIN_MODEL.md)
- [Execution Model](docs/EXECUTION_MODEL.md)


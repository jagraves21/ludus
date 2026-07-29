
# Ludus Development Guide

## Purpose

This document describes the development practices used when building Ludus.

Ludus is a software framework for creating turn-based games. Development decisions should prioritize:

- clear architecture
- maintainable code
- stable APIs
- reusable components

## Development Principles

### Design Before Implementation

Major architectural decisions should be documented before writing code. The goal is to understand:

- what responsibility a component owns
- what responsibility it does not own
- how it interacts with existing systems

### Keep Responsibilities Separate

Components should have clear boundaries. For instance:

- The domain model represents game state.
- Rules define game behavior.
- The engine manages execution.
- Players provide decisions.
- Renderers display information.

Avoid placing unrelated responsibilities into existing classes.

### Prefer Simplicity

Do not add complexity until it solves a real problem. Avoid adding:

- unnecessary abstractions
- premature frameworks
- dependencies without clear value

The framework should grow based on actual needs.

### Preserve Immutability

Game states should be treated as immutable snapshots. A move creates a new state rather than modifying an existing state. This supports:

- AI search
- replay
- debugging
- deterministic simulations

## Git Workflow

Work should be completed on feature branches. Branches should represent focused changes. For example,

```text
feature/domain-model-update  
feature/core-model  
feature/chess-rules
```

Commits should represent logical changes. Examples:

```text
define domain model  
implement coordinate  
add chess rules
```

## Documentation

Architectural changes should update documentation before or alongside implementation.

Important documents:

- [Roadmap](ROADMAP.md)
- [Domain Model](DOMAIN_MODEL.md)
- [Architecture](ARCHITECTURE.md)
- [API Design](API_DESIGN.md)

Documentation should explain why decisions exist, not just what the code does.

## Adding Features

Before adding a feature:

1. Identify which layer owns the responsibility.
2. Determine whether it belongs in the framework or a game module.
3. Prefer the smallest solution that satisfies the requirement.

## Long-Term Goal

Ludus should remain a framework that is:

- understandable
- extensible
- reusable
- stable


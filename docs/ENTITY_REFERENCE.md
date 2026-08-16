# Ludus Entity Reference

## Purpose

This document defines the detailed reference information for the core Ludus entities, services, and concepts.

The Domain Model and Execution Model describe architectural concepts and interactions. This document provides the detailed definitions of individual types, including their responsibilities, ownership boundaries, references, and examples.

## Core Concepts

### Game Definition Concepts

#### GameDefinition

##### Description

A [GameDefinition](#gamedefinition) represents the immutable definition of a game supported by the framework. A [GameDefinition](#gamedefinition) describes the static characteristics of a game, including the [GameObjectTypes](#gameobjecttype) and [LocationTypes](#locationtype) that define the objects and locations that may exist within the game. It provides the immutable structural information required by other framework components, such as the [RulesEngine](#rulesengine), to interpret and evolve [GameStates](#gamestate). A [GameDefinition](#gamedefinition) does not represent a running game instance, a specific [GameState](#gamestate), or the behavior used to apply game rules.

##### Owns

- [LocationType](#locationtype)
- [GameObjectType](#gameobjecttype)

##### Does Not Own

- [Location](#location)
- [Player](#player)
- [GameObject](#gameobject)
- [RulesEngine](#rulesengine)
- [GameConfiguration](#gameconfiguration)
- [GameState](#gamestate)

##### Example

```text
GameDefinition

    Chess

        GameObjectTypes:
            King
            Queen
            Bishop
            Knight
            Rook
            Pawn

        LocationTypes:
            Board Square
```

#### GameConfiguration

##### Description

A [GameConfiguration](#gameconfiguration) represents the immutable options selected when creating a new game instance. A [GameConfiguration](#gameconfiguration) references a [GameDefinition](#gamedefinition) and defines how that game is initialized. It is responsible for producing the initial [GameState](#gamestate). A [GameConfiguration](#gameconfiguration) may define the game variant, initial setup, player configuration, and other creation-time options. The resulting [GameState](#gamestate) is managed independently by the [RulesEngine](#rulesengine). A [GameConfiguration](#gameconfiguration) does not represent the current state of a game. Multiple [GameConfigurations](#gameconfiguration) may reference the same [GameDefinition](#gamedefinition) while producing different initial [GameStates](#gamestate) (e.g., standard chess vs Fischer random).

##### References

- [GameDefinition](#gamedefinition)

##### Creates

- [GameState](#gamestate)

##### Does Not Own

- [GameDefinition](#gamedefinition)
- [GameState](#gamestate)
- [Location](#location)
- [Player](#player)
- [GameObject](#gameobject)

#### LocationType

##### Description

A [LocationType](#locationtype) defines the immutable characteristics shared by all [Locations](#location) of the same type. A [LocationType](#locationtype) identifies what kind of location a [Location](#location) represents, such as a board space, inventory, territory, hand, deck, or discard pile. A [LocationType](#locationtype) contains the static attributes required to interpret [Locations](#location) of that type but does not represent a specific [Location](#location) within a game and does not contain mutable state.

##### Owns

- unique type identity
- immutable attributes

##### Does Not Own

- [Location](#location)
- [Player](#player)
- [GameObject](#gameobject)
- [GameState](#gamestate)

#### GameObjectType

##### Description

A [GameObjectType](#gameobjecttype) defines the immutable characteristics shared by all [GameObjects](#gameobject) of the same type. A [GameObjectType](#gameobjecttype) identifies what kind of object a [GameObject](#gameobject) represents, such as a chess piece, playing card, territory, unit, or resource. A [GameObjectType](#gameobjecttype) contains the static attributes and rules-related information required to interpret objects of that type. A [GameObjectType](#gameobjecttype) does not represent an individual object within a game and does not contain mutable state.

##### Owns

- unique type identity
- immutable attributes

##### Does Not Own

- [GameObject](#gameobject)
- [GameObjectState](#gameobjectstate)
- [GameObjectOwner](#gameobjectowner)
- [GameObjectController](#gameobjectcontroller)
- [GameState](#gamestate)

##### Example

```text
GameObjectType

    Iron Helm

    Immutable Attributes:
        Material: Iron
        Armor Rating: 5
        Equipment Slot: Head
        Maximum Durability: 100
```

### Runtime Concepts

#### GameInstance

##### Description

A [GameInstance](#gameinstance) represents a single occurrence of a game being played or a completed game that has been played. A [GameInstance](#gameinstance) represents one unique play session and maintains the runtime data associated with that session, including its configuration, history, and current [GameState](#gamestate). Multiple [GameInstances](#gameinstance) may be created from the same [GameDefinition](#gamedefinition), each representing an independent play session. A [GameInstance](#gameinstance) does not define game behavior or coordinate game execution. Game behavior is provided by the [RulesEngine](#rulesengine), while execution is coordinated by the [GameEngine](#gameengine).

##### Owns

- [GameConfiguration](#gameconfiguration)
- Initial [GameState](#gamestate)
- Current [GameState](#gamestate)
- [GameHistory](#gamehistory)

##### References

- [GameDefinition](#gamedefinition)

##### Does Not Own

- [RulesEngine](#rulesengine)
- [Move](#move)
- [MoveResolution](#moveresolution)
- [Player](#player)
- [GameObject](#gameobject)
- [Location](#location)

##### Example

```text
GameInstance

    Chess Match #1024

    GameDefinition:
        Chess

    GameConfiguration:
        Standard Chess

    RulesEngine:
        ChessRulesEngine

    Current GameState:
        Move 42 Position

    GameHistory:
        Initial GameState:
            Starting Chess Position

        Moves:
            e4
            e5
            Nf3
            Nc6
            ...
```

#### GameHistory

##### Description

A [GameHistory](#gamehistory) represents the sequence of accepted [Moves](#move) that transformed an initial [GameState](#gamestate) into the current state of a game. A [GameHistory](#gamehistory) contains the initial [GameState](#gamestate) and the ordered sequence of [Moves](#move) applied after that state. Intermediate [GameStates](#gamestate) are not owned by [GameHistory](#gamehistory) and are derived by applying [Moves](#move) through the [RulesEngine](#rulesengine). [GameHistory](#gamehistory) does not define game behavior and does not directly modify [GameStates](#gamestate).

##### Owns

- Ordered Sequence of [Moves](#move)

##### References

- Initial [GameState](#gamestate)

##### Uses

- [RulesEngine](#rulesengine)

##### Does Not Own

- Derived [GameStates](#gamestate)
- [MoveResolution](#moveresolution)

#### GameState

##### Description

A [GameState](#gamestate) represents an immutable snapshot of a game at a specific point in time. A [GameState](#gamestate) contains all mutable information required to completely describe the current position of a game, including the player whose turn it currently is. A [GameState](#gamestate) does not contain the history of how it was reached or the rules that determine how the game progresses. Applying a [Move](#move) never modifies an existing [GameState](#gamestate). Instead, the [RulesEngine](#rulesengine) produces a new [GameState](#gamestate) representing the result of applying a [Move](#move), including any changes to the current player turn.

##### Contains

- [LocationState](#locationstate)
- [GameObjectState](#gameobjectstate)
- [GameObjectOwner](#gameobjectowner)
- [GameObjectController](#gameobjectcontroller)

##### References

- [LocationId](#locationid)
- [PlayerId](#playerid)
- [GameObjectId](#gameobjectid)

##### Does Not Own

- [GameDefinition](#gamedefinition)
- [GameConfiguration](#gameconfiguration)
- [RulesEngine](#rulesengine)
- [Move](#move)
- [GameHistory](#gamehistory)

##### Example

```text
GameState

    Contains:

        GameObjectState:
            Helm_001
                Durability: 10

            Helm_002
                Durability: 5


        GameObjectOwner:
            Helm_001
                Owner: Player_A

            Helm_002
                Owner: Player_B
```

#### Location

##### Description

A [Location](#location) represents an immutable instance of a [LocationType](#locationtype) within a game. A [Location](#location) defines the identity of a specific place where [GameObjects](#gameobject) and [Players](#player) may exist, such as a board position, inventory, territory, hand, deck, or discard pile. A [Location](#location) references the [LocationType](#locationtype) that describes its shared immutable characteristics. Mutable information associated with a [Location](#location) is represented separately by [LocationState](#locationstate).

##### Owns

- [LocationId](#locationid)

##### References

- [LocationType](#locationtype)

##### Does Not Own

- [LocationType](#locationtype)
- [LocationState](#locationstate)
- [GameState](#gamestate)
- [GameObject](#gameobject)
- [Player](#player)

#### Player

##### Description

A [Player](#player) represents an entity that participates in a game. A [Player](#player) may represent a human participant or an automated participant. The Game Master is a special system entity used to represent objects, resources, and information controlled by the game itself. The Game Master is represented using a [PlayerId](#playerid) for ownership and control relationships, but it does not necessarily represent an actual player participating in the game.

##### Owns

- [PlayerId](#playerid)

##### Does Not Own

- [GameObject](#gameobject)
- [GameObjectOwner](#gameobjectowner)
- [GameObjectController](#gameobjectcontroller)
- [GameState](#gamestate)

##### Example

```text
Player

    Player_A
        Id: Player_A
        Name: Alice
        Type: Human


    Player_B
        Id: Player_B
        Name: Bob
        Type: Human


    Game_Master
        Id: Game_Master
        Name: Game Master
        Type: System
```

#### GameObject

##### Description

A [GameObject](#gameobject) represents an immutable instance of a [GameObjectType](#gameobjecttype) that exists within a game. A [GameObject](#gameobject) defines the identity of a specific object and references the [GameObjectType](#gameobjecttype) that describes its shared immutable characteristics. Mutable information about an object is represented separately through state and relationship concepts such as [GameObjectState](#gameobjectstate), [GameObjectOwner](#gameobjectowner), and [GameObjectController](#gameobjectcontroller). A [GameObject](#gameobject) does not contain the current state of the object and does not directly own ownership or control relationships.

##### Owns

- [GameObjectId](#gameobjectid)

##### References

- [GameObjectType](#gameobjecttype)

##### Does Not Own

- [GameObjectType](#gameobjecttype)
- [GameObjectState](#gameobjectstate)
- [GameObjectOwner](#gameobjectowner)
- [GameObjectController](#gameobjectcontroller)
- [GameState](#gamestate)
- [Player](#player)

##### Example

```text
GameObject

    Helm_001
        Type: Iron Helm

    Helm_002
        Type: Iron Helm
```

Helm_001 and Helm_002 are separate [GameObjects](#gameobject) with unique identities. Both reference the same [GameObjectType](#gameobjecttype) but represent different individual objects within the game.

### Location State Concepts

#### LocationState

##### Description

[LocationState](#locationstate) represents the mutable state values associated with a specific [Location](#location) within a [GameState](#gamestate). These values may change throughout the course of a game as the result of domain actions. [LocationState](#locationstate) does not define the identity or immutable characteristics of a [Location](#location); it stores the current values required to represent that [Location](#location) at a specific point in time. [LocationState](#locationstate) is part of a [GameState](#gamestate) and is replaced whenever a new [GameState](#gamestate) is produced.

##### Contains

- mutable location state values

##### References

- [LocationId](#locationid)

##### Does Not Own

- [Location](#location)
- [LocationType](#locationtype)
- [GameState](#gamestate)

### Player State Concepts

#### PlayerState

##### Description

[PlayerState](#playerstate) represents the mutable state values associated with a specific [Player](#player) within a [GameState](#gamestate). These values may change throughout the course of a game as the result of domain actions. [PlayerState](#playerstate) does not define the identity of a [Player](#player) or the immutable characteristics of a participant; it stores the current values required to represent that [Player](#player)'s state at a specific point in time. [PlayerState](#playerstate) is part of a [GameState](#gamestate) and is replaced when a new [GameState](#gamestate) is produced.

##### Contains

- mutable player state values

##### References

- [PlayerId](#playerid)

##### Does Not Own

- [Player](#player)
- [PlayerId](#playerid)
- [GameState](#gamestate)
- [GameObject](#gameobject)
- [GameObjectOwner](#gameobjectowner)
- [GameObjectController](#gameobjectcontroller)

##### Example

```text
PlayerState

    PlayerId:
        Player_A

    Attributes:

        Health:
            85 / 100

        Strength:
            14

        Dexterity:
            12

        Intelligence:
            16

        Level:
            5

        Experience:
            2400

        Gold:
            150
```

### Game Object State Concepts

#### GameObjectState

##### Description

[GameObjectState](#gameobjectstate) represents the mutable state values associated with a specific [GameObject](#gameobject). These values may change throughout the course of a game as the result of domain actions. [GameObjectState](#gameobjectstate) does not define what properties a [GameObject](#gameobject) has; it stores the current values of those properties for an individual [GameObject](#gameobject). [GameObjectState](#gameobjectstate) is part of a [GameState](#gamestate) and is replaced when a new [GameState](#gamestate) is produced.

##### Contains

- stat values

##### References

- [GameObjectId](#gameobjectid)

##### Does Not Own

- [GameObject](#gameobject)
- [GameObjectType](#gameobjecttype)
- [GameState](#gamestate)
- [GameObjectOwner](#gameobjectowner)
- [GameObjectController](#gameobjectcontroller)

##### Example

```text
GameObjectState

    Helm_001
        Durability: 10

    Helm_002
        Durability: 5
```

Both [GameObjects](#gameobject) share the same [GameObjectType](#gameobjecttype) but maintain separate mutable stats.

### Game Object Relationship Concepts

#### GameObjectOwner

##### Description

[GameObjectOwner](#gameobjectowner) represents the ownership relationship between a [GameObject](#gameobject) and a [Player](#player). Every [GameObject](#gameobject) has exactly one owner. The owner may be a human player, an automated participant, or a system-controlled participant such as the Game Master. Ownership may change throughout the course of a game as the result of domain actions. Ownership is independent from control and does not grant control automatically.

##### References

- [PlayerId](#playerid)
- [GameObjectId](#gameobjectid)

##### Does Not Own

- [GameObject](#gameobject)
- [Player](#player)
- [GameObjectController](#gameobjectcontroller)

##### Example

```text
GameObjectOwner

    Helm_001
        Owner: Player_A

    Helm_002
        Owner: Player_B
```

Ownership is independent for each [GameObject](#gameobject) instance, even when the [GameObjects](#gameobject) share the same [GameObjectType](#gameobjecttype).

#### GameObjectController

##### Description

[GameObjectController](#gameobjectcontroller) represents the control relationship between a [GameObject](#gameobject) and one or more [Players](#player). Controllers identify the [Players](#player) that are currently able to act on behalf of a [GameObject](#gameobject) through domain actions. Control is independent from ownership and may change throughout the course of a game as the result of domain actions. A [GameObject](#gameobject) may have multiple controllers, and a controller does not imply ownership of the [GameObject](#gameobject).

##### References

- [PlayerId](#playerid)
- [GameObjectId](#gameobjectid)

##### Does Not Own

- [GameObject](#gameobject)
- [Player](#player)
- [GameObjectOwner](#gameobjectowner)

##### Example

```text
GameObjectController

    Helm_001
        Controllers:
            Player_A

    Tent_001:
        Player_A, Player_B
```

### Action Concepts

#### Move

##### Description

A [Move](#move) represents an immutable request to perform a domain action that may transform one [GameState](#gamestate) into another. A [Move](#move) describes an intended change and contains the information required by the [RulesEngine](#rulesengine) to evaluate that change. A [Move](#move) does not directly modify a [GameState](#gamestate) and does not indicate whether the requested action is valid or has occurred. The [RulesEngine](#rulesengine) evaluates the [Move](#move) and produces a [MoveResolution](#moveresolution) describing the result.

##### Owns

- [Move](#move) parameters

##### References

- [PlayerId](#playerid)
- [GameObjectId](#gameobjectid)

##### Uses

- [GameState](#gamestate)

##### Does Not Own

- [Player](#player)
- [GameObject](#gameobject)
- [GameState](#gamestate)
- [GameDefinition](#gamedefinition)
- [MoveResolution](#moveresolution)
- [GameHistory](#gamehistory)

#### MoveResolution

##### Description

A [MoveResolution](#moveresolution) represents the result of evaluating a [Move](#move) against a [GameState](#gamestate). A [MoveResolution](#moveresolution) always contains a [GameState](#gamestate) representing the current state after resolution. If a [Move](#move) is accepted, the [GameState](#gamestate) is a new immutable snapshot produced by the [RulesEngine](#rulesengine). If a [Move](#move) is rejected, the [GameState](#gamestate) is the original unchanged snapshot and the [MoveResolution](#moveresolution) contains the reasons the [Move](#move) was not accepted. A [MoveResolution](#moveresolution) does not modify a [Move](#move) or a [GameState](#gamestate).

##### Owns

- ResolutionStatus
- ValidationErrors

##### References

- [Move](#move)
- [GameState](#gamestate)

##### Does Not Own

- [Move](#move)
- [GameState](#gamestate)
- [RulesEngine](#rulesengine)

### Identity Concepts

#### LocationId

##### Description

A [LocationId](#locationid) uniquely identifies a [Location](#location) within a domain. A [LocationId](#locationid) is immutable and remains associated with the same [Location](#location) for the lifetime of the [Location](#location). A [LocationId](#locationid) does not represent a [Location](#location) and does not contain information about the location's properties, contents, or current state.

##### Owns

- unique identifier value

##### Does Not Own

- [Location](#location)
- [LocationState](#locationstate)

#### PlayerId

##### Description

A [PlayerId](#playerid) uniquely identifies a [Player](#player) within a domain. A [PlayerId](#playerid) is immutable and remains associated with the same [Player](#player) for the lifetime of the [Player](#player). A [PlayerId](#playerid) does not represent a [Player](#player) and does not contain any information about the [Player](#player)'s attributes, state, ownership, or relationships.

##### Owns

- unique identifier value

##### Does Not Own

- [Player](#player)
- [GameObject](#gameobject)
- [GameObjectOwner](#gameobjectowner)
- [GameObjectController](#gameobjectcontroller)

##### Example

```text
PlayerId

    Player_A
    Player_B
```

Each [Player](#player) has a unique [PlayerId](#playerid).

[PlayerId](#playerid) is used by domain relationships to identify [Players](#player) without requiring a direct reference to the [Player](#player) entity.

#### GameObjectId

##### Description

A [GameObjectId](#gameobjectid) uniquely identifies a [GameObject](#gameobject) within a domain. A [GameObjectId](#gameobjectid) is immutable and remains associated with the same [GameObject](#gameobject) for the lifetime of the object. A [GameObjectId](#gameobjectid) does not represent a [GameObject](#gameobject) and does not contain information about the object's type, state, ownership, control, or relationships.

##### Owns

- unique identifier value

##### Does Not Own

- [GameObject](#gameobject)
- [GameObjectType](#gameobjecttype)
- [GameObjectState](#gameobjectstate)
- [GameObjectOwner](#gameobjectowner)
- [GameObjectController](#gameobjectcontroller)

##### Example

```text
GameObjectId

    Helm_001
    Helm_002
```

Each [GameObject](#gameobject) has a unique [GameObjectId](#gameobjectid).

[GameObjectId](#gameobjectid) is used by domain relationships and state components to identify specific [GameObjects](#gameobject) without requiring direct references to the [GameObject](#gameobject) entity.

### Domain Service Concepts

#### RulesEngine

##### Description

A [RulesEngine](#rulesengine) defines the game-specific behavior required to interpret [GameStates](#gamestate), evaluate [Moves](#move), and produce resulting [GameStates](#gamestate). It is stateless and does not own game state or game progression. Given a [GameState](#gamestate), a [RulesEngine](#rulesengine) can determine the set of legal [Moves](#move) available from that state. This capability allows game states to be explored by simulation systems and AI search algorithms such as Minimax and Monte Carlo Tree Search. Given a [GameState](#gamestate) and [Move](#move), the [RulesEngine](#rulesengine) evaluates the requested action and produces a [MoveResolution](#moveresolution) describing the result of the evaluation. The [RulesEngine](#rulesengine) never modifies existing [GameStates](#gamestate), stores current state, or maintains game history.

##### References

- [GameDefinition](#gamedefinition)

##### Uses

- [GameState](#gamestate)
- [Move](#move)

##### Creates

- [MoveResolution](#moveresolution)
- [GameState](#gamestate)
- [Move](#move)

##### Does Not Own

- [GameDefinition](#gamedefinition)
- [GameConfiguration](#gameconfiguration)
- [GameState](#gamestate)
- [Move](#move)
- [MoveResolution](#moveresolution)
- [GameHistory](#gamehistory)

## Execution Services

### Execution Service Concepts

#### GameEngine

##### Description

A [GameEngine](#gameengine) is a framework service responsible for coordinating the execution of a game session. A [GameEngine](#gameengine) manages the execution lifecycle by requesting [Moves](#moves) from a [MoveProvider](#moveprovider), submitting [Moves](#moves) and the current [GameState](#gamestate) to the [RulesEngine](#rulesengine) for evaluation, processing [MoveResolutions](#moveresolutions), advancing the [GameInstance](#gameinstance) to resulting immutable [GameStates](#gamestates), and recording accepted [Moves](#moves) in [GameHistory](#gamehistory). A [GameEngine](#gameengine) does not contain game-specific rules, interpret the meaning of [Moves](#moves), or modify existing [GameStates](#gamestates). Instead, it coordinates the interaction between execution components and domain concepts while preserving the framework guarantees of immutable state transitions and deterministic execution.

##### Responsibilities

- coordinate game execution
- manage the execution lifecycle of a [GameInstance](#gameinstance)
- request [Moves](#moves) from [MoveProviders](#moveproviders)
- provide the current [GameState](#gamestate) as decision context
- submit [Moves](#moves) and [GameStates](#gamestates) to the [RulesEngine](#rulesengine)
- process [MoveResolutions](#moveresolutions)
- advance the [GameInstance](#gameinstance) to resulting immutable [GameStates](#gamestates)
- record accepted [Moves](#moves) in [GameHistory](#gamehistory)
- detect when execution reaches a terminal state

##### References

- [GameInstance](#gameinstance)
- [RulesEngine](#rulesengine)
- [MoveProvider](#moveprovider)
- [GameHistory](#gamehistory)
- [GameState](#gamestate)
- [Move](#move)
- [MoveResolution](#moveresolution)

##### Does Not Own

- [GameDefinition](#gamedefinition)
- [GameConfiguration](#gameconfiguration)
- [RulesEngine](#rulesengine)
- [GameState](#gamestate)
- [Move](#move)
- [MoveResolution](#moveresolution)
- [MoveProvider](#moveprovider)
- [GameHistory](#gamehistory)
- [GameObject](#gameobject)
- [Player](#player)

##### Relationship

```text
GameEngine

    coordinates:
        GameInstance

    using:
        RulesEngine
        MoveProvider

    records:
        GameHistory
```

#### MoveProvider

##### Description

A [MoveProvider](#moveprovider) is an execution abstraction responsible for supplying [Moves](#moves) to a [GameEngine](#gameengine) during game execution. A [MoveProvider](#moveprovider) represents the source of a decision within a game session and may be implemented by a human player interface, artificial intelligence agent, network client, replay system, automated test, or other decision-making system. A [MoveProvider](#moveprovider) receives the current [GameState](#gamestate) as decision context and produces a [Move](#move) representing an intended action. It does not evaluate whether a [Move](#move) is valid, apply game rules, modify [GameStates](#gamestates), or control execution. [Moves](#moves) are evaluated by the [RulesEngine](#rulesengine) through the [GameEngine](#gameengine).

##### Responsibilities

- supply [Moves](#moves) during game execution
- receive the current [GameState](#gamestate) as decision context
- produce a [Move](#move) representing an intended action
- support different sources of decision making

##### References

- [GameEngine](#gameengine)
- [GameState](#gamestate)
- [Move](#move)
- [Player](#player)

##### Does Not Own

- [GameDefinition](#gamedefinition)
- [GameConfiguration](#gameconfiguration)
- [GameInstance](#gameinstance)
- [GameState](#gamestate)
- [RulesEngine](#rulesengine)
- [Move](#move)
- [MoveResolution](#moveresolution)
- [GameHistory](#gamehistory)
- [GameObject](#gameobject)
- [Location](#location)

##### Relationship

```text
MoveProvider

    supplies:
        Move

    to:
        GameEngine

    using:
        GameState
```

## Event Services

### Game Lifecycle Event Concepts

#### GameInitializedEvent

##### Description

A [GameInitializedEvent](#gameinitializedevent) represents an immutable notification that a [GameInstance](#gameinstance) has been initialized and is ready for game execution. A [GameInitializedEvent](#gameinitializedevent) indicates that game initialization has completed but does not itself create, modify, or control the [GameInstance](#gameinstance) or its [GameState](#gamestate).

##### Owns

- event information

##### Does Not Own

- [GameInstance](#gameinstance)
- [GameDefinition](#gamedefinition)
- [GameConfiguration](#gameconfiguration)
- [GameState](#gamestate)
- [GameEngine](#gameengine)
- [RulesEngine](#rulesengine)

#### TerminalStateReachedEvent

##### Description

A [TerminalStateReachedEvent](#terminalstatereachedevent) represents an immutable notification that a [GameInstance](#gameinstance) has reached a terminal [GameState](#gamestate). It indicates that game execution has reached a state in which no further moves are expected to be executed. A [TerminalStateReachedEvent](#terminalstatereachedevent) does not determine whether a [GameState](#gamestate) is terminal, modify the [GameState](#gamestate), or control game execution.

##### Owns

- event information

##### Does Not Own

- [GameInstance](#gameinstance)
- [GameState](#gamestate)
- [Move](#move)
- [GameEngine](#gameengine)
- [RulesEngine](#rulesengine)

### Move Event Concepts

#### MoveAcquiredEvent

##### Description

A [MoveAcquiredEvent](#moveacquiredevent) represents an immutable notification that a [Move](#move) has been acquired for evaluation during game execution. It indicates that the [GameEngine](#gameengine) has received a [Move](#move) from a [MoveProvider](#moveprovider) and that the [Move](#move) is ready to be evaluated by the [RulesEngine](#rulesengine). A [MoveAcquiredEvent](#moveacquiredevent) does not determine whether the [Move](#move) is valid, modify the [GameState](#gamestate), or apply game rules.

##### Owns

- event information

##### Does Not Own

- [Move](#move)
- [GameState](#gamestate)
- [MoveProvider](#moveprovider)
- [GameEngine](#gameengine)
- [RulesEngine](#rulesengine)

#### MoveResolutionEvent

##### Description

A [MoveResolutionEvent](#moveresolutionevent) represents an immutable notification that a [Move](#move) has been evaluated by the [RulesEngine](#rulesengine) and that a [MoveResolution](#moveresolution) has been produced. It indicates the result of evaluating the [Move](#move), including whether the [Move](#move) was accepted or rejected. A [MoveResolutionEvent](#moveresolutionevent) does not modify the [GameState](#gamestate) or apply the [Move](#move) itself.

##### Owns

- event information

##### References

- [Move](#move)
- [MoveResolution](#moveresolution)

##### Does Not Own

- [Move](#move)
- [MoveResolution](#moveresolution)
- [GameState](#gamestate)
- [RulesEngine](#rulesengine)
- [GameEngine](#gameengine)

### State Event Concepts

#### StateUpdatedEvent

##### Description

A [StateUpdatedEvent](#stateupdatedevent) represents an immutable notification that the current [GameState](#gamestate) of a [GameInstance](#gameinstance) has been replaced by a new immutable [GameState](#gamestate). It indicates that the new [GameState](#gamestate) was established either during game initialization or as the result of an accepted [Move](#move). A [StateUpdatedEvent](#stateupdatedevent) does not modify the [GameState](#gamestate) or determine whether a state transition should occur.

##### Owns

- event information

##### References

- [GameState](#gamestate)

##### Does Not Own

- [GameInstance](#gameinstance)
- [GameState](#gamestate)
- [Move](#move)
- [MoveResolution](#moveresolution)
- [RulesEngine](#rulesengine)
- [GameEngine](#gameengine)


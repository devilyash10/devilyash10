# Architecture

Sonexa has been engineered as a modern Android media application that separates audio playback from the user interface through a reactive, service-oriented architecture.

Unlike conventional music players where playback logic resides directly inside Activities or Fragments, Sonexa delegates all media responsibilities to a persistent `MediaSessionService`, allowing playback to continue independently of the application's user interface lifecycle.

The application combines MVVM, StateFlow, Media3, Room Database, and Jetpack Compose into a scalable architecture designed for long-term maintainability and future cloud expansion.

This document explains the architectural principles, component relationships, playback engine, and internal communication model that power Sonexa.

---

# Architectural Goals

Every architectural decision within Sonexa was guided by several long-term objectives.

## Continuous Playback

Music playback should never depend on whether a screen is currently visible.

Playback must continue while:

- Navigating between screens
- Rotating the device
- Locking the display
- Minimizing the application

---

## Reactive User Interface

Every visual component should react automatically to playback state changes.

Rather than manually updating UI components, Compose observes immutable StateFlow objects emitted by ViewModels and the AudioController.

---

## Scalability

The architecture should support future capabilities without major redesign.

Examples include:

- Cloud streaming
- Firebase synchronization
- Android Auto
- Wear OS
- Remote media sources
- Podcast support

---

## Maintainability

Playback logic, business logic, persistence, and presentation remain isolated from one another.

Each layer has clearly defined responsibilities and communicates through stable interfaces.

---

## Hardware Integration

Native Android media capabilities—including MediaSession, Audio Focus, and AudioFX—should integrate naturally into the playback engine while remaining independent from the user interface.

---

# Design Principles

Several architectural principles guide the implementation of Sonexa.

## Single Responsibility Principle

Every component performs one primary responsibility.

Examples include:

- AudioController manages playback communication.
- AudioService owns ExoPlayer.
- HomeViewModel manages Home UI state.
- AudioRepository abstracts data access.
- SettingsManager stores user preferences.

Each class exists for a clearly defined purpose.

---

## Separation of Concerns

The application is intentionally divided into independent responsibilities.

The Compose UI renders information.

ViewModels coordinate presentation logic.

Repositories manage data.

MediaSessionService controls playback.

No component performs responsibilities outside its own layer.

---

## Unidirectional Data Flow

Every interaction follows a predictable execution path.

```text
User Interaction

        │

        ▼

Compose UI

        │

        ▼

ViewModel

        │

        ▼

AudioController

        │

        ▼

MediaSessionService

        │

        ▼

ExoPlayer

        │

        ▼

Playback State

        │

        ▼

StateFlow

        │

        ▼

Compose UI
```

The user interface never modifies playback state directly.

Instead, it dispatches events which propagate through the playback engine before updated state returns to the UI.

This guarantees consistent application behavior and greatly simplifies debugging.

---

# High-Level Architecture

Sonexa is organized around six major subsystems.

```text
                Jetpack Compose UI
                        │
                        ▼
                  Screen ViewModels
                        │
                        ▼
                 AudioController
                        │
        ┌───────────────┴───────────────┐
        ▼                               ▼
MediaBrowser                    AudioRepository
        │                               │
        ▼                               ▼
MediaSessionService           Room Database
        │
        ▼
Media3 ExoPlayer
        │
        ▼
Android Audio Hardware
```

Each subsystem communicates only through well-defined interfaces.

This separation enables playback, storage, networking, and presentation to evolve independently.

---

# Layered Architecture

Sonexa follows a layered architecture while organizing source code primarily by feature.

The major architectural layers are:

```text
Presentation

↓

Business Logic

↓

Playback Engine

↓

Persistence

↓

Hardware
```

Unlike traditional applications, the Playback Engine operates independently from both the Presentation and Persistence layers.

This separation allows playback to continue regardless of UI lifecycle events.

---

## Presentation Layer

The Presentation layer contains everything responsible for rendering the application's interface.

Components include:

- Home Screen
- Player Screen
- MiniPlayer
- Lyrics Screen
- Library
- Search
- Settings

Responsibilities include:

- Rendering StateFlow
- User interaction
- Navigation
- Animations

No playback logic exists within the Presentation layer.

---

## Business Layer

The Business layer coordinates communication between the user interface and the playback engine.

Major components include:

- ViewModels
- AudioController
- AudioRepository

Responsibilities include:

- Playback requests
- Queue management
- Library management
- Favorites
- Playlist operations
- Search

Business logic remains independent from Compose.

---

## Playback Engine

The playback engine forms the core of Sonexa.

Components include:

- MediaSessionService
- MediaBrowser
- Media3 ExoPlayer
- Audio Focus
- Media Notification

The playback engine remains active even when every Compose screen has been destroyed.

This architecture enables uninterrupted background playback and system-level media integration.

---

Continue to **Part 2**, covering:

- MediaSession architecture
- AudioController
- ExoPlayer lifecycle
- Playback pipeline
- Repository architecture
- StateFlow hubs

---

# Media Playback Architecture

At the heart of Sonexa lies a dedicated playback engine built around Android's Media3 framework.

Rather than embedding ExoPlayer directly within the user interface, playback responsibilities are delegated to a persistent `MediaSessionService`.

This separation ensures that music playback remains completely independent from the application's visual lifecycle.

The playback engine continues operating while:

- The application is minimized
- The device is locked
- The screen rotates
- Users navigate between screens
- Configuration changes occur

This architecture closely follows Android's recommended media application guidelines.

---

# MediaSessionService

The MediaSessionService acts as the central execution environment for all playback operations.

Instead of allowing each screen to communicate directly with ExoPlayer, every playback request passes through the MediaSession.

Primary responsibilities include:

- Owning the ExoPlayer instance
- Managing playback lifecycle
- Publishing playback state
- Managing media notifications
- Handling Audio Focus
- Responding to Bluetooth controls
- Supporting lock screen controls

Because the MediaSession exists independently of the UI, playback remains uninterrupted even if every Compose screen is destroyed.

---

# AudioController

The AudioController acts as the communication bridge between the Presentation layer and the playback engine.

Rather than exposing Media3 directly to ViewModels, AudioController provides a simplified API responsible for coordinating playback operations.

Typical responsibilities include:

- Play requests
- Pause requests
- Queue management
- Current song tracking
- Playback position updates
- Playback state monitoring
- Audio session management

This abstraction prevents UI components from depending directly on Media3 implementation details.

---

# Playback Pipeline

Every playback request follows the same execution sequence.

```text
User selects song

        │

        ▼

HomeViewModel

        │

        ▼

AudioController

        │

        ▼

MediaBrowser

        │

        ▼

MediaSessionService

        │

        ▼

Media3 ExoPlayer

        │

        ▼

Audio Decoder

        │

        ▼

Android Audio Output
```

The reverse flow distributes playback updates back to the user interface.

```text
ExoPlayer

        │

        ▼

Player.Listener

        │

        ▼

AudioController

        │

        ▼

StateFlow

        │

        ▼

ViewModel

        │

        ▼

Compose UI
```

This bidirectional communication model ensures that playback state remains synchronized across every screen.

---

# ExoPlayer Lifecycle

The lifecycle of the playback engine is intentionally decoupled from the Activity lifecycle.

## Initialization

The ExoPlayer instance is created during `AudioService.onCreate()`.

All required playback components—including the MediaSession and Player.Listener—are initialized before user interaction begins.

---

## Active Playback

During playback the player continuously publishes:

- Current media item
- Playback position
- Playback state
- Buffering state
- Duration
- Repeat mode
- Shuffle mode

These updates are propagated to the UI using StateFlow.

---

## Resource Cleanup

When playback is permanently terminated and the service is destroyed, ExoPlayer resources are released.

This prevents:

- Memory leaks
- Decoder leaks
- Audio session leaks
- Background resource consumption

---

# Repository Architecture

All media-related data passes through the AudioRepository.

Rather than allowing screens to communicate directly with MediaStore or Room Database, repositories provide a unified interface responsible for coordinating multiple data sources.

Current repository responsibilities include:

- Local music retrieval
- Playlist management
- Favorites
- Playback history
- Future cloud integration

The repository abstracts data origins from the rest of the application.

Whether a song originates from local storage or a future streaming service, the Presentation layer remains unchanged.

---

# Unified Song Model

One of Sonexa's key architectural decisions is the use of a single Song model throughout the application.

Every screen, repository, and playback component communicates using the same data model.

A Song may reference:

- Local MediaStore content
- Remote streaming URLs
- Cached audio files

Because the playback engine operates only on this unified model, future cloud streaming integration requires minimal architectural changes.

---

# Reactive State Management

Application state is managed through Kotlin StateFlow.

The AudioController exposes immutable playback streams observed throughout the application.

Examples include:

- Current Song
- Playback Position
- Playback State
- Queue
- Repeat Mode
- Shuffle State

Every screen observes only the data required for its own functionality.

This minimizes unnecessary UI recomposition while maintaining consistent playback behavior.

---

# StateFlow Hub

The AudioController serves as the central StateFlow hub.

```text
MediaSessionService

        │

        ▼

AudioController

        │

 ┌──────┼───────────┬───────────┐

 ▼      ▼           ▼           ▼

Player  MiniPlayer  Lyrics   Notification

Screen
```

Rather than each screen polling ExoPlayer independently, a single reactive stream distributes playback information across the application.

This architecture reduces duplication while ensuring every interface remains synchronized.

---

# Queue Management

Playback queues are managed independently from the user interface.

Queue operations include:

- Add media
- Remove media
- Next track
- Previous track
- Shuffle
- Repeat
- Queue navigation

Because queue state exists inside the playback engine rather than individual screens, playback remains uninterrupted during navigation or configuration changes.

---

Continue to **Part 3**, covering:

- Room Database architecture
- MediaStore scanning
- AudioFX architecture
- Lyrics synchronization
- Palette engine
- Navigation architecture
- Package organization

---

# Local Media Architecture

Sonexa retrieves music directly from the device using Android's MediaStore API.

Instead of recursively scanning storage directories, the application queries Android's indexed media database through the system ContentResolver.

This approach provides several advantages:

- Faster library initialization
- Scoped Storage compliance
- Improved performance
- Reduced storage permissions
- Better compatibility across Android versions

The MediaStore layer produces a collection of media objects which are transformed into Sonexa's unified Song model before entering the repository layer.

---

# Media Discovery Pipeline

Music discovery follows a predictable execution flow.

```text
Device Storage

        │

        ▼

Android MediaStore

        │

        ▼

ContentResolver Query

        │

        ▼

Media Metadata

        │

        ▼

Song Mapping

        │

        ▼

AudioRepository

        │

        ▼

StateFlow

        │

        ▼

Compose UI
```

Before exposing discovered media to the user interface, Sonexa performs intelligent filtering to exclude unsupported and non-music audio sources such as temporary messaging recordings and system-generated files.

This preprocessing stage results in a cleaner and more relevant music library.

---

# Room Database Architecture

Room Database provides persistent storage for user-generated content and listening statistics.

Unlike MediaStore, which reflects the device's external media collection, Room stores application-specific information that cannot be retrieved from the operating system.

Current responsibilities include:

- Favorites
- Custom Playlists
- Recently Played
- Most Played
- Playback Statistics

The Room layer operates independently of MediaStore while remaining synchronized through the repository.

```text
Playback Event

        │

        ▼

AudioController

        │

        ▼

Repository

        │

        ▼

Room DAO

        │

        ▼

SQLite

        │

        ▼

Flow

        │

        ▼

Compose UI
```

Reactive database streams ensure that library screens update automatically whenever stored information changes.

---

# AudioFX Architecture

One of Sonexa's defining architectural characteristics is its integration with Android's native AudioFX framework.

Rather than implementing software-based equalization, Sonexa attaches audio effects directly to the active playback session.

The processing pipeline is illustrated below.

```text
Media3 ExoPlayer

        │

        ▼

Audio Session ID

        │

 ┌──────┼──────────────┐

 ▼      ▼              ▼

Equalizer

Bass Boost

Virtualizer

        │

        ▼

Android DSP

        │

        ▼

Speaker / Headphones
```

Because processing occurs within Android's native audio pipeline, compatible devices can apply hardware-accelerated audio enhancements with minimal additional overhead.

---

# Audio Session Management

Every active playback session is identified by a unique Audio Session ID.

Whenever playback begins, Sonexa retrieves this identifier from ExoPlayer and distributes it to the AudioFX subsystem.

The Equalizer, Bass Boost, and Virtualizer are then attached to the same session.

This architecture ensures that audio effects are applied only to Sonexa's playback while avoiding interference with other media applications running on the device.

When Android creates a new audio session—for example after switching from the device speaker to Bluetooth headphones—the application automatically reconnects supported audio effects to the updated session.

---

# Lyrics Engine Architecture

The synchronized lyrics engine has been designed independently from the playback interface.

Instead of embedding lyric timing inside the user interface, Sonexa maintains a dedicated parsing and synchronization pipeline.

```text
LRC File

        │

        ▼

LrcParser

        │

        ▼

LyricLine Objects

        │

        ▼

Playback Position

        │

        ▼

Active Lyric Index

        │

        ▼

Lyrics Screen
```

The playback engine continuously compares the current playback position with parsed timestamps and emits the active lyric index through StateFlow.

The user interface reacts automatically by highlighting the active line while smoothly scrolling it into view.

This separation keeps synchronization logic independent from presentation concerns.

---

# Dynamic Palette Engine

Visual personalization is implemented using Android's Palette API.

Whenever album artwork changes, the artwork bitmap is processed asynchronously to extract dominant colors.

The resulting palette is distributed to the presentation layer where it influences multiple interface components.

```text
Album Artwork

        │

        ▼

Coil

        │

        ▼

Palette API

        │

        ▼

Dominant Colors

        │

        ▼

Compose Theme

        │

        ▼

Player UI
```

Palette extraction executes on background threads, preventing image analysis from interrupting playback or user interaction.

---

# Navigation Architecture

Sonexa adopts a Single Activity architecture powered by Navigation Compose.

Every destination is represented as an independent feature while sharing a common playback engine.

Current destinations include:

- Home
- Player
- Library
- Search
- Online
- Settings
- Lyrics

Navigation events affect only the presentation layer.

Playback remains completely independent of navigation, ensuring uninterrupted audio regardless of the currently visible screen.

---

# Feature-Based Package Organization

Instead of organizing source code exclusively by technical layer, Sonexa groups related components by feature.

```text
core/
│
├── media/
├── navigation/
└── util/

data/
│
├── local/
└── repository/

feature/
│
├── home/
├── player/
├── library/
├── online/
├── search/
└── settings/

model/

service/
```

This organization improves scalability by allowing new features to evolve independently while keeping related classes physically close together.

As the application grows, additional functionality can be introduced without significantly affecting existing modules.

---

Continue to **Part 4**, covering:

- Dependency Injection strategy
- Performance architecture
- Scalability
- Future architectural evolution
- Architecture summary
- Related documentation

---

# Dependency Management

Sonexa has been designed around loosely coupled components that communicate through clearly defined interfaces rather than direct implementation dependencies.

The Presentation layer never communicates directly with ExoPlayer, MediaStore, or Room Database. Instead, these responsibilities are delegated to intermediary components such as the AudioController and repository layer.

The dependency hierarchy follows a predictable flow.

```text
Compose Screens

        │

        ▼

ViewModels

        │

        ▼

AudioController

        │

        ▼

Repositories

        │

 ┌──────┼─────────────┬─────────────┐

 ▼      ▼             ▼             ▼

MediaStore    Room Database   MediaSession
```

This structure minimizes coupling while simplifying testing and future feature expansion.

---

# Communication Between Components

Every subsystem communicates through a single direction of data flow.

```text
User Interaction

        │

        ▼

Compose Screen

        │

        ▼

ViewModel

        │

        ▼

AudioController

        │

        ▼

Playback Engine

        │

        ▼

Playback Events

        │

        ▼

StateFlow

        │

        ▼

Compose UI
```

Because state always originates from the playback engine, every interface within the application remains synchronized automatically.

No screen performs direct polling or manual synchronization.

---

# Background Execution Model

Background playback responsibilities are isolated inside the MediaSessionService.

Unlike lifecycle-bound UI components, the playback engine remains active independently from application navigation.

The service manages:

- Playback lifecycle
- Notification lifecycle
- Audio Focus
- Bluetooth controls
- Lock screen controls
- Media button events

This separation allows Android to treat Sonexa as a first-class media application while ensuring reliable playback continuity.

---

# Performance Architecture

Performance has influenced every major architectural decision within Sonexa.

Rather than optimizing a single subsystem, the application minimizes unnecessary work across rendering, storage, playback, and media processing.

Major optimizations include:

## Shared Playback Engine

A single ExoPlayer instance is shared across every playback interface.

The Player Screen, MiniPlayer, Media Notification, Lyrics Screen, and future Android Auto integration all communicate with the same playback engine.

This eliminates duplicated playback state and reduces memory consumption.

---

## Reactive Rendering

Jetpack Compose recomposes only components observing updated StateFlow values.

Large interfaces are intentionally divided into reusable composables, reducing unnecessary rendering work.

---

## Asynchronous Media Processing

Resource-intensive operations execute on background coroutine dispatchers.

Examples include:

- Media scanning
- Database queries
- Palette extraction
- Lyrics parsing
- Playback history updates

This prevents expensive operations from blocking the main thread.

---

## Intelligent Database Updates

Playback statistics are updated only when meaningful playback events occur.

Instead of continuously writing to the database, Sonexa records listening history at stable playback states, reducing unnecessary storage operations while maintaining accurate statistics.

---

# Scalability Considerations

The current architecture has been designed to support future growth without requiring major structural changes.

Examples include:

### Streaming Providers

Future music services can be integrated by extending the repository layer without modifying the playback engine.

---

### Cloud Synchronization

Firebase Authentication and Firestore can synchronize playlists, favorites, and listening history while preserving the existing Room-based local experience.

---

### Additional Platforms

The playback architecture naturally supports:

- Android Auto
- Wear OS
- Android TV
- Foldables
- Tablets

Because playback logic remains independent from the presentation layer, additional interfaces can reuse the existing media engine.

---

### Expanded Audio Processing

Future enhancements may include:

- Graphic Equalizer Presets
- ReplayGain Support
- Crossfade Playback
- Gapless Playback
- Loudness Normalization
- Audio Visualizers

These capabilities can be introduced without redesigning the playback architecture.

---

# Architectural Trade-offs

Several engineering trade-offs were made during development.

| Decision | Benefit | Trade-off |
|----------|---------|-----------|
| MediaSessionService | Reliable background playback | Increased architectural complexity |
| MVVM | Clear separation of concerns | Additional ViewModel layer |
| StateFlow | Reactive state updates | Requires coroutine-based design |
| Room Database | Offline persistence | Database maintenance overhead |
| Feature-based packages | Better scalability | Larger project structure |
| Palette API | Dynamic personalized UI | Small processing overhead |
| AudioFX API | Native hardware processing | Device-dependent behavior |

These trade-offs were intentionally accepted to prioritize long-term maintainability and production-quality architecture.

---

# Future Architectural Evolution

The current architecture establishes a strong foundation while leaving room for future improvements.

Planned architectural enhancements include:

- Dependency Injection using Hilt
- Multi-module Gradle structure
- Streaming repository aggregation
- Expanded unit testing
- Integration testing
- Cloud synchronization layer
- Offline download manager
- Improved playback analytics

Each enhancement can be introduced incrementally without disrupting existing application behavior.

---

# Architecture Summary

Sonexa combines modern Android technologies into a scalable media platform centered around a persistent playback engine.

Core architectural characteristics include:

- Single Activity architecture
- Jetpack Compose UI
- MVVM presentation pattern
- Feature-based project organization
- Media3 ExoPlayer playback engine
- MediaSessionService
- AudioController abstraction
- Repository pattern
- Room Database persistence
- StateFlow reactive updates
- MediaStore integration
- Native AudioFX processing
- Palette-based dynamic theming

Rather than treating playback as a UI concern, Sonexa elevates the playback engine into an independent subsystem capable of supporting future cloud streaming and cross-device experiences.

---

# Related Documentation

The architecture described throughout this document is complemented by the remaining project documentation.

| Document | Description |
|----------|-------------|
| **OVERVIEW.md** | Project overview and repository guide |
| **FEATURES.md** | Complete feature reference |
| **IMPLEMENTATION.md** | Playback workflows and runtime behavior |
| **DECISIONS.md** | Engineering decisions and technology selection |
| **ROADMAP.md** | Planned milestones and future development |

---

# Conclusion

Sonexa has been engineered as a modern Android media platform that emphasizes modularity, scalability, and uninterrupted playback.

By separating the playback engine from the user interface, adopting reactive state management, and integrating Android's native media capabilities, the application provides a solid foundation for future expansion into cloud streaming, cross-device synchronization, and additional platform support.

The architectural decisions documented here ensure that Sonexa remains maintainable as new functionality is introduced while preserving a consistent and responsive user experience.
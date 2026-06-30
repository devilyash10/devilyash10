# Architecture

AtmosLive has been engineered around modern Android development principles with a strong emphasis on scalability, maintainability, and separation of concerns.

Rather than organizing code around individual screens or features alone, the application adopts a layered architecture inspired by **Clean Architecture** and implemented using the **Model–View–ViewModel (MVVM)** pattern.

Every layer has a clearly defined responsibility and communicates only through well-defined interfaces, making the application easier to test, extend, and maintain as additional features are introduced.

This document provides a detailed overview of the architectural decisions, internal data flow, dependency relationships, and engineering practices used throughout the project.

---

# Architectural Goals

The architecture of AtmosLive was designed around several key objectives.

## Scalability

The application should continue to grow without requiring major structural changes.

Adding new weather providers, screens, widgets, or services should require minimal modifications to existing components.

---

## Maintainability

Business logic, networking, storage, and presentation are intentionally isolated from one another.

Each layer can evolve independently while minimizing unintended side effects across the application.

---

## Testability

Core business logic is independent of Android framework classes wherever possible.

This separation allows repositories, domain models, and ViewModels to be tested independently from the user interface.

---

## Reactive User Interface

The interface reacts automatically to changes in application state.

Rather than manually updating views, the UI observes immutable StateFlow objects exposed by ViewModels.

This creates a predictable and lifecycle-aware rendering pipeline.

---

## Offline-First Design

Previously synchronized weather information is always prioritized before requesting new network data.

This design minimizes loading states while significantly improving perceived application performance.

---

# Design Principles

AtmosLive follows several architectural principles that influence every major implementation decision.

## Single Responsibility Principle

Every class performs one clearly defined task.

Examples include:

- WeatherRepository handles weather retrieval.
- LocationTracker manages location services.
- SettingsRepository persists user preferences.
- WeatherSyncWorker performs background synchronization.

Each component has one responsibility and one reason to change.

---

## Separation of Concerns

Networking, persistence, business logic, and user interface remain completely independent.

The Presentation layer has no knowledge of Retrofit.

The Data layer has no knowledge of Compose.

The Domain layer contains no Android framework dependencies.

This separation dramatically improves maintainability.

---

## Dependency Inversion

High-level components depend on abstractions rather than concrete implementations.

For example:

```
HomeViewModel

↓

WeatherRepository

↓

WeatherRepositoryImpl
```

The ViewModel interacts only with the repository interface.

The implementation details remain hidden inside the Data layer.

---

## Unidirectional Data Flow

Application state always flows in one direction.

```
User Action

↓

ViewModel

↓

Repository

↓

Data Source

↓

Repository

↓

ViewModel

↓

UI
```

The user interface never modifies application state directly.

Instead, every interaction is processed by the ViewModel, ensuring predictable behavior and easier debugging.

---

# High-Level Architecture

AtmosLive follows a layered architecture inspired by Clean Architecture.

```text
                 Presentation Layer
         ┌───────────────────────────────┐
         │ HomeScreen                    │
         │ SearchScreen                  │
         │ SettingsScreen                │
         │ Widget                        │
         └───────────────▲───────────────┘
                         │
                  StateFlow Updates
                         │
         ┌───────────────┴───────────────┐
         │ ViewModels                    │
         └───────────────▲───────────────┘
                         │
                  Repository Interfaces
                         │
         ┌───────────────┴───────────────┐
         │ Repository Implementations    │
         └───────▲──────────────▲────────┘
                 │              │
         Remote API       Local Storage
             │                  │
     Open-Meteo API      Room / DataStore
```

Every request follows the same execution path regardless of whether data originates from the network or local persistence.

This predictable architecture simplifies debugging while allowing each subsystem to evolve independently.

---

# Layered Architecture

AtmosLive is organized into three primary layers.

```
Presentation

↓

Domain

↓

Data
```

Each layer communicates only with the layer directly beneath it.

No layer is permitted to bypass another.

This prevents tight coupling and keeps architectural boundaries clearly defined.

---

## Presentation Layer

The Presentation layer contains everything responsible for rendering information on the screen.

Components include:

- Screens
- ViewModels
- UI State
- Navigation
- Compose Components

Responsibilities include:

- Rendering UI
- Observing StateFlow
- Handling user interaction
- Forwarding actions to ViewModels

The Presentation layer never communicates directly with Retrofit, Room, or DataStore.

---

## Domain Layer

The Domain layer represents the application's business logic.

It contains:

- Repository Interfaces
- Domain Models
- Use-case abstractions
- Location abstractions

The Domain layer has no dependency on Android UI components.

This makes business logic portable, testable, and framework-independent.

---

## Data Layer

The Data layer is responsible for interacting with external systems.

These include:

- Open-Meteo API
- Room Database
- DataStore
- Fused Location Provider

Repository implementations coordinate these data sources and expose clean interfaces to the Domain layer.

The Presentation layer never accesses these components directly.

---

Continue to **Part 2**, covering:

- Complete data flow
- Repository pattern
- MVVM interaction
- ViewModel lifecycle
- StateFlow architecture

---

# Data Flow

Every feature within AtmosLive follows a predictable data flow based on the MVVM architectural pattern. Regardless of whether data originates from a remote API, local storage, or device hardware, the same execution pipeline is maintained throughout the application.

Maintaining a single data flow significantly simplifies debugging, improves testability, and prevents inconsistent application state.

The overall request lifecycle can be represented as:

```text
User Interaction
        │
        ▼
 Jetpack Compose UI
        │
        ▼
    ViewModel
        │
        ▼
 Repository Interface
        │
        ▼
Repository Implementation
   ┌──────────────┬───────────────┐
   ▼              ▼               ▼
Remote API     Room Database   DataStore
   │              │               │
   └──────────────┴───────────────┘
                │
                ▼
         Domain Models
                │
                ▼
         StateFlow Update
                │
                ▼
      Jetpack Compose UI
```

Every layer has a single responsibility and communicates only with adjacent layers.

---

# MVVM Implementation

AtmosLive follows the Model–View–ViewModel architectural pattern.

Rather than allowing the user interface to directly communicate with repositories or networking components, every interaction is routed through a ViewModel.

The ViewModel acts as the single source of truth for the presentation layer.

## View

The View consists entirely of Jetpack Compose composables.

Responsibilities include:

- Rendering application state
- Handling user interaction
- Forwarding events
- Displaying loading, success, and error states

The View contains no business logic.

---

## ViewModel

ViewModels coordinate every user interaction.

Responsibilities include:

- Requesting weather information
- Coordinating repositories
- Managing UI state
- Handling exceptions
- Exposing immutable StateFlow

Each screen owns an independent ViewModel, preventing unrelated features from sharing mutable state.

---

## Model

The Model consists of:

- Domain Models
- Repository Interfaces
- Repository Implementations
- Remote DTOs
- Local Database Entities

The Model layer remains completely independent from the user interface.

---

# Repository Pattern

AtmosLive implements the Repository Pattern to isolate data retrieval from business logic.

Instead of allowing ViewModels to communicate directly with Retrofit, Room, or DataStore, all requests pass through repository abstractions.

```
HomeViewModel

↓

WeatherRepository

↓

WeatherRepositoryImpl

↓

Remote API
Room Database
```

This abstraction provides several important advantages.

## Decoupling

The ViewModel never knows whether weather data originates from:

- Open-Meteo
- Room Database
- Cache
- Future API providers

The repository decides which data source should be used.

---

## Flexibility

Changing a weather provider requires modifications only inside the repository implementation.

Presentation logic remains completely unaffected.

---

## Testability

Repositories can easily be replaced with fake implementations during unit testing.

This enables ViewModel testing without requiring network connectivity.

---

# Repository Responsibilities

AtmosLive currently contains several repository abstractions.

## WeatherRepository

Responsible for:

- Current weather retrieval
- Forecast retrieval
- Error handling
- DTO mapping
- Domain model creation

---

## SettingsRepository

Responsible for:

- Temperature units
- Synchronization interval
- Privacy preferences
- Persistent application settings

Implemented using Jetpack DataStore.

---

## Location Services

Location retrieval is abstracted through the LocationTracker interface.

Concrete implementation is provided by DefaultLocationTracker using Google's FusedLocationProviderClient.

This abstraction prevents ViewModels from depending directly on Android location APIs.

---

# State Management

Application state is managed using Kotlin StateFlow.

Every screen exposes immutable state objects observed directly by Jetpack Compose.

```kotlin
private val _state = MutableStateFlow(...)

val state: StateFlow<HomeState> = _state
```

This pattern guarantees that only the ViewModel may modify application state.

The UI remains strictly read-only.

---

# UI State Lifecycle

Each screen follows the same lifecycle.

```text
Loading

↓

Success

↓

Error
```

Loading

The application retrieves data while displaying progress indicators.

↓

Success

Weather information becomes available and is rendered.

↓

Error

A reusable EmptyState component provides contextual feedback together with retry actions.

This predictable lifecycle simplifies debugging and ensures consistent behavior throughout the application.

---

# Reactive Updates

StateFlow allows the interface to react automatically whenever application state changes.

Examples include:

- New weather forecast received
- Temperature unit changed
- Saved city added
- Synchronization completed
- Background worker finished

No manual UI refreshes are required.

Compose automatically recomposes only the affected UI components.

---

# Error Handling Strategy

Rather than throwing exceptions directly into the presentation layer, repositories convert failures into predictable Result objects.

Typical failure sources include:

- Network unavailable
- API timeout
- Invalid location
- GPS unavailable
- Permission denied

ViewModels transform these failures into user-friendly UI states while preventing application crashes.

This approach centralizes failure handling and maintains a consistent user experience.

---

Continue to **Part 3**, covering:

- Dependency Injection
- Room Database
- DataStore
- WorkManager
- Widget Architecture
- Navigation Architecture
- Package Structure
- Folder Organization

---

# Dependency Injection

AtmosLive uses **Dagger Hilt** as its dependency injection framework.

Rather than manually constructing dependencies throughout the application, Hilt provides and manages object lifecycles automatically, reducing boilerplate while improving scalability and testability.

Dependency injection is configured from the application entry point and remains available throughout every architectural layer.

Major injected components include:

- WeatherRepository
- Retrofit API Service
- Room Database
- DataStore
- Location Services
- WorkManager
- ViewModels

This centralized dependency graph allows individual components to remain loosely coupled while simplifying future feature expansion.

---

# Application Dependency Graph

The dependency hierarchy can be summarized as follows.

```text
DynamicWeatherApp
        │
        ▼
    Hilt Modules
        │
        ▼
Singleton Components
        │
 ┌──────┼────────┬─────────┐
 ▼      ▼        ▼         ▼
API   Room   DataStore  Location
 │      │        │         │
 └──────┼────────┴─────────┘
        │
        ▼
Repository Implementations
        │
        ▼
ViewModels
        │
        ▼
Jetpack Compose UI
```

Every dependency has a clearly defined lifecycle, preventing duplicate object creation and reducing unnecessary memory usage.

---

# Remote Networking

AtmosLive retrieves weather forecasts from the **Open-Meteo Forecast API**.

Networking is implemented using Retrofit together with Kotlin Coroutines.

Responsibilities of the networking layer include:

- HTTP request execution
- JSON deserialization
- DTO generation
- Network exception handling
- Response mapping

Network models are intentionally isolated from the rest of the application.

Instead of exposing raw API responses to the Presentation layer, Retrofit DTOs are transformed into domain models before reaching the ViewModel.

This approach prevents API implementation details from leaking throughout the project.

---

# Local Persistence

AtmosLive separates local persistence into two independent systems.

## Room Database

Room stores structured application data requiring relational persistence.

Current responsibilities include:

- Saved cities
- Cached weather information
- Location metadata

Room exposes Kotlin Flow streams that automatically notify the repository whenever local data changes.

Advantages include:

- Offline availability
- Reactive database updates
- Compile-time query validation
- SQLite abstraction

---

## DataStore

User preferences are stored using Jetpack DataStore.

Unlike SharedPreferences, DataStore provides asynchronous, coroutine-based persistence together with strong type safety.

AtmosLive currently stores:

- Temperature units
- Background synchronization interval
- Privacy policy acceptance
- Application preferences

Changes propagate reactively throughout the application using Flow.

No manual preference synchronization is required.

---

# Background Processing

Periodic work is managed using Android WorkManager.

Instead of relying on continuously running services, AtmosLive schedules constrained background tasks that execute only when Android determines appropriate system conditions.

This design aligns with Android's modern background execution policies while improving battery efficiency.

Current responsibilities include:

- Weather synchronization
- Cache updates
- Severe weather detection
- Notification generation
- Widget refresh scheduling

WorkManager ensures these operations continue functioning across device restarts without requiring manual user interaction.

---

# Weather Synchronization Pipeline

Every scheduled synchronization follows the same workflow.

```text
WorkManager

        │

        ▼

WeatherSyncWorker

        │

        ▼

WeatherRepository

        │

        ▼

Open-Meteo API

        │

        ▼

Weather Mapping

        │

        ▼

Room Database Update

        │

        ▼

StateFlow

        │

        ▼

Compose UI

Widget

Notifications
```

Maintaining a single synchronization pipeline prevents inconsistencies between the application interface, widgets, and background notifications.

---

# Widget Architecture

AtmosLive includes a home screen widget implemented using **Jetpack Glance**.

Unlike legacy RemoteViews implementations, Jetpack Glance enables a Compose-inspired API while remaining compatible with Android's App Widget framework.

Widget updates reuse the same repository responsible for the main application.

```text
Widget

        │

        ▼

Repository

        │

        ▼

Room Database

        │

        ▼

Open-Meteo API
```

Sharing the repository guarantees that both interfaces operate on identical data while avoiding duplicated networking logic.

---

# Navigation Architecture

Navigation is implemented using **Navigation Compose**.

Each screen exists as an independent navigation destination.

Current destinations include:

- Home
- Search
- Settings

Navigation state is preserved using Android's back stack restoration APIs.

Each destination retains:

- Scroll position
- Previously loaded data
- Search state
- User selections

Animated transitions are applied globally, providing smooth movement throughout the application while maintaining consistent navigation behavior.

---

# Package Organization

The project structure has been organized according to architectural responsibilities rather than individual features.

```text
app/

├── data/
│   ├── local/
│   ├── remote/
│   ├── mapper/
│   ├── repository/
│   └── worker/
│
├── domain/
│   ├── model/
│   ├── repository/
│   ├── location/
│   └── util/
│
├── presentation/
│   ├── home/
│   ├── search/
│   ├── settings/
│   ├── widget/
│   └── components/
│
├── navigation/
│
├── ui/
│
├── di/
│
└── utils/
```

This organization improves discoverability while maintaining strict architectural boundaries between layers.

---

# Architectural Benefits

The architecture adopted by AtmosLive provides several long-term advantages.

- Clear separation of responsibilities
- Predictable data flow
- Reactive user interface
- Simplified testing
- Reduced coupling
- Reusable components
- Scalable feature expansion
- Consistent engineering practices

Rather than optimizing for rapid development alone, the architecture has been designed to support long-term maintainability as additional functionality is introduced.

---

Continue to **Part 4**, covering:

- Scalability considerations
- Future architectural evolution
- Architecture summary
- Related documentation
- Closing notes

---

# Scalability Considerations

AtmosLive has been designed with long-term maintainability in mind. The current architecture intentionally separates responsibilities to ensure future features can be integrated without introducing unnecessary coupling between existing components.

Most architectural decisions were made not only to solve current requirements but also to support future application growth.

---

## Modular Expansion

New features can be introduced by extending the Presentation, Domain, and Data layers independently.

For example, adding Air Quality Index (AQI) support would require:

```text
Presentation
    ├── AQIScreen
    ├── AQIViewModel

Domain
    ├── AQIRepository
    ├── AQIModel

Data
    ├── AQIApiService
    ├── AQIRepositoryImpl
```

Existing weather functionality remains completely unaffected.

This modular approach minimizes regression risk while simplifying future development.

---

## API Independence

The networking layer has been intentionally isolated behind repository abstractions.

Current weather data is retrieved using Open-Meteo, however the remainder of the application remains unaware of the underlying provider.

Future migration to another weather service would primarily involve replacing:

- Retrofit API definitions
- DTO models
- Repository implementation

The Presentation and Domain layers would require little to no modification.

---

## UI Scalability

Jetpack Compose enables the interface to be composed from small reusable components.

Instead of constructing large monolithic screens, AtmosLive is built from independent UI elements such as:

- Weather Header
- Forecast Cards
- Metric Components
- Empty States
- Loading Indicators
- Search Components
- Settings Cards

This approach simplifies maintenance while encouraging consistent design throughout the application.

---

## State Management Scalability

Each screen maintains its own independent ViewModel and immutable UI state.

As new screens are introduced, additional ViewModels can be created without affecting existing state containers.

This prevents unrelated features from sharing mutable state while reducing unintended side effects.

The application therefore maintains a predictable state lifecycle regardless of future expansion.

---

## Background Task Scalability

WorkManager provides a centralized scheduling framework capable of managing multiple independent background operations.

Future scheduled tasks may include:

- Air Quality updates
- Weather radar synchronization
- Pollen forecasts
- Widget refresh improvements
- Periodic cache cleanup

Each worker can be introduced independently while continuing to leverage Android's constraint-based scheduling system.

---

## Widget Evolution

The current widget architecture has been designed for future extensibility.

Possible future widgets include:

- Compact Weather Widget
- Forecast Widget
- Hourly Timeline Widget
- AQI Widget
- Material You Widget

Since widgets share the application's repository layer, additional widgets can reuse existing business logic without introducing duplicated networking code.

---

## Platform Expansion

Although AtmosLive currently targets Android smartphones, the existing architecture supports future platform expansion.

Potential future targets include:

- Android Tablets
- Foldable Devices
- Wear OS
- Android Auto
- Compose Multiplatform

The clear separation between business logic and presentation simplifies adaptation to additional device categories.

---

# Architectural Trade-offs

Every architecture introduces trade-offs.

The decisions adopted within AtmosLive intentionally prioritize long-term maintainability over minimizing project size.

Examples include:

| Decision | Benefit | Trade-off |
|----------|---------|-----------|
| MVVM Architecture | Predictable state management | Additional abstraction |
| Clean Architecture | Scalability and maintainability | More project structure |
| Repository Pattern | Decoupled data sources | Additional interfaces |
| StateFlow | Reactive UI updates | Requires coroutine-based architecture |
| Hilt | Simplified dependency management | Annotation processing overhead |
| Room Database | Offline persistence | Additional database maintenance |
| WorkManager | Reliable background execution | Limited execution timing precision |

These trade-offs were considered acceptable given the project's long-term objectives.

---

# Future Architectural Improvements

Several architectural enhancements are planned for future releases.

### Planned Improvements

- Dedicated UseCase layer for complex business operations
- Expanded unit testing coverage
- Integration testing for repositories
- Modular Gradle project structure
- Improved error recovery strategy
- Enhanced offline synchronization
- Material You dynamic color architecture
- Improved widget update scheduling

These enhancements will continue strengthening the project's maintainability while preserving existing architectural principles.

---

# Architecture Summary

AtmosLive combines modern Android development practices into a cohesive architecture focused on scalability, reliability, and maintainability.

Core architectural characteristics include:

- MVVM presentation pattern
- Clean Architecture principles
- Repository abstraction
- Kotlin Coroutines
- StateFlow reactive state management
- Dagger Hilt dependency injection
- Retrofit networking
- Room local persistence
- Jetpack DataStore preferences
- WorkManager background execution
- Jetpack Glance widgets
- Navigation Compose

Rather than relying on tightly coupled components, the application maintains clear architectural boundaries that simplify development, testing, and future feature expansion.

---

# Related Documentation

The architecture described within this document is complemented by additional technical documentation.

| Document | Description |
|----------|-------------|
| **OVERVIEW.md** | Project overview and repository guide |
| **FEATURES.md** | Complete feature documentation |
| **IMPLEMENTATION.md** | Internal implementation workflows |
| **DECISIONS.md** | Engineering decisions and technology selection |
| **ROADMAP.md** | Planned features and future milestones |

---

# Conclusion

AtmosLive has been engineered as a modern Android application with a strong emphasis on clean code, predictable data flow, and long-term maintainability.

Every architectural decision—from dependency injection and repository abstraction to offline-first caching and reactive state management—has been made to ensure that the project remains scalable as new functionality is introduced.

By separating responsibilities across clearly defined layers and adopting widely accepted Android development practices, AtmosLive provides a stable foundation for future enhancements while maintaining a clean, testable, and maintainable codebase.
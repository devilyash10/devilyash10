# Architecture

BloodLink has been designed as a modern Android platform that coordinates multiple healthcare participants through a unified, role-aware architecture.

Unlike conventional applications that focus on a single user journey, BloodLink supports independent yet interconnected workflows for blood donors, hospitals, blood banks, and emergency request management. Every subsystem has been designed to operate independently while contributing to a shared real-time ecosystem powered by Firebase services.

The application combines MVVM, Jetpack Compose, Hilt, Cloud Firestore, Kotlin Coroutines, and StateFlow to create a scalable architecture capable of supporting future nationwide deployment and additional healthcare services.

This document explains the architectural principles, subsystem organization, data flow, and runtime communication that power BloodLink.

---

# Architectural Goals

Every architectural decision within BloodLink is guided by several long-term objectives.

## Role Isolation

Each user category should interact only with workflows relevant to its responsibilities.

Instead of exposing identical interfaces to every user, BloodLink provides dedicated navigation paths and dashboards for:

- Blood Donors
- Hospitals
- Blood Banks

This improves usability while reducing unnecessary complexity.

---

## Real-Time Collaboration

Emergency requests involve multiple participants simultaneously.

The architecture therefore prioritizes real-time synchronization so that request updates are reflected across connected users with minimal delay.

Cloud Firestore acts as the central synchronization layer, ensuring that request state remains consistent throughout the platform.

---

## Scalability

The architecture has been designed to support future expansion without requiring major structural changes.

Planned areas of growth include:

- Push notifications
- Advanced inventory management
- Nationwide deployment
- Multi-language support
- Healthcare partnerships
- Additional institutional roles

---

## Maintainability

Responsibilities are clearly separated across independent architectural layers.

Presentation logic, business logic, cloud communication, and shared utilities remain isolated from one another.

This separation reduces coupling while improving long-term maintainability.

---

## Cloud-First Design

BloodLink has been designed around cloud-native services.

Rather than treating remote synchronization as an optional feature, Cloud Firestore, Firebase Authentication, and Firebase Storage form the foundation of the platform's architecture.

---

# Design Principles

Several engineering principles guide every subsystem within BloodLink.

## Separation of Concerns

Each architectural layer performs a clearly defined responsibility.

- Compose renders the interface.
- ViewModels coordinate UI state.
- Repositories manage business operations.
- Firebase services provide persistence and synchronization.

Each layer communicates through stable interfaces while remaining independent from implementation details.

---

## Reactive State Management

BloodLink follows a reactive programming model.

Instead of manually refreshing screens after data changes, application state is exposed through immutable StateFlow streams observed by Jetpack Compose.

Whenever cloud data changes, the corresponding interface updates automatically.

---

## Unidirectional Data Flow

Every interaction follows the same execution pattern.

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

Repository

        │

        ▼

Firebase Services

        │

        ▼

Cloud Firestore

        │

        ▼

StateFlow

        │

        ▼

Compose UI
```

This predictable flow simplifies debugging while ensuring consistent behavior across every feature.

---

# High-Level System Architecture

BloodLink consists of several interconnected architectural subsystems.

```text
                 Jetpack Compose UI
                         │
                         ▼
                  Screen ViewModels
                         │
                         ▼
                    Repositories
                         │
        ┌────────────────┼────────────────┐
        ▼                ▼                ▼
 Firebase Auth     Cloud Firestore   Firebase Storage
        │                │                │
        └────────────────┼────────────────┘
                         ▼
                  Shared Cloud Data
```

Each subsystem communicates through clearly defined interfaces while maintaining independent responsibilities.

---

# Layered Architecture

BloodLink follows a layered architecture organized around feature modules.

The primary architectural layers are:

```text
Presentation

↓

Business Logic

↓

Data Layer

↓

Cloud Services

↓

Infrastructure
```

This layered organization allows each component to evolve independently while preserving a consistent communication model.

---

# Presentation Layer

The Presentation layer is responsible for rendering the user interface and handling user interactions.

Primary responsibilities include:

- Authentication screens
- Donor interface
- Hospital interface
- Blood Bank interface
- Emergency request screens
- Inventory screens
- Search
- Profile
- Settings

The Presentation layer contains no direct database logic.

Instead, it observes immutable UI state provided by ViewModels.

---

# Business Layer

The Business layer coordinates application workflows and user interactions.

Major responsibilities include:

- Authentication coordination
- Request management
- Inventory operations
- Profile management
- Search
- Role validation
- Dashboard state

Business logic remains independent from both Compose and Firebase implementations.

---

Continue to **Part 2**, covering:

- Repository Architecture
- Firebase Integration
- Authentication Architecture
- Role-Based Navigation
- StateFlow Communication
- Dependency Injection

---

# Repository Architecture

BloodLink adopts the Repository pattern to establish a clear separation between the Presentation layer and cloud services.

Rather than allowing ViewModels to communicate directly with Firebase Authentication, Cloud Firestore, or Firebase Storage, all business operations are delegated to dedicated repository classes.

Current repository responsibilities include:

- User authentication
- User profile management
- Emergency request operations
- Blood inventory management
- Institution management
- Image upload
- Cloud synchronization

This abstraction isolates business logic from infrastructure, improving maintainability while simplifying future backend expansion.

---

# Repository Communication Flow

Every business operation follows a predictable execution pipeline.

```text
Compose Screen

        │

        ▼

ViewModel

        │

        ▼

Repository

        │

 ┌──────┼─────────────┬──────────────┐

 ▼      ▼             ▼

Firebase Auth

Cloud Firestore

Firebase Storage

        │

        ▼

Repository Result

        │

        ▼

StateFlow

        │

        ▼

Compose UI
```

The Presentation layer never communicates directly with Firebase services, ensuring that infrastructure details remain hidden behind stable repository interfaces.

---

# Firebase Architecture

Firebase provides the cloud infrastructure that powers BloodLink.

Instead of relying on a custom backend, the platform integrates multiple Firebase services, each responsible for a distinct aspect of the application.

### Firebase Authentication

Responsible for:

- User registration
- Secure login
- Session management
- Identity verification

---

### Cloud Firestore

Acts as the application's primary cloud database.

Responsibilities include:

- User profiles
- Emergency requests
- Hospital information
- Blood bank information
- Inventory data
- Real-time synchronization

---

### Firebase Storage

Responsible for storing user-uploaded media.

Typical use cases include:

- Profile images
- Institution images
- Future document uploads

Each service remains focused on its own responsibility while collectively supporting the platform's cloud-first architecture.

---

# Authentication Architecture

Authentication forms the entry point into every workflow within BloodLink.

After successful sign-in, the platform retrieves the authenticated user's profile and determines the associated account role before loading the appropriate application interface.

The authentication sequence follows the workflow below.

```text
Application Launch

        │

        ▼

Authentication Check

        │

        ▼

Firebase Authentication

        │

        ▼

User Profile Retrieval

        │

        ▼

Role Identification

        │

        ▼

Role-Based Dashboard
```

This architecture ensures that every authenticated user enters the platform through the workflow appropriate to their responsibilities.

---

# Role-Based Navigation

Navigation within BloodLink is driven by authenticated user roles rather than static application routes.

Instead of exposing identical destinations to every account, navigation is configured dynamically after authentication.

Supported navigation paths include:

### Blood Donor

- Home
- Active Requests
- Search
- Profile
- Settings

---

### Hospital

- Dashboard
- Request Management
- Patient Requests
- Profile
- Settings

---

### Blood Bank

- Inventory
- Active Requests
- Blood Availability
- Profile
- Settings

This role-aware navigation model simplifies the user experience while reducing unnecessary complexity.

---

# Reactive State Management

BloodLink uses Kotlin StateFlow as the primary mechanism for distributing application state.

Every ViewModel exposes immutable state streams observed directly by Jetpack Compose.

Examples include:

- Authentication state
- User profile
- Emergency requests
- Blood inventory
- Search results
- Dashboard information

Whenever cloud data changes, updated values automatically propagate throughout the interface.

---

# callbackFlow Integration

Several real-time operations within BloodLink are implemented using Kotlin's `callbackFlow`.

Firebase callback-based APIs are converted into coroutine-compatible streams before entering the Presentation layer.

This provides several advantages:

- Reactive cloud updates
- Structured coroutine support
- Automatic lifecycle awareness
- Simplified asynchronous programming
- Consistent StateFlow integration

By standardizing asynchronous communication around coroutine flows, the platform maintains a clean and predictable data pipeline.

---

# Combining Multiple Data Sources

Certain screens require information originating from multiple cloud collections.

To support these scenarios, BloodLink combines reactive streams before exposing them to the Presentation layer.

Typical examples include:

- User profile with role information
- Request lists with filtering
- Inventory combined with availability status
- Dashboard summaries

This reactive aggregation allows complex interfaces to remain synchronized while minimizing redundant cloud operations.

---

# Dependency Injection

BloodLink uses Hilt to manage object creation and dependency lifecycles throughout the application.

Rather than manually instantiating repositories or Firebase services, required dependencies are injected where needed.

Primary benefits include:

- Reduced boilerplate
- Centralized dependency management
- Improved scalability
- Easier testing
- Consistent object lifecycle management

Hilt enables ViewModels, repositories, and supporting services to remain loosely coupled while simplifying application configuration.

---

Continue to **Part 3**, covering:

- Request Management Architecture
- Hospital & Blood Bank Subsystems
- Inventory Architecture
- Cloud Synchronization
- Search Architecture
- Maps Integration
- Package Organization

---

# Emergency Request Architecture

Emergency blood requests represent the primary business workflow within BloodLink.

Rather than existing as isolated database records, requests move through a structured lifecycle involving multiple participants, including patients, donors, hospitals, and blood banks.

The architecture ensures that each participant observes only the information relevant to their responsibilities while maintaining a consistent request state across the platform.

---

# Request Processing Pipeline

Every emergency request follows the same execution sequence.

```text
Request Creation

        │

        ▼

Input Validation

        │

        ▼

Repository

        │

        ▼

Cloud Firestore

        │

        ▼

Real-Time Synchronization

        │

        ▼

Eligible Participants

        │

        ▼

Request Updates

        │

        ▼

Request Completion
```

This workflow enables multiple users to collaborate on the same request without requiring manual synchronization.

---

# Request State Management

BloodLink manages every request through explicit lifecycle states rather than treating requests as static documents.

Typical state transitions include:

```text
Created

    │

    ▼

Published

    │

    ▼

Active

    │

    ▼

Accepted

    │

    ▼

Fulfilled

    │

    ▼

Closed
```

Explicit state management improves transparency while simplifying request tracking across different user roles.

---

# Donor Matching Architecture

One of BloodLink's primary objectives is reducing the time required to locate suitable blood donors.

When a request becomes active, the platform evaluates available donor information together with request requirements before presenting relevant opportunities to eligible users.

Matching considers information such as:

- Registered blood group
- Availability status
- User role
- Geographic information (where applicable)

This architecture minimizes unnecessary notifications while improving request relevance.

---

# Hospital Subsystem

Hospitals operate as institutional participants within the platform.

Rather than sharing the same workflow as individual users, hospital accounts coordinate emergency requests across multiple patients.

Primary responsibilities include:

- Creating emergency requests
- Monitoring request progress
- Coordinating with donors
- Reviewing request responses
- Managing institutional information

The Hospital subsystem communicates with the same repositories used throughout the application while exposing workflows specific to institutional requirements.

---

# Blood Bank Subsystem

Blood Banks extend the emergency response network by contributing available inventory and participating in request fulfillment.

The subsystem provides dedicated functionality for:

- Monitoring emergency requests
- Managing blood inventory
- Updating blood availability
- Supporting institutional coordination

Because Blood Banks operate independently from hospitals, the architecture isolates their workflows while maintaining shared cloud synchronization.

---

# Inventory Architecture

Blood inventory management is implemented as an independent subsystem.

Rather than coupling inventory directly with emergency requests, BloodLink maintains inventory information separately, allowing institutions to update blood availability without affecting active request workflows.

Typical inventory operations include:

- Inventory retrieval
- Availability updates
- Blood group management
- Stock monitoring

This separation improves maintainability while supporting future expansion of inventory capabilities.

---

# Cloud Synchronization

Real-time synchronization is one of BloodLink's defining architectural characteristics.

Whenever application data changes, Cloud Firestore distributes updates to connected clients through reactive data streams.

Synchronization occurs for:

- User profiles
- Emergency requests
- Inventory
- Institutional information
- Request status
- Dashboard summaries

This cloud-first architecture ensures every participant observes consistent information throughout the platform.

---

# Search Architecture

Search functionality has been designed as a reusable subsystem rather than an isolated feature.

Instead of implementing independent search logic for each screen, repositories expose filtered reactive data streams consumed by ViewModels.

Search supports:

- Blood requests
- Users
- Hospitals
- Blood Banks

Combined with reactive filtering, this approach enables responsive search experiences without unnecessary cloud queries.

---

# Maps & Location Architecture

BloodLink integrates Android location services with Google Maps to provide geographic awareness across the platform.

Location information is used to enhance user experience by presenting nearby healthcare resources and improving emergency coordination.

The architecture separates location acquisition from presentation logic.

```text
Device Location

        │

        ▼

Location Services

        │

        ▼

Repository

        │

        ▼

ViewModel

        │

        ▼

Map Screen
```

This separation simplifies testing while allowing future expansion into proximity-based donor discovery.

---

# Feature-Based Package Organization

BloodLink organizes source code by feature while preserving a layered architectural structure.

A simplified representation is shown below.

```text
app/

├── data/
├── di/
├── model/
├── navigation/
├── repository/
├── services/
├── ui/
│
│   ├── auth/
│   ├── donor/
│   ├── hospital/
│   ├── bloodbank/
│   ├── request/
│   ├── profile/
│   └── settings/
│
├── utils/
└── workers/
```

Grouping related functionality together improves readability, reduces coupling, and allows new healthcare modules to be introduced with minimal impact on existing code.

---

Continue to **Part 4**, covering:

- Performance Architecture
- Offline Strategy
- Scalability
- Dependency Graph
- Architecture Trade-offs
- Future Evolution
- Architecture Summary
---

# Performance Architecture

BloodLink has been designed to maintain responsive user interactions while coordinating multiple cloud-backed workflows.

Rather than optimizing a single subsystem, performance considerations are applied throughout the presentation layer, business layer, and cloud communication pipeline.

Key optimization strategies include:

## Reactive Data Streams

Cloud Firestore listeners propagate updates only when underlying data changes.

By subscribing to reactive streams instead of repeatedly polling the backend, BloodLink minimizes unnecessary network traffic while keeping application state synchronized.

---

## Efficient UI Rendering

Jetpack Compose renders interfaces based on immutable StateFlow objects exposed by ViewModels.

Only the composables affected by state changes are recomposed, reducing rendering overhead and improving animation smoothness.

---

## Asynchronous Cloud Operations

All network communication, database interactions, image uploads, and long-running operations execute asynchronously using Kotlin Coroutines.

This ensures that cloud operations never block the main thread, preserving a responsive user experience.

---

## Optimized Image Loading

Profile and institutional images are loaded asynchronously using Coil.

Built-in memory and disk caching reduce repeated downloads while improving scrolling performance throughout the application.

---

## Structured State Management

StateFlow acts as the single source of truth for presentation state.

By exposing immutable UI state from ViewModels, BloodLink minimizes inconsistent rendering while simplifying lifecycle management.

---

# Offline Strategy

BloodLink follows a cloud-first architecture while leveraging Firestore's offline capabilities where appropriate.

When temporary connectivity interruptions occur, previously synchronized data remains available through local caching.

The synchronization strategy provides:

- Local data caching
- Automatic synchronization after reconnection
- Seamless transition between offline and online states
- Preservation of UI state during temporary network loss

Operations requiring active server validation continue to depend on network availability.

---

# Scalability Strategy

The current architecture has been designed to support long-term platform growth.

Rather than tightly coupling features together, each subsystem communicates through repositories and shared cloud services, allowing new functionality to be introduced with minimal structural changes.

Planned areas of expansion include:

- Push notification infrastructure
- Advanced donor matching
- Nationwide deployment
- Additional healthcare institutions
- Volunteer organizations
- Regional administration
- Analytics and reporting

The existing architecture provides a flexible foundation for these future enhancements.

---

# Dependency Graph

BloodLink maintains a clear dependency hierarchy to reduce coupling between application layers.

```text
Compose Screens

        │

        ▼

ViewModels

        │

        ▼

Repositories

        │

 ┌──────┼─────────────┬──────────────┐

 ▼      ▼             ▼

Firebase Auth

Cloud Firestore

Firebase Storage

        │

        ▼

Google Services
```

Each layer depends only on the layer immediately below it, improving maintainability and simplifying future architectural changes.

---

# Architectural Trade-offs

Several engineering trade-offs were made during development.

| Decision | Benefit | Trade-off |
|----------|---------|-----------|
| Cloud Firestore | Real-time synchronization | Requires reliable network connectivity |
| MVVM | Clear separation of responsibilities | Additional architectural layers |
| StateFlow | Reactive UI updates | Increased coroutine usage |
| Repository Pattern | Decoupled business logic | Additional abstraction |
| Hilt Dependency Injection | Simplified dependency management | Learning curve and setup complexity |
| Feature-Based Organization | Better scalability | Larger project structure |
| Cloud-First Architecture | Seamless collaboration | Offline capabilities require additional planning |

These trade-offs were intentionally accepted to prioritize maintainability, scalability, and collaborative workflows.

---

# Future Architectural Evolution

BloodLink's architecture has been designed to evolve without requiring major redesigns.

Future architectural enhancements include:

- Firebase Cloud Messaging integration
- Advanced inventory synchronization
- Geolocation-based donor discovery
- Multi-region deployment
- Analytics infrastructure
- Audit logging
- Background synchronization workers
- Improved offline capabilities
- Enhanced testing infrastructure

Each enhancement can be incorporated incrementally while preserving the existing architectural foundation.

---

# Architecture Summary

BloodLink combines modern Android technologies with cloud-native services to support collaborative healthcare workflows.

The architecture is characterized by:

- MVVM architecture
- Jetpack Compose UI
- Feature-based project organization
- Hilt Dependency Injection
- Repository pattern
- Kotlin Coroutines
- StateFlow reactive programming
- Firebase Authentication
- Cloud Firestore
- Firebase Storage
- Google Maps integration
- Real-time cloud synchronization

Rather than focusing on a single user journey, the architecture enables multiple participants—including donors, hospitals, and blood banks—to collaborate through a unified and scalable platform.

---

# Related Documentation

The architecture described throughout this document is complemented by the remaining project documentation.

| Document | Description |
|----------|-------------|
| **OVERVIEW.md** | Project overview and repository guide |
| **FEATURES.md** | Complete feature reference |
| **IMPLEMENTATION.md** | Runtime workflows and internal behavior |
| **DECISIONS.md** | Engineering decisions and technology choices |
| **ROADMAP.md** | Future milestones and planned development |

---

# Conclusion

BloodLink has been engineered as a scalable Android platform that combines reactive user interfaces, cloud-native infrastructure, and role-based workflows into a unified healthcare coordination system.

By separating presentation, business logic, and cloud communication through modern architectural patterns, the platform remains maintainable while supporting real-time collaboration between donors, hospitals, and blood banks.

The architectural decisions documented here establish a strong foundation for future expansion into larger healthcare networks, enhanced institutional collaboration, and additional emergency response capabilities.
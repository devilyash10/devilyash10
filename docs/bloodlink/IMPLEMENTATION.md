# Implementation

This document describes the internal implementation of BloodLink and explains how the application behaves during runtime.

While the Architecture document focuses on system organization, this document follows the execution of major workflows as they occur inside the application. It explains how user interactions travel through the Presentation layer, repositories, cloud services, and back to the user interface.

The implementation emphasizes reactive programming, cloud synchronization, and role-aware workflows, allowing multiple healthcare participants to collaborate through a unified platform.

---

# Application Startup

Every application session begins with a controlled initialization sequence.

Rather than immediately loading the Home screen, BloodLink first verifies authentication state before determining which workflow should be presented.

The startup sequence follows the workflow below.

```text
Application Launch

        │

        ▼

Initialize Dependencies

        │

        ▼

Firebase Initialization

        │

        ▼

Authentication Check

        │

        ▼

Load User Profile

        │

        ▼

Determine User Role

        │

        ▼

Navigate to Dashboard
```

This initialization process ensures that every authenticated user enters the application through the correct role-specific workflow.

---

# Authentication Workflow

Authentication is implemented using Firebase Authentication and acts as the gateway to every feature within the platform.

When a user signs in:

```text
User Credentials

        │

        ▼

Firebase Authentication

        │

        ▼

Authentication Success

        │

        ▼

User Profile Retrieval

        │

        ▼

Role Resolution

        │

        ▼

Dashboard Navigation
```

Once authentication succeeds, additional user information is retrieved from Cloud Firestore before the interface is rendered.

This allows BloodLink to configure navigation and permissions according to the authenticated user's role.

---

# User Profile Initialization

After authentication, BloodLink initializes the user's profile information.

The initialization process retrieves cloud data required throughout the application.

Typical information includes:

- Personal details
- User role
- Blood group
- Contact information
- Profile image
- Institution details (where applicable)

Once retrieved, the information is exposed through StateFlow and becomes available to every screen requiring profile data.

---

# Dashboard Loading

Each dashboard is initialized independently according to the authenticated user's role.

Rather than loading unnecessary information, BloodLink requests only the datasets required for the active workflow.

Examples include:

### Blood Donor

- Active blood requests
- Compatible requests
- Donation history
- Profile summary

---

### Hospital

- Active emergency requests
- Request statistics
- Institution overview
- Request management

---

### Blood Bank

- Inventory information
- Active requests
- Blood availability
- Institutional profile

This selective initialization reduces unnecessary cloud operations while improving application responsiveness.

---

# Reactive Data Pipeline

BloodLink follows a reactive execution model.

Every user interaction follows the same communication pattern.

```text
User Action

        │

        ▼

Compose Screen

        │

        ▼

ViewModel

        │

        ▼

Repository

        │

        ▼

Firebase

        │

        ▼

callbackFlow

        │

        ▼

StateFlow

        │

        ▼

Compose UI
```

This pipeline eliminates manual UI synchronization while ensuring that every connected participant observes consistent application state.

---

# ViewModel Responsibilities

ViewModels coordinate business operations between the Presentation layer and repositories.

Typical responsibilities include:

- Validating user input
- Managing screen state
- Triggering repository operations
- Observing cloud updates
- Handling loading states
- Managing error messages

ViewModels remain independent from Firebase implementations, allowing repositories to encapsulate all infrastructure-related logic.

---

# Repository Execution

Repositories provide the application's central business layer.

Rather than exposing Firebase APIs directly to the user interface, repositories coordinate cloud communication and return structured results to ViewModels.

Current repository responsibilities include:

- Authentication
- User management
- Emergency requests
- Inventory operations
- Profile updates
- Image uploads
- Search operations

This abstraction keeps cloud implementation details isolated from the Presentation layer while improving maintainability.

---

# State Synchronization

Application state is propagated using immutable StateFlow streams.

Whenever repositories receive updated cloud data, the corresponding ViewModel updates its exposed state.

Compose automatically recomposes affected UI components, allowing information to remain synchronized without explicit refresh operations.

This reactive execution model forms the foundation of BloodLink's runtime behavior.

---

Continue to **Part 2**, covering:

- Emergency Request Lifecycle
- Donor Matching
- Hospital Workflow
- Blood Bank Workflow
- Inventory Updates
- Firestore Synchronization
- callbackFlow Implementation

---

# Emergency Request Lifecycle

Emergency blood requests form the primary operational workflow within BloodLink.

Unlike static database records, requests continuously evolve as different participants interact with them throughout their lifecycle.

Every request follows a structured execution sequence that begins with creation and concludes only after successful fulfillment or closure.

---

# Request Creation Workflow

When an authorized user creates a new blood request, BloodLink validates the submitted information before synchronizing it with Cloud Firestore.

The complete workflow is illustrated below.

```text
User Input

        │

        ▼

Input Validation

        │

        ▼

Request ViewModel

        │

        ▼

Request Repository

        │

        ▼

Cloud Firestore

        │

        ▼

Real-Time Distribution

        │

        ▼

Eligible Participants
```

Only validated requests are committed to the cloud, ensuring consistency throughout the platform.

---

# Request State Updates

After publication, every request progresses through a defined lifecycle.

Whenever a participant performs an action, the corresponding request state is updated inside Cloud Firestore.

Examples of state transitions include:

- Created
- Published
- Active
- Accepted
- Fulfilled
- Closed

Every authorized participant observes these transitions automatically through reactive cloud synchronization.

---

# Donor Discovery Workflow

Once a request becomes active, BloodLink begins identifying relevant donor opportunities.

Rather than presenting every request to every user, the platform filters available requests according to donor eligibility.

Matching currently considers:

- Registered blood group
- Account role
- Availability status
- Request visibility

This filtering process reduces unnecessary information while helping donors focus on requests they are more likely to fulfill.

---

# Hospital Coordination Workflow

Hospital accounts coordinate emergency requests from creation through completion.

Whenever a hospital creates or manages a request, the workflow follows a structured sequence.

```text
Hospital Dashboard

        │

        ▼

Create Request

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

Donor Responses

        │

        ▼

Request Monitoring
```

Hospitals remain informed about request progress through continuously synchronized cloud data rather than manual refresh operations.

---

# Blood Bank Workflow

Blood Banks participate as institutional collaborators within the emergency response process.

Their runtime workflow includes:

- Viewing active requests
- Reviewing blood availability
- Updating inventory
- Participating in fulfillment
- Monitoring institutional activity

This subsystem operates independently while remaining synchronized with the same cloud infrastructure used throughout the platform.

---

# Inventory Update Workflow

Inventory management follows an independent execution path.

Whenever Blood Bank personnel modify blood availability, the update is processed through the repository layer before being synchronized with Cloud Firestore.

```text
Inventory Update

        │

        ▼

Inventory ViewModel

        │

        ▼

Inventory Repository

        │

        ▼

Cloud Firestore

        │

        ▼

Updated Inventory

        │

        ▼

Compose UI
```

Separating inventory from emergency requests simplifies maintenance while supporting future inventory expansion.

---

# Firestore Synchronization

Cloud Firestore acts as the central synchronization engine for BloodLink.

Instead of repeatedly querying the database, repositories subscribe to cloud updates and convert incoming snapshots into reactive streams.

Information synchronized in real time includes:

- User profiles
- Emergency requests
- Inventory records
- Institution information
- Dashboard statistics

This event-driven model keeps every participant synchronized without unnecessary network requests.

---

# callbackFlow Integration

Firebase listeners are implemented using Kotlin's `callbackFlow` to bridge callback-based APIs with coroutine-based reactive streams.

The execution sequence follows the pattern below.

```text
Cloud Firestore

        │

        ▼

Snapshot Listener

        │

        ▼

callbackFlow

        │

        ▼

Repository

        │

        ▼

StateFlow

        │

        ▼

Compose UI
```

This approach provides lifecycle-aware data streams while simplifying asynchronous programming across the application.

---

# Combining Cloud Data

Several user interfaces require information originating from multiple cloud sources.

To support these scenarios, repositories combine multiple reactive streams before exposing them to the Presentation layer.

Examples include:

- Dashboard summaries
- Request filtering
- Profile information
- Inventory status
- Institution data

By combining cloud data before it reaches the user interface, ViewModels remain focused on presentation logic rather than data aggregation.

---

# Error Handling

Runtime operations anticipate both network failures and invalid user input.

Typical scenarios include:

- Authentication failures
- Network interruptions
- Firestore operation failures
- Image upload failures
- Invalid form submissions

Repositories return structured results that allow ViewModels to present meaningful feedback while maintaining consistent application state.

---

Continue to **Part 3**, covering:

- Image Upload Implementation
- Search & Filtering Runtime
- Maps & Location Workflow
- Offline Behaviour
- Performance Optimizations
- Memory Management
- Runtime Summary
- Related Documentation

---

# Image Upload Implementation

BloodLink supports cloud-based profile image management using Firebase Storage.

Rather than storing image data directly inside Cloud Firestore, uploaded media is handled independently to improve scalability and reduce database overhead.

The upload process follows the workflow below.

```text
User Selects Image

        │

        ▼

Image Validation

        │

        ▼

Firebase Storage

        │

        ▼

Download URL

        │

        ▼

Cloud Firestore

        │

        ▼

Updated Profile
```

Once the upload is complete, the generated download URL is stored within the user's profile, allowing the application to retrieve profile images efficiently across future sessions.

---

# Search & Filtering Implementation

Search functionality has been designed around reactive filtering rather than repeated cloud requests.

As users interact with the search interface, filtering operations execute through the repository layer before updated results are exposed to the Presentation layer.

The execution flow follows the sequence below.

```text
Search Query

        │

        ▼

Search ViewModel

        │

        ▼

Repository

        │

        ▼

Reactive Filtering

        │

        ▼

StateFlow

        │

        ▼

Compose UI
```

Whenever the search query changes, only the relevant UI components are recomposed, providing responsive interaction while minimizing unnecessary processing.

---

# Maps & Location Workflow

Location-aware functionality combines Android's location services with Google Maps to improve healthcare coordination.

Whenever location information is required, the application follows a structured workflow.

```text
Location Permission

        │

        ▼

Fused Location Provider

        │

        ▼

Current Location

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

Separating location acquisition from presentation logic allows the same location data to be reused across multiple workflows while simplifying future enhancements.

---

# Profile Synchronization

Profile updates are synchronized automatically using Cloud Firestore.

Whenever users modify personal or institutional information, the updated data is validated before being written to the cloud.

The synchronization sequence includes:

- Input validation
- Repository update
- Firestore write
- Snapshot listener notification
- StateFlow update
- Automatic UI refresh

This reactive approach ensures profile information remains consistent across all connected sessions.

---

# Offline Behaviour

BloodLink has been designed around cloud-native services while taking advantage of Firestore's built-in offline capabilities.

When temporary network interruptions occur:

- Previously synchronized data remains available.
- Cached information continues to populate supported screens.
- Pending cloud operations resume after connectivity is restored.
- Application state remains stable during configuration changes.

Operations requiring authentication or immediate server validation continue to depend on active network connectivity.

---

# Coroutine Execution Model

Long-running operations execute asynchronously using Kotlin Coroutines.

Typical background operations include:

- Authentication requests
- Cloud Firestore operations
- Firebase Storage uploads
- Profile synchronization
- Inventory updates
- Request synchronization
- Image loading

Running these operations outside the main thread prevents interface blocking while maintaining responsive user interactions.

---

# Runtime Performance

Several implementation strategies contribute to BloodLink's runtime performance.

## Reactive Cloud Streams

Cloud updates are received through persistent snapshot listeners rather than repeated polling, reducing network overhead while keeping data synchronized.

---

## Efficient UI Rendering

Compose automatically recomposes only the components affected by StateFlow updates, minimizing unnecessary rendering work.

---

## Asynchronous Image Loading

Coil manages profile and institutional images using asynchronous loading together with memory and disk caching.

This improves scrolling performance while reducing repeated downloads.

---

## Optimized State Management

ViewModels expose immutable StateFlow objects as the single source of truth for presentation state.

This approach minimizes inconsistent UI behaviour while simplifying lifecycle management.

---

# Memory Management

Resource usage is controlled throughout the application's execution.

Current practices include:

- Lifecycle-aware ViewModels
- Automatic coroutine cancellation
- Managed Firebase listeners
- Cached image resources
- Scoped dependency injection through Hilt

These mechanisms help reduce unnecessary resource consumption while maintaining application stability.

---

# Runtime Summary

BloodLink combines modern Android technologies with cloud-native services to deliver responsive, real-time healthcare workflows.

Core runtime characteristics include:

- Firebase Authentication
- Cloud Firestore synchronization
- Firebase Storage integration
- Hilt dependency injection
- Repository-based business logic
- Kotlin Coroutines
- callbackFlow
- StateFlow
- Jetpack Compose
- Google Maps integration

Together, these technologies enable multiple healthcare participants to collaborate through a unified and reactive platform.

---

# Related Documentation

Additional technical documentation is available throughout the repository.

| Document | Description |
|----------|-------------|
| **OVERVIEW.md** | Project introduction and repository guide |
| **FEATURES.md** | Complete feature reference |
| **ARCHITECTURE.md** | System architecture and subsystem design |
| **DECISIONS.md** | Engineering decisions and technology selection |
| **ROADMAP.md** | Future milestones and planned development |

---

# Conclusion

BloodLink's implementation combines modern Android architecture, cloud-native services, and reactive programming into a platform capable of supporting collaborative healthcare workflows.

By coordinating authentication, emergency request management, institutional participation, cloud synchronization, and role-aware interfaces through well-defined runtime pipelines, the application establishes a scalable foundation for continued development while maintaining responsiveness, maintainability, and a consistent user experience.
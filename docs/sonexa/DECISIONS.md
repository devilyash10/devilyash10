# Engineering Decisions

Every major technology, library, and architectural pattern used within Sonexa was selected to solve a specific engineering problem rather than simply following current development trends.

As the application evolved from a basic local music player into a production-oriented media platform, each decision was evaluated based on scalability, maintainability, performance, user experience, and long-term extensibility.

This document explains the reasoning behind those decisions.

---

# Why Kotlin?

Sonexa is implemented entirely in Kotlin.

Music applications require continuous asynchronous operations including playback monitoring, media scanning, image loading, and database synchronization.

Kotlin provides language features that naturally simplify these workflows.

Primary advantages include:

- Null Safety
- Coroutines
- Extension Functions
- Data Classes
- Sealed Classes
- Modern Android support

These capabilities reduce boilerplate while improving readability and application reliability.

---

# Why Jetpack Compose?

The entire user interface has been developed using Jetpack Compose.

Instead of maintaining XML layouts together with imperative UI updates, Compose allows the interface to become a direct representation of application state.

This approach offers several advantages.

- Less boilerplate
- Better animation support
- Easier maintenance
- Reactive rendering
- Faster UI iteration

Compose also integrates naturally with Kotlin StateFlow, allowing playback information to update automatically throughout the interface.

---

# Why Media3 Instead of Legacy ExoPlayer APIs?

Media3 represents Google's current recommendation for modern Android media applications.

It provides a unified framework for playback, MediaSession integration, notifications, Bluetooth controls, Android Auto, and future platform expansion.

Media3 was selected because it provides:

- Active long-term support
- Better MediaSession integration
- Modern APIs
- Improved extensibility
- Cleaner architecture

Adopting Media3 also simplifies future integration with Android Auto and Wear OS.

---

# Why MediaSessionService?

Music playback should continue independently from the application's user interface.

Embedding ExoPlayer directly inside Activities or Composables would tightly couple playback to the lifecycle of visible screens.

MediaSessionService solves this problem by hosting playback inside a persistent Android service.

Benefits include:

- Continuous playback
- Lock screen controls
- Bluetooth headset integration
- Quick Settings controls
- Android Auto compatibility
- Improved lifecycle management

This architecture closely follows Android's official recommendations for media applications.

---

# Why AudioController?

Rather than exposing Media3 directly throughout the application, Sonexa introduces an intermediate AudioController.

The controller centralizes playback coordination while isolating Media3 implementation details from ViewModels.

Responsibilities include:

- Playback commands
- Queue management
- StateFlow updates
- Audio Session distribution
- Playback position tracking

This abstraction significantly reduces coupling between the Presentation layer and the playback engine.

---

# Why StateFlow Instead of LiveData?

Playback state changes continuously.

Using StateFlow provides a coroutine-native reactive stream capable of emitting frequent playback updates efficiently.

StateFlow also integrates directly with Jetpack Compose through collectAsState().

Advantages include:

- Coroutine-first design
- Immutable state exposure
- Better Compose interoperability
- Predictable state updates
- Reactive playback synchronization

---

# Why Room Database?

Android's MediaStore provides information about audio files but cannot store application-specific information.

Room Database was selected to persist:

- Favorites
- Playlists
- Listening history
- Playback statistics

Keeping this information separate from MediaStore preserves user-generated data independently from the device's physical music collection.

---

# Why MediaStore?

MediaStore is Android's official interface for accessing device media.

Using MediaStore provides several advantages over manually scanning directories.

Benefits include:

- Scoped Storage compliance
- Better Android compatibility
- Faster queries
- Metadata access
- Reduced permission complexity

MediaStore therefore serves as the foundation of Sonexa's local music library.

---

# Why Native AudioFX?

Instead of implementing software-based equalization, Sonexa integrates directly with Android's AudioFX framework.

This approach allows compatible hardware DSP components to perform audio processing.

Advantages include:

- Lower latency
- Native processing
- Better battery efficiency
- Hardware acceleration
- System compatibility

Device capabilities may vary, but supported hardware benefits from improved performance compared with purely software-based processing.

---

# Why Palette API?

Album artwork provides valuable visual information beyond simply displaying an image.

Palette extraction allows Sonexa to generate dynamic themes that adapt to every song.

Rather than maintaining static accent colors, the player interface evolves automatically according to the dominant colors contained within album artwork.

This creates a more immersive listening experience while maintaining visual consistency.

---

# Why Coil?

Album artwork changes frequently during playback.

Coil was selected because it provides:

- Coroutine support
- Efficient image caching
- Compose integration
- Low memory usage
- Smooth asynchronous loading

This improves scrolling performance while reducing image loading latency.

---

# Why Feature-Based Organization?

The project organizes source code primarily by feature rather than strictly by architectural layer.

Instead of grouping every ViewModel or Repository into separate directories, each feature maintains its own UI and supporting components.

Benefits include:

- Better modularity
- Easier navigation
- Improved scalability
- Reduced coupling
- Cleaner feature ownership

This structure becomes increasingly valuable as the application grows.

---

# Why Cloud-Ready Architecture?

Although Sonexa currently focuses on local playback, the playback engine has been designed around a unified Song model.

Because every track exposes a generic media URI, the playback engine remains unaware of whether content originates from:

- Local storage
- Cloud storage
- Future streaming services

This architectural decision allows future streaming integration with minimal changes to existing playback logic.

---

# Guiding Engineering Principles

Several principles influence every implementation decision throughout Sonexa.

- Simplicity before complexity
- Reactive programming over manual updates
- Separation of concerns
- Reusable components
- Platform-first integration
- Long-term maintainability
- User experience driven engineering

These principles continue guiding future development as the application expands.

---

# Conclusion

Sonexa has been designed as a modern Android media platform where every architectural decision supports a specific engineering objective.

From Media3 and MediaSessionService to AudioFX, StateFlow, Room Database, and Jetpack Compose, each technology has been selected to improve scalability, maintainability, responsiveness, and user experience.

As the project evolves toward cloud streaming and cross-device support, these engineering decisions provide a stable foundation capable of supporting future growth without requiring significant architectural redesign.
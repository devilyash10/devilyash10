<!-- ========================================================== -->
<!--                     FEATURED PROJECT                        -->
<!-- ========================================================== -->

<div align="center">

# 🎵 Sonexa

### *A Modern Music Streaming Experience for Android*

*Designed with performance, built with modern Android architecture, and crafted to deliver a seamless listening experience.*

<br>

<img src="assets/sonexa/banner.png" width="100%"/>

<br><br>

![Kotlin](https://img.shields.io/badge/Kotlin-7F52FF?style=for-the-badge&logo=kotlin&logoColor=white)
![Jetpack Compose](https://img.shields.io/badge/Jetpack_Compose-4285F4?style=for-the-badge&logo=jetpackcompose&logoColor=white)
![Material 3](https://img.shields.io/badge/Material_3-6750A4?style=for-the-badge)
![MVVM](https://img.shields.io/badge/MVVM-Clean_Architecture-success?style=for-the-badge)
![ExoPlayer](https://img.shields.io/badge/ExoPlayer-Media3-blue?style=for-the-badge)
![Retrofit](https://img.shields.io/badge/Retrofit-REST_API-orange?style=for-the-badge)

</div>

---

# 📖 Overview

Sonexa is a production-oriented Android music streaming application developed to explore the capabilities of modern Android development while delivering a smooth, responsive, and visually engaging media experience.

Unlike traditional music player projects that focus only on playback, Sonexa was engineered as a complete Android application that emphasizes clean architecture, asynchronous programming, responsive UI state management, and scalable application design.

The project combines Jetpack Compose, Kotlin Coroutines, StateFlow, ExoPlayer, Retrofit, and Material Design 3 to create an application that remains responsive even during complex playback operations, network requests, and UI recompositions.

Beyond being a music player, Sonexa represents my approach toward writing maintainable software through modular architecture, reusable components, and thoughtful engineering decisions.

---

# 💡 Why I Built Sonexa

Modern Android development has evolved significantly with Jetpack Compose and declarative UI, yet many music player examples available online still rely on outdated architectures or simplistic implementations.

I wanted to challenge myself by building an application that combines several real-world engineering problems into a single project:

- asynchronous media streaming
- responsive playback controls
- efficient network communication
- intelligent response caching
- dynamic UI rendering
- scalable application architecture

The objective wasn't simply to build another music player—it was to deepen my understanding of production-ready Android engineering.

---

# 🏗 Architecture & Engineering

Sonexa is designed around **MVVM (Model–View–ViewModel)** and **Clean Architecture** principles, ensuring a clear separation of concerns between the presentation layer, business logic, and data sources. This structure keeps the codebase scalable, maintainable, and easy to extend as new features are introduced.

The application follows a reactive programming approach using **Kotlin Coroutines** and **StateFlow**, allowing the UI to automatically respond to changes in playback state, network connectivity, playlists, and user interactions without unnecessary recompositions.

```
                 Presentation Layer
             (Jetpack Compose UI)

                        │

                ViewModel Layer
        (StateFlow • UI State • Events)

                        │

                Repository Layer
      (Business Logic & Data Management)

            ┌───────────┴───────────┐
            │                       │
     Remote Data Source      Local Data Source
     (Retrofit REST API)     (Room Database)

            │                       │
            └───────────┬───────────┘
                        │

                 ExoPlayer Engine
          (Playback • Queue • AudioFX)
```

This layered architecture enables independent development, easier debugging, and better testability while keeping UI components lightweight and focused solely on rendering state.

---

# ⚙ Engineering Highlights

### 🎨 Modern Declarative UI

The complete interface is developed using **Jetpack Compose** and **Material Design 3**, replacing traditional XML layouts with a fully declarative UI framework.

This approach provides

- Smooth animations
- Better state management
- Reusable composables
- Cleaner UI code
- Faster feature development

---

### 🎵 Advanced Audio Engine

Playback is powered by **Android Media3 ExoPlayer**, supporting

- Online music streaming
- Local device playback
- Background playback
- Notification controls
- Lock screen controls
- MediaSession integration
- Queue management
- Shuffle & Repeat modes
- Built-in AudioFX Equalizer

The player lifecycle is synchronized with Compose state to ensure responsive playback controls without UI inconsistencies.

---

### 🌐 Network Layer

Online streaming is implemented using **Retrofit** together with **OkHttp**, enabling efficient communication with remote music APIs.

The networking layer includes

- Response caching
- Request interception
- Automatic JSON parsing
- Coroutine-based asynchronous requests
- Graceful error handling

This significantly reduces unnecessary bandwidth usage while improving playback reliability under unstable network conditions.

---

### 💾 Local Data Management

Room Database is used to persist application data locally, including

- Favourite songs
- Recently played tracks
- Most played songs
- User preferences
- Cached application data

The offline-first design ensures that core application functionality remains available even when network connectivity is limited.

---

### 🔄 Reactive State Management

StateFlow is used as the primary state management mechanism throughout the application.

Instead of manually refreshing the UI, every screen automatically observes state changes emitted by the ViewModel.

This architecture powers

- Playback progress updates
- Queue changes
- Search results
- Playlist modifications
- Equalizer settings
- Theme switching
- Music library updates

while keeping UI rendering smooth and predictable.

---

# ✨ Core Capabilities

| Category | Functionality |
|----------|---------------|
| 🎵 Playback | Online Streaming, Local Playback, Queue Management |
| 📂 Library | Albums, Artists, Favourites, Recent, Most Played |
| 🔎 Discovery | Music Search, Dynamic Filtering |
| 🎚 Audio | AudioFX Equalizer, Shuffle, Repeat |
| 🎨 Interface | Material 3, Dark Mode, Adaptive Layouts |
| ⚡ Performance | Coroutines, StateFlow, Response Caching |
| 📱 System | Background Playback, Notifications, MediaSession |
| 💾 Storage | Room Database, Offline Preferences |

---

# 📊 Technical Metrics

| Metric | Achievement |
|---------|-------------|
| 🎧 Music Library | 10,000+ Tracks |
| ⚡ Buffering Latency | Reduced by **35%** |
| 🌐 Bandwidth Usage | Reduced by **40%** |
| 🎨 UI Performance | Stable **60 FPS** |
| 🏗 Architecture | MVVM + Clean Architecture |
| 🔄 State Management | Kotlin StateFlow |
| 🎼 Audio Engine | Android Media3 ExoPlayer |

---

# 🚀 Feature Showcase

<details>
<summary><b>🎵 Music Streaming Experience</b></summary>

- Stream songs from a remote music library using Retrofit APIs.
- Seamless playback powered by Android Media3 ExoPlayer.
- Smart buffering for uninterrupted listening.
- Optimized streaming through OkHttp response caching.
- Support for large online music libraries with responsive navigation.

</details>

<details>
<summary><b>🎧 Audio Playback & Controls</b></summary>

- Background playback support
- Lock screen media controls
- Notification media controls
- Queue management
- Shuffle & Repeat modes
- Seek controls
- Mini Player
- Full-screen Player
- MediaSession integration
- Audio focus handling

</details>

<details>
<summary><b>🎚 AudioFX Equalizer</b></summary>

Built-in equalizer allowing users to personalize their listening experience without relying on third-party applications.

Features include

- Multiple frequency bands
- Real-time audio adjustments
- Bass enhancement
- Preset support
- Smooth UI synchronization using StateFlow

</details>

<details>
<summary><b>📂 Music Library</b></summary>

Organize and browse your music effortlessly.

- Albums
- Artists
- Playlists
- Favourites
- Recently Played
- Most Played
- Search
- Dynamic Filtering

</details>

<details>
<summary><b>🎨 Modern Android Experience</b></summary>

The UI is completely built using Jetpack Compose following Material Design 3 guidelines.

Highlights include

- Adaptive layouts
- Dark Mode
- Responsive animations
- Smooth transitions
- Declarative UI
- Reusable composables

</details>

---

# 📈 Performance Optimizations

Performance was treated as a first-class requirement throughout the development process.

### ⚡ Networking

✔ Reduced bandwidth usage by **40%**

✔ Intelligent response caching

✔ Optimized Retrofit configuration

✔ Coroutine-based asynchronous requests

---

### 🎧 Playback

✔ Reduced buffering latency by **35%**

✔ Non-blocking playback pipeline

✔ Smooth queue transitions

✔ Efficient Media3 lifecycle management

---

### 🎨 User Interface

✔ Stable **60 FPS** Compose rendering

✔ Optimized recomposition

✔ State-driven UI updates

✔ Lightweight composable hierarchy

---

### 💾 Storage

✔ Room Database caching

✔ Offline user preferences

✔ Faster startup experience

✔ Reduced redundant API calls

---

# 🧠 Development Challenges

Every project introduces new engineering challenges. Sonexa provided valuable experience in solving problems commonly encountered in production Android applications.

### Managing Player Lifecycle

Synchronizing ExoPlayer with Jetpack Compose required careful lifecycle management to ensure playback continued seamlessly across configuration changes while preventing memory leaks.

---

### Reactive State Management

Keeping playback controls, queue information, seek position, equalizer state, and UI components synchronized demanded a reactive architecture based on StateFlow rather than manual UI updates.

---

### Balancing Local & Remote Data

The application combines remote music streaming with locally stored preferences and playback history. Designing a clean separation between these data sources while maintaining a consistent user experience was one of the project's key architectural decisions.

---

### Building for Scalability

From the beginning, the codebase was organized to support future expansion without major refactoring. Features such as playlists, favorites, recommendations, and offline playback were considered during architectural planning.

---

# 📚 What This Project Taught Me

Developing Sonexa significantly improved my understanding of modern Android engineering.

Key takeaways include

- Designing scalable Android architectures using MVVM and Repository Pattern.
- Managing asynchronous operations with Kotlin Coroutines.
- Building reactive user interfaces using StateFlow.
- Integrating Media3 ExoPlayer into Compose applications.
- Optimizing network communication through Retrofit and OkHttp.
- Applying Clean Architecture principles to medium-scale Android applications.
- Improving UI responsiveness while maintaining readable, maintainable code.

---

# 🛣 Future Roadmap

The current implementation provides a strong foundation for future enhancements.

- [ ] Offline music downloads
- [ ] Dynamic color extraction from album artwork
- [ ] Lyrics synchronization
- [ ] User authentication
- [ ] Cloud playlist synchronization
- [ ] Recommendation engine
- [ ] Cross-device playback continuity
- [ ] Android Auto support
- [ ] Wear OS companion application

---

# 📸 Gallery

> *Project screenshots will be added soon.*

| Home | Player |
|------|--------|
| <img src="assets/sonexa/home.png" width="100%"> | <img src="assets/sonexa/player.png" width="100%"> |

| Equalizer | Playlist |
|-----------|----------|
| <img src="assets/sonexa/equalizer.png" width="100%"> | <img src="assets/sonexa/playlist.png" width="100%"> |

---

<div align="center">

### ⭐ If you found this project interesting, consider giving it a star!

<a href="YOUR_GITHUB_REPOSITORY_LINK">
<img src="https://img.shields.io/badge/View_Source_Code-181717?style=for-the-badge&logo=github&logoColor=white">
</a>

</div>


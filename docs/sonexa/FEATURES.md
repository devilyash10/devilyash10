# Features

Sonexa has been engineered to deliver a premium Android music experience by combining modern Android architecture, high-performance audio playback, and an immersive user interface.

Rather than functioning solely as a local music player, the application has been designed as a scalable media platform capable of supporting both offline playback and future cloud streaming services through a unified playback architecture.

This document describes every major capability currently available within Sonexa together with the design philosophy behind each feature.

---

# Core Experience

The primary objective of Sonexa is to provide uninterrupted, responsive, and visually engaging music playback.

Every major workflow—from discovering songs to controlling playback—has been designed around three principles:

- Instant responsiveness
- Reactive user interfaces
- Seamless background playback

The application combines Media3, Jetpack Compose, StateFlow, and Material 3 to create a modern Android listening experience.

---

# Intelligent Music Library

Sonexa automatically scans local device storage using Android's MediaStore API to build a structured music library.

Rather than indexing every audio file on the device, the scanner intelligently filters unsupported or unwanted content such as messaging application voice notes and temporary recordings.

Supported audio formats include:

- MP3
- WAV
- FLAC
- AAC
- M4A

The resulting library is organized into a unified Song model used consistently throughout the application.

### Benefits

- Fast media discovery
- Reduced library clutter
- Native Android compatibility
- Future cloud integration support

---

# Background Audio Playback

Music playback continues uninterrupted regardless of application state.

Playback is hosted inside a dedicated MediaSessionService rather than the user interface.

This enables music to continue while:

- The application is minimized
- The device is locked
- The screen rotates
- The user switches applications

The playback engine remains independent from Compose, ensuring lifecycle changes never interrupt the listening experience.

---

# Local & Cloud Ready Architecture

Although the current release focuses primarily on local playback, Sonexa has been architected to support cloud streaming without redesigning the playback engine.

Every song is represented using a unified data model.

A media source may reference:

- Local MediaStore content
- Remote HTTPS streaming URLs

Since Media3 natively supports both formats, future streaming integration requires minimal changes to the presentation layer.

---

# Reactive Playback Controls

Playback controls are fully reactive and synchronized with the underlying Media3 player.

Every user interaction—including play, pause, seek, skip, repeat, and shuffle—is propagated through StateFlow before reaching the Compose interface.

The user interface therefore reflects playback state instantly without requiring manual refresh logic.

### Current Controls

- Play / Pause
- Next Track
- Previous Track
- Seek Position
- Repeat Modes
- Shuffle Playback
- Queue Navigation

---

# Dynamic Player Experience

The Now Playing screen serves as the central interaction hub for active playback.

Rather than displaying static information, the player continuously reacts to the currently playing media.

Displayed information includes:

- Album artwork
- Song title
- Artist information
- Playback progress
- Audio controls
- Queue actions

Transitions between tracks occur smoothly without disrupting playback.

---

# Animated MiniPlayer

Sonexa includes a persistent MiniPlayer positioned at the bottom of the application.

The MiniPlayer provides immediate access to playback controls while users continue browsing their music library.

Displayed information includes:

- Album artwork
- Song title
- Play / Pause
- Skip controls
- Animated playback indicator

Selecting the MiniPlayer expands it into the full player using smooth, Apple Music-inspired transitions.

---

# Immersive Player Interface

The full player has been designed to maximize focus on the currently playing track.

Large album artwork, responsive controls, adaptive gradients, and fluid animations combine to create an immersive listening experience.

The player emphasizes:

- Large touch targets
- Smooth animations
- Clean typography
- Minimal visual distractions
- Consistent Material 3 interaction patterns

---

# Smart Search

Sonexa includes an integrated search experience allowing users to quickly locate music within their local library.

Search updates dynamically as users type, providing responsive filtering without requiring manual refresh actions.

Search supports:

- Song titles
- Artist names
- Album names

Results update automatically using reactive state management.

---

# Media Library Organization

Music is presented through multiple logical collections rather than a single scrolling list.

Current collections include:

- All Songs
- Favorites
- Recently Played
- Most Played
- User Playlists

This organization enables faster navigation while improving music discovery.

---

Continue to **Part 2**, covering:

- Playlists & Favorites
- Intelligent History Tracking
- AudioFX Engine
- Dynamic Palette UI
- Lyrics Engine
- Navigation Experience
- Material 3 Design
---

# Playlist Management

Sonexa provides a flexible playlist management system that allows users to organize their music into custom collections.

Unlike temporary playback queues, playlists are stored persistently using Room Database and remain available across application launches.

Users can:

- Create custom playlists
- Rename playlists
- Delete playlists
- Add songs
- Remove songs
- Browse playlist contents

The playlist system has been designed around relational database mappings, allowing songs to belong to multiple playlists without data duplication.

### Benefits

- Persistent storage
- Efficient database queries
- Scalable architecture
- Future cloud synchronization support

---

# Favorites Collection

Frequently played songs can be marked as favorites for quick access.

Favorite information is stored locally inside the Room database and updates reactively throughout the application.

Whenever a song is marked or unmarked as a favorite:

- The library updates automatically
- The Favorites screen refreshes instantly
- Playback information remains synchronized
- Database consistency is maintained

Reactive updates eliminate the need for manual refresh operations.

---

# Intelligent Listening History

Sonexa continuously analyzes playback activity to generate listening insights automatically.

Instead of requiring users to manually organize recently played music, the application intercepts playback events and records listening history in the background.

Current collections include:

- Recently Played
- Most Played

History updates occur automatically whenever a track reaches a valid playback state.

### Engineering Highlights

- Background database updates
- Automatic statistics generation
- Non-blocking database operations
- Reactive UI synchronization

---

# Native AudioFX Engine

One of Sonexa's most distinctive capabilities is its direct integration with Android's native AudioFX framework.

Rather than relying on software-based equalization, Sonexa communicates directly with the device's Digital Signal Processor (DSP), allowing supported hardware to perform real-time audio processing.

Available audio controls include:

- 5-Band Equalizer
- Bass Boost
- Virtualizer
- Built-in Presets

Because processing occurs through Android's native audio pipeline, supported devices can deliver lower latency and improved audio fidelity.

---

# Hardware Equalizer

The Equalizer module attaches directly to the active ExoPlayer audio session.

Whenever playback begins, Sonexa retrieves the active Audio Session ID and binds hardware audio effects to that session.

This implementation ensures that equalizer adjustments affect only Sonexa's playback while leaving other applications unaffected.

The Equalizer interface provides intuitive controls for:

- Individual frequency bands
- Preset selection
- Gain adjustment
- Reset functionality

---

# Bass Boost & Virtualizer

Beyond traditional equalization, Sonexa exposes Android's native Bass Boost and Virtualizer effects.

These effects operate alongside the Equalizer, allowing users to customize their listening experience according to personal preference.

Bass Boost enhances lower frequencies, producing a fuller sound profile on compatible hardware.

Virtualizer introduces spatial processing designed to improve stereo separation, particularly when using headphones.

Because these effects are hardware-dependent, available functionality may vary across Android devices.

---

# Dynamic Palette Experience

Sonexa dynamically adapts its interface using the Palette API.

Whenever album artwork changes, dominant colors are extracted asynchronously and applied throughout the player interface.

Affected UI elements include:

- Background gradients
- Playback controls
- Progress indicators
- Accent colors
- Interactive components

This creates a personalized visual experience where each song generates its own unique interface.

---

# Adaptive Visual Design

Instead of relying on a fixed color scheme, the application's appearance continuously evolves alongside the currently playing album.

Color extraction is performed asynchronously to prevent blocking the user interface.

Smooth animated transitions ensure visual consistency when switching between tracks.

The result is an interface that feels responsive, dynamic, and closely connected to the media being played.

---

# Immersive Lyrics Engine

Sonexa includes a synchronized lyrics experience inspired by modern streaming platforms.

Lyrics are parsed from standard LRC files containing timestamped text entries.

As playback progresses, the application automatically identifies the active lyric line and smoothly scrolls it into view.

The active lyric receives visual emphasis through animated styling while surrounding lines remain subtly de-emphasized.

This produces a distraction-free reading experience that remains synchronized with playback.

---

# Haptic Feedback

Critical interactions throughout the application provide subtle tactile feedback using Android's LocalHapticFeedback APIs.

Haptic responses have been integrated into actions including:

- Play / Pause
- Track navigation
- Favorite toggles
- Equalizer adjustments
- Slider interactions

These interactions provide immediate physical confirmation while maintaining a lightweight user experience.

---

# Material 3 User Experience

Sonexa adopts Google's Material 3 design language while extending it with a premium visual identity.

The interface combines:

- Glassmorphism
- Rounded surfaces
- Soft shadows
- Animated transitions
- Adaptive spacing
- Large album artwork
- Responsive typography

Every screen has been designed specifically for Jetpack Compose, enabling smooth animations and responsive layouts across supported Android devices.

---

# AMOLED Optimized Theme

A dedicated AMOLED theme provides an alternative visual experience for OLED displays.

Pure black surfaces reduce power consumption while increasing contrast throughout the interface.

Benefits include:

- Reduced battery usage
- Improved nighttime readability
- Higher visual contrast
- Cleaner presentation

Users may switch themes directly from the application settings.

---

Continue to **Part 3**, covering:

- Background Playback
- Media Notifications
- Audio Focus
- Cloud Streaming Architecture
- Performance Optimizations
- Privacy
- Feature Status
- Related Documentation

---

# Background Playback

Sonexa has been engineered to deliver uninterrupted music playback regardless of application state.

Unlike conventional media applications where playback is tightly coupled with the user interface, Sonexa delegates all playback responsibilities to a persistent `MediaSessionService`.

This architecture enables continuous playback while:

- The application is minimized
- The device screen is turned off
- The user switches to another application
- Configuration changes occur (such as screen rotation)

Separating playback from the presentation layer ensures a reliable listening experience without interrupting the active media session.

---

# Media Notification & Lock Screen Controls

Whenever playback begins, Sonexa promotes itself to a foreground media application using Android's Media3 framework.

A system media notification is automatically generated, allowing users to control playback without reopening the application.

Available controls include:

- Play / Pause
- Previous Track
- Next Track
- Album Artwork
- Song Information

The same MediaSession also powers Android's lock screen controls, Quick Settings media player, Bluetooth headsets, vehicle infotainment systems, and other compatible media interfaces.

---

# Intelligent Audio Focus Management

Sonexa respects Android's Audio Focus guidelines to ensure harmonious interaction with other media applications.

Playback automatically responds to system events such as:

- Incoming phone calls
- Navigation voice instructions
- Alarm interruptions
- Competing media applications

Depending on the type of focus change, Sonexa can:

- Pause playback
- Resume playback automatically
- Temporarily reduce volume (ducking)
- Restore the previous playback state

This behavior improves user experience while complying with Android's recommended media standards.

---

# Cloud-Ready Streaming Architecture

Although the current release primarily focuses on local playback, the application's architecture has been designed to support online music streaming without major structural changes.

Every track is represented by a unified `Song` model whose media source can reference either:

- Local MediaStore content (`content://`)
- Remote streaming endpoints (`https://`)

Because Media3 natively supports both source types, future cloud integration can reuse the existing playback engine with minimal changes to the Presentation layer.

Planned streaming capabilities include:

- Online search
- Remote playback
- Streaming recommendations
- Cloud playlists
- Hybrid local and online libraries

---

# Smooth Animations & Microinteractions

Motion plays an important role throughout Sonexa's user experience.

Rather than relying on abrupt interface transitions, the application uses carefully designed animations to create continuity between screens and playback states.

Examples include:

- MiniPlayer expansion
- Album artwork transitions
- Animated progress indicators
- Dynamic gradient transitions
- Equalizer interactions
- Lyrics highlighting
- Player entrance animations

These subtle animations contribute to a more polished and engaging listening experience.

---

# Performance Optimizations

Performance has been a key consideration throughout Sonexa's development.

Several implementation strategies have been adopted to ensure responsive interaction while minimizing unnecessary resource consumption.

Key optimizations include:

## Reactive UI Updates

Compose recomposes only the UI elements affected by changes in playback state, reducing rendering overhead.

---

## Efficient Image Loading

Album artwork is loaded asynchronously using Coil, minimizing memory usage while avoiding unnecessary UI blocking.

---

## Background Processing

Long-running operations—including media scanning, palette extraction, and playback state monitoring—execute outside the main thread using Kotlin Coroutines.

---

## Shared Playback Engine

All playback-related interfaces—including the Player Screen, MiniPlayer, notifications, and MediaSession—reuse a single ExoPlayer instance.

This guarantees consistent playback behavior while eliminating duplicate resource usage.

---

## Optimized Database Operations

Playback history, favorites, and playlist updates execute asynchronously through Room, preventing database writes from interrupting user interaction.

---

# Privacy & User Data

Sonexa has been designed with user privacy in mind.

The current release:

- Does not require user accounts
- Does not upload listening history
- Does not collect personal information
- Stores playlists locally
- Stores favorites locally
- Stores playback statistics locally

Future cloud synchronization features will remain optional and require explicit user authentication.

---

# Accessibility

Sonexa follows Android accessibility recommendations wherever practical.

Current accessibility considerations include:

- Large touch targets
- Material 3 typography
- High contrast AMOLED mode
- Consistent navigation patterns
- Readable player controls
- Adaptive layouts

Future releases will continue improving accessibility through enhanced screen reader support and expanded dynamic text scaling.

---

# Technology Summary

| Layer | Technologies |
|------|--------------|
| Language | Kotlin |
| UI | Jetpack Compose, Material 3 |
| Architecture | MVVM |
| Media Engine | Media3 ExoPlayer |
| Audio Processing | AudioFX API |
| Local Storage | Room Database |
| Images | Coil |
| Dynamic Theming | Palette API |
| Background Playback | MediaSessionService |
| Navigation | Navigation Compose |
| Concurrency | Kotlin Coroutines, StateFlow |

---

# Feature Status

| Feature | Status |
|----------|:------:|
| Local Music Playback | ✅ |
| Background Playback | ✅ |
| MediaSession Integration | ✅ |
| Media Notifications | ✅ |
| Intelligent Media Scanner | ✅ |
| Favorites | ✅ |
| Custom Playlists | ✅ |
| Recently Played | ✅ |
| Most Played | ✅ |
| Dynamic Palette UI | ✅ |
| Glassmorphism Interface | ✅ |
| Native AudioFX Equalizer | ✅ |
| Bass Boost | ✅ |
| Virtualizer | ✅ |
| Lyrics Engine | ✅ |
| AMOLED Theme | ✅ |
| Haptic Feedback | ✅ |
| Cloud Streaming Architecture | 🚧 |
| Firebase Synchronization | 📅 |
| Android Auto | 📅 |
| Wear OS Support | 📅 |

---

# Related Documentation

Further technical documentation is available for readers interested in Sonexa's internal architecture.

| Document | Description |
|----------|-------------|
| **OVERVIEW.md** | Project overview and quick start |
| **ARCHITECTURE.md** | System architecture and playback engine |
| **IMPLEMENTATION.md** | Internal playback workflows |
| **DECISIONS.md** | Engineering decisions and technology choices |
| **ROADMAP.md** | Planned features and future milestones |

---

# Conclusion

Sonexa has been engineered as a modern Android media platform that combines high-performance audio playback, reactive user interfaces, and scalable architecture into a cohesive listening experience.

From hardware-level AudioFX integration and intelligent playback tracking to dynamic theming and cloud-ready streaming architecture, every feature has been designed with maintainability, responsiveness, and long-term evolution in mind.

As development continues, Sonexa will expand beyond local playback into a fully connected media ecosystem while preserving the architectural principles established throughout the project.
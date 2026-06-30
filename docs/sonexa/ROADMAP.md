# Roadmap

Sonexa is under active development with the goal of evolving from a modern offline music player into a complete Android media platform.

The current release delivers a stable playback engine, reactive user interface, intelligent media management, and hardware audio integration. Future development will continue expanding media capabilities while preserving the application's scalable architecture and production-oriented design principles.

This document outlines the project's completed milestones, current development priorities, and long-term technical vision.

---

# Project Status

| Information | Status |
|------------|--------|
| Current Version | **v3.1** |
| Development Status | Active Development |
| Platform | Android |
| Language | Kotlin |
| Minimum SDK | API 29 |
| UI Framework | Jetpack Compose |
| Media Engine | Media3 (ExoPlayer) |
| Architecture | MVVM + Feature-Based Architecture |

---

# Release Timeline

## Version 1.x

The initial phase focused on building a reliable local music player.

### Milestones

- Project initialization
- MediaStore integration
- Basic playback engine
- Jetpack Compose interface
- Initial navigation
- Material Design foundation

---

## Version 2.x

The second development phase focused on improving architecture, usability, and playback reliability.

### Completed

- Media3 migration
- MediaSession integration
- Background playback
- Notification controls
- Playlist management
- Favorites
- Recently Played
- Most Played
- Room Database
- StateFlow architecture

---

## Version 3.0

A major redesign centered around user experience and advanced media capabilities.

### Completed

- Glassmorphism redesign
- Dynamic Palette theming
- Native AudioFX integration
- Equalizer
- Bass Boost
- Virtualizer
- Lyrics engine
- Improved MiniPlayer
- Player redesign
- Better animations
- Performance optimization

---

## Version 3.1

Current Development Release

The current release refines the existing architecture while preparing the application for cloud-enabled functionality.

Recent improvements include:

- AudioController refinements
- Improved playback synchronization
- UI consistency improvements
- Repository optimization
- Better playback statistics
- Adaptive launcher icons
- Stability improvements
- Internal refactoring

---

# Current Development Focus

Current development efforts are centered around strengthening the playback engine while preparing the architecture for hybrid local and cloud media support.

Priority areas include:

- Streaming infrastructure
- Playback reliability
- Library optimization
- UI refinements
- Performance improvements
- Testing coverage

---

# Planned Features

## Cloud Streaming

Future releases are planned to introduce online media capabilities.

Potential features include:

- Online music streaming
- Cloud library
- Hybrid local and online playback
- Streaming search
- Remote album artwork
- Cloud playlists

The existing playback architecture has already been designed to support these capabilities.

---

## User Accounts

Future versions may introduce optional account synchronization.

Planned capabilities include:

- Firebase Authentication
- Cloud playlist synchronization
- Favorite synchronization
- Listening history backup
- Cross-device preferences

All cloud functionality will remain optional.

---

## Audio Engine

The playback engine will continue evolving with additional audio enhancements.

Potential improvements include:

- Gapless playback
- Crossfade transitions
- ReplayGain support
- Loudness normalization
- Graphic equalizer presets
- Audio visualizer

---

## User Experience

Planned interface improvements include:

- Material You integration
- Improved animations
- Tablet optimization
- Foldable layouts
- Landscape player
- Better accessibility
- Dynamic typography
- Additional themes

---

## Widgets

Widget support is planned for future releases.

Potential widgets include:

- Compact Player
- Playback Controls
- Recently Played
- Favorites Widget
- Material You Widget

---

## Platform Expansion

The playback engine has been designed to support additional Android platforms.

Future targets include:

- Android Auto
- Wear OS
- Android TV
- Tablets
- Foldable devices

Because playback is isolated inside MediaSessionService, additional interfaces can reuse the existing playback engine.

---

# Performance Roadmap

Future optimization efforts include:

- Faster media scanning
- Reduced startup time
- Smarter artwork caching
- Lower memory usage
- Faster playlist loading
- Optimized recomposition
- Reduced battery consumption
- Improved database indexing

---

# Architecture Roadmap

The architecture will continue evolving alongside application growth.

Planned improvements include:

- Dependency Injection with Hilt
- Multi-module Gradle architecture
- Expanded repository abstraction
- Improved test coverage
- Analytics layer
- Download manager
- Better logging
- Enhanced error recovery

These improvements will strengthen maintainability while preserving the existing playback architecture.

---

# Long-Term Vision

Sonexa is intended to evolve beyond a traditional offline music player.

The long-term objective is to build a modern Android media platform capable of delivering:

- Premium local playback
- Online streaming
- Cross-device synchronization
- Advanced audio customization
- Intelligent recommendations
- Personalized listening experience

Future development will continue prioritizing clean architecture, responsive interfaces, and production-quality engineering practices.

---

# Known Limitations

The current release intentionally excludes several capabilities while the playback architecture continues to mature.

Current limitations include:

- No online streaming(Only 30 sec Streaming is Supported)
- No cloud synchronization
- No Android Auto integration
- No Wear OS support
- No home screen widgets
- No podcast support
- No offline download manager

These features are planned for future releases where they align with the project's long-term vision.

---

# Versioning Strategy

Sonexa follows Semantic Versioning.

```
MAJOR.MINOR.PATCH
```

Example:

```
3.1.0

Major → 3
Minor → 1
Patch → 0
```

Version definitions:

- **MAJOR** — Significant architectural or platform changes.
- **MINOR** — New features while maintaining compatibility.
- **PATCH** — Bug fixes, optimizations, and refinements.

---

# Contribution Philosophy

Every new feature introduced into Sonexa should:

- Improve user experience
- Preserve playback stability
- Follow Material 3 guidelines
- Maintain reactive architecture
- Avoid unnecessary complexity
- Minimize technical debt
- Remain consistent with existing design principles

Quality and maintainability take precedence over rapid feature expansion.

---

# Future Documentation

As Sonexa continues to evolve, additional documentation will accompany the codebase.

Planned documents include:

- API Reference
- Testing Guide
- Release Notes
- Changelog
- Contribution Guide
- UI Design Guidelines
- Audio Engine Reference

Maintaining high-quality documentation remains an integral part of the project's development process.

---

# Conclusion

Sonexa has evolved from a local Android music player into a scalable media platform built on modern Android development practices.

The current release establishes a strong foundation through Media3, Jetpack Compose, reactive state management, and a service-based playback engine. Future development will focus on expanding cloud capabilities, advanced audio processing, and broader platform support while preserving the architectural principles established throughout the project.

This roadmap reflects the project's long-term direction while remaining adaptable to new technologies, user feedback, and the evolving Android ecosystem.
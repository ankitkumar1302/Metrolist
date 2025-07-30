# Metrolist - Android Music App Architecture Documentation

## Project Overview

Metrolist is a feature-rich YouTube Music client for Android, built using modern Android development practices. It's a fork/successor of InnerTune and OuterTune projects, providing a native Android experience for YouTube Music streaming with advanced features like lyrics, offline playback, and Discord integration.

## High-Level Architecture

### Project Structure
```
Metrolist/
├── app/                 # Main Android application module
├── innertube/          # YouTube API integration module
├── kugou/              # KuGou lyrics provider module
├── lrclib/             # LRCLib lyrics provider module
├── kizzy/              # Discord Rich Presence integration module
├── gradle/             # Gradle wrapper and dependencies
└── fastlane/           # CI/CD and publishing automation
```

## Core Architectural Patterns

### 1. Modular Architecture
The app follows a **multi-module architecture** pattern:

- **app**: Main Android module containing UI, business logic, and coordination
- **innertube**: Pure Kotlin module for YouTube API communication
- **kugou/lrclib**: Lyrics providers as separate modules
- **kizzy**: Discord integration as isolated module

**Benefits:**
- Separation of concerns
- Independent testing and development
- Better build times through parallel compilation
- Clear dependency boundaries

### 2. Clean Architecture + MVVM
The app implements **Clean Architecture** principles with **MVVM (Model-View-ViewModel)** pattern:

```
UI Layer (Jetpack Compose)
    ↓
ViewModel Layer (Business Logic)
    ↓
Repository/Use Case Layer
    ↓
Data Layer (Room Database + Remote APIs)
```

### 3. Dependency Injection (Hilt)
Uses **Dagger Hilt** for dependency injection:
- `@HiltAndroidApp` on Application class
- `@Module` and `@InstallIn` for dependency provision
- Scoped dependencies for proper lifecycle management

## Technology Stack

### Core Technologies
- **Language**: Kotlin (100%)
- **UI Framework**: Jetpack Compose with Material 3
- **Architecture**: MVVM + Clean Architecture
- **Database**: Room (SQLite)
- **Networking**: Ktor Client
- **Media Playback**: Media3 (ExoPlayer)
- **Image Loading**: Coil
- **Dependency Injection**: Dagger Hilt

### Build System
- **Build Tool**: Gradle with Kotlin DSL
- **Min SDK**: 26 (Android 8.0)
- **Target SDK**: 36
- **Java Version**: 21
- **Kotlin Version**: Latest stable

## Module Deep Dive

### 1. App Module (`app/`)

#### Package Structure
```
com.metrolist.music/
├── db/                 # Database entities and DAOs
├── di/                 # Dependency injection modules
├── playback/           # Music playback service and management
├── ui/                 # Compose UI components and screens
├── viewmodels/         # ViewModels for business logic
├── models/             # Data models and DTOs
├── utils/              # Utility functions and extensions
├── constants/          # App constants and configuration
└── extensions/         # Kotlin extensions
```

#### Key Components

**Database Layer (Room)**
- **Entities**: Songs, Albums, Artists, Playlists, etc.
- **Relations**: Many-to-many mappings between entities
- **Migration**: Automatic schema migrations
- **Views**: SQL views for optimized queries

**Music Service Architecture**
- `MusicService`: MediaLibraryService implementation
- `PlayerConnection`: Service binding and communication
- `MediaLibrarySessionCallback`: Media session handling
- ExoPlayer integration with custom audio processing

**UI Architecture (Jetpack Compose)**
- Material 3 design system
- Compose Navigation
- State management with ViewModels
- Reusable components in `ui/component/`

### 2. InnerTube Module (`innertube/`)

#### Purpose
Handles all YouTube API communication using unofficial InnerTube API:
- Song search and metadata
- Playlist management
- User authentication
- Browse and recommendation endpoints

#### Key Files
- `YouTube.kt`: Main API facade
- `InnerTube.kt`: Core HTTP client and request handling
- `models/`: Response data models
- `pages/`: Parsed page objects

#### Architecture Highlights
- Pure Kotlin module (no Android dependencies)
- Ktor HTTP client with JSON serialization
- Comprehensive error handling
- Support for proxy and authentication

### 3. Lyrics Modules

#### KuGou Module (`kugou/`)
- Chinese lyrics provider
- Traditional/Simplified Chinese support
- REST API integration

#### LRCLib Module (`lrclib/`)
- International lyrics provider
- Synced lyrics (.lrc format)
- Community-driven lyrics database

### 4. Kizzy Module (`kizzy/`)
- Discord Rich Presence integration
- Real-time now playing status
- WebSocket communication with Discord

## Data Flow Architecture

### 1. Music Playback Flow
```
User Action (UI)
    ↓
ViewModel
    ↓
MusicService (MediaLibraryService)
    ↓
ExoPlayer (Media3)
    ↓
DataSource (Cache/Network)
    ↓
Audio Output
```

### 2. Data Synchronization Flow
```
YouTube API (innertube)
    ↓
Repository Layer
    ↓
Room Database (local cache)
    ↓
ViewModel (business logic)
    ↓
Compose UI (reactive updates)
```

### 3. Search and Discovery Flow
```
User Search Query
    ↓
YouTube.search() (innertube)
    ↓
Parsed Results (models)
    ↓
Cached in Database
    ↓
Displayed in UI
```

## Key Features Implementation

### 1. Offline Playback
- **Caching Strategy**: LRU cache with configurable size
- **Download Management**: Background download service
- **Storage**: ExoPlayer cache + custom download cache
- **Sync**: Automatic cache cleanup and management

### 2. Lyrics Integration
- **Multi-Provider**: KuGou + LRCLib for maximum coverage
- **Sync**: Time-synced lyrics display
- **Fallback**: Graceful degradation when lyrics unavailable
- **Languages**: Support for multiple languages and character sets

### 3. Media Session Integration
- **Android Auto**: Full Android Auto support
- **Notifications**: Rich media notifications
- **Controls**: Lock screen and notification controls
- **Background Play**: Foreground service for uninterrupted playback

### 4. User Account Sync
- **YouTube Account**: Optional login for personalization
- **Playlist Sync**: Bidirectional playlist synchronization
- **Recommendations**: Personalized recommendations
- **Privacy**: Configurable privacy settings

## Build Configuration

### Gradle Multi-Module Setup
- **Version Catalogs**: Centralized dependency management
- **Build Variants**: Debug/Release with different signing
- **Architecture Variants**: Universal, ARM64, x86, etc.
- **Optimization**: ProGuard/R8 for release builds

### Development Features
- **Compose Compiler Reports**: Performance analysis
- **Persistent Debug Keystore**: Consistent debug signing
- **Build Caching**: Optimized build times
- **Lint Configuration**: Custom lint rules

## Security and Privacy

### Data Protection
- **Local Storage**: All personal data stored locally
- **Optional Sync**: User controls account synchronization
- **No Tracking**: No analytics or user tracking
- **Open Source**: Transparent implementation

### Network Security
- **HTTPS Only**: All network communication encrypted
- **Certificate Pinning**: Protection against MITM attacks
- **Proxy Support**: Optional proxy configuration
- **Rate Limiting**: Respectful API usage

## Performance Optimizations

### Memory Management
- **Image Caching**: Configurable Coil image cache
- **Database Optimization**: Indexed queries and views
- **Lazy Loading**: On-demand content loading
- **Memory Monitoring**: Proactive memory management

### Network Efficiency
- **Request Caching**: HTTP response caching
- **Batch Operations**: Grouped API requests
- **Compression**: Gzip/Brotli compression support
- **Connection Pooling**: Efficient network resource usage

### Audio Processing
- **Hardware Acceleration**: Platform-optimized audio
- **Audio Effects**: Sonic audio processing for speed/pitch
- **Silence Skipping**: Automatic silence detection
- **Normalization**: Volume normalization across tracks

This architecture provides a solid foundation for a modern, performant, and user-friendly music streaming application while maintaining clean separation of concerns and extensibility for future features.
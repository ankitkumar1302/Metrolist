# Metrolist Architecture Overview

## 🎵 Introduction

Metrolist is a feature-rich YouTube Music client for Android that provides a seamless music streaming experience with offline capabilities. The app is built using modern Android development practices with a focus on performance, modularity, and user experience.

## 🏗️ Architecture Pattern

The application follows a **Clean Architecture** pattern combined with **MVVM (Model-View-ViewModel)** architectural design:

```
┌─────────────────────────────────────────────────────────────┐
│                     Presentation Layer                       │
│  ┌─────────────────────────────────────────────────────┐   │
│  │   UI (Compose)  │  ViewModels  │  Navigation       │   │
│  └─────────────────────────────────────────────────────┘   │
├─────────────────────────────────────────────────────────────┤
│                      Domain Layer                            │
│  ┌─────────────────────────────────────────────────────┐   │
│  │   Use Cases    │    Models    │   Repositories     │   │
│  └─────────────────────────────────────────────────────┘   │
├─────────────────────────────────────────────────────────────┤
│                       Data Layer                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │  Local DB (Room) │ Remote APIs │ Cache (ExoPlayer)  │   │
│  └─────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
```

## 🛠️ Technology Stack

### Core Technologies
- **Language**: Kotlin
- **UI Framework**: Jetpack Compose
- **Dependency Injection**: Hilt (Dagger)
- **Database**: Room
- **Media Playback**: Media3 (ExoPlayer)
- **Networking**: Ktor Client
- **Image Loading**: Coil
- **Async Operations**: Kotlin Coroutines & Flow

### Build Configuration
- **Min SDK**: 26 (Android 8.0)
- **Target SDK**: 36
- **Compile SDK**: 36
- **Java Version**: 21

## 📦 Module Structure

### 1. **App Module** (`/app`)
The main Android application module containing:
- UI components and screens
- ViewModels for state management
- Dependency injection setup
- Media playback services
- Database implementation

### 2. **InnerTube Module** (`/innertube`)
YouTube Music API integration module:
- Handles all YouTube Music API communications
- Parses YouTube responses
- Provides data models for YouTube content
- Manages authentication and session handling

### 3. **KuGou Module** (`/kugou`)
Lyrics provider integration:
- Fetches lyrics from KuGou music service
- Handles Chinese lyrics (simplified and traditional)

### 4. **LrcLib Module** (`/lrclib`)
Alternative lyrics provider:
- Integrates with LrcLib API
- Provides synced lyrics functionality

### 5. **Kizzy Module** (`/kizzy`)
Discord Rich Presence integration:
- Shows currently playing music on Discord
- Manages Discord RPC connection

## 🏛️ Key Components

### 1. **Application Class** (`App.kt`)
- Initializes Hilt dependency injection
- Configures YouTube API client
- Sets up global app configuration
- Manages visitor data and authentication
- Configures image caching with Coil

### 2. **MainActivity**
- Single Activity architecture
- Hosts all Compose screens
- Manages navigation
- Handles deep links and intents
- Controls system UI (status bar, navigation bar)

### 3. **MusicService**
- Core media playback service
- Extends MediaLibraryService
- Manages ExoPlayer instance
- Handles audio focus
- Provides media session for system integration
- Manages playback queue
- Implements caching for offline playback

### 4. **Database Layer**
Uses Room database with multiple entities:
- **SongEntity**: Stores song metadata
- **AlbumEntity**: Album information
- **ArtistEntity**: Artist details
- **PlaylistEntity**: User playlists
- **FormatEntity**: Audio format information
- **LyricsEntity**: Cached lyrics
- **SearchHistory**: User search history
- **Event**: Playback history events

### 5. **Navigation**
- Uses Jetpack Navigation Compose
- Centralized navigation in `NavigationBuilder.kt`
- Supports deep linking
- Animated transitions between screens

## 🎨 UI Architecture

### Screen Structure
1. **Home Screen**: Personalized recommendations and quick picks
2. **Library Screen**: User's saved music, playlists, and downloads
3. **Search**: Browse and search YouTube Music catalog
4. **Player Screen**: Now playing interface with controls
5. **Settings**: App configuration and preferences

### UI Components
- Custom Material 3 components
- Reusable composables in `/ui/component`
- Theme system supporting light/dark/dynamic modes
- Animated transitions and gestures

## 🔄 Data Flow

### 1. **Music Playback Flow**
```
User Action → ViewModel → MusicService → ExoPlayer
                ↓              ↓
            Database    YouTube API
```

### 2. **Content Loading Flow**
```
Screen Request → ViewModel → Repository → InnerTube API
        ↓                         ↓
    UI Update ← StateFlow ← Database Cache
```

### 3. **Offline Playback**
- Songs are cached using ExoPlayer's SimpleCache
- Metadata stored in Room database
- Automatic cache management based on user preferences

## 🔐 Security & Privacy

### Authentication
- Optional YouTube account login
- Cookie-based authentication
- Visitor data for anonymous usage
- Proxy support for region restrictions

### Data Storage
- All user data stored locally
- No external analytics
- Optional cloud sync via Google account

## 🚀 Performance Optimizations

### 1. **Lazy Loading**
- Compose LazyColumn/LazyRow for lists
- Pagination for large datasets
- Image loading with Coil's memory/disk cache

### 2. **Background Processing**
- Coroutines for async operations
- Background music download service
- Efficient database queries with Flow

### 3. **Memory Management**
- Configurable cache sizes
- Automatic cache eviction
- Memory-efficient image loading

## 🔧 Build Variants

The app supports multiple ABI-specific builds:
- **Universal**: All architectures
- **ARM64**: 64-bit ARM devices
- **ARMv7**: 32-bit ARM devices
- **x86**: Intel 32-bit devices
- **x86_64**: Intel 64-bit devices

## 🎯 Key Features Implementation

### 1. **YouTube Music Integration**
- Direct API communication without official SDK
- Supports browsing, searching, and streaming
- Handles various content types (songs, videos, playlists)

### 2. **Offline Playback**
- Progressive caching during playback
- Manual download functionality
- Smart cache management

### 3. **Lyrics Support**
- Multiple providers (KuGou, LrcLib)
- Synced and static lyrics
- Automatic language detection

### 4. **Personalization**
- Quick picks based on listening history
- Custom playlists
- Playback statistics

### 5. **Advanced Playback Features**
- Gapless playback
- Crossfade support
- Speed/pitch adjustment
- Audio normalization
- Skip silence

## 📱 Platform Integration

### 1. **Media Session**
- System media controls
- Lock screen integration
- Bluetooth controls
- Android Auto support

### 2. **Notifications**
- Rich media notifications
- Playback controls
- Album artwork

### 3. **Background Playback**
- Continues playing when app is minimized
- Respects system audio focus
- Battery optimization handling

This architecture provides a robust foundation for a feature-rich music streaming application with excellent performance and user experience.
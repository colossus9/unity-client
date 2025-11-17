# Virtual Rodeo - Unity VR Client

[![Unity](https://img.shields.io/badge/Unity-2022.3_LTS-black?logo=unity)](https://unity.com/)
[![Meta Quest](https://img.shields.io/badge/Meta_Quest-2%20%7C%203-blue)](https://www.meta.com/quest/)
[![License](https://img.shields.io/badge/license-MIT-green)](LICENSE)

Immersive VR rodeo experience built with Unity for Meta Quest devices.

## 🎯 Overview

The Unity VR client is the primary user interface for Virtual Rodeo, delivering an immersive first-person rodeo experience. Players compete in various rodeo events, earning scores that sync with the leaderboard service for real-time rankings.

## 🏗️ Architecture Context

Part of the Virtual Rodeo microservices ecosystem:

```
┌─────────────────┐     ┌──────────────────┐     ┌────────────────────┐
│  Unity Client   │────▶│  Backend API     │────▶│ Leaderboard Service│
│  (Meta Quest)   │     │  (REST/SignalR)  │     │  (Real-time Scores)│
└─────────────────┘     └──────────────────┘     └────────────────────┘
                               │                           │
                               ▼                           ▼
                        ┌─────────────┐           ┌──────────────┐
                        │ Redis Cache │           │ Redis Cache  │
                        │ (East US 2) │           │ (West US 2)  │
                        └─────────────┘           └──────────────┘
```

### Related Repositories

- **[init](https://github.com/dusty-plate/init)**: Project documentation, infrastructure, deployment guides
- **[backend-api](https://github.com/dusty-plate/backend-api)**: Core REST API for game state, user management, event logic
- **[frontend-web](https://github.com/dusty-plate/frontend-web)**: Web portal for account management and leaderboards
- **[leaderboard-service](https://github.com/colossus9/leaderboard-service)**: Real-time leaderboard microservice

## 🚀 Features

### Rodeo Events
- **Bull Riding**: 8-second physics-based challenge with dynamic difficulty
- **Barrel Racing**: Precision navigation with time-based scoring
- **Roping Events**: Timing and accuracy mechanics
- **Bronc Riding**: Advanced balance and control systems

### VR Capabilities
- **6DOF Tracking**: Full positional and rotational tracking
- **Hand Presence**: Natural hand interactions with Quest controllers
- **Spatial Audio**: 3D positional audio for immersive atmosphere
- **Comfort Options**: Configurable comfort settings (teleport, snap turn, vignetting)

### Multiplayer Features
- **Real-time Leaderboards**: Live score updates via SignalR
- **Spectator Mode**: Watch other players compete
- **Social Integration**: Friend challenges and comparisons
- **Regional Rankings**: Compare scores within geographic regions

## 🛠️ Tech Stack

| Component | Technology | Purpose |
|-----------|-----------|---------|
| Game Engine | Unity 2022.3 LTS | Core VR development platform |
| VR SDK | Meta XR SDK | Quest-specific features and optimization |
| Networking | REST + SignalR | Backend communication and real-time updates |
| Physics | Unity Physics | Realistic rodeo mechanics |
| Audio | Unity Audio + FMOD | Spatial audio and dynamic sound |
| State Management | Custom C# Managers | Game state, user session, event flow |
| Analytics | Unity Analytics | Player behavior and performance tracking |

## 📦 Project Structure

```
unity-client/
├── Assets/
│   ├── Scenes/                    # Unity scenes
│   │   ├── MainMenu.unity
│   │   ├── RodeoArena.unity
│   │   └── Leaderboard.unity
│   ├── Scripts/                   # C# game logic
│   │   ├── Core/                  # Core systems
│   │   ├── Events/                # Rodeo event implementations
│   │   ├── Multiplayer/           # Networking and SignalR
│   │   ├── UI/                    # VR UI systems
│   │   └── Utils/                 # Helper utilities
│   ├── Prefabs/                   # Reusable game objects
│   ├── Materials/                 # Visual materials
│   ├── Models/                    # 3D models
│   ├── Audio/                     # Sound effects and music
│   └── Resources/                 # Runtime-loaded assets
├── Packages/                      # Unity packages
├── ProjectSettings/               # Unity configuration
└── .github/
    └── copilot-instructions.md    # Agent development context
```

## 🔧 Development Setup

### Prerequisites

- Unity 2022.3 LTS or newer
- Meta Quest Developer Account
- Meta Quest Link or Air Link setup
- Visual Studio 2022 or Rider

### Installation

1. **Clone the repository**:
```bash
git clone https://github.com/colossus9/unity-client.git
cd unity-client
```

2. **Open in Unity Hub**:
   - Add project from disk
   - Unity will automatically resolve package dependencies

3. **Configure Meta Quest SDK**:
   - Import Meta XR All-in-One SDK from Package Manager
   - Configure XR Plugin Management for Oculus

4. **Set Backend Endpoint**:
   - Edit `Assets/Resources/config.json`
   - Set `apiBaseUrl` to your backend-api instance

### Building for Quest

```bash
# Build APK
Unity -quit -batchmode -projectPath . -buildTarget Android -executeMethod BuildScript.BuildQuest

# Install to connected Quest device
adb install -r Builds/VirtualRodeo.apk
```

## 🎮 Game Architecture

### Core Systems

**GameManager**: Central singleton managing game state, scene transitions, and system initialization

**EventController**: Handles rodeo event logic, scoring, and progression

**NetworkManager**: Manages REST API calls and SignalR connections to backend services

**PlayerController**: VR input handling, movement, and interaction systems

**LeaderboardManager**: Real-time score synchronization with leaderboard-service

### Data Flow

1. **Player starts event** → `EventController.StartEvent()`
2. **Event logic runs** → Physics updates, score calculation
3. **Event completes** → `EventController.CompleteEvent(score)`
4. **Score submission** → `NetworkManager.SubmitScore()` → Backend API
5. **Real-time update** → SignalR pushes leaderboard changes
6. **UI refresh** → `LeaderboardManager.OnScoreUpdate()`

## 🌐 API Integration

### Backend API Endpoints

```csharp
// User authentication
POST /api/auth/login
POST /api/auth/register

// Event scores
POST /api/scores/submit
GET  /api/scores/user/{userId}

// Leaderboard queries (proxied to leaderboard-service)
GET  /api/leaderboard/global
GET  /api/leaderboard/regional/{region}
GET  /api/leaderboard/friends
```

### SignalR Real-time Events

```csharp
// Subscribe to leaderboard updates
hubConnection.On<LeaderboardUpdate>("LeaderboardChanged", update => {
    LeaderboardManager.Instance.UpdateLeaderboard(update);
});

// Receive friend achievements
hubConnection.On<Achievement>("FriendAchievement", achievement => {
    NotificationManager.Instance.ShowAchievement(achievement);
});
```

## 🧪 Testing

### Unity Test Framework

```bash
# Run edit mode tests
Unity -runTests -testPlatform EditMode -testResults TestResults-EditMode.xml

# Run play mode tests  
Unity -runTests -testPlatform PlayMode -testResults TestResults-PlayMode.xml
```

### Manual Testing Checklist

- [ ] All rodeo events load and complete successfully
- [ ] Scores submit correctly to backend
- [ ] Leaderboard updates in real-time
- [ ] VR comfort settings work as expected
- [ ] No performance issues (maintain 72+ FPS on Quest 2)
- [ ] Audio positioning is accurate
- [ ] UI is readable and accessible in VR

## 📊 Performance Targets

| Metric | Quest 2 Target | Quest 3 Target |
|--------|---------------|----------------|
| Frame Rate | 72 FPS | 90 FPS |
| Draw Calls | < 200 | < 300 |
| Triangles | < 100k | < 150k |
| Texture Memory | < 512 MB | < 768 MB |
| Load Time (scene) | < 3s | < 2s |

## 🚢 Deployment

### Quest Store Submission

1. Build release APK with production backend URLs
2. Run Oculus Platform validation tools
3. Submit to Oculus Store via developer dashboard
4. Monitor review feedback

### Beta Testing (App Lab)

1. Upload build to App Lab
2. Share access link with beta testers
3. Collect feedback via in-game surveys
4. Iterate based on user testing

## 💰 Cost Estimates

Development resources are minimal as Unity client runs on user devices:

- **Unity Pro License**: $185/month (optional, if team > 100k revenue)
- **Meta Quest Developer**: Free
- **Cloud Services**: Covered by backend-api infrastructure

## 🔐 Security Considerations

- **No Local Secrets**: All auth tokens from backend API
- **Certificate Pinning**: Validate backend SSL certificates
- **Input Validation**: Sanitize all user inputs before API calls
- **Cheat Prevention**: Server-authoritative scoring (scores validated by backend)

## 📚 Documentation

- [Unity Development Guide](docs/UNITY-DEVELOPMENT.md) (TODO)
- [VR Best Practices](docs/VR-BEST-PRACTICES.md) (TODO)
- [API Integration Guide](docs/API-INTEGRATION.md) (TODO)
- [Performance Optimization](docs/PERFORMANCE-OPTIMIZATION.md) (TODO)

## 🤝 Contributing

See [init repository](https://github.com/dusty-plate/init) for contribution guidelines and architecture documentation.

## 📝 License

MIT License - See LICENSE file for details

## 🔗 Links

- [Backend API](https://github.com/dusty-plate/backend-api)
- [Leaderboard Service](https://github.com/colossus9/leaderboard-service)
- [Frontend Web](https://github.com/dusty-plate/frontend-web)
- [Project Documentation](https://github.com/dusty-plate/init)
- [Meta Quest Developer Portal](https://developer.oculus.com/)

---

**Status**: 🚧 Repository initialized - Unity project structure coming soon

**Last Updated**: November 16, 2025

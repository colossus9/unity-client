# Unity VR Client - GitHub Copilot Instructions

## Project Overview

**Virtual Rodeo Unity VR Client** is an immersive virtual reality rodeo game for Meta Quest devices (Quest 2 and Quest 3). This repository contains the Unity-based VR client that delivers first-person rodeo experiences, integrating with backend services for multiplayer features, leaderboards, and user management.

## Architecture Context

### Microservices Ecosystem

This Unity client is part of a 5-repository microservices architecture:

```
┌─────────────────────────────────────────────────────────────────────┐
│                      Virtual Rodeo Platform                          │
├─────────────────────────────────────────────────────────────────────┤
│                                                                       │
│  ┌──────────────┐     ┌─────────────┐     ┌──────────────────────┐ │
│  │Unity Client  │────▶│Backend API  │────▶│Leaderboard Service   │ │
│  │(Meta Quest)  │     │(REST/SignalR│     │(Real-time Rankings)  │ │
│  │              │◀────│Hub)         │◀────│                      │ │
│  └──────────────┘     └─────────────┘     └──────────────────────┘ │
│         │                    │                      │                │
│         │                    ▼                      ▼                │
│         │             ┌─────────────┐      ┌──────────────┐         │
│         │             │Redis Cache  │      │Redis Cache   │         │
│         │             │(East US 2)  │      │(West US 2)   │         │
│         │             └─────────────┘      └──────────────┘         │
│         │                                                            │
│         └─────────────────────┐                                     │
│                               ▼                                      │
│                     ┌──────────────────┐                            │
│                     │Frontend Web      │                            │
│                     │(Account Mgmt)    │                            │
│                     └──────────────────┘                            │
└─────────────────────────────────────────────────────────────────────┘
```

### Repository Responsibilities

| Repository | Role | Technology | URL |
|-----------|------|-----------|-----|
| **init** | Documentation, infrastructure, deployment | Bicep/Terraform | https://github.com/dusty-plate/init |
| **backend-api** | Core game logic, user management, event handling | .NET 8 / C# | https://github.com/dusty-plate/backend-api |
| **frontend-web** | Web portal for accounts and leaderboards | React/Next.js | https://github.com/dusty-plate/frontend-web |
| **unity-client** (THIS REPO) | VR game client | Unity 2022.3 LTS / C# | https://github.com/colossus9/unity-client |
| **leaderboard-service** | Real-time leaderboard microservice | .NET 8 / SignalR | https://github.com/colossus9/leaderboard-service |

## Tech Stack

### Core Technologies

- **Game Engine**: Unity 2022.3 LTS (Long Term Support)
- **Programming Language**: C# 11 (.NET Standard 2.1)
- **VR Platform**: Meta Quest 2 & 3
- **VR SDK**: Meta XR All-in-One SDK (formerly Oculus Integration)
- **Build Target**: Android (Oculus Quest runs on Android)

### Key Unity Packages

- **XR Plugin Management**: Unity's XR framework
- **Oculus XR Plugin**: Meta Quest device support
- **TextMeshPro**: Enhanced text rendering for UI
- **Unity Input System**: Modern input handling (optional)
- **Cinemachine**: Camera control system (optional)

### Networking & APIs

- **REST API Client**: UnityWebRequest / HttpClient for backend-api calls
- **SignalR Client**: Microsoft.AspNetCore.SignalR.Client for real-time updates
- **JSON Serialization**: Newtonsoft.Json or Unity JsonUtility

### Physics & Audio

- **Physics**: Unity Physics Engine (PhysX)
- **Audio**: Unity Audio + FMOD Studio (optional for advanced audio)
- **Spatial Audio**: Unity Spatializer Plugin for 3D audio

## Development Guidelines

### Code Organization

```
Assets/
├── Scenes/                         # Unity scenes (.unity files)
│   ├── MainMenu.unity              # Main menu and settings
│   ├── RodeoArena.unity            # Primary gameplay arena
│   ├── Leaderboard.unity           # Leaderboard UI scene
│   └── Tutorial.unity              # Onboarding experience
│
├── Scripts/                        # C# game logic
│   ├── Core/                       # Core systems and managers
│   │   ├── GameManager.cs          # Central game state manager
│   │   ├── SceneLoader.cs          # Scene transition manager
│   │   └── ConfigManager.cs        # Configuration and settings
│   │
│   ├── Events/                     # Rodeo event implementations
│   │   ├── BullRiding.cs           # Bull riding physics and scoring
│   │   ├── BarrelRacing.cs         # Barrel racing logic
│   │   ├── RopingEvent.cs          # Roping mechanics
│   │   └── BroncRiding.cs          # Bronc riding system
│   │
│   ├── Multiplayer/                # Networking and real-time features
│   │   ├── NetworkManager.cs       # REST API calls to backend
│   │   ├── SignalRHub.cs           # SignalR connection manager
│   │   ├── LeaderboardSync.cs      # Real-time leaderboard updates
│   │   └── Models/                 # API data models
│   │
│   ├── Player/                     # Player control and interaction
│   │   ├── VRPlayerController.cs   # VR movement and input
│   │   ├── HandController.cs       # Hand tracking and interactions
│   │   └── ComfortSettings.cs      # Locomotion comfort options
│   │
│   ├── UI/                         # User interface systems
│   │   ├── VRCanvas.cs             # VR-friendly UI canvas
│   │   ├── LeaderboardUI.cs        # Leaderboard display
│   │   ├── ScoreDisplay.cs         # In-game score feedback
│   │   └── MenuNavigation.cs       # Menu interaction
│   │
│   └── Utils/                      # Helper utilities
│       ├── APIClient.cs            # HTTP request wrapper
│       ├── JsonHelper.cs           # JSON serialization helpers
│       └── Logger.cs               # Debug logging system
│
├── Prefabs/                        # Reusable game objects
│   ├── Player/                     # Player prefabs
│   ├── Environment/                # Arena and environment
│   ├── UI/                         # UI prefabs
│   └── Animals/                    # Bull, horse prefabs
│
├── Materials/                      # Visual materials
├── Models/                         # 3D models (.fbx, .obj)
├── Audio/                          # Sound effects and music
├── Resources/                      # Runtime-loaded assets
│   └── config.json                 # Runtime configuration
│
└── StreamingAssets/                # Platform-specific assets
```

### Coding Standards

#### C# Style Guide

- **Naming Conventions**:
  - Classes: `PascalCase` (e.g., `GameManager`)
  - Methods: `PascalCase` (e.g., `StartGame()`)
  - Private fields: `_camelCase` (e.g., `_playerScore`)
  - Public properties: `PascalCase` (e.g., `PlayerScore`)
  - Constants: `UPPER_SNAKE_CASE` (e.g., `MAX_PLAYERS`)

- **MonoBehaviour Best Practices**:
  - Use `Awake()` for internal initialization
  - Use `Start()` for external dependencies
  - Cache component references in `Awake()`
  - Use `Update()` sparingly (consider events instead)

#### Example Code

```csharp
public class GameManager : MonoBehaviour
{
    private static GameManager _instance;
    public static GameManager Instance => _instance;
    
    [SerializeField] private int _maxPlayers = 4;
    [SerializeField] private float _roundDuration = 120f;
    
    private NetworkManager _networkManager;
    private LeaderboardManager _leaderboardManager;
    
    private void Awake()
    {
        if (_instance != null && _instance != this)
        {
            Destroy(gameObject);
            return;
        }
        
        _instance = this;
        DontDestroyOnLoad(gameObject);
        
        _networkManager = GetComponent<NetworkManager>();
        _leaderboardManager = GetComponent<LeaderboardManager>();
    }
    
    private void Start()
    {
        InitializeGame();
    }
    
    private async void InitializeGame()
    {
        await _networkManager.ConnectAsync();
        await _leaderboardManager.LoadLeaderboardAsync();
    }
}
```

### API Integration

#### Backend Endpoints

**Base URL** (from `Assets/Resources/config.json`):
- Development: `https://backend-dev.azurecontainerapps.io`
- Production: `https://backend-prod.azurecontainerapps.io`

**Authentication**:
```csharp
POST /api/auth/login
POST /api/auth/register
POST /api/auth/refresh

// Example
var response = await apiClient.PostAsync<LoginResponse>(
    "/api/auth/login", 
    new { username, password }
);
```

**Scores**:
```csharp
POST /api/scores/submit
GET  /api/scores/user/{userId}
GET  /api/scores/event/{eventType}

// Example
await apiClient.PostAsync("/api/scores/submit", new {
    userId = CurrentUser.Id,
    eventType = "BullRiding",
    score = 89.5,
    timestamp = DateTime.UtcNow
});
```

**Leaderboards** (proxied to leaderboard-service):
```csharp
GET /api/leaderboard/global?limit=100
GET /api/leaderboard/regional/{region}?limit=50
GET /api/leaderboard/friends

// Example
var leaderboard = await apiClient.GetAsync<LeaderboardResponse>(
    "/api/leaderboard/global?limit=100"
);
```

#### SignalR Real-time Hub

**Hub URL**: `https://leaderboard-service.azurecontainerapps.io/leaderboardhub`

**Events to Subscribe**:
```csharp
// Leaderboard updates
hubConnection.On<LeaderboardUpdate>("LeaderboardChanged", update => {
    LeaderboardManager.Instance.UpdateLeaderboard(update);
});

// Friend achievements
hubConnection.On<Achievement>("FriendAchievement", achievement => {
    NotificationManager.Instance.ShowAchievement(achievement);
});

// Real-time score updates
hubConnection.On<ScoreUpdate>("ScoreUpdated", update => {
    ScoreDisplay.Instance.AnimateNewScore(update);
});
```

#### Data Models

```csharp
[Serializable]
public class Score
{
    public string userId;
    public string eventType;
    public float score;
    public DateTime timestamp;
    public string region;
}

[Serializable]
public class LeaderboardEntry
{
    public int rank;
    public string username;
    public float score;
    public string region;
    public DateTime achievedAt;
}

[Serializable]
public class LeaderboardUpdate
{
    public List<LeaderboardEntry> entries;
    public DateTime lastUpdated;
}
```

### VR Development Best Practices

#### Performance Optimization

**Target Frame Rates**:
- Quest 2: 72 FPS (minimum), 90 FPS (recommended)
- Quest 3: 90 FPS (minimum), 120 FPS (recommended)

**Optimization Checklist**:
- [ ] Keep draw calls under 200 (Quest 2) / 300 (Quest 3)
- [ ] Limit triangle count to 100k (Quest 2) / 150k (Quest 3)
- [ ] Use texture atlasing to reduce material count
- [ ] Enable GPU instancing for repeated objects
- [ ] Use occlusion culling for large environments
- [ ] Implement LOD (Level of Detail) for distant objects
- [ ] Pool frequently spawned objects (don't use Instantiate in Update)

#### VR Comfort Settings

Always provide options to reduce motion sickness:

```csharp
public enum LocomotionType
{
    Teleport,           // Safest for comfort
    SmoothLocomotion,   // Most immersive
    Hybrid              // Best of both
}

public enum TurnType
{
    SnapTurn,           // 30/45-degree increments
    SmoothTurn,         // Continuous rotation
    PhysicalTurnOnly    // No artificial rotation
}
```

#### VR UI Guidelines

- **Canvas Distance**: Place UI 2-3 meters from player
- **Text Size**: Minimum 36pt for readability
- **Button Size**: Minimum 5cm for reliable interaction
- **World Space**: Use world-space canvases, not screen-space
- **Laser Pointers**: Implement ray-based UI interaction

```csharp
// VR Canvas setup
public class VRCanvas : MonoBehaviour
{
    private const float OPTIMAL_DISTANCE = 2.5f;
    private Transform _cameraTransform;
    
    void Start()
    {
        GetComponent<Canvas>().renderMode = RenderMode.WorldSpace;
        _cameraTransform = Camera.main.transform;
        
        // Position canvas in front of player
        transform.position = _cameraTransform.position + 
                            _cameraTransform.forward * OPTIMAL_DISTANCE;
        transform.LookAt(_cameraTransform);
    }
}
```

### Testing Strategy

#### Unity Test Framework

Create tests in `Assets/Tests/`:

```
Tests/
├── EditMode/               # Edit-mode tests (no Play mode needed)
│   ├── DataModelTests.cs
│   ├── UtilityTests.cs
│   └── APIClientTests.cs
│
└── PlayMode/               # Play-mode tests (require scene execution)
    ├── GameManagerTests.cs
    ├── EventLogicTests.cs
    └── NetworkTests.cs
```

**Example Test**:
```csharp
using NUnit.Framework;
using UnityEngine;

public class ScoreCalculationTests
{
    [Test]
    public void BullRiding_FullTime_ReturnsMaxScore()
    {
        var calculator = new ScoreCalculator();
        float score = calculator.CalculateBullRidingScore(8.0f, 0);
        Assert.AreEqual(100f, score);
    }
    
    [Test]
    public void BullRiding_EarlyBuckOff_ReturnsPenalty()
    {
        var calculator = new ScoreCalculator();
        float score = calculator.CalculateBullRidingScore(5.0f, 0);
        Assert.Less(score, 100f);
    }
}
```

#### Manual Testing Checklist

Before each build:
- [ ] All rodeo events load without errors
- [ ] Score submission works (check backend logs)
- [ ] Leaderboard updates in real-time
- [ ] VR comfort settings apply correctly
- [ ] Performance maintains target FPS
- [ ] Audio positioning is accurate
- [ ] UI is readable and interactive
- [ ] No null reference exceptions in console

### Build & Deployment

#### Build Configuration

**Development Build**:
```csharp
// config.json
{
  "apiBaseUrl": "https://backend-dev.azurecontainerapps.io",
  "signalRHubUrl": "https://leaderboard-dev.azurecontainerapps.io/leaderboardhub",
  "environment": "development",
  "logLevel": "debug"
}
```

**Production Build**:
```csharp
// config.json
{
  "apiBaseUrl": "https://backend-prod.azurecontainerapps.io",
  "signalRHubUrl": "https://leaderboard-prod.azurecontainerapps.io/leaderboardhub",
  "environment": "production",
  "logLevel": "error"
}
```

#### Build Commands

```bash
# Build APK for Quest
Unity -quit -batchmode -projectPath . -buildTarget Android \
      -executeMethod BuildScript.BuildQuest

# Install to connected Quest device
adb install -r Builds/VirtualRodeo.apk

# View device logs
adb logcat -s Unity
```

#### Quest Store Submission

1. **Prepare Release Build**:
   - Set Production configuration
   - Enable code stripping
   - Disable debug logging
   - Test on both Quest 2 and Quest 3

2. **Oculus Platform Validation**:
   - Run Oculus Platform SDK validation tools
   - Check VRC (Virtual Reality Check) guidelines
   - Ensure all required permissions are declared

3. **Submit via Developer Dashboard**:
   - Upload APK to Oculus Developer Portal
   - Complete store listing (description, screenshots, videos)
   - Submit for review

## Cost Considerations

### Unity Licensing

- **Unity Personal**: Free (for teams with < $100k revenue/year)
- **Unity Pro**: $185/month per seat (required for teams > $100k revenue)

**This project uses**: Unity Personal (free tier)

### Cloud Services

The Unity client **does not incur direct cloud costs** since it runs on user devices. All cloud infrastructure costs are covered by backend services (see init repository).

**Related Infrastructure**:
- Backend API: ~$10/month (Azure Container Apps)
- Leaderboard Service: ~$10-20/month (Azure Container Apps)
- Redis Cache: ~$15/month per region (Standard C0)
- Azure Front Door: ~$35/month (Premium tier)

Total ecosystem cost: **$549-559/month**

## Security Best Practices

### Authentication

- **No hardcoded credentials** in Unity project
- **Token-based auth**: Obtain JWT tokens from backend-api
- **Secure storage**: Use PlayerPrefs encryption or native keystore

```csharp
// Good: Fetch token from backend
var loginResponse = await apiClient.PostAsync<LoginResponse>(
    "/api/auth/login", 
    new { username, password }
);
PlayerPrefs.SetString("AuthToken", loginResponse.token);

// Bad: Hardcoded credentials
// const string API_KEY = "abc123"; // NEVER DO THIS
```

### API Security

- **Certificate Pinning**: Validate SSL certificates
```csharp
// Unity Web Request with certificate validation
var request = UnityWebRequest.Get(url);
request.certificateHandler = new CustomCertificateHandler();
```

- **Input Validation**: Sanitize all user inputs before sending to backend
- **Rate Limiting**: Respect API rate limits (handle 429 responses)

### Cheat Prevention

- **Server-Authoritative Scoring**: All scores validated by backend
```csharp
// Unity client submits score
await SubmitScore(score);

// Backend validates score is physically possible
// Reject if score exceeds realistic bounds
```

- **Obfuscation**: Use code obfuscation for production builds
- **Tamper Detection**: Check for modified APKs

## Documentation References

### Internal Documentation

- **[init repository](https://github.com/dusty-plate/init)**: Architecture overview, deployment guides
- **[CONTAINER-APPS-ARCHITECTURE.md](https://github.com/dusty-plate/init/blob/main/docs/CONTAINER-APPS-ARCHITECTURE.md)**: Cloud infrastructure details
- **[REDIS-FRONT-DOOR-SETUP.md](https://github.com/dusty-plate/init/blob/main/docs/REDIS-FRONT-DOOR-SETUP.md)**: Caching and load balancing

### External Resources

- [Unity Documentation](https://docs.unity3d.com/)
- [Meta Quest Developer Center](https://developer.oculus.com/)
- [Meta XR SDK Documentation](https://developer.oculus.com/documentation/unity/)
- [Unity XR Interaction Toolkit](https://docs.unity3d.com/Packages/com.unity.xr.interaction.toolkit@latest)
- [C# Coding Conventions](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/coding-style/coding-conventions)

## GitHub Copilot Usage

### Effective Prompting Examples

**Request game logic**:
```
// Create a bull riding event controller that tracks ride time and calculates score
```

**Request API integration**:
```
// Implement async method to submit player score to backend-api POST /api/scores/submit
```

**Request VR interaction**:
```
// Create a VR hand controller that can grab and throw objects using Meta Quest controllers
```

**Request optimization**:
```
// Optimize this mesh rendering code to reduce draw calls for Quest 2
```

### Code Generation Tips

1. **Be Specific**: Include context about Unity version, platform (Quest), and C# version
2. **Reference Architecture**: Mention backend-api, leaderboard-service when integrating APIs
3. **Specify Performance**: Mention target FPS, draw call limits, triangle counts
4. **Include Examples**: Reference existing code patterns in the codebase

## Common Patterns

### Singleton Manager Pattern

```csharp
public class GameManager : MonoBehaviour
{
    private static GameManager _instance;
    public static GameManager Instance
    {
        get
        {
            if (_instance == null)
            {
                _instance = FindObjectOfType<GameManager>();
                if (_instance == null)
                {
                    Debug.LogError("GameManager not found in scene!");
                }
            }
            return _instance;
        }
    }
    
    private void Awake()
    {
        if (_instance != null && _instance != this)
        {
            Destroy(gameObject);
            return;
        }
        _instance = this;
        DontDestroyOnLoad(gameObject);
    }
}
```

### Async API Calls

```csharp
public async Task<T> GetAsync<T>(string endpoint)
{
    using (UnityWebRequest request = UnityWebRequest.Get(_baseUrl + endpoint))
    {
        request.SetRequestHeader("Authorization", $"Bearer {_authToken}");
        
        var operation = request.SendWebRequest();
        
        while (!operation.isDone)
            await Task.Yield();
        
        if (request.result == UnityWebRequest.Result.Success)
        {
            return JsonUtility.FromJson<T>(request.downloadHandler.text);
        }
        else
        {
            throw new Exception($"API Error: {request.error}");
        }
    }
}
```

### SignalR Connection

```csharp
private HubConnection _hubConnection;

private async Task ConnectToSignalR()
{
    _hubConnection = new HubConnectionBuilder()
        .WithUrl("https://leaderboard-service.azurecontainerapps.io/leaderboardhub")
        .WithAutomaticReconnect()
        .Build();
    
    _hubConnection.On<LeaderboardUpdate>("LeaderboardChanged", OnLeaderboardUpdate);
    
    await _hubConnection.StartAsync();
    Debug.Log("SignalR connected");
}

private void OnLeaderboardUpdate(LeaderboardUpdate update)
{
    MainThreadDispatcher.Enqueue(() => {
        LeaderboardUI.Instance.RefreshLeaderboard(update.entries);
    });
}
```

## Troubleshooting

### Common Issues

**Issue**: Build fails with "Gradle build failed"
- **Solution**: Update Android SDK tools, check `minSdkVersion` in Player Settings

**Issue**: VR camera not tracking
- **Solution**: Ensure XR Plugin Management is enabled, Oculus plugin is active

**Issue**: SignalR disconnects frequently
- **Solution**: Implement automatic reconnection logic, check network stability

**Issue**: Performance drops below 72 FPS
- **Solution**: Profile with Unity Profiler, reduce draw calls, optimize shaders

**Issue**: API calls fail with CORS errors
- **Solution**: Ensure backend-api has proper CORS headers (already configured)

## Contact & Support

- **Primary Documentation**: [init repository](https://github.com/dusty-plate/init)
- **Backend API Issues**: [backend-api repository](https://github.com/dusty-plate/backend-api)
- **Leaderboard Issues**: [leaderboard-service repository](https://github.com/colossus9/leaderboard-service)

---

**Last Updated**: November 16, 2025  
**Unity Version**: 2022.3 LTS  
**Target Platform**: Meta Quest 2 & 3

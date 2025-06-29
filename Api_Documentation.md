# Modular AI Kit - API Documentation

## Table of Contents
1. [AICore API](#aicore-api)
2. [PlayerDetectionSetup API](#playerdetectionsetup-api)
3. [AIAnimationSystem API](#aianimationsystem-api)
4. [AIFleeHealthSystem API](#aifleeheathsystem-api)
5. [AIMemorySystem API](#aimemoryystem-api)
6. [AIGroupCoordination API](#aigroupcoordination-api)
7. [AIEnvironmentalAwareness API](#aienvironmentalawareness-api)
8. [Profile System API](#profile-system-api)
9. [Waypoint System API](#waypoint-system-api)
10. [Event System API](#event-system-api)
11. [Debug Utilities](#debug-utilities)

---

## AICore API

### Properties
```csharp
// State Management
AIState CurrentState { get; }           // Current AI state
bool HasTarget { get; }                 // Whether AI has a target
float DistanceToTarget { get; }         // Distance to current target
Transform target                        // Current target reference

// Component References
NavMeshAgent navAgent                   // NavMesh agent reference
Rigidbody rb                           // Rigidbody reference
Animator animator                      // Animator reference
WaypointSystem waypointSystem          // Waypoint system reference
```

### Core Methods
```csharp
// State Control
void ChangeState(AIState newState)
void EnableAllComponents()             // Enable all AI components

// Movement Control
void MoveTo(Vector3 destination)
bool IsAtDestination()
bool IsNearTarget()
Vector3 GetNextPatrolPoint()
float GetCurrentWaitTime()

// Combat System
bool CanAttack()
void Attack()
void TriggerAnimation(string triggerName)

// Target Management
Vector3 GetLastKnownTargetPosition()
void SetSearchTimer(float time)
float GetSearchTimer()
void DecreaseSearchTimer()

// Alert System
void ResetAlertState()
void ForceAlert()
```

### Events
```csharp
// State Events
event Action<AIState, AIState> OnStateChanged;
event Action<Transform> OnTargetDetected;
event Action OnTargetLost;
event Action OnAttack;
```

### Usage Examples
```csharp
// Basic state control
aiCore.ChangeState(AIState.Chase);

// Check if AI can attack
if (aiCore.CanAttack())
{
    aiCore.Attack();
}

// Subscribe to events
aiCore.OnTargetDetected += (target) => {
    Debug.Log($"Target detected: {target.name}");
};

// Move AI to specific location
aiCore.MoveTo(targetPosition);
```

---

## PlayerDetectionSetup API

### Configuration Properties
```csharp
// Detection Config
PlayerDetectionConfig config           // Main configuration
AnimatorParameterConfig animatorParams // Animator parameters
AudioClipConfig audioClips            // Audio configuration
```

### Public Methods
```csharp
// Detection Control
void ForceAlert()
void ForceAttack()
void ForceFlee()
void ResetAlert()

// Health Management
void TakeDamage(float damage)
float GetHealth()
float GetHealthPercentage()
void SetHealth(float health)
bool IsLowHealth()

// Audio System
void PlayAudio(AudioClip[] clips, float volume)
void StopAllAudio()

// Animation Control
void TriggerAnimator(string parameter)
bool HasAnimatorParameter(string paramName)

// State Queries
bool IsTargetingPlayer()
bool IsInCombat()
bool IsAlerted()
```

### Events (UnityEvents)
```csharp
// Detection Events
UnityEvent OnPlayerDetected
UnityEvent OnPlayerLost
UnityEvent OnAlertTriggered

// Combat Events
UnityEvent OnAttackPlayer
UnityEvent OnTakeDamage
UnityEvent OnLowHealth
UnityEvent OnDeath

// State Events
UnityEvent OnEnterCombat
UnityEvent OnExitCombat
```

### Usage Examples
```csharp
// Subscribe to detection events
playerDetection.OnPlayerDetected.AddListener(() => {
    Debug.Log("Player detected!");
});

// Force AI into alert state
playerDetection.ForceAlert();

// Check AI status
if (playerDetection.IsTargetingPlayer())
{
    // Player is being targeted
}

// Apply damage
playerDetection.TakeDamage(25f);
```

---

## AIAnimationSystem API

### Configuration
```csharp
AIAnimationSettings animSettings       // Animation configuration
```

### Public Methods
```csharp
// Animation Control
void PlayAttackAnimation()
void PlayAlertAnimation()
void PlayDeathAnimation()

// Audio Control
void SetFootStepVolume(float volume)
void EnableFootSteps(bool enable)
void TriggerFootStep()               // Manual footstep trigger

// System Control
void EnableAnimations(bool enable)
```

### Configuration Properties
```csharp
// Animation Settings
bool enableAnimations
string speedParameter
string isMovingParameter
string attackTrigger
string alertTrigger
string deathTrigger

// FootStep Audio
bool enableFootSteps
AudioClip[] walkFootSteps
AudioClip[] runFootSteps
float footStepVolume
float walkStepInterval
float runStepInterval
```

### Usage Examples
```csharp
// Trigger specific animations
animSystem.PlayAttackAnimation();
animSystem.PlayAlertAnimation();

// Control footstep audio
animSystem.SetFootStepVolume(0.8f);
animSystem.EnableFootSteps(true);

// Manual footstep for animation events
animSystem.TriggerFootStep();
```

---

## AIFleeHealthSystem API

### Properties
```csharp
// Health Status
float CurrentHealth { get; }
float MaxHealth { get; }
float HealthPercentage { get; }
bool IsLowHealth { get; }
bool IsCriticalHealth { get; }

// Flee Status
bool IsFleeing { get; }
bool ShouldFlee { get; }
bool IsUsingProfileSettings { get; }
```

### Health Management
```csharp
// Health Control
void TakeDamage(float damage)
void Heal(float amount)
void SetHealth(float newHealth)
void SetMaxHealth(float newMaxHealth)
void Die()
void Revive(float healthAmount = -1)

// Flee Control
void ForceFlee()
void StopFlee()
void SetCowardly(bool isCowardly)
void SetFleeThreshold(float threshold)
```

### Profile Integration
```csharp
// Profile Management
void ApplyProfileSettings(AIMasterProfile profile)
void EnableProfileSettings(bool enable)
void OverrideProfileSettings(bool overrideSettings)
void RefreshFromProfile()
AIMasterProfile GetCurrentProfile()
```

### Events
```csharp
// Health Events
UnityEvent<float> OnHealthChanged
UnityEvent<float> OnDamageTaken
UnityEvent OnLowHealth
UnityEvent OnCriticalHealth
UnityEvent OnDeath

// Flee Events
UnityEvent OnFleeStart
UnityEvent OnFleeEnd
UnityEvent OnCowardlyBehavior

// Profile Events
UnityEvent<AIMasterProfile> OnProfileApplied
```

### Usage Examples
```csharp
// Health management
healthSystem.TakeDamage(30f);
healthSystem.Heal(15f);

// Check health status
if (healthSystem.IsLowHealth)
{
    healthSystem.ForceFlee();
}

// Apply profile
healthSystem.ApplyProfileSettings(combatProfile);

// Subscribe to events
healthSystem.OnFleeStart.AddListener(() => {
    Debug.Log("AI started fleeing!");
});
```

---

## AIMemorySystem API

### Properties
```csharp
bool HasMemories { get; }
bool IsInvestigating { get; }
int MemoryCount { get; }
Vector3 LastInvestigationPoint { get; }
```

### Memory Management
```csharp
// Add Memory
void AddMemory(Vector3 position, MemoryType type, float confidence = 1f, 
               Transform source = null, string description = "")

// Query Memory
MemoryEntry? GetBestMemory()
List<MemoryEntry> GetMemoriesByType(MemoryType type)
List<MemoryEntry> GetMemoriesInRadius(Vector3 center, float radius)
float GetMemoryConfidenceAt(Vector3 position, float radius = 2f)
bool HasMemoryOfType(MemoryType type)

// Investigation Control
void ForceInvestigation(Vector3 location)
void StopInvestigation()
void ClearAllMemories()
```

### Memory Types
```csharp
public enum MemoryType
{
    DirectSight,    // Saw directly
    Sound,          // Heard a sound
    Reported,       // Reported by another AI
    Footprints,     // Found evidence
    Suspicious,     // Something suspicious
    LastKnown       // Last known position
}
```

### Events
```csharp
UnityEvent<MemoryEntry> OnMemoryAdded
UnityEvent<Vector3> OnStartInvestigation
UnityEvent OnFinishInvestigation
UnityEvent<int> OnMemoryShared
UnityEvent<Vector3> OnSuspiciousActivityFound
```

### Usage Examples
```csharp
// Add memory of player sighting
memorySystem.AddMemory(playerPosition, MemoryType.DirectSight, 1.0f, 
                      playerTransform, "Player spotted");

// Get best memory to investigate
var bestMemory = memorySystem.GetBestMemory();
if (bestMemory.HasValue)
{
    memorySystem.ForceInvestigation(bestMemory.Value.position);
}

// Check for specific memory types
if (memorySystem.HasMemoryOfType(MemoryType.Sound))
{
    // React to sound memories
}
```

---

## AIGroupCoordination API

### Properties
```csharp
bool IsGroupLeader { get; }
bool IsInGroup { get; }
int GroupSize { get; }
CoordinationRole CurrentRole { get; }
bool HasActiveOrder { get; }
Vector3 AssignedPosition { get; }
```

### Group Management
```csharp
// Manual Control
void ReceiveTacticalOrder(TacticalOrder order)
void ReceiveSharedTarget(Transform target)

// Information Access
List<Vector3> GetAvailableCoverPositions()
List<Vector3> GetAvailableHighGroundPositions()
```

### Coordination Roles
```csharp
public enum CoordinationRole
{
    None, Leader, Flanker, Support, 
    Distractor, Sniper, Rusher
}
```

### Tactical Actions
```csharp
public enum TacticalAction
{
    None, Flank, Suppress, Advance, Retreat,
    Surround, Ambush, CoverFire, CallReinforcements
}
```

### Events
```csharp
// Group Events
UnityEvent<int> OnGroupFormed
UnityEvent OnGroupDisbanded
UnityEvent<CoordinationRole> OnRoleAssigned

// Tactical Events
UnityEvent<TacticalAction> OnTacticalOrderReceived
UnityEvent<Vector3> OnFlankingPositionReached
UnityEvent OnCoverFireStarted
```

### Usage Examples
```csharp
// Check group status
if (coordination.IsGroupLeader)
{
    Debug.Log($"Leading group of {coordination.GroupSize} AIs");
}

// Manual tactical order
var order = new TacticalOrder(TacticalAction.Flank, targetPosition, 
                             CoordinationRole.Flanker, 0.8f);
coordination.ReceiveTacticalOrder(order);

// Subscribe to coordination events
coordination.OnRoleAssigned.AddListener((role) => {
    Debug.Log($"Assigned role: {role}");
});
```

---

## AIEnvironmentalAwareness API

### Environmental Control
```csharp
// Force Actions
void ForceLightManipulation()
void ForceCoverSeek()

// State Queries
bool IsInCover()
bool IsOnHighGround()

// Position Access
List<Vector3> GetAvailableCoverPositions()
List<Vector3> GetAvailableHighGroundPositions()
```

### Events
```csharp
// Interaction Events
UnityEvent<InteractableType> OnEnvironmentInteraction
UnityEvent<GameObject> OnObjectThrown
UnityEvent<Vector3> OnCoverTaken
UnityEvent<Vector3> OnHighGroundReached

// Tactical Events
UnityEvent OnLightTurnedOff
UnityEvent OnDistrationCreated
```

### Interactable Types
```csharp
public enum InteractableType
{
    Light, Door, Window, Switch, Alarm, Camera,
    Cover, HighGround, ThrowableObject, Destructible,
    Container, Ladder, Elevator, Computer
}
```

### Usage Examples
```csharp
// Force tactical actions
envAwareness.ForceLightManipulation();
envAwareness.ForceCoverSeek();

// Check tactical status
if (envAwareness.IsInCover())
{
    Debug.Log("AI is safely in cover");
}

// Get available positions
var coverPositions = envAwareness.GetAvailableCoverPositions();
```

---

## Profile System API

### AIMasterProfile
```csharp
// Profile Information
string profileName
string description
AIType aiType
float maxHealth
bool hasSpecialAbilities

// Sub-Profiles
AIDetectionProfile detectionProfile
AICombatProfile combatProfile
AIMovementProfile movementProfile

// Methods
void ApplyToAICore(AICore aiCore)
void ApplyToPlayerDetection(PlayerDetectionSetup playerDetection)
float GetEffectiveValue(float baseValue, string valueType)
string GetRandomPhrase(string situationType)
```

### AIProfileIntegration
```csharp
// Profile Management
void ApplyProfile(AIMasterProfile profile, bool force = false)
void SwitchProfile(AIMasterProfile newProfile)
void ApplyTemporaryModifier(AIMasterProfile modifier, float duration)
void ResetToOriginalSettings()
AIMasterProfile GetCurrentEffectiveProfile()
void RefreshProfile()
```

### Usage Examples
```csharp
// Apply profile to AI
var warriorProfile = Resources.Load<AIMasterProfile>("WarriorProfile");
profileIntegration.ApplyProfile(warriorProfile);

// Temporary behavior change
profileIntegration.ApplyTemporaryModifier(berserkerProfile, 10f);

// Runtime profile switching
profileIntegration.SwitchProfile(stealthProfile);
```

---

## Waypoint System API

### Waypoint Management
```csharp
// Properties
int WaypointCount { get; }
Waypoint CurrentWaypoint { get; }

// Navigation
Waypoint GetWaypoint(int index)
Waypoint GetNextWaypoint()
Waypoint GetClosestWaypoint(Vector3 position)
List<Waypoint> GetWaypointsByType(WaypointType type)
void SetCurrentWaypoint(Waypoint waypoint)
```

### Waypoint Types
```csharp
public enum WaypointType
{
    Patrol, Guard, Rest, Investigate, Custom
}
```

### Usage Examples
```csharp
// Get next patrol point
var nextWaypoint = waypointSystem.GetNextWaypoint();
if (nextWaypoint != null)
{
    aiCore.MoveTo(nextWaypoint.Position);
}

// Find specific waypoint types
var guardPoints = waypointSystem.GetWaypointsByType(WaypointType.Guard);
```

---

## Event System API

### AIEventSystem
```csharp
// State Events
AIEvent OnEnterIdle
AIEvent OnEnterPatrol
AIEvent OnEnterChase
AIEvent OnEnterAttack

// General Events
AIEvent OnTargetDetected
AIEvent OnTargetLost
AIEvent OnAttackPerformed
AIEvent OnWaypointReached

// Manual Triggers
void TriggerWaypointReached()
```

### Usage Examples
```csharp
// Subscribe to AI events
eventSystem.OnEnterCombat.AddListener((ai) => {
    Debug.Log($"{ai.name} entered combat!");
});

// Manual event triggering
eventSystem.TriggerWaypointReached();
```

---

## Debug Utilities

### AIDebugHelper
```csharp
// Context Menu Commands
[ContextMenu("Force Target Player")]
void ForceTargetPlayer()

[ContextMenu("Force Attack State")]
void ForceAttackState()

[ContextMenu("Test Attack")]
void TestAttack()
```

### AIDebugUtils (Static)
```csharp
// Debug Visualization
static void DrawPath(AICore ai, Color color)
static void DrawDetectionRadius(AICore ai, Color color)
static void LogAIStatus(AICore ai)
```

### Usage Examples
```csharp
// Debug AI behavior
AIDebugUtils.LogAIStatus(aiCore);
AIDebugUtils.DrawPath(aiCore, Color.red);

// Force behaviors for testing
debugHelper.ForceTargetPlayer();
debugHelper.TestAttack();
```

---

## Best Practices

### Performance Optimization
```csharp
// Use LOD for distant AIs
if (Vector3.Distance(transform.position, player.position) > 50f)
{
    // Reduce update frequency
    aiCore.enabled = false;
}

// Batch operations
foreach (var ai in nearbyAIs)
{
    ai.ReceiveSharedTarget(target);
}
```

### Error Handling
```csharp
// Always check for null references
if (aiCore != null && aiCore.HasTarget)
{
    aiCore.Attack();
}

// Validate components
if (memorySystem.HasMemories)
{
    var memory = memorySystem.GetBestMemory();
    // Process memory
}
```

### Event Management
```csharp
// Subscribe in Start
void Start()
{
    aiCore.OnTargetDetected += HandleTargetDetected;
}

// Unsubscribe in OnDestroy
void OnDestroy()
{
    if (aiCore != null)
        aiCore.OnTargetDetected -= HandleTargetDetected;
}
```

---

## Complete Integration Example

```csharp
public class CustomAIController : MonoBehaviour
{
    private AICore aiCore;
    private AIFleeHealthSystem healthSystem;
    private AIMemorySystem memorySystem;
    private AIProfileIntegration profileIntegration;
    
    void Start()
    {
        // Get components
        aiCore = GetComponent<AICore>();
        healthSystem = GetComponent<AIFleeHealthSystem>();
        memorySystem = GetComponent<AIMemorySystem>();
        profileIntegration = GetComponent<AIProfileIntegration>();
        
        // Subscribe to events
        aiCore.OnTargetDetected += OnTargetSpotted;
        healthSystem.OnLowHealth.AddListener(OnHealthLow);
        memorySystem.OnMemoryAdded.AddListener(OnNewMemory);
        
        // Apply initial profile
        var profile = Resources.Load<AIMasterProfile>("MyCustomProfile");
        profileIntegration.ApplyProfile(profile);
    }
    
    void OnTargetSpotted(Transform target)
    {
        // Add memory and alert group
        memorySystem.AddMemory(target.position, MemoryType.DirectSight, 1.0f);
        
        // Switch to combat profile
        var combatProfile = Resources.Load<AIMasterProfile>("CombatProfile");
        profileIntegration.ApplyProfile(combatProfile);
    }
    
    void OnHealthLow()
    {
        // Switch to defensive profile and flee
        var fleeProfile = Resources.Load<AIMasterProfile>("FleeProfile");
        profileIntegration.ApplyProfile(fleeProfile);
        healthSystem.ForceFlee();
    }
    
    void OnNewMemory(MemoryEntry memory)
    {
        if (memory.type == MemoryType.Sound)
        {
            // Investigate sound
            memorySystem.ForceInvestigation(memory.position);
        }
    }
}
```

This comprehensive API documentation covers all the major systems and their public interfaces, providing developers with everything they need to effectively use and extend the Modular AI Kit.
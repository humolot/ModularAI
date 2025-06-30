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
---
## 🎯 AdvancedEnemySpawner API

### Properties
```csharp
bool IsSpawning { get; }              // Is spawner currently active
bool IsPaused { get; }                // Is spawner paused
int CurrentEnemyCount { get; }        // Number of active enemies
int MaxEnemies { get; }               // Maximum enemy limit
bool CanSpawnMore { get; }            // Can spawn more enemies
float SpawnProgress { get; }          // Progress (0-1) towards max enemies
int CurrentWave { get; }              // Current wave number
bool IsWaveActive { get; }            // Is wave system active
float SessionTime { get; }            // Time since spawner started
```

### Basic Controls
```csharp
// Core Spawner Control
void StartSpawning()                  // Start the spawning system
void StopSpawning()                   // Stop spawning completely
void PauseSpawning()                  // Pause spawning temporarily
void ResumeSpawning()                 // Resume paused spawning

// Manual Spawning
void SpawnEnemyManually(string enemyTypeName)           // Spawn specific enemy type
void SpawnEnemyAtPoint(string enemyTypeName, int index) // Spawn at specific point
void ClearAllEnemies()                                  // Remove all spawned enemies
```

### Wave System
```csharp
// Wave Control
void StartNextWave()                  // Start next wave manually
void RestartWaves()                   // Restart wave system from beginning
void SetDifficulty(float multiplier) // Set global difficulty multiplier (affects all waves)
```

### Configuration
```csharp
// Spawn Points
void AddSpawnPoint(Transform point, bool isElite = false, bool isBoss = false)
void RemoveSpawnPoint(Transform point)

// Player Reference
void SetPlayerReference(Transform player)
```

### Statistics & Information
```csharp
// Statistics
Dictionary<string, int> GetSpawnStatistics()    // Get spawn count per enemy type
List<GameObject> GetActiveEnemies()             // Get list of all active enemies
int GetEnemyCount(string enemyTypeName)         // Count of specific enemy type
float GetSpawnRate()                            // Enemies spawned per second
void ResetStatistics()                          // Reset all statistics
```

### Example Usage
```csharp
// Basic setup and control
AdvancedEnemySpawner spawner = GetComponent<AdvancedEnemySpawner>();
spawner.StartSpawning();
spawner.SetDifficulty(1.5f);

// Manual spawning
spawner.SpawnEnemyManually("Basic Enemy");
spawner.SpawnEnemyManually("Elite Soldier");

// Wave control
if (!spawner.IsWaveActive)
    spawner.StartNextWave();

// Emergency controls
if (spawner.CurrentEnemyCount > 50)
    spawner.ClearAllEnemies();

// Statistics
var stats = spawner.GetSpawnStatistics();
Debug.Log($"Basic Enemies spawned: {stats["Basic Enemy"]}");
```

---

## 🎛️ AdvancedTriggerSystem API

### Properties
```csharp
bool IsActive { get; }                // Can trigger activate
bool HasObjectsInTrigger { get; }     // Are there objects in trigger
int ObjectCount { get; }              // Number of objects in trigger
float CooldownRemaining { get; }      // Time left on cooldown
```

### Trigger Controls
```csharp
// Manual Control
void ResetTrigger()                   // Reset trigger state and usage count
void ForceTrigger()                   // Force trigger activation
void SetCooldown(float cooldownTime)  // Change cooldown duration

// Tag Management
void AddAllowedTag(string tag)        // Add allowed tag to filter
void RemoveAllowedTag(string tag)     // Remove tag from filter

// Configuration
void SetTriggerMode(TriggerMode mode) // Change trigger mode
void SetMaxUses(int maxUses)          // Set usage limit
void EnableAudio(bool enable)         // Enable/disable audio
void EnableAnimation(bool enable)     // Enable/disable animations
void SetActivationKey(KeyCode key)    // Change activation key
void SetDebugMode(bool enable)        // Enable/disable debug logs
```

### Object Queries
```csharp
// Object Information
void ClearObjectsInTrigger()          // Clear all tracked objects
List<GameObject> GetObjectsInTrigger() // Get all objects in trigger
bool HasObjectWithTag(string tag)     // Check if specific tag present
GameObject GetFirstObjectWithTag(string tag) // Get first object with tag
int GetObjectCountWithTag(string tag) // Count objects with specific tag
```

### Save/Load System
```csharp
// Persistence
TriggerSaveData GetSaveData()         // Get save data
void LoadSaveData(TriggerSaveData data) // Load save data
```

### Example Usage
```csharp
AdvancedTriggerSystem trigger = GetComponent<AdvancedTriggerSystem>();

// Setup
trigger.AddAllowedTag("Player");
trigger.AddAllowedTag("NPC");
trigger.SetMaxUses(5);

// Runtime control
if (trigger.HasObjectWithTag("Player"))
{
    trigger.ForceTrigger();
}

// Configuration changes
trigger.SetActivationKey(KeyCode.F);
trigger.SetCooldown(10f);
```

---

## 🔫 AIRangedCombat API

### Properties
```csharp
bool CanFire { get; }                 // Can weapon fire right now
bool HasAmmo { get; }                 // Has ammunition remaining
float AmmoPercentage { get; }         // Ammo as percentage (0-1)
bool IsInRange { get; }               // Target is in weapon range
bool HasLineOfSight { get; }          // Clear line of sight to target
Transform CurrentTarget { get; }      // Current target reference
```

### Combat Control
```csharp
// Weapon Control
bool CanPerformRangedAttack()         // Check if can attack now
void ForceReload()                    // Force weapon reload
void SetAmmo(int ammo)                // Set current ammunition
int GetCurrentAmmo()                  // Get current ammo count
bool IsReloading()                    // Is weapon currently reloading

// Target Management
bool IsTargetInRange(Transform target)    // Check if target in range
bool CheckLineOfSight(Transform target)   // Check line of sight to target
void ApplyDamage(Transform target, float damage, Vector3 hitPoint, Vector3 hitNormal)
```

### Weapon Configuration
```csharp
// Configuration
float GetFireInterval()               // Get time between shots
```

### Example Usage
```csharp
AIRangedCombat rangedCombat = GetComponent<AIRangedCombat>();

// Combat checks
if (rangedCombat.CanPerformRangedAttack() && rangedCombat.HasLineOfSight)
{
    // AI will attack automatically through events
}

// Ammo management
if (rangedCombat.AmmoPercentage < 0.2f)
{
    rangedCombat.ForceReload();
}

// Manual ammo control
rangedCombat.SetAmmo(30);
```

---

## 🛡️ CoverPoint API

### Properties
```csharp
bool IsAvailable { get; }             // Can accept new occupants
bool IsOccupied { get; }              // Has any occupants
bool IsFull { get; }                  // At maximum capacity
bool IsCompromised { get; }           // Is cover compromised by threats
int OccupantCount { get; }            // Number of current occupants
float EffectiveProtection { get; }    // Protection value (0-1)
Vector3 CoverPosition { get; }        // Optimal cover position
```

### Occupancy Management
```csharp
// Cover Control
bool CanAcceptOccupant(GameObject requester)     // Check if can accept occupant
bool RequestCover(GameObject requester)           // Request to use cover
void ReleaseCover(GameObject occupant)            // Release cover usage
void ForceClearOccupants()                       // Force clear all occupants

// Information
Vector3 GetOptimalCoverPosition()                 // Get best position for cover
float GetDistanceFrom(Vector3 position)           // Distance from position
bool IsOccupiedBy(GameObject occupant)            // Check if specific occupant
List<GameObject> GetCurrentOccupants()           // Get all current occupants
```

### Example Usage
```csharp
CoverPoint coverPoint = GetComponent<CoverPoint>();

// Request cover
if (coverPoint.CanAcceptOccupant(gameObject))
{
    if (coverPoint.RequestCover(gameObject))
    {
        Vector3 coverPos = coverPoint.GetOptimalCoverPosition();
        transform.position = coverPos;
    }
}

// Release when safe
if (healthSystem.HealthPercentage > 80f)
{
    coverPoint.ReleaseCover(gameObject);
}
```

---

## 🏃 CoverSystemIntegration API

### Properties
```csharp
bool IsInCover { get; }               // Currently in cover
bool IsSeekingCover { get; }          // Moving to cover
bool HasValidCover { get; }           // Has valid cover point
float TimeInCover { get; }            // Time spent in current cover
```

### Cover Control
```csharp
// Cover Management
bool TrySeekCover()                   // Try to find and move to cover
void ForceLeaveCover()                // Force leave current cover
void ForceSeekCover()                 // Force seek cover immediately

// Information
CoverPoint GetCurrentCover()          // Get current cover point
float GetCoverProtection()            // Get protection value (0-1)
bool IsInEffectiveCover()             // In cover with good protection

// Configuration
void SetCoverSystemEnabled(bool enabled) // Enable/disable cover system
```

### Example Usage
```csharp
CoverSystemIntegration coverSystem = GetComponent<CoverSystemIntegration>();

// Automatic cover seeking
if (underFire && !coverSystem.IsInCover)
{
    coverSystem.TrySeekCover();
}

// Manual control
if (coverSystem.IsInCover && safe)
{
    coverSystem.ForceLeaveCover();
}

// Status checking
if (coverSystem.IsInEffectiveCover())
{
    Debug.Log($"Protection: {coverSystem.GetCoverProtection():P0}");
}
```

---

## 🌐 CoverManager API (Static)

### Cover Point Management
```csharp
// Registration (automatic)
static void RegisterCoverPoint(CoverPoint coverPoint)     // Register cover point
static void UnregisterCoverPoint(CoverPoint coverPoint)   // Unregister cover point

// Search Functions
static CoverPoint FindNearestAvailableCover(Vector3 position, float maxDistance = 20f)
static List<CoverPoint> FindAllAvailableCoversInRange(Vector3 position, float maxDistance = 20f)
static CoverPoint FindBestCoverFromThreat(Vector3 position, Vector3 threatPosition, float maxDistance = 20f)

// Statistics
static int GetTotalCoverPoints()      // Total registered cover points
static int GetAvailableCoverPoints()  // Available cover points
```

### Example Usage
```csharp
// Find cover
CoverPoint nearestCover = CoverManager.FindNearestAvailableCover(transform.position);
if (nearestCover != null)
{
    nearestCover.RequestCover(gameObject);
}

// Find cover from specific threat
CoverPoint safeCover = CoverManager.FindBestCoverFromThreat(
    transform.position, 
    threatPosition, 
    20f
);

// Statistics
Debug.Log($"Available covers: {CoverManager.GetAvailableCoverPoints()}");
```

---

## 🎮 Common Integration Patterns

### Basic AI Setup
```csharp
// Get all AI components
AICore aiCore = GetComponent<AICore>();
AIFleeHealthSystem health = GetComponent<AIFleeHealthSystem>();
AIRangedCombat ranged = GetComponent<AIRangedCombat>();
CoverSystemIntegration cover = GetComponent<CoverSystemIntegration>();

// Setup AI behavior
health.SetFleeThreshold(30f);
cover.SetCoverSystemEnabled(true);
```

### Combat Management
```csharp
// Check AI state and control accordingly
if (health.ShouldFlee && !cover.IsInCover)
{
    cover.TrySeekCover();
}
else if (ranged.CanPerformRangedAttack())
{
    // Combat handled automatically
}
```

### Spawner Integration
```csharp
// Setup spawner with AI systems
spawner.StartSpawning();
spawner.SetDifficulty(1.2f);

// Monitor spawned enemies
var enemies = spawner.GetActiveEnemies();
foreach(var enemy in enemies)
{
    var health = enemy.GetComponent<AIFleeHealthSystem>();
    if (health != null && health.HealthPercentage < 20f)
    {
        // Low health enemy detected
    }
}
```
---
This comprehensive API documentation covers all the major systems and their public interfaces, providing developers with everything they need to effectively use and extend the Modular AI Kit.

# Modular AI Kit - Complete User Manual

**Version 2.0 | Unity 2021.3 LTS or Higher | GBX Studios**

![ModularAI Editor](https://gbxstudios.xyz/ModularAI/Img/main.png)
---

## Table of Contents

1. [Getting Started](#getting-started)
2. [Quick Setup Guide](#quick-setup-guide)
3. [Core Systems](#core-systems)
4. [Advanced Features](#advanced-features)
5. [Profile System](#profile-system)
6. [Automation Tools](#automation-tools)
7. [Customization Guide](#customization-guide)
8. [Troubleshooting](#troubleshooting)
9. [Performance Optimization](#performance-optimization)
10. [Best Practices](#best-practices)

---

## Getting Started

### System Requirements

**Minimum Requirements:**
- Unity 2021.3 LTS or higher
- NavMesh System (built into Unity)
- Physics System (built into Unity)

**Recommended:**
- Unity 2022.3 LTS for best performance
- 8GB RAM or higher
- Dedicated graphics card for complex scenes

### Installation

1. **Import the Package**
   - Open Unity Hub and create a new 3D project
   - Import the Modular AI Kit package
   - Wait for scripts to compile

2. **Verify Installation**
   - Check `Tools > Modular AI` menu appears
   - Ensure no compilation errors in Console

3. **Open Documentation**
   - Go to `Tools > Modular AI > About & Manual`
   - Familiarize yourself with available tools

---

## Quick Setup Guide

### Method 1: Complete Auto Setup (Recommended)

**Step 1: Open Setup Window**
```
Tools > Modular AI > Complete AI Setup
```

**Step 2: Create or Select AI**
- Click "Create Capsule AI" for instant setup
- Or drag existing GameObject to "AI GameObject" field

**Step 3: Choose AI Type**
- **Combat**: Aggressive, attacks players
- **Guard**: Defensive, chases but doesn't attack
- **Civilian**: Peaceful, flees from threats
- **Cowardly**: Extremely fearful, immediate flee

**Step 4: Deploy**
- Click "SETUP COMPLETE AI"
- System automatically configures all components
- Creates player if none exists
- Sets up NavMesh requirements

**Step 5: Test**
- Press Play
- Move near the AI to see detection
- Observe behavior based on selected type

### Method 2: Manual Setup

**Step 1: Create AI GameObject**
```csharp
GameObject ai = GameObject.CreatePrimitive(PrimitiveType.Capsule);
ai.name = "My_AI";
```

**Step 2: Add Required Components**
```csharp
// Core components
ai.AddComponent<AICore>();
ai.AddComponent<NavMeshAgent>();

// Detection and behavior
ai.AddComponent<PlayerDetectionSetup>();

// Animation and audio
ai.AddComponent<AIAnimationSystem>();

// Health and flee system
ai.AddComponent<AIFleeHealthSystem>();
```

**Step 3: Configure Settings**
- Adjust AICore settings for desired behavior
- Set up detection radius and field of view
- Configure health and flee thresholds

**Step 4: Create Player**
```csharp
GameObject player = GameObject.CreatePrimitive(PrimitiveType.Capsule);
player.name = "Player";
player.tag = "Player";
player.layer = LayerMask.NameToLayer("Player");
```

**Step 5: Bake NavMesh**
```
Window > AI > Navigation
Select all static geometry
Click "Bake"
```

---

## Core Systems

### AICore - The Brain

**Purpose**: Central AI controller managing states, movement, and behavior.

**Key Settings:**
```csharp
// Detection
detectionRadius = 10f;        // How far AI can see
fieldOfView = 60f;           // Vision cone angle
targetLayer = 1;             // What layers to detect

// Movement  
moveSpeed = 3.5f;            // Walking speed
runSpeed = 6f;               // Running speed
stoppingDistance = 1.5f;     // Stop distance from target

// Combat
attackRange = 2f;            // Attack reach
attackCooldown = 1.5f;       // Time between attacks
damageAmount = 10f;          // Damage per attack
```

**Navigation Modes:**
- **Waypoints**: Use WaypointSystem for complex routes
- **RandomNavMesh**: Random patrol within radius
- **FixedPatrol**: Traditional patrol points array

**State Machine:**
- **Idle**: Waiting, looking around
- **Patrol**: Following waypoints or random movement
- **Chase**: Pursuing detected target
- **Attack**: Engaging target in combat
- **Search**: Looking for lost target
- **Flee**: Escaping from threats

### PlayerDetectionSetup - Detection & Events

**Purpose**: Handles player detection, audio, and basic AI responses.

**Configuration Options:**
```csharp
// Behavior
attackPlayer = true;         // Will AI attack player?
chasePlayer = true;          // Will AI chase player?
alertOtherAIs = true;        // Alert nearby AIs?
playerTag = "Player";        // What tag to look for

// Audio
enableAudio = true;          // Play sounds?
detectionVolume = 0.8f;      // Audio volume
alertVolume = 0.9f;          // Alert sound volume
```

**Audio Configuration:**
- **Idle Sounds**: Played during normal patrol
- **Alert Sounds**: Played when detecting player
- **Attack Sounds**: Played during combat
- **Pain Sounds**: Played when taking damage
- **Death Sounds**: Played when dying

**Events Available:**
- OnPlayerDetected
- OnPlayerLost
- OnAttackPlayer
- OnTakeDamage
- OnLowHealth
- OnDeath

### AIAnimationSystem - Animations & Audio

**Purpose**: Manages animations and footstep audio based on AI state.

**Animation Parameters:**
```csharp
speedParameter = "Speed";        // Float: Movement speed
isMovingParameter = "IsMoving";  // Bool: Is AI moving?
attackTrigger = "Attack";        // Trigger: Attack animation
alertTrigger = "Alert";          // Trigger: Alert animation
deathTrigger = "Death";          // Trigger: Death animation
```

**Footstep System:**
```csharp
enableFootSteps = true;          // Enable footstep audio
footStepVolume = 0.5f;          // Footstep volume
walkStepInterval = 0.5f;        // Time between walk steps
runStepInterval = 0.3f;         // Time between run steps
pitchVariation = 0.1f;          // Random pitch variation
```

**Setup Requirements:**
- Animator Controller with proper parameters
- AudioSource component for footsteps
- FootStep AudioClips assigned

### AIFleeHealthSystem - Health & Survival

**Purpose**: Manages AI health and personality-based flee behavior.

**Health Settings:**
```csharp
maxHealth = 100f;               // Maximum health
currentHealth = 100f;           // Current health
enableHealthRegen = false;      // Health regeneration
regenRate = 1f;                 // Health per second
regenDelay = 5f;                // Delay after damage
```

**Flee Configuration:**
```csharp
enableFleeSystem = true;        // Enable flee behavior
fleeHealthThreshold = 30f;      // Health % to flee at
isCowardlyAI = false;          // Always flees regardless
fleeDistance = 15f;            // How far to flee
fleeSpeed = 8f;                // Speed while fleeing
fleeTime = 5f;                 // How long to flee
```

**Profile Integration:**
- Automatically configures from Master Profiles
- Personality affects flee behavior
- Runtime profile switching supported

---

## Advanced Features

### Memory System

**Purpose**: AI remembers player locations, sounds, and evidence.

**Memory Types:**
- **DirectSight**: Saw player directly
- **Sound**: Heard suspicious noise
- **Reported**: Information from other AI
- **Footprints**: Found evidence
- **Suspicious**: Something unusual
- **LastKnown**: Last known player position

**Configuration:**
```csharp
maxMemoryDuration = 60f;        // How long memories last
maxMemoryEntries = 20;          // Maximum memories stored
investigationTime = 8f;         // Time spent investigating
investigationRadius = 3f;       // Search area radius
```

**Usage Example:**
```csharp
// Add memory manually
memorySystem.AddMemory(playerPosition, MemoryType.DirectSight, 1.0f);

// Force investigation
memorySystem.ForceInvestigation(suspiciousLocation);

// Check for memories
if (memorySystem.HasMemories)
{
    var bestMemory = memorySystem.GetBestMemory();
    // Process memory
}
```

### Group Coordination

**Purpose**: Multiple AIs work together with tactical roles.

**Coordination Roles:**
- **Leader**: Commands the group
- **Flanker**: Attacks from sides
- **Support**: Provides backup
- **Sniper**: Long-range engagement
- **Rusher**: Close combat specialist

**Tactical Actions:**
- **Flank**: Move to target's side
- **Suppress**: Provide covering fire
- **Advance**: Move toward target
- **Retreat**: Fall back to safety
- **Surround**: Encircle target

**Configuration:**
```csharp
groupDetectionRadius = 25f;     // Group formation range
enableAutomaticCoordination = true;
enableFlankingBehavior = true;
enableSuppressiveFire = true;
shareTargetInformation = true;
```

### Environmental Awareness

**Purpose**: Smart interaction with environment objects.

**Interaction Types:**
- **Light Manipulation**: Turn lights on/off
- **Door Operation**: Open/close doors
- **Cover Usage**: Hide behind objects
- **Object Throwing**: Use items as weapons
- **High Ground**: Seek tactical advantage

**Configuration:**
```csharp
environmentScanRadius = 15f;    // Detection range
enableSmartInteraction = true;
preferLightManipulation = true;
preferCoverUsage = true;
useThrowableObjects = true;
```

### Sound Detection

**Purpose**: Detect and respond to audio sources.

**Detection Settings:**
```csharp
detectionRadius = 20f;          // Sound detection range
volumeThreshold = 0.5f;         // Minimum volume to detect
investigationTime = 5f;         // Investigation duration
```

**Sound Types Detected:**
- Footsteps
- Voices
- Object interactions
- Weapon sounds
- Environmental audio

---

## Profile System

### Master Profiles

**Purpose**: Reusable AI configurations with personality traits.

**Profile Components:**
- **Detection Profile**: Vision, hearing, awareness
- **Combat Profile**: Attacks, defenses, tactics
- **Movement Profile**: Speeds, formations, navigation
- **Personality**: Traits affecting behavior

### Using Existing Profiles

**Step 1: Load Profile Creator**
```
Tools > Modular AI > Profile Creator
```

**Step 2: Generate Profiles**
- Click profile type buttons (Warrior, Archer, etc.)
- Or click "Create All Profiles" for complete set
- Profiles saved to Assets/AI/Profiles/

**Step 3: Apply to AI**
```csharp
// Get profile integration component
var profileIntegration = ai.GetComponent<AIProfileIntegration>();

// Load and apply profile
var warriorProfile = Resources.Load<AIMasterProfile>("WarriorProfile");
profileIntegration.ApplyProfile(warriorProfile);
```

### Available Profile Types

**Combat Profiles:**
- **Warrior**: Balanced melee fighter
- **Archer**: Long-range tactical
- **Berserker**: Aggressive close-combat
- **Tank**: Heavy defensive unit
- **Assassin**: Stealth-based striker
- **Mage**: Magic-wielding support
- **Paladin**: Holy warrior with healing
- **Rogue**: Cunning opportunist

**Guard Profiles:**
- **City Guard**: Standard patrol unit
- **Palace Guard**: Elite security
- **Scout**: Fast reconnaissance
- **Sentry**: Stationary observer

**Animal Profiles:**
- **Wolf**: Pack hunting behavior
- **Bear**: Territorial aggressive
- **Eagle**: Aerial surveillance
- **Snake**: Ambush predator

**Monster Profiles:**
- **Orc**: Brutal warrior
- **Goblin**: Cunning group fighter
- **Troll**: Massive regenerating brute
- **Dragon**: Ultimate boss encounter

**Civilian Profiles:**
- **Villager**: Peaceful citizen
- **Merchant**: Trade-focused NPC
- **Noble**: High-class character
- **Child**: Vulnerable innocent

### Runtime Profile Switching

```csharp
// Temporary behavior change
profileIntegration.ApplyTemporaryModifier(berserkerProfile, 10f);

// Permanent switch
profileIntegration.SwitchProfile(stealthProfile);

// Conditional switching
if (healthSystem.HealthPercentage < 25f)
{
    profileIntegration.SwitchProfile(fleeProfile);
}
```

---

## Automation Tools

### Complete AI Setup

**Purpose**: One-click AI creation and configuration.

**Features:**
- Automatic component addition
- Type-based configuration
- Player creation
- NavMesh validation
- Component validation

**Usage:**
1. Open `Tools > Modular AI > Complete AI Setup`
2. Select or create AI GameObject
3. Choose AI type preset
4. Click "SETUP COMPLETE AI"
5. System handles everything automatically

### Auto Animator Setup

**Purpose**: Generate Animator Controllers with proper parameters.

**Features:**
- Automatic parameter creation
- Smart blend tree generation
- State transition setup
- Compatible with all AI systems

**Parameters Created:**
- Speed (Float): Movement speed
- HasTarget (Bool): Target detected
- IsChasing (Bool): Chasing target
- IsAttacking (Bool): Attacking target
- IsRunning (Bool): Running vs walking
- Attack (Trigger): Attack animation
- Alert (Trigger): Alert animation
- Health (Float): Current health
- IsFleeing (Bool): Flee state

**Usage:**
1. Open `Tools > Modular AI > Auto Animator Setup`
2. Configure controller name and path
3. Assign animation clips
4. Choose blend tree settings
5. Click "Create Animator Controller"
6. Apply to AI using "Create & Apply to AI"

---

## Customization Guide

### Creating Custom Behaviors

**Step 1: Create Behavior Class**
```csharp
public class CustomBehavior : AIBehavior
{
    public CustomBehavior(AICore core) : base(core) { }
    
    public override void Enter()
    {
        Debug.Log("Entering custom behavior");
        // Initialization logic
    }
    
    public override void Update()
    {
        // Behavior logic executed every frame
        if (SomeCondition())
        {
            aiCore.ChangeState(AIState.Idle);
        }
    }
    
    public override void Exit()
    {
        Debug.Log("Exiting custom behavior");
        // Cleanup logic
    }
}
```

**Step 2: Register Behavior**
```csharp
// In AICore or custom controller
if (!aiCore.behaviors.ContainsKey(AIState.Custom))
{
    aiCore.behaviors.Add(AIState.Custom, new CustomBehavior(aiCore));
}
```

### Custom Profile Creation

**Step 1: Create Master Profile**
```csharp
// Create ScriptableObject
AIMasterProfile customProfile = ScriptableObject.CreateInstance<AIMasterProfile>();
customProfile.profileName = "Custom AI";
customProfile.aiType = AIMasterProfile.AIType.Custom;

// Configure personality
customProfile.personality.courage = 0.8f;
customProfile.personality.intelligence = 0.9f;
customProfile.personality.aggression = 0.3f;

// Set health
customProfile.maxHealth = 150f;
```

**Step 2: Create Sub-Profiles**
```csharp
// Detection profile
AIDetectionProfile detection = ScriptableObject.CreateInstance<AIDetectionProfile>();
detection.detectionRadius = 15f;
detection.fieldOfView = 90f;
customProfile.detectionProfile = detection;

// Combat profile  
AICombatProfile combat = ScriptableObject.CreateInstance<AICombatProfile>();
combat.preferredFightingDistance = 3f;
combat.aggressiveness = 0.6f;
customProfile.combatProfile = combat;

// Movement profile
AIMovementProfile movement = ScriptableObject.CreateInstance<AIMovementProfile>();
movement.defaultSpeedName = "Walk";
customProfile.movementProfile = movement;
```

**Step 3: Save and Use**
```csharp
// Save as asset
AssetDatabase.CreateAsset(customProfile, "Assets/Profiles/CustomProfile.asset");

// Apply to AI
profileIntegration.ApplyProfile(customProfile);
```

### Custom Events Integration

**Step 1: Subscribe to Events**
```csharp
void Start()
{
    var aiCore = GetComponent<AICore>();
    var healthSystem = GetComponent<AIFleeHealthSystem>();
    
    // Subscribe to core events
    aiCore.OnTargetDetected += OnTargetSpotted;
    aiCore.OnStateChanged += OnStateChanged;
    
    // Subscribe to health events
    healthSystem.OnLowHealth.AddListener(OnHealthLow);
    healthSystem.OnFleeStart.AddListener(OnFleeStarted);
}

void OnTargetSpotted(Transform target)
{
    Debug.Log($"Target spotted: {target.name}");
    // Custom logic here
}

void OnStateChanged(AIState previous, AIState current)
{
    Debug.Log($"State changed: {previous} -> {current}");
    // Custom logic here
}
```

**Step 2: Create Custom Events**
```csharp
public class CustomAIEvents : MonoBehaviour
{
    [Header("Custom Events")]
    public UnityEvent OnCustomBehaviorTriggered;
    public UnityEvent<float> OnCustomValueChanged;
    
    public void TriggerCustomBehavior()
    {
        OnCustomBehaviorTriggered?.Invoke();
    }
}
```

### Extending Detection System

**Custom Detection Component:**
```csharp
public class CustomDetection : MonoBehaviour
{
    private AICore aiCore;
    
    void Start()
    {
        aiCore = GetComponent<AICore>();
    }
    
    void Update()
    {
        // Custom detection logic
        if (DetectSpecialCondition())
        {
            // Trigger AI response
            aiCore.target = detectedTarget;
            aiCore.ChangeState(AIState.Chase);
        }
    }
    
    bool DetectSpecialCondition()
    {
        // Your custom detection logic
        return false;
    }
}
```

---

## Troubleshooting

### Common Issues

**AI Not Moving**
```
Symptoms: AI stays in place, doesn't patrol or chase
Solutions:
1. Check NavMesh is baked: Window > AI > Navigation > Bake
2. Verify NavMeshAgent is enabled
3. Ensure AI has patrol points or waypoints
4. Check stoppingDistance isn't too large
5. Verify AI isn't in Alert state (blocks movement)
```

**AI Not Detecting Player**
```
Symptoms: AI ignores player completely
Solutions:
1. Check player has correct tag ("Player")
2. Verify player is on correct layer
3. Check targetLayer mask in AICore settings
4. Ensure detection radius is large enough
5. Verify field of view includes player
6. Check for obstacles blocking line of sight
```

**Animations Not Working**
```
Symptoms: AI moves but animations don't play
Solutions:
1. Ensure Animator component is assigned
2. Check animation parameters exist in controller
3. Verify parameter names match AIAnimationSystem settings
4. Use Auto Animator Setup for automatic configuration
5. Check animation clips are assigned
```

**Audio Not Playing**
```
Symptoms: No sounds from AI
Solutions:
1. Check AudioSource component exists
2. Verify audio clips are assigned
3. Check audio volume settings
4. Ensure AudioListener exists in scene
5. Verify audio import settings
```

**Performance Issues**
```
Symptoms: Low framerate with many AIs
Solutions:
1. Enable LOD system in AIManager
2. Reduce detection radius for distant AIs
3. Increase update intervals
4. Use culling distance settings
5. Disable complex systems for background AIs
```

**Profile System Not Working**
```
Symptoms: Profiles don't affect AI behavior
Solutions:
1. Ensure AIProfileIntegration component exists
2. Check profile is assigned correctly
3. Verify useProfileSystem is enabled
4. Check for override conflicts
5. Use RefreshFromProfile() to force update
```

### Debug Tools

**AIDebugHelper**
```csharp
// Add to AI GameObject for debugging
var debugHelper = ai.AddComponent<AIDebugHelper>();

// Use context menu commands:
// - Force Target Player
// - Force Attack State  
// - Test Attack
```

**Console Debugging**
```csharp
// Enable debug logging
AICore.isDebug = true;

// Manual state checking
Debug.Log($"AI State: {aiCore.CurrentState}");
Debug.Log($"Has Target: {aiCore.HasTarget}");
Debug.Log($"Distance: {aiCore.DistanceToTarget}");
```

**Visual Debugging**
```csharp
// Enable gizmos in Scene view
void OnDrawGizmosSelected()
{
    // Detection radius
    Gizmos.color = Color.yellow;
    Gizmos.DrawWireSphere(transform.position, detectionRadius);
    
    // Attack range
    Gizmos.color = Color.red;
    Gizmos.DrawWireSphere(transform.position, attackRange);
}
```

---

## Performance Optimization

### AI Manager Setup

**Configure Global Settings:**
```csharp
// Set maximum active AIs
AIManager.Instance.maxActiveAIs = 10;

// Set culling distance
AIManager.Instance.cullingDistance = 50f;

// Set update interval
AIManager.Instance.updateInterval = 0.1f;
```

### LOD Implementation

**Distance-Based Optimization:**
```csharp
void Update()
{
    float distanceToPlayer = Vector3.Distance(transform.position, player.position);
    
    if (distanceToPlayer > 50f)
    {
        // Disable complex systems
        memorySystem.enabled = false;
        environmentalAwareness.enabled = false;
        
        // Reduce update frequency
        if (Time.time % 1f < 0.1f) // 10% update rate
        {
            // Basic updates only
        }
    }
    else
    {
        // Enable all systems
        memorySystem.enabled = true;
        environmentalAwareness.enabled = true;
    }
}
```

### Memory Management

**Efficient Memory Usage:**
```csharp
// Limit memory entries
memorySystem.settings.maxMemoryEntries = 10;

// Reduce memory duration
memorySystem.settings.maxMemoryDuration = 30f;

// Clean expired memories regularly
void Update()
{
    if (Time.time % 5f < Time.deltaTime) // Every 5 seconds
    {
        memorySystem.CleanExpiredMemories();
    }
}
```

### Batch Operations

**Group Processing:**
```csharp
// Process AIs in batches
void ProcessAIBatch(List<AICore> ais)
{
    foreach (var ai in ais)
    {
        if (ai.HasTarget)
        {
            // Batch target sharing
            foreach (var otherAI in ais)
            {
                if (otherAI != ai && !otherAI.HasTarget)
                {
                    otherAI.target = ai.target;
                }
            }
            break; // Only need one target source
        }
    }
}
```

---

## Best Practices

### Code Organization

**Component Structure:**
```csharp
public class MyAIController : MonoBehaviour
{
    [Header("Component References")]
    private AICore aiCore;
    private AIFleeHealthSystem healthSystem;
    private AIMemorySystem memorySystem;
    
    [Header("Configuration")]
    [SerializeField] private float customValue = 1f;
    
    void Awake()
    {
        // Get components in Awake
        aiCore = GetComponent<AICore>();
        healthSystem = GetComponent<AIFleeHealthSystem>();
        memorySystem = GetComponent<AIMemorySystem>();
    }
    
    void Start()
    {
        // Subscribe to events in Start
        aiCore.OnTargetDetected += HandleTargetDetected;
        healthSystem.OnLowHealth.AddListener(HandleLowHealth);
    }
    
    void OnDestroy()
    {
        // Always unsubscribe
        if (aiCore != null)
            aiCore.OnTargetDetected -= HandleTargetDetected;
    }
}
```

### Event Management

**Safe Event Handling:**
```csharp
// Check for null before unsubscribing
void OnDestroy()
{
    if (aiCore != null)
    {
        aiCore.OnTargetDetected -= HandleTargetDetected;
        aiCore.OnStateChanged -= HandleStateChanged;
    }
    
    // For UnityEvents, just remove listener
    healthSystem?.OnLowHealth.RemoveListener(HandleLowHealth);
}
```

### Error Prevention

**Null Checks:**
```csharp
// Always verify components exist
if (aiCore != null && aiCore.HasTarget)
{
    float distance = aiCore.DistanceToTarget;
    if (distance <= attackRange)
    {
        aiCore.Attack();
    }
}
```

**Validation Methods:**
```csharp
[ContextMenu("Validate Setup")]
void ValidateSetup()
{
    var issues = new List<string>();
    
    if (GetComponent<AICore>() == null)
        issues.Add("AICore missing");
        
    if (GetComponent<NavMeshAgent>() == null)
        issues.Add("NavMeshAgent missing");
        
    if (GameObject.FindGameObjectWithTag("Player") == null)
        issues.Add("No Player found");
    
    if (issues.Count > 0)
    {
        Debug.LogWarning($"Issues found: {string.Join(", ", issues)}");
    }
    else
    {
        Debug.Log("Setup validation passed!");
    }
}
```

### Scene Setup

**Proper Layer Configuration:**
```
0. Default (Environment)
1. TransparentFX
2. Ignore Raycast  
3. Player (Player objects)
4. Water
5. UI
6. AI (AI objects)
7. Ground (NavMesh surfaces)
8. Obstacles (Vision blocking)
```

**NavMesh Setup:**
```
1. Mark all ground/floor objects as "Navigation Static"
2. Window > AI > Navigation
3. Bake tab > Bake
4. Verify blue NavMesh areas appear on walkable surfaces
```

**Audio Setup:**
```
1. Ensure AudioListener exists on Camera
2. Set 3D sound settings for spatial audio
3. Configure audio rolloff for distance attenuation
4. Use audio groups for volume control
```

### Testing Workflow

**Development Testing:**
```csharp
// Use debug helpers for quick testing
[ContextMenu("Quick Test Setup")]
void QuickTestSetup()
{
    // Force AI to detect player
    aiCore.target = GameObject.FindGameObjectWithTag("Player")?.transform;
    aiCore.ChangeState(AIState.Chase);
    
    // Set low health for flee testing
    healthSystem.SetHealth(20f);
    
    // Add test memory
    memorySystem.AddMemory(transform.position + Vector3.forward * 5f, 
                          MemoryType.Sound, 0.8f);
}
```

**Build Testing:**
```
1. Test with various AI counts (1, 5, 10, 20+)
2. Verify performance on target platform
3. Test all AI types and behaviors
4. Validate multiplayer scenarios if applicable
5. Check memory usage over time
```

---

This comprehensive manual covers everything developers need to successfully implement and customize the Modular AI Kit. From basic setup to advanced customization, it provides clear guidance for users of all skill levels.

# GASDocumentation
My understanding of Unreal Engine 5's GameplayAbilitySystem plugin (GAS) with a simple multiplayer sample project. This is not official documentation and neither this project nor myself are affiliated with Epic Games. I make no guarantee for the accuracy of this information.

The goal of this documentation is to explain the major concepts and classes in GAS and provide some additional commentary based on my experience with it. There is a lot of 'tribal knowledge' of GAS among users in the community and I aim to share all of mine here.

The Sample Project and documentation are current with **Unreal Engine 5.3** (UE5). There are branches of this documentation for older versions of Unreal Engine, but they are no longer supported and are liable to have bugs or out of date information. Please use the branch that matches your engine version.

[GASShooter](https://github.com/tranek/GASShooter) is a sister Sample Project demonstrating advanced techniques with GAS for a multiplayer FPS/TPS.

The best documentation will always be the plugin source code.

<a name="table-of-contents"></a>
## Table of Contents

> 1. [Intro to the GameplayAbilitySystem Plugin](#intro)
> 1. [Sample Project](#sp)
> 1. [Setting Up a Project Using GAS](#setup)
> 1. [Concepts](#concepts)  
>    4.1 [Ability System Component](#concepts-asc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.1.1 [Replication Mode](#concepts-asc-rm)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.1.2 [Setup and Initialization](#concepts-asc-setup)  
>    4.2 [Gameplay Tags](#concepts-gt)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.2.1 [Responding to Changes in Gameplay Tags](#concepts-gt-change)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.2.2 [Loading Gameplay Tags from Plugin .ini Files](#concepts-gt-loadfromplugin)  
>    4.3 [Attributes](#concepts-a)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.1 [Attribute Definition](#concepts-a-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.2 [BaseValue vs CurrentValue](#concepts-a-value)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.3 [Meta Attributes](#concepts-a-meta)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.4 [Responding to Attribute Changes](#concepts-a-changes)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.5 [Derived Attributes](#concepts-a-derived)  
>    4.4 [Attribute Set](#concepts-as)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.1 [Attribute Set Definition](#concepts-as-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2 [Attribute Set Design](#concepts-as-design)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.1 [Subcomponents with Individual Attributes](#concepts-as-design-subcomponents)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.2 [Adding and Removing AttributeSets at Runtime](#concepts-as-design-addremoveruntime)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3 [Item Attributes (Weapon Ammo)](#concepts-as-design-itemattributes)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3.1 [Plain Floats on the Item](#concepts-as-design-itemattributes-plainfloats)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3.2 [`AttributeSet` on the Item](#concepts-as-design-itemattributes-attributeset)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3.3 [`ASC` on the Item](#concepts-as-design-itemattributes-asc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.3 [Defining Attributes](#concepts-as-attributes)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.4 [Initializing Attributes](#concepts-as-init)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.5 [PreAttributeChange()](#concepts-as-preattributechange)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.6 [PostGameplayEffectExecute()](#concepts-as-postgameplayeffectexecute)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.7 [OnAttributeAggregatorCreated()](#concepts-as-onattributeaggregatorcreated)  
>    4.5 [Gameplay Effects](#concepts-ge)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.1 [Gameplay Effect Definition](#concepts-ge-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.2 [Applying Gameplay Effects](#concepts-ge-applying)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.3 [Removing Gameplay Effects](#concepts-ga-removing)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.4 [Gameplay Effect Modifiers](#concepts-ge-mods)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.4.1 [Multiply and Divide Modifiers](#concepts-ge-mods-multiplydivide)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.4.2 [Gameplay Tags on Modifiers](#concepts-ge-mods-gameplaytags)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.5 [Stacking Gameplay Effects](#concepts-ge-stacking)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.6 [Granted Abilities](#concepts-ge-ga)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.7 [Gameplay Effect Tags](#concepts-ge-tags)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.8 [Immunity](#concepts-ge-immunity)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.9 [Gameplay Effect Spec](#concepts-ge-spec)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.9.1 [SetByCallers](#concepts-ge-spec-setbycaller)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.10 [Gameplay Effect Context](#concepts-ge-context)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.11 [Modifier Magnitude Calculation](#concepts-ge-mmc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12 [Gameplay Effect Execution Calculation](#concepts-ge-ec)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1 [Sending Data to Execution Calculations](#concepts-ge-ec-senddata)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.1 [SetByCaller](#concepts-ge-ec-senddata-setbycaller)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.2 [Backing Data Attribute Calculation Modifier](#concepts-ge-ec-senddata-backingdataattribute)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.3 [Backing Data Temporary Variable Calculation Modifier](#concepts-ge-ec-senddata-backingdatatempvariable)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.4 [Gameplay Effect Context](#concepts-ge-ec-senddata-effectcontext)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.13 [Custom Application Requirement](#concepts-ge-car)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.14 [Cost Gameplay Effect](#concepts-ge-cost)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15 [Cooldown Gameplay Effect](#concepts-ge-cooldown)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15.1 [Get the Cooldown Gameplay Effect's Remaining Time](#concepts-ge-cooldown-tr)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15.2 [Listening for Cooldown Begin and End](#concepts-ge-cooldown-listen)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15.3 [Predicting Cooldowns](#concepts-ge-cooldown-prediction)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.16 [Changing Active Gameplay Effect Duration](#concepts-ge-duration)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.17 [Creating Dynamic Gameplay Effects at Runtime](#concepts-ge-dynamic)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.18 [Gameplay Effect Containers](#concepts-ge-containers)  
>    4.6 [Gameplay Abilities](#concepts-ga)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1 [Gameplay Ability Definition](#concepts-ga-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1.1 [Replication Policy](#concepts-ga-definition-reppolicy)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1.2 [Server Respects Remote Ability Cancellation](#concepts-ga-definition-remotecancel)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1.3 [Replicate Input Directly](#concepts-ga-definition-repinputdirectly)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.2 [Binding Input to the ASC](#concepts-ga-input)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.2.1 [Binding to Input without Activating Abilities](#concepts-ga-input-noactivate)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.3 [Granting Abilities](#concepts-ga-granting)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.4 [Activating Abilities](#concepts-ga-activating)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.4.1 [Passive Abilities](#concepts-ga-activating-passive)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.4.2 [Activation Failed Tags](#concepts-ga-activating-failedtags)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.5 [Canceling Abilities](#concepts-ga-cancelabilities)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.6 [Getting Active Abilities](#concepts-ga-definition-activeability)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.7 [Instancing Policy](#concepts-ga-instancing)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.8 [Net Execution Policy](#concepts-ga-net)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.9 [Ability Tags](#concepts-ga-tags)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.10 [Gameplay Ability Spec](#concepts-ga-spec)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.11 [Passing Data to Abilities](#concepts-ga-data)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.12 [Ability Cost and Cooldown](#concepts-ga-commit)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.13 [Leveling Up Abilities](#concepts-ga-leveling)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.14 [Ability Sets](#concepts-ga-sets)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.15 [Ability Batching](#concepts-ga-batching)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.16 [Net Security Policy](#concepts-ga-netsecuritypolicy)  
>    4.7 [Ability Tasks](#concepts-at)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.1 [Ability Task Definition](#concepts-at-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.2 [Custom Ability Tasks](#concepts-at-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.3 [Using Ability Tasks](#concepts-at-using)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.4 [Root Motion Source Ability Tasks](#concepts-at-rms)  
>    4.8 [Gameplay Cues](#concepts-gc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.1 [Gameplay Cue Definition](#concepts-gc-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.2 [Triggering Gameplay Cues](#concepts-gc-trigger)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.3 [Local Gameplay Cues](#concepts-gc-local)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.4 [Gameplay Cue Parameters](#concepts-gc-parameters)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.5 [Gameplay Cue Manager](#concepts-gc-manager)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.6 [Prevent Gameplay Cues from Firing](#concepts-gc-prevention)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.7 [Gameplay Cue Batching](#concepts-gc-batching)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.7.1 [Manual RPC](#concepts-gc-batching-manualrpc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.7.2 [Multiple GCs on one GE](#concepts-gc-batching-gcsonge)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.8 [Gameplay Cue Events](#concepts-gc-events)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.9 [Gameplay Cue Reliability](#concepts-gc-reliability)  
>    4.9 [Ability System Globals](#concepts-asg)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.9.1 [InitGlobalData()](#concepts-asg-initglobaldata)  
>    4.10 [Prediction](#concepts-p)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.1 [Prediction Key](#concepts-p-key)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.2 [Creating New Prediction Windows in Abilities](#concepts-p-windows)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.3 [Predictively Spawning Actors](#concepts-p-spawn)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.4 [Future of Prediction in GAS](#concepts-p-future)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.5 [Network Prediction Plugin](#concepts-p-npp)  
>    4.11 [Targeting](#concepts-targeting)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.1 [Target Data](#concepts-targeting-data)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.2 [Target Actors](#concepts-targeting-actors)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.3 [Target Data Filters](#concepts-target-data-filters)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.4 [Gameplay Ability World Reticles](#concepts-targeting-reticles)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.5 [Gameplay Effect Containers Targeting](#concepts-targeting-containers)  
> 1. [Commonly Implemented Abilities and Effects](#cae)  
>    5.1 [Stun](#cae-stun)  
>    5.2 [Sprint](#cae-sprint)  
>    5.3 [Aim Down Sights](#cae-ads)  
>    5.4 [Lifesteal](#cae-ls)  
>    5.5 [Generating a Random Number on Client and Server](#cae-random)  
>    5.6 [Critical Hits](#cae-crit)  
>    5.7 [Non-Stacking Gameplay Effects but Only the Greatest Magnitude Actually Affects the Target](#cae-nonstackingge)  
>    5.8 [Generate Target Data While Game is Paused](#cae-paused)  
>    5.9 [One Button Interaction System](#cae-onebuttoninteractionsystem)  
> 1. [Debugging GAS](#debugging)  
>    6.1 [showdebug abilitysystem](#debugging-sd)  
>    6.2 [Gameplay Debugger](#debugging-gd)  
>    6.3 [GAS Logging](#debugging-log)  
> 1. [Optimizations](#optimizations)  
>    7.1 [Ability Batching](#optimizations-abilitybatching)  
>    7.2 [Gameplay Cue Batching](#optimizations-gameplaycuebatching)  
>    7.3 [AbilitySystemComponent Replication Mode](#optimizations-ascreplicationmode)  
>    7.4 [Attribute Proxy Replication](#optimizations-attributeproxyreplication)  
>    7.5 [ASC Lazy Loading](#optimizations-asclazyloading)  
> 1. [Quality of Life Suggestions](#qol)  
>    8.1 [Gameplay Effect Containers](#qol-gameplayeffectcontainers)  
>    8.2 [Blueprint AsyncTasks to Bind to ASC Delegates](#qol-asynctasksascdelegates)  
> 1. [Troubleshooting](#troubleshooting)  
>    9.1 [`LogAbilitySystem: Warning: Can't activate LocalOnly or LocalPredicted ability %s when not local!`](#troubleshooting-notlocal)  
>    9.2 [`ScriptStructCache` errors](#troubleshooting-scriptstructcache)  
>    9.3 [Animation Montages are not replicating to clients](#troubleshooting-replicatinganimmontages)  
>    9.4 [Duplicating Blueprint Actors is setting AttributeSets to nullptr](#troubleshooting-duplicatingblueprintactors)  
>    9.5 [unresolved external symbol UEPushModelPrivate::MarkPropertyDirty(int,int)](#troubleshooting-unresolvedexternalsymbolmarkpropertydirty)  
>    9.6 [Enum names are now represented by path name](#troubleshooting-enumnamesarenowpathnames)  
> 1. [Common GAS Acronyms](#acronyms)
> 1. [Other Resources](#resources)  
>    11.1 [Q&A With Epic Game's Dave Ratti](#resources-daveratti)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;11.1.1 [Community Questions 1](#resources-daveratti-community1)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;11.1.2 [Community Questions 2](#resources-daveratti-community2)  
> 1. [GAS Changelog](#changelog)  
>    * [5.3](#changelog-5.3)  
>    * [5.2](#changelog-5.2)  
>    * [5.1](#changelog-5.1)  
>    * [5.0](#changelog-5.0)  
>    * [4.27](#changelog-4.27)  
>    * [4.26](#changelog-4.26)  
>    * [4.25.1](#changelog-4.25.1)  
>    * [4.25](#changelog-4.25)  
>    * [4.24](#changelog-4.24)
         
<a name="intro"></a>
## 1. Intro to the GameplayAbilitySystem Plugin
From the [Official Documentation](https://docs.unrealengine.com/en-US/Gameplay/GameplayAbilitySystem/index.html):
>The Gameplay Ability System is a highly-flexible framework for building abilities and attributes of the type you might find in an RPG or MOBA title. You can build actions or passive abilities for the characters in your games to use, status effects that can build up or wear down various attributes as a result of these actions, implement "cooldown" timers or resource costs to regulate the usage of these actions, change the level of the ability and its effects at each level, activate particle or sound effects, and more. Put simply, this system can help you to design, implement, and efficiently network in-game abilities as simple as jumping or as complex as your favorite character's ability set in any modern RPG or MOBA title.

The GameplayAbilitySystem plugin is developed by Epic Games and comes with Unreal Engine. It has been battle tested in AAA commercial games such as Paragon and Fortnite among others.

The plugin provides an out-of-the-box solution in single and multiplayer games for:
* Implementing level-based character abilities or skills with optional costs and cooldowns ([GameplayAbilities](#concepts-ga))
* Manipulating numerical `Attributes` belonging to actors ([Attributes](#concepts-a))
* Applying status effects to actors ([GameplayEffects](#concepts-ge))
* Applying `GameplayTags` to actors ([GameplayTags](#concepts-gt))
* Spawning visual or sound effects ([GameplayCues](#concepts-gc))
* Replication of everything mentioned above

In multiplayer games, GAS provides support for [client-side prediction](#concepts-p) of:
* Ability activation
* Playing animation montages
* Changes to `Attributes`
* Applying `GameplayTags`
* Spawning `GameplayCues`
* Movement via `RootMotionSource` functions connected to the `CharacterMovementComponent`.

**GAS must be set up in C++**, but `GameplayAbilities` and `GameplayEffects` can be created in Blueprint by the designers.

Current issues with GAS:
* `GameplayEffect` latency reconciliation (can't predict ability cooldowns resulting in players with higher latencies having lower rate of fire for low cooldown abilities compared to players with lower latencies).
* Cannot predict the removal of `GameplayEffects`. We can however predict adding `GameplayEffects` with the inverse effects, effectively removing them. This is not always appropriate or feasible and still remains an issue.
* Lack of boilerplate templates, multiplayer examples, and documentation. Hopefully this helps with that!

**[⬆ Back to Top](#table-of-contents)**

<a name="sp"></a>
## 2. Sample Project
A multiplayer third person shooter sample project is included with this documentation aimed at people new to the GameplayAbilitySystem Plugin but not new to Unreal Engine. Users are expected to know C++, Blueprints, UMG, Replication, and other intermediate topics in UE. This project provides an example of how to set up a basic third person shooter multiplayer-ready project with the `AbilitySystemComponent` (`ASC`) on the `PlayerState` class for player/AI controlled heroes and the `ASC` on the `Character` class for AI controlled minions.

The goal is to keep this project simple while showing the GAS basics and demonstrating some commonly requested abilities with well-commented code. Because of its beginner focus, the project does not show advanced topics like [predicting projectiles](#concepts-p-spawn).

Concepts demonstrated:
* `ASC` on `PlayerState` vs `Character`
* Replicated `Attributes`
* Replicated animation montages
* `GameplayTags`
* Applying and removing `GameplayEffects` inside of and externally from `GameplayAbilities`
* Applying damage mitigated by armor to change health of a character
* `GameplayEffectExecutionCalculations`
* Stun effect
* Death and respawn
* Spawning actors (projectiles) from an ability on the server
* Predictively changing the local player's speed with aim down sights and sprinting
* Constantly draining stamina to sprint
* Using mana to cast abilities
* Passive abilities
* Stacking `GameplayEffects`
* Targeting actors
* `GameplayAbilities` created in Blueprint
* `GameplayAbilities` created in C++
* Instanced per `Actor` `GameplayAbilities`
* Non-Instanced `GameplayAbilities` (Jump)
* Static `GameplayCues` (FireGun projectile impact particle effect)
* Actor `GameplayCues` (Sprint and Stun particle effects)

The hero class has the following abilities:

| Ability                    | Input Bind          | Predicted  | C++ / Blueprint | Description                                                                                                                                                                  |
| -------------------------- | ------------------- | ---------- | --------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Jump                       | Space Bar           | Yes        | C++             | Makes the hero jump.                                                                                                                                                         |
| Gun                        | Left Mouse Button   | No         | C++             | Fires a projectile from the hero's gun. The animation is predicted but the projectile is not.                                                                                |
| Aim Down Sights            | Right Mouse Button  | Yes        | Blueprint       | While the button is held, the hero will walk slower and the camera will zoom in to allow more precise shots with the gun.                                                    |
| Sprint                     | Left Shift          | Yes        | Blueprint       | While the button is held, the hero will run faster draining stamina.                                                                                                         |
| Forward Dash               | Q                   | Yes        | Blueprint       | The hero dashes forward at the cost of stamina.                                                                                                                              |
| Passive Armor Stacks       | Passive             | No         | Blueprint       | Every 4 seconds the hero gains a stack of armor up to a maximum of 4 stacks. Receiving damage removes one stack of armor.                                                    |
| Meteor                     | R                   | No         | Blueprint       | Player targets a location to drop a meteor on the enemies causing damage and stunning them. The targeting is predicted while spawning the meteor is not.                     |

It does not matter if `GameplayAbilities` are created in C++ or Blueprint. A mixture of the two were used here for example of how to do them in each language.

Minions do not come with any predefined `GameplayAbilities`. The Red Minions have more health regen while the Blue Minions have higher starting health.

For `GameplayAbility` naming, I used the suffix `_BP` to denote the `GameplayAbility's` logic was created in Blueprint. The lack of suffix means the logic was created in C++.

**Blueprint Asset Naming Prefixes**

| Prefix      | Asset Type          |
| ----------- | ------------------- |
| GA_         | GameplayAbility     |
| GC_         | GameplayCue         |
| GE_         | GameplayEffect      |

**[⬆ Back to Top](#table-of-contents)**

<a name="setup"></a>
## 3. Setting Up a Project Using GAS
Basic steps to set up a project using GAS:
1. Enable GameplayAbilitySystem plugin in the Editor
1. Edit `YourProjectName.Build.cs` to add `"GameplayAbilities", "GameplayTags", "GameplayTasks"` to your `PrivateDependencyModuleNames`
1. Refresh/Regenerate your Visual Studio project files
1. Starting with 4.24, it is now mandatory to call `UAbilitySystemGlobals::Get().InitGlobalData()` to use [`TargetData`](#concepts-targeting-data). The Sample Project does this in `UAssetManager::StartInitialLoading()`. See [`InitGlobalData()`](#concepts-asg-initglobaldata) for more information.

That's all that you have to do to enable GAS. From here, add an [`ASC`](#concepts-asc) and [`AttributeSet`](#concepts-as) to your `Character` or `PlayerState` and start making [`GameplayAbilities`](#concepts-ga) and [`GameplayEffects`](#concepts-ge)!

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts"></a>
## 4. GAS Concepts

#### Sections

> 4.1 [Ability System Component](#concepts-asc)  
> 4.2 [Gameplay Tags](#concepts-gt)  
> 4.3 [Attributes](#concepts-a)  
> 4.4 [Attribute Set](#concepts-as)  
> 4.5 [Gameplay Effects](#concepts-ge)  
> 4.6 [Gameplay Abilities](#concepts-ga)  
> 4.7 [Ability Tasks](#concepts-at)  
> 4.8 [Gameplay Cues](#concepts-gc)  
> 4.9 [Ability System Globals](#concepts-asg)  
> 4.10 [Prediction](#concepts-p)

<a name="concepts-asc"></a>
### 4.1 Ability System Component
The `AbilitySystemComponent` (`ASC`) is the heart of GAS. It's a `UActorComponent` ([`UAbilitySystemComponent`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UAbilitySystemComponent/index.html)) that handles all interactions with the system. Any `Actor` that wishes to use [`GameplayAbilities`](#concepts-ga), have [`Attributes`](#concepts-a), or receive [`GameplayEffects`](#concepts-ge) must have one `ASC` attached to them. These objects all live inside of and are managed and replicated by (with the exception of `Attributes` which are replicated by their [`AttributeSet`](#concepts-as)) the `ASC`. Developers are expected but not required to subclass this.

The `Actor` with the `ASC` attached to it is referred to as the `OwnerActor` of the `ASC`. The physical representation `Actor` of the `ASC` is called the `AvatarActor`. The `OwnerActor` and the `AvatarActor` can be the same `Actor` as in the case of a simple AI minion in a MOBA game. They can also be different `Actors` as in the case of a player controlled hero in a MOBA game where the `OwnerActor` is the `PlayerState` and the `AvatarActor` is the hero's `Character` class. Most `Actors` will have the `ASC` on themselves. If your `Actor` will respawn and need persistence of `Attributes` or `GameplayEffects` between spawns (like a hero in a MOBA), then the ideal location for the `ASC` is on the `PlayerState`.

**Note:** If your `ASC` is on your `PlayerState`, then you will need to increase the `NetUpdateFrequency` of your `PlayerState`. It defaults to a very low value on the `PlayerState` and can cause delays or perceived lag before changes to things like `Attributes` and `GameplayTags` happen on the clients. Be sure to enable [`Adaptive Network Update Frequency`](https://docs.unrealengine.com/en-US/Gameplay/Networking/Actors/Properties/index.html#adaptivenetworkupdatefrequency), Fortnite uses it.

Both, the `OwnerActor` and the `AvatarActor` if different `Actors`, should implement the `IAbilitySystemInterface`. This interface has one function that must be overriden, `UAbilitySystemComponent* GetAbilitySystemComponent() const`, which returns a pointer to its `ASC`. `ASCs` interact with each other internally to the system by looking for this interface function.

The `ASC` holds its current active `GameplayEffects` in `FActiveGameplayEffectsContainer ActiveGameplayEffects`.

The `ASC` holds its granted `Gameplay Abilities` in `FGameplayAbilitySpecContainer ActivatableAbilities`. Any time that you plan to iterate over `ActivatableAbilities.Items`, be sure to add `ABILITYLIST_SCOPE_LOCK();` above your loop to lock the list from changing (due to removing an ability). Every `ABILITYLIST_SCOPE_LOCK();` in scope increments `AbilityScopeLockCount` and then decrements when it falls out of scope. Do not try to remove an ability inside the scope of `ABILITYLIST_SCOPE_LOCK();` (the clear ability functions check `AbilityScopeLockCount` internally to prevent removing abilities if the list is locked).

<a name="concepts-asc-rm"></a>
### 4.1.1 Replication Mode
The `ASC` defines three different replication modes for replicating `GameplayEffects`, `GameplayTags`, and `GameplayCues` - `Full`, `Mixed`, and `Minimal`. `Attributes` are replicated by their `AttributeSet`.

| Replication Mode   | When to Use                             | Description                                                                                                                    |
| ------------------ | --------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| `Full`             | Single Player                           | Every `GameplayEffect` is replicated to every client.                                                                          |
| `Mixed`            | Multiplayer, player controlled `Actors` | `GameplayEffects` are only replicated to the owning client. Only `GameplayTags` and `GameplayCues` are replicated to everyone. |
| `Minimal`          | Multiplayer, AI controlled `Actors`     | `GameplayEffects` are never replicated to anyone. Only `GameplayTags` and `GameplayCues` are replicated to everyone.           |

**Note:** `Mixed` replication mode expects the `OwnerActor's` `Owner` to be the `Controller`. `PlayerState's` `Owner` is the `Controller` by default but `Character's` is not. If using `Mixed` replication mode with the `OwnerActor` not the `PlayerState`, then you need to call `SetOwner()` on the `OwnerActor` with a valid `Controller`.

Starting with 4.24, `PossessedBy()` now sets the owner of the `Pawn` to the new `Controller`.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-asc-setup"></a>
### 4.1.2 Setup and Initialization
`ASCs` are typically constructed in the `OwnerActor's` constructor and explicitly marked replicated. **This must be done in C++**.

```c++
AGDPlayerState::AGDPlayerState()
{
	// Create ability system component, and set it to be explicitly replicated
	AbilitySystemComponent = CreateDefaultSubobject<UGDAbilitySystemComponent>(TEXT("AbilitySystemComponent"));
	AbilitySystemComponent->SetIsReplicated(true);
	//...
}
```

The `ASC` needs to be initialized with its `OwnerActor` and `AvatarActor` on both the server and the client. You want to initialize after the `Pawn's` `Controller` has been set (after possession). Single player games only need to worry about the server path.

For player controlled characters where the `ASC` lives on the `Pawn`, I typically initialize on the server in the `Pawn's` `PossessedBy()` function and initialize on the client in the `PlayerController's` `AcknowledgePossession()` function.

```c++
void APACharacterBase::PossessedBy(AController * NewController)
{
	Super::PossessedBy(NewController);

	if (AbilitySystemComponent)
	{
		AbilitySystemComponent->InitAbilityActorInfo(this, this);
	}

	// ASC MixedMode replication requires that the ASC Owner's Owner be the Controller.
	SetOwner(NewController);
}
```

```c++
void APAPlayerControllerBase::AcknowledgePossession(APawn* P)
{
	Super::AcknowledgePossession(P);

	APACharacterBase* CharacterBase = Cast<APACharacterBase>(P);
	if (CharacterBase)
	{
		CharacterBase->GetAbilitySystemComponent()->InitAbilityActorInfo(CharacterBase, CharacterBase);
	}

	//...
}
```

For player controlled characters where the `ASC` lives on the `PlayerState`, I typically initialize the server in the `Pawn's` `PossessedBy()` function and initialize on the client in the `Pawn's` `OnRep_PlayerState()` function. This ensures that the `PlayerState` exists on the client.

```c++
// Server only
void AGDHeroCharacter::PossessedBy(AController * NewController)
{
	Super::PossessedBy(NewController);

	AGDPlayerState* PS = GetPlayerState<AGDPlayerState>();
	if (PS)
	{
		// Set the ASC on the Server. Clients do this in OnRep_PlayerState()
		AbilitySystemComponent = Cast<UGDAbilitySystemComponent>(PS->GetAbilitySystemComponent());

		// AI won't have PlayerControllers so we can init again here just to be sure. No harm in initing twice for heroes that have PlayerControllers.
		PS->GetAbilitySystemComponent()->InitAbilityActorInfo(PS, this);
	}
	
	//...
}
```

```c++
// Client only
void AGDHeroCharacter::OnRep_PlayerState()
{
	Super::OnRep_PlayerState();

	AGDPlayerState* PS = GetPlayerState<AGDPlayerState>();
	if (PS)
	{
		// Set the ASC for clients. Server does this in PossessedBy.
		AbilitySystemComponent = Cast<UGDAbilitySystemComponent>(PS->GetAbilitySystemComponent());

		// Init ASC Actor Info for clients. Server will init its ASC when it possesses a new Actor.
		AbilitySystemComponent->InitAbilityActorInfo(PS, this);
	}

	// ...
}
```

If you get the error message `LogAbilitySystem: Warning: Can't activate LocalOnly or LocalPredicted ability %s when not local!` then you did not initialize your `ASC` on the client.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gt"></a>
### 4.2 Gameplay Tags
[`FGameplayTags`](https://docs.unrealengine.com/en-US/API/Runtime/GameplayTags/FGameplayTag/index.html) are hierarchical names in the form of `Parent.Child.Grandchild...` that are registered with the `GameplayTagManager`. These tags are incredibly useful for classifying and describing the state of an object. For example, if a character is stunned, we could give it a `State.Debuff.Stun` `GameplayTag` for the duration of the stun.

You will find yourself replacing things that you used to handle with booleans or enums with `GameplayTags` and doing boolean logic on whether or not objects have certain `GameplayTags`.

When giving tags to an object, we typically add them to its `ASC` if it has one so that GAS can interact with them. `UAbilitySystemComponent` implements the `IGameplayTagAssetInterface` giving functions to access its owned `GameplayTags`.

Multiple `GameplayTags` can be stored in an `FGameplayTagContainer`. It is preferable to use a `GameplayTagContainer` over a `TArray<FGameplayTag>` since the `GameplayTagContainers` add some efficiency magic. While tags are standard `FNames`, they can be efficiently packed together in `FGameplayTagContainers` for replication if `Fast Replication` is enabled in the project settings. `Fast Replication` requires that the server and the clients have the same list of `GameplayTags`. This generally shouldn't be a problem so you should enable this option. `GameplayTagContainers` can also return a `TArray<FGameplayTag>` for iteration.

`GameplayTags` stored in `FGameplayTagCountContainer` have a `TagMap` that stores the number of instances of that `GameplayTag`. A `FGameplayTagCountContainer` may still have the `GameplayTag` in it but its `TagMapCount` is zero. You may encounter this while debugging if an `ASC` still has a `GameplayTag`. Any of the `HasTag()` or `HasMatchingTag()` or similar functions will check the `TagMapCount` and return false if the `GameplayTag` is not present or its `TagMapCount` is zero.

`GameplayTags` must be defined ahead of time in the `DefaultGameplayTags.ini`. The Unreal Engine Editor provides an interface in the project settings to let developers manage `GameplayTags` without needing to manually edit the `DefaultGameplayTags.ini`. The `GameplayTag` editor can create, rename, search for references, and delete `GameplayTags`.

![GameplayTag Editor in Project Settings](https://github.com/tranek/GASDocumentation/raw/master/Images/gameplaytageditor.png)

Searching for `GameplayTag` references will bring up the familiar `Reference Viewer` graph in the Editor showing all the assets that reference the `GameplayTag`. This will not however show any C++ classes that reference the `GameplayTag`.

Renaming `GameplayTags` creates a redirect so that assets still referencing the original `GameplayTag` can redirect to the new `GameplayTag`. I prefer if possible to instead create a new `GameplayTag`, update all the references manually to the new `GameplayTag`, and then delete the old `GameplayTag` to avoid creating a redirect.

In addition to `Fast Replication`, the `GameplayTag` editor has an option to fill in commonly replicated `GameplayTags` to optimize them further.

`GameplayTags` are replicated if they're added from a `GameplayEffect`. The `ASC` allows you to add `LooseGameplayTags` that are not replicated and must be managed manually. The Sample Project uses a `LooseGameplayTag` for `State.Dead` so that the owning clients can immediately respond to when their health drops to zero. Respawning manually sets the `TagMapCount` back to zero. Only manually adjust the `TagMapCount` when working with `LooseGameplayTags`. It is preferable to use the `UAbilitySystemComponent::AddLooseGameplayTag()` and `UAbilitySystemComponent::RemoveLooseGameplayTag()` functions than manually adjusting the `TagMapCount`.

Getting a reference to a `GameplayTag` in C++:
```c++
FGameplayTag::RequestGameplayTag(FName("Your.GameplayTag.Name"))
```

For advanced `GameplayTag` manipulation like getting the parent or children `GameplayTags`, look at the functions offered by the `GameplayTagManager`. To access the `GameplayTagManager`, include `GameplayTagManager.h` and call it with `UGameplayTagManager::Get().FunctionName`. The `GameplayTagManager` actually stores the `GameplayTags` as relational nodes (parent, child, etc) for faster processing than constant string manipulation and comparisons.

`GameplayTags` and `GameplayTagContainers` can have the optional `UPROPERTY` specifier `Meta = (Categories = "GameplayCue")` that filters the tags in the Blueprint to show only `GameplayTags` that have the parent tag of `GameplayCue`. This is useful when you know the `GameplayTag` or `GameplayTagContainer` variable should only be used for `GameplayCues`.

Alternatively, there's a separate structure called `FGameplayCueTag` that encapsulates a `FGameplayTag` and also automatically filters `GameplayTags` in Blueprint to only show those tags with the parent tag of `GameplayCue`.

If you want to filter a `GameplayTag` parameter in a function, use the `UFUNCTION` specifier `Meta = (GameplayTagFilter = "GameplayCue")`. `GameplayTagContainer` parameters in functions can not be filtered. If you would like to edit your engine to allow this, look at how `SGameplayTagGraphPin::ParseDefaultValueData()` from `Engine\Plugins\Editor\GameplayTagsEditor\Source\GameplayTagsEditor\Private\SGameplayTagGraphPin.cpp` calls `FilterString = UGameplayTagsManager::Get().GetCategoriesMetaFromField(PinStructType);` and passes `FilterString` to `SGameplayTagWidget` in `SGameplayTagGraphPin::GetListContent()`. The `GameplayTagContainer` version of these functions in `Engine\Plugins\Editor\GameplayTagsEditor\Source\GameplayTagsEditor\Private\SGameplayTagContainerGraphPin.cpp` do not check for the meta field properties and pass along the filter.

The Sample Project extensively uses `GameplayTags`.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gt-change"></a>
### 4.2.1 Responding to Changes in Gameplay Tags
The `ASC` provides a delegate for when `GameplayTags` are added or removed. It takes in a `EGameplayTagEventType` that can specify only to fire when the `GameplayTag` is added/removed or for any change in the `GameplayTag's` `TagMapCount`.

```c++
AbilitySystemComponent->RegisterGameplayTagEvent(FGameplayTag::RequestGameplayTag(FName("State.Debuff.Stun")), EGameplayTagEventType::NewOrRemoved).AddUObject(this, &AGDPlayerState::StunTagChanged);
```

The callback function has a parameter for the `GameplayTag` and the new `TagCount`.
```c++
virtual void StunTagChanged(const FGameplayTag CallbackTag, int32 NewCount);
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gt-loadfromplugin"></a>
### 4.2.2 Loading Gameplay Tags from Plugin .ini Files
If you create a plugin with its own .ini files with `GameplayTags`, you can load that plugin's `GameplayTag` .ini directory in your plugin's `StartupModule()` function.

For example, this is how the CommonConversation plugin that comes with Unreal Engine does it:

```c++
void FCommonConversationRuntimeModule::StartupModule()
{
	TSharedPtr<IPlugin> ThisPlugin = IPluginManager::Get().FindPlugin(TEXT("CommonConversation"));
	check(ThisPlugin.IsValid());
	
	UGameplayTagsManager::Get().AddTagIniSearchPath(ThisPlugin->GetBaseDir() / TEXT("Config") / TEXT("Tags"));

	//...
}
```

This would look for the directory `Plugins\CommonConversation\Config\Tags` and load any .ini files with `GameplayTags` in them into your project when the Engine starts up if the plugin is enabled.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-a"></a>
### 4.3 Attributes

<a name="concepts-a-definition"></a>
#### 4.3.1 Attribute Definition
`Attributes` are float values defined by the struct [`FGameplayAttributeData`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FGameplayAttributeData/index.html). These can represent anything from the amount of health a character has to the character's level to the number of charges that a potion has. If it is a gameplay-related numerical value belonging to an `Actor`, you should consider using an `Attribute` for it. `Attributes` should generally only be modified by [`GameplayEffects`](#concepts-ge) so that the ASC can [predict](#concepts-p) the changes.

`Attributes` are defined by and live in an [`AttributeSet`](#concepts-as). The `AttributeSet` is responsible for replicating `Attributes` that are marked for replication. See the section on [`AttributeSets`](#concepts-as) for how to define `Attributes`.

**Tip:** If you don't want an `Attribute` to show up in the Editor's list of `Attributes`, you can use the `Meta = (HideInDetailsView)` `property specifier`.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-a-value"></a>
#### 4.3.2 BaseValue vs CurrentValue
An `Attribute` is composed of two values - a `BaseValue` and a `CurrentValue`. The `BaseValue` is the permanent value of the `Attribute` whereas the `CurrentValue` is the `BaseValue` plus temporary modifications from `GameplayEffects`. For example, your `Character` may have a movespeed `Attribute` with a `BaseValue` of 600 units/second. Since there are no `GameplayEffects` modifying the movespeed yet, the `CurrentValue` is also 600 u/s. If she gets a temporary 50 u/s movespeed buff, the `BaseValue` stays the same at 600 u/s while the `CurrentValue` is now 600 + 50 for a total of 650 u/s. When the movespeed buff expires, the `CurrentValue` reverts back to the `BaseValue` of 600 u/s.

Often beginners to GAS will confuse `BaseValue` with a maximum value for an `Attribute` and try to treat it as such. This is an incorrect approach. Maximum values for `Attributes` that can change or are referenced in abilities or UI should be treated as separate `Attributes`. For hardcoded maximum and minimum values, there is a way to define a `DataTable` with `FAttributeMetaData` that can set maximum and minimum values, but Epic's comment above the struct calls it a "work in progress". See `AttributeSet.h` for more information. To prevent confusion, I recommend that maximum values that can be referenced in abilities or UI be made as separate `Attributes` and hardcoded maximum and minimum values that are only used for clamping `Attributes` be defined as hardcoded floats in the `AttributeSet`. Clamping of `Attributes` is discussed in [PreAttributeChange()](#concepts-as-preattributechange) for changes to the `CurrentValue` and [PostGameplayEffectExecute()](#concepts-as-postgameplayeffectexecute) for changes to the `BaseValue` from `GameplayEffects`.

Permanent changes to the `BaseValue` come from `Instant` `GameplayEffects` whereas `Duration` and `Infinite` `GameplayEffects` change the `CurrentValue`. Periodic `GameplayEffects` are treated like instant `GameplayEffects` and change the `BaseValue`.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-a-meta"></a>
#### 4.3.3 Meta Attributes
Some `Attributes` are treated as placeholders for temporary values that are intended to interact with `Attributes`. These are called `Meta Attributes`. For example, we commonly define damage as a `Meta Attribute`. Instead of a `GameplayEffect` directly changing our health `Attribute`, we use a `Meta Attribute` called damage as a placeholder. This way the damage value can be modified with buffs and debuffs in an [`GameplayEffectExecutionCalculation`](#concepts-ge-ec) and can be further manipulated in the `AttributeSet`, for example subtracting the damage from a current shield `Attribute`, before finally subtracting the remainder from the health `Attribute`. The damage `Meta Attribute` has no persistence between `GameplayEffects` and is overriden by every one. `Meta Attributes` are not typically replicated.

`Meta Attributes` provide a good logical separation for things like damage and healing between "How much damage did we do?" and "What do we do with this damage?". This logical separation means our `Gameplay Effects` and `Execution Calculations` don't need to know how the Target handles the damage. Continuing our damage example, the `Gameplay Effect` determines how much damage and then the `AttributeSet` decides what to do with that damage. Not all characters may have the same `Attributes`, especially if you use subclassed `AttributeSets`. The base `AttributeSet` class may only have a health `Attribute`, but a subclassed `AttributeSet` may add a shield `Attribute`. The subclassed `AttributeSet` with the shield `Attribute` would distribute the damage received differently than the base `AttributeSet` class.

While `Meta Attributes` are a good design pattern, they are not mandatory. If you only ever have one `Execution Calculation` used for all instances of damage and one `Attribute Set` class shared by all characters, then you may be fine doing the damage distribution to health, shields, etc. inside of the `Execution Calculation` and directly modifying those `Attributes`. You'll only be sacrificing flexibility, but that may be okay for you.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-a-changes"></a>
#### 4.3.4 Responding to Attribute Changes
To listen for when an `Attribute` changes to update the UI or other gameplay, use `UAbilitySystemComponent::GetGameplayAttributeValueChangeDelegate(FGameplayAttribute Attribute)`. This function returns a delegate that you can bind to that will be automatically called whenever an `Attribute` changes. The delegate provides a `FOnAttributeChangeData` parameter with the `NewValue`, `OldValue`, and `FGameplayEffectModCallbackData`. **Note:** The `FGameplayEffectModCallbackData` will only be set on the server.

```c++
AbilitySystemComponent->GetGameplayAttributeValueChangeDelegate(AttributeSetBase->GetHealthAttribute()).AddUObject(this, &AGDPlayerState::HealthChanged);
```

```c++
virtual void HealthChanged(const FOnAttributeChangeData& Data);
```

The Sample Project binds to the `Attribute` value changed delegates on the `GDPlayerState` to update the HUD and to respond to player death when health reaches zero.

A custom Blueprint node that wraps this into an `ASyncTask` is included in the Sample Project. It is used in the `UI_HUD` UMG Widget to update the health, mana, and stamina values. This `AsyncTask` will live forever until manually called `EndTask()`, which we do in the UMG Widget's `Destruct` event. See `AsyncTaskAttributeChanged.h/cpp`.

![Listen for Attribute Change BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/attributechange.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-a-derived"></a>
#### 4.3.5 Derived Attributes
To make an `Attribute` that has some or all of its value derived from one or more other `Attributes`, use an `Infinite` `GameplayEffect` with one or more `Attribute Based` or [`MMC`](#concepts-ge-mmc) [`Modifiers`](#concepts-ge-mods). The `Derived Attribute` will update automatically when an `Attribute` that it depends on is updated.

The final formula for all the `Modifiers` on a `Derived Attribute` is the same formula for `Modifier Aggregators`. If you need calculations to happen in a certain order, do it all inside of an `MMC`.

```
((CurrentValue + Additive) * Multiplicitive) / Division
```

**Note:** If playing with multiple clients in PIE, you need to disable `Run Under One Process` in the Editor Preferences otherwise the `Derived Attributes` will not update when their independent `Attributes` update on clients other than the first.

In this example, we have an `Infinite` `GameplayEffect` that derives the value of `TestAttrA` from the `Attributes`, `TestAttrB` and `TestAttrC`, in the formula `TestAttrA = (TestAttrA + TestAttrB) * ( 2 * TestAttrC)`. `TestAttrA` recalculates its value automatically whenever any of the `Attributes` update their values.

![Derived Attribute Example](https://github.com/tranek/GASDocumentation/raw/master/Images/derivedattribute.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as"></a>
### 4.4 Attribute Set

<a name="concepts-as-definition"></a>
#### 4.4.1 Attribute Set Definition
The `AttributeSet` defines, holds, and manages changes to `Attributes`. Developers should subclass from [`UAttributeSet`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UAttributeSet/index.html). Creating an `AttributeSet` in an `OwnerActor's` constructor automatically registers it with its `ASC`. **This must be done in C++**.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-design"></a>
#### 4.4.2 Attribute Set Design
An `ASC` may have one or many `AttributeSets`. AttributeSets have negligible memory overhead so how many `AttributeSets` to use is an organizational decision left up to the developer.

It is acceptable to have one large monolithic `AttributeSet` shared by every `Actor` in your game and only use attributes if needed while ignoring unused attributes.

Alternatively, you may choose to have more than one `AttributeSet` representing groupings of `Attributes` that you selectively add to your `Actors` as needed. For example, you could have an `AttributeSet` for health related `Attributes`, an `AttributeSet` for mana related `Attributes`, and so on. In a MOBA game, heroes might need mana but minions might not. Therefore the heroes would get the mana `AttributeSet` and minions would not.

Additionally, `AttributeSets` can be subclassed as another means of selectively choosing which `Attributes` an `Actor` has. `Attributes` are internally referred to as `AttributeSetClassName.AttributeName`. When you subclass an `AttributeSet`, all of the `Attributes` from the parent class will still have the parent class's name as the prefix.

While you can have more than one `AttributeSet`, you should not have more than one `AttributeSet` of the same class on an `ASC`. If you have more than one `AttributeSet` from the same class, it won't know which `AttributeSet` to use and will just pick one.

<a name="concepts-as-design-subcomponents"></a>
##### 4.4.2.1 Subcomponents with Individual Attributes
In the scenario where you have multiple damageable components on a `Pawn` like individually damageable armor pieces, I recommend that if you know the maximum number of damageable components that a `Pawn` could have to make that many health `Attributes` on one `AttributeSet` - DamageableCompHealth0, DamageableCompHealth1, etc. to represent logical 'slots' for those damageable components. In your damageable component class instance, assign the slot number `Attribute` that can be read by `GameplayAbilities` or [`Executions`](#concepts-ge-ec) to know which `Attribute` to apply damage to. `Pawns` that have less than the maximum number or zero of damageable components are fine. Just because a `AttributeSet` has an `Attribute`, doesn't mean that you have to use it. Unused `Attributes` take up trivial amount of memory.

If your subcomponents need many `Attributes` each, there's potentially an unbounded number of subcomponents, the subcomponents can detach and be used by other players (e.g. weapons), or for any other reason this approach doesn't work for you, I'd recommend switching away from `Attributes` and instead store plain old floats on the components. See [Item Attributes](#concepts-as-design-itemattributes).

<a name="concepts-as-design-addremoveruntime"></a>
##### 4.4.2.2 Adding and Removing AttributeSets at Runtime
`AttributeSets` can be added and removed from an `ASC` at runtime; however, removing `AttributeSets` can be dangerous. For example, if an `AttributeSet` is removed on a client before the server and an `Attribute` value change is replicated to client, the `Attribute` won't find its `AttributeSet` and crash the game.

On weapon add to inventory:
```c++
AbilitySystemComponent->GetSpawnedAttributes_Mutable().AddUnique(WeaponAttributeSetPointer);
AbilitySystemComponent->ForceReplication();
```

On weapon remove from inventory:
```c++
AbilitySystemComponent->GetSpawnedAttributes_Mutable().Remove(WeaponAttributeSetPointer);
AbilitySystemComponent->ForceReplication();
```
<a name="concepts-as-design-itemattributes"></a>
##### 4.4.2.3 Item Attributes (Weapon Ammo)
There's a few ways to implement equippable items with `Attributes` (weapon ammo, armor durability, etc). All of these approaches store values directly on the item. This is necessary for items that can be equipped by more than one player over its lifetime.

> 1. Use plain floats on the item (**Recommended**)
> 1. Separate `AttributeSet` on the item
> 1. Separate `ASC` on the item

<a name="concepts-as-design-itemattributes-plainfloats"></a>
###### 4.4.2.3.1 Plain Floats on the Item
Instead of `Attributes`, store plain float values on the item class instance. Fortnite and [GASShooter](https://github.com/tranek/GASShooter) handle gun ammo this way. For a gun, store the max clip size, current ammo in clip, reserve ammo, etc directly as replicated floats (`COND_OwnerOnly`) on the gun instance. If weapons share reserve ammo, you would move the reserve ammo onto the character as an `Attribute` in a shared ammo `AttributeSet` (reload abilities can use a `Cost GE` to pull from reserve ammo into the gun's float clip ammo). Since you're not using `Attributes` for current clip ammo, you will need to override some functions in `UGameplayAbility` to check and apply cost against the floats on the gun. Making the gun the `SourceObject` in the [`GameplayAbilitySpec`](https://github.com/tranek/GASDocumentation#concepts-ga-spec) when granting the ability means you'll have access to the gun that granted the ability inside the ability.

To prevent the gun from replicating back the ammo amount and clobbering the local ammo amount during automatic fire, disable replication while the player has a `IsFiring` `GameplayTag` in `PreReplication()`. You're essentially doing your own local prediction here.

```c++
void AGSWeapon::PreReplication(IRepChangedPropertyTracker& ChangedPropertyTracker)
{
	Super::PreReplication(ChangedPropertyTracker);

	DOREPLIFETIME_ACTIVE_OVERRIDE(AGSWeapon, PrimaryClipAmmo, (IsValid(AbilitySystemComponent) && !AbilitySystemComponent->HasMatchingGameplayTag(WeaponIsFiringTag)));
	DOREPLIFETIME_ACTIVE_OVERRIDE(AGSWeapon, SecondaryClipAmmo, (IsValid(AbilitySystemComponent) && !AbilitySystemComponent->HasMatchingGameplayTag(WeaponIsFiringTag)));
}
```

Benefits:
1. Avoids limitations of using `AttributeSets` (see below)

Limitations:
1. Can not use existing `GameplayEffect` workflow (`Cost GEs` for ammo use, etc)
1. Requires work to override key functions on `UGameplayAbility` to check and apply ammo costs against the gun's floats

<a name="concepts-as-design-itemattributes-attributeset"></a>
###### 4.4.2.3.2 `AttributeSet` on the Item
Using a separate `AttributeSet` on the item that gets [added to the player's `ASC` on adding it to the player's inventory](#concepts-as-design-addremoveruntime) can work, but it has some major limitations. I had this working in early versions of [GASShooter](https://github.com/tranek/GASShooter) for the weapon ammo. The weapon stores its `Attributes` such as max clip size, current ammo in clip, reserve ammo, etc in an `AttributeSet` that lives on the weapon class. If weapons share reserve ammo, you would move the reserve ammo onto the character in a shared ammo `AttributeSet`. When a weapon is added to the player's inventory on the server, the weapon would add its `AttributeSet` to the player's `ASC::SpawnedAttributes`. The server would then replicate this down to the client. If the weapon is removed from the inventory, it would remove its `AttributeSet` from the `ASC::SpawnedAttributes`.

When the `AttributeSet` lives on something other than the `OwnerActor` (say a weapon), you'll initially get some compilation errors in the `AttributeSet`. The fix is to construct the `AttributeSet` in `BeginPlay()` instead of in the constructor and to implement `IAbilitySystemInterface` (set the pointer to the `ASC` when you add the weapon to the player inventory) on the weapon.

```c++
void AGSWeapon::BeginPlay()
{
	if (!AttributeSet)
	{
		AttributeSet = NewObject<UGSWeaponAttributeSet>(this);
	}
	//...
}
```

You can see it in practice by checking out this [older version of GASShooter](https://github.com/tranek/GASShooter/tree/df5949d0dd992bd3d76d4a728f370f2e2c827735).

Benefits:
1. Can use existing `GameplayAbility` and `GameplayEffect` workflow (`Cost GEs` for ammo use, etc)
1. Simple to setup for a very small set of items

Limitations:
1. You have to make a new `AttributeSet` class for every weapon type. `ASCs` can only functionally have one `AttributeSet` instance of a class since changes to an `Attribute` look for the first instance of their `AttributeSet` class in the `ASCs` `SpawnedAttributes` array. Additional instances of the same `AttributeSet` class are ignored.
1. You can only have one of each type of weapon in the player's inventory due to previous reason of one `AttributeSet` instance per `AttributeSet` class.
1. Removing an `AttributeSet` is dangerous. In GASShooter if the player killed himself from a rocket, the player would immediately remove the rocket launcher from his inventory (including its `AttributeSet` from the `ASC`). When the server replicated that the rocket launcher's ammo `Attribute` changed, the `AttributeSet` no longer existed on the client's `ASC` and the game crashed.

<a name="concepts-as-design-itemattributes-asc"></a>
###### 4.4.2.3.3 `ASC` on the Item
Putting a whole `AbilitySystemComponent` on each item is an extreme approach. I have not personally done this nor have I seen it in the wild. It would take a lot of engineering to make it work.

> Is it viable to have several AbilitySystemComponents which have the same owner but different avatars (e.g. on pawn and weapon/items/projectiles with Owner set to PlayerState)?
> 
> The first problem I see there would be implementing the IGameplayTagAssetInterface and IAbilitySystemInterface on the owning actor. The former may be possible: just aggregate the tags from all all ASCs (but watch out -HasAllMatchingGameplayTags may be met only via cross ASC aggregation. It wouldn't be enough to just forward that calls to each ASC and OR the results together). But the later is even trickier: which ASC is the authoritative one? If someone wants to apply a GE -which one should receive it? Maybe you can work these out but this side of the problem will be the hardest: owners will have multiple ASCs beneath them.
> 
> Separate ASCs on the pawn and the weapon can make sense on its own though. E.g, distinguishing between tags that describe the weapon vs those that describe the owning pawn. Maybe it does make sense that tags granted to the weapon also “apply” to the owner and nothing else (e.g, attributes and GEs are independent but the owner will aggregate the owned tags like I describe above). This could work out, I am sure. But having multiple ASCs with the same owner may get dicey.

*Dave Ratti from Epic's answer to [community questions #6](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89)*

Benefits:
1. Can use existing `GameplayAbility` and `GameplayEffect` workflow (`Cost GEs` for ammo use, etc)
1. Can reuse `AttributeSet` classes (one on each weapon's ASC)

Limitations:
1. Unknown engineering cost
1. Is it even possible?

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-attributes"></a>
#### 4.4.3 Defining Attributes
**`Attributes` can only be defined in C++** in the `AttributeSet's` header file. It is recommended to add this block of macros to the top of every `AttributeSet` header file. It will automatically generate getter and setter functions for your `Attributes`.

```c++
// Uses macros from AttributeSet.h
#define ATTRIBUTE_ACCESSORS(ClassName, PropertyName) \
	GAMEPLAYATTRIBUTE_PROPERTY_GETTER(ClassName, PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_GETTER(PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_SETTER(PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_INITTER(PropertyName)
```

A replicated health attribute would be defined like this:

```c++
UPROPERTY(BlueprintReadOnly, Category = "Health", ReplicatedUsing = OnRep_Health)
FGameplayAttributeData Health;
ATTRIBUTE_ACCESSORS(UGDAttributeSetBase, Health)
```

Also define the `OnRep` function in the header:
```c++
UFUNCTION()
virtual void OnRep_Health(const FGameplayAttributeData& OldHealth);
```

The .cpp file for the `AttributeSet` should fill in the `OnRep` function with the `GAMEPLAYATTRIBUTE_REPNOTIFY` macro used by the prediction system:
```c++
void UGDAttributeSetBase::OnRep_Health(const FGameplayAttributeData& OldHealth)
{
	GAMEPLAYATTRIBUTE_REPNOTIFY(UGDAttributeSetBase, Health, OldHealth);
}
```

Finally, the `Attribute` needs to be added to `GetLifetimeReplicatedProps`:
```c++
void UGDAttributeSetBase::GetLifetimeReplicatedProps(TArray<FLifetimeProperty>& OutLifetimeProps) const
{
	Super::GetLifetimeReplicatedProps(OutLifetimeProps);

	DOREPLIFETIME_CONDITION_NOTIFY(UGDAttributeSetBase, Health, COND_None, REPNOTIFY_Always);
}
```

`REPNOTIFY_Always` tells the `OnRep` function to trigger if the local value is already equal to the value being repped down from the Server (due to prediction). By default it won't trigger the `OnRep` function if the local value is the same as the value being repped down from the Server.

If the `Attribute` is not replicated like a `Meta Attribute`, then the `OnRep` and `GetLifetimeReplicatedProps` steps can be skipped.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-init"></a>
#### 4.4.4 Initializing Attributes
There are multiple ways to initialize `Attributes` (set their `BaseValue` and consequently their `CurrentValue` to some initial value). Epic recommends using an instant `GameplayEffect`. This is the method used in the Sample Project too.

See `GE_HeroAttributes` Blueprint in the Sample Project for how to make an instant `GameplayEffect` to initialize `Attributes`. Application of this `GameplayEffect` happens in C++.

If you used the `ATTRIBUTE_ACCESSORS` macro when you defined your `Attributes`, an initialization function will automatically be generated on the `AttributeSet` for each `Attribute` that you can call at your leisure in C++.

```c++
// InitHealth(float InitialValue) is an automatically generated function for an Attribute 'Health' defined with the `ATTRIBUTE_ACCESSORS` macro
AttributeSet->InitHealth(100.0f);
```

See `AttributeSet.h` for more ways to initialize `Attributes`.

**Note:** Prior to 4.24, `FAttributeSetInitterDiscreteLevels` did not work with `FGameplayAttributeData`. It was created when `Attributes` were raw floats and will complain about `FGameplayAttributeData` not being `Plain Old Data` (`POD`). This is fixed in 4.24 https://issues.unrealengine.com/issue/UE-76557.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-preattributechange"></a>
#### 4.4.5 PreAttributeChange()
`PreAttributeChange(const FGameplayAttribute& Attribute, float& NewValue)` is one of the main functions in the `AttributeSet` to respond to changes to an `Attribute's` `CurrentValue` before the change happens. It is the ideal place to clamp incoming changes to `CurrentValue` via the reference parameter `NewValue`.

For example to clamp movespeed modifiers the Sample Project does it like so:
```c++
if (Attribute == GetMoveSpeedAttribute())
{
	// Cannot slow less than 150 units/s and cannot boost more than 1000 units/s
	NewValue = FMath::Clamp<float>(NewValue, 150, 1000);
}
```
The `GetMoveSpeedAttribute()` function is created by the macro block that we added to the `AttributeSet.h` ([Defining Attributes](#concepts-as-attributes)).

This is triggered from any changes to `Attributes`, whether using `Attribute` setters (defined by the macro block in `AttributeSet.h` ([Defining Attributes](#concepts-as-attributes))) or using [`GameplayEffects`](#concepts-ge).

**Note:** Any clamping that happens here does not permanently change the modifier on the `ASC`. It only changes the value returned from querying the modifier. This means anything that recalculates the `CurrentValue` from all of the modifiers like [`GameplayEffectExecutionCalculations`](#concepts-ge-ec) and [`ModifierMagnitudeCalculations`](#concepts-ge-mmc) need to implement clamping again.

**Note:** Epic's comments for `PreAttributeChange()` say not to use it for gameplay events and instead use it mainly for clamping. The recommended place for gameplay events on `Attribute` change is `UAbilitySystemComponent::GetGameplayAttributeValueChangeDelegate(FGameplayAttribute Attribute)` ([Responding to Attribute Changes](#concepts-a-changes)).

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-postgameplayeffectexecute"></a>
#### 4.4.6 PostGameplayEffectExecute()
`PostGameplayEffectExecute(const FGameplayEffectModCallbackData & Data)` only triggers after changes to the `BaseValue` of an `Attribute` from an instant [`GameplayEffect`](#concepts-ge). This is a valid place to do more `Attribute` manipulation when they change from a `GameplayEffect`.

For example, in the Sample Project we subtract the final damage `Meta Attribute` from the health `Attribute` here. If there was a shield `Attribute`, we would subtract the damage from it first before subtracting the remainder from health. The Sample Project also uses this location to apply hit react animations, show floating Damage Numbers, and assign experience and gold bounties to the killer. By design, the damage `Meta Attribute` will always come through an instant `GameplayEffect` and never the `Attribute` setter.

Other `Attributes` that will only have their `BaseValue` changed from instant `GameplayEffects` like mana and stamina can also be clamped to their maximum value counterpart `Attributes` here.

**Note:** When `PostGameplayEffectExecute()` is called, changes to the `Attribute` have already happened, but they have not replicated back to clients yet so clamping values here will not cause two network updates to clients. Clients will only receive the update after clamping.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-onattributeaggregatorcreated"></a>
#### 4.4.7 OnAttributeAggregatorCreated()
`OnAttributeAggregatorCreated(const FGameplayAttribute& Attribute, FAggregator* NewAggregator)` triggers when an `Aggregator` is created for an `Attribute` in this set. It allows custom setup of [`FAggregatorEvaluateMetaData`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FAggregatorEvaluateMetaData/index.html). `AggregatorEvaluateMetaData` is used by the `Aggregator` in evaluating the `CurrentValue` of an `Attribute` based on all the [`Modifiers`](#concepts-ge-mods) applied to it. By default, `AggregatorEvaluateMetaData` is only used by the `Aggregator` to determine which `Modifiers` qualify with the example of `MostNegativeMod_AllPositiveMods` which allows all positive `Modifiers` but restricts negative `Modifiers` to only the most negative one. This was used by Paragon to only allow the most negative move speed slow effect to apply to a player regardless of how many slow effects where on them at any one time while applying all positive move speed buffs. `Modifiers` that don't qualify still exist on the `ASC`, they just aren't aggregated into the final `CurrentValue`. They can potentially qualify later once conditions change, like in the case if the most negative `Modifier` expires, the next most negative `Modifier` (if one exists) then qualifies.

To use AggregatorEvaluateMetaData in the example of only allowing the most negative `Modifier` and all positive `Modifiers`:

```c++
virtual void OnAttributeAggregatorCreated(const FGameplayAttribute& Attribute, FAggregator* NewAggregator) const override;
```

```c++
void UGSAttributeSetBase::OnAttributeAggregatorCreated(const FGameplayAttribute& Attribute, FAggregator* NewAggregator) const
{
	Super::OnAttributeAggregatorCreated(Attribute, NewAggregator);

	if (!NewAggregator)
	{
		return;
	}

	if (Attribute == GetMoveSpeedAttribute())
	{
		NewAggregator->EvaluationMetaData = &FAggregatorEvaluateMetaDataLibrary::MostNegativeMod_AllPositiveMods;
	}
}
```

Your custom `AggregatorEvaluateMetaData` for Qualifiers should be added to `FAggregatorEvaluateMetaDataLibrary` as static variables.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge"></a>
### 4.5 Gameplay Effects

<a name="concepts-ge-definition"></a>
#### 4.5.1 Gameplay Effect Definition
[`GameplayEffects`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayEffect/index.html) (`GE`) 是abilities改变[`Attributes`](#concepts-a) 和 [`GameplayTags`](#concepts-gt) 的一种介质. 它可以造成`Attribute`立即改变比如伤害或者生命值或者添加一个长期的状态 buff/debuffs 像加速或者眩晕. `UGameplayEffect` 类是纯数据类，定义了单个游戏效果. `GameplayEffects`不允许添加额外的逻辑代码. 通常设计者会创建很多继承自`UGameplayEffect`的蓝图.

`GameplayEffects` 改变 `Attributes` 通过 [`Modifiers`](#concepts-ge-mods) 和 [`Executions` (`GameplayEffectExecutionCalculation`)](#concepts-ge-ec).

`GameplayEffects` 拥有三种作用时间类型: `Instant`（立即）, `Duration`（一段时间内）, and `Infinite`（无限）.

此外, `GameplayEffects` 可以添加/执行 [`GameplayCues`](#concepts-gc). 一个 `Instant` `GameplayEffect` 会调用 `GameplayCue` `GameplayTags` 上的 `Execute`，而 `Duration` 或者`Infinite` `GameplayEffect` 则会调用 `GameplayCue` `GameplayTags` 上的 `Add` 和 `Remove`.

| 时间类型 | GameplayCue 时间 | 使用时机                                                                                                                                                                                                                                |
| ------------- | ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `Instant`     | Execute           | 用于立即永久更改属性的 `BaseValue`。 `GameplayTags` 不会被应用，即使对于一帧也是如此.                                                                                                                    |
| `Duration`    | Add & Remove      | 用于临时更改属性的 `CurrentValue` 并应用 `GameplayTags`，当 GameplayEffect 过期或手动删除时，这些 `GameplayTags` 将被删除。持续时间在 `UGameplayEffect` 类/蓝图中指定.       |
| `Infinite`    | Add & Remove      | 用于临时更改属性的 `CurrentValue` 并应用 `GameplayTags`，当 `GameplayEffect` 被删除时，这些 `GameplayTags` 将被删除。它们永远不会自行过期，必须由能力或 `ASC` 手动删除。. |

`Duration` 和 `Infinite` `GameplayEffects` 可以选择使用 `Periodic Effects`（周期性效果），根据`Period`中的定义每`X`秒调用`Modifiers` 和 `Executions`. `Periodic Effects` 会被当作 `Instant` `GameplayEffects` 在改变 `Attribute's` `BaseValue` 和 `Executing` `GameplayCues`时. 这些对随时间推移造成的伤害效果（DOT）会很有用 . **提示:** `Periodic Effects` 周期性效果不可预测 not [predicted](#concepts-p).

`Duration` 和 `Infinite` `GameplayEffects` 在满足/不满足`Ongoing Tag Requirements`Tag([Gameplay Effect Tags](#concepts-ge-tags))要求时可以被临时打开或关闭. 关闭 `GameplayEffect`会移除它的`Modifiers` 并修改 `GameplayTags`但是并不会移除整个`GameplayEffect`. 再次打开 `GameplayEffect` 会重新应用`Modifiers` 和 `GameplayTags`.

如果你需要自行重新计算一个`Duration` 或 `Infinite`类型效果的 `Modifiers` (假设你有一个 `MMC` 并且使用的数据并非来自`Attributes`), 你可以调用`UAbilitySystemComponent::ActiveGameplayEffects.SetActiveGameplayEffectLevel(FActiveGameplayEffectHandle ActiveHandle, int32 NewLevel)` 并使用相同的 level `UAbilitySystemComponent::ActiveGameplayEffects.GetActiveGameplayEffect(ActiveHandle).Spec.GetLevel()`. 当`Attributes`更新时，基于此的`Modifiers`也会自动更新. 更新`Modifiers`的关键函数`SetActiveGameplayEffectLevel()` 是:

```C++
MarkItemDirty(Effect);
Effect.Spec.CalculateModifierMagnitudes();
// Private function otherwise we'd call these three functions without needing to set the level to what it already is
UpdateAllAggregatorModMagnitudes(Effect);
```

`GameplayEffects` 通常不需要实例化. 当一个 ability 或 `ASC` 想应用一种效果 `GameplayEffect`时, 它会创建一个 [`GameplayEffectSpec`](#concepts-ge-spec) 包含 `GameplayEffect's` `ClassDefaultObject`.成功使用后会创建一个 `FActiveGameplayEffect`的结构体并由 `ASC` 中 `ActiveGameplayEffects`保持引用.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-applying"></a>
#### 4.5.2 Applying Gameplay Effects
`GameplayEffects` 可以由[`GameplayAbilities`](#concepts-ga) 和 `ASC`上的多种函数调用使用，通常调用的的方式是 `ApplyGameplayEffectTo`. 不同的调用接口都是为了方便使用的，最终都是调用目标上的`UAbilitySystemComponent::ApplyGameplayEffectSpecToSelf()`.

想要在`GameplayAbility`外使用 `GameplayEffects` 比如炮弹, 你需要获取目标的`ASC`并使用其上的方法`ApplyGameplayEffectToSelf`.

你可以绑定以下代理监听任何一个 `Duration` 或 `Infinite`效果被应用到`ASC`:
```c++
AbilitySystemComponent->OnActiveGameplayEffectAddedDelegateToSelf.AddUObject(this, &APACharacterBase::OnActiveGameplayEffectAddedCallback);
```
回调函数是:
```c++
virtual void OnActiveGameplayEffectAddedCallback(UAbilitySystemComponent* Target, const FGameplayEffectSpec& SpecApplied, FActiveGameplayEffectHandle ActiveHandle);
```

无论是否开启复制同步，服务器都会调用该函数. 自治代理 autonomous proxy 只会在`Full` and `Mixed`的复制模式下调用. 模拟代理Simulated proxies 只会在 `Full`同步模式下调用 [replication mode](#concepts-asc-rm).

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-removing"></a>
#### 4.5.3 Removing Gameplay Effects
[`GameplayAbilities`](#concepts-ga) 和 `ASC`上有很多方法可以移除`GameplayEffects`，通常使用`RemoveActiveGameplayEffect`. 不同的调用接口最终都会调用目标上 `FActiveGameplayEffectsContainer::RemoveActiveEffects()`.

要移除`GameplayAbility`外的`GameplayEffects`, 你需要先获取目标上的 `ASC` 并使用方法  `RemoveActiveGameplayEffect`.

你可以绑定以下代理监听任何一个`Duration` or `Infinite` 效果从`ASC`上被移除时:
```c++
AbilitySystemComponent->OnAnyGameplayEffectRemovedDelegate().AddUObject(this, &APACharacterBase::OnRemoveGameplayEffectCallback);
```
回调函数:
```c++
virtual void OnRemoveGameplayEffectCallback(const FActiveGameplayEffect& EffectRemoved);
```

无论是否开启复制同步，服务器都会调用该函数. 自治代理 autonomous proxy 只会在`Full` and `Mixed`的复制模式下调用. 模拟代理Simulated proxies 只会在 `Full`同步模式下调用 [replication mode](#concepts-asc-rm).

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-mods"></a>
#### 4.5.4 Gameplay Effect Modifiers
`Modifiers` 会更改 `Attribute` 并且是唯一可以 预测[predictively](#concepts-p)更改的方法. 一个 `GameplayEffect` 可以拥有0或多个 `Modifiers`. 每个 `Modifier` 通过指定操作金科更改一个 `Attribute`.

| 操作  | 描述                                                                                                         |
| ---------- | ------------------------------------------------------------------------------------------------------------------- |
| `Add`      | 将结果添加到 `Modifier's` 指定 `Attribute`. 使用负数进行减法.                    |
| `Multiply` | 将结果与 `Modifier's` 指定 `Attribute`相乘.                                                    |
| `Divide`   | 将结果除以 `Modifier's` 指定      `Attribute`.                                                  |
| `Override` | 用结果覆盖 `Modifier's` 指定 `Attribute`.                                                   |

一个`Attribute` 的 `CurrentValue`当前值是所有`Modifiers`添加到其`BaseValue`的集和.`Modifiers`是如何集合值的公式被定义在 `FAggregatorModChannel::EvaluateWithBase` in `GameplayEffectAggregator.cpp`:
```c++
((InlineBaseValue + Additive) * Multiplicitive) / Division
```

任何 `Override` `Modifiers`会覆盖最终结果，最后调用的会覆盖之前的.

**注:** 对于百分比的变化，请确保乘法操作在加法后.

**注:** [Prediction](#concepts-p)预测在百分比变化中可能并不准确.

这里有四种`Modifiers`: Scalable Float, Attribute Based, Custom Calculation Class, 和 Set By Caller. 它们都会生成一些float数值，用于根据操作更改指定的`Attribute`.

| `Modifier` 类型            | 介绍                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Scalable Float`           | `FScalableFloats` 是一个可以指向数据表的结构，该数据表的变量为行，levels为列. Scalable Floats会自动读取指定数据表行 current level 列的值 (或不同 level 重载于 [`GameplayEffectSpec`](#concepts-ge-spec)). 该值可以进一步由系数操纵. 如果没有指定数据表或数据行，该值将被强制写为1. ![ScalableFloat](https://github.com/tranek/GASDocumentation/raw/master/Images/scalablefloats.png)                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| `Attribute Based`          | `Attribute Based` `Modifiers`是一个基于 源（谁创建的）或者 目标 (谁接收的) 去修改`CurrentValue` 或者 `BaseValue` ，可进一步通过一个系数去修改并添加前项、后项系数进行调整. `Snapshotting`意思是在创建`GameplayEffectSpec`就立即捕获  `Attribute` 值， 而 no snapshotting 意味着直到`GameplayEffectSpec`被应用时再捕获对应值.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| `Custom Calculation Class` | `Custom Calculation Class` 为复杂的 `Modifiers` 提供最大的便捷性. 这类 `Modifier` 拥有一个 [`ModifierMagnitudeCalculation`](#concepts-ge-mmc) class 并可通过参数操控结果并添加自定义前后项参数.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| `Set By Caller`            | `SetByCaller` `Modifiers`是ability运行时在`GameplayEffect`外设置的值 或创建 `GameplayEffectSpec`在 `GameplayEffectSpec`外设置的值. 例如, 如果您想要根据玩家按住按钮来充能技能的时间来设置伤害，则可以使用 `SetByCaller`. `SetByCallers` 本质是 `TMap<FGameplayTag, float>` `GameplayEffectSpec`上的 . `Modifier` 只是告诉 `Aggregator` 去寻找与 `GameplayTag` 相关的 `SetByCaller` 值. `Modifiers` 使用 `SetByCallers` 仅会使用  `GameplayTag` 相关， `FName` 相关的此处会被禁止使用. 如果一个 `Modifier` 被设置为 `SetByCaller` 但是 `SetByCaller` 拥有正确的 `GameplayTag` 并不存在于 `GameplayEffectSpec`, 游戏会报一个运行时错误并返回 0. 在除法 `Divide` 操作时会有问题. 参考 [`SetByCallers`](#concepts-ge-spec-setbycaller) 获取更多使用信息. |

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-mods-multiplydivide"></a>
##### 4.5.4.1 Multiply and Divide Modifiers
默认情况下, 所有 `Multiply` 和 `Divide` `Modifiers` 会在应用到 `Attribute`'的 `BaseValue`前先相加.

```c++
float FAggregatorModChannel::EvaluateWithBase(float InlineBaseValue, const FAggregatorEvaluateParameters& Parameters) const
{
	...
	float Additive = SumMods(Mods[EGameplayModOp::Additive], GameplayEffectUtilities::GetModifierBiasByModifierOp(EGameplayModOp::Additive), Parameters);
	float Multiplicitive = SumMods(Mods[EGameplayModOp::Multiplicitive], GameplayEffectUtilities::GetModifierBiasByModifierOp(EGameplayModOp::Multiplicitive), Parameters);
	float Division = SumMods(Mods[EGameplayModOp::Division], GameplayEffectUtilities::GetModifierBiasByModifierOp(EGameplayModOp::Division), Parameters);
	...
	return ((InlineBaseValue + Additive) * Multiplicitive) / Division;
	...
}
```

```c++
float FAggregatorModChannel::SumMods(const TArray<FAggregatorMod>& InMods, float Bias, const FAggregatorEvaluateParameters& Parameters)
{
	float Sum = Bias;

	for (const FAggregatorMod& Mod : InMods)
	{
		if (Mod.Qualifies())
		{
			Sum += (Mod.EvaluatedMagnitude - Bias);
		}
	}

	return Sum;
}
```
*from `GameplayEffectAggregator.cpp`*

`Multiply` 和 `Divide` `Modifiers` 公式中都有一个值为1的变量 `Bias`，(`Addition` 中`Bias` 为 `0`). 所以它也可以这么写:

```
1 + (Mod1.Magnitude - 1) + (Mod2.Magnitude - 1) + ...
```

这个公式会导致一些意想不到的结果. 首先，此公式将所有修饰符相加，然后将它们乘以或除以 `BaseValue`. 大多数人会期望它能将它们相乘或相除. 例如，你有两个1.5的 `Multiply` 乘法修改器, 大多数人期望 `BaseValue` 为 `1.5 x 1.5 = 2.25`. 相反, 将两个 `1.5`加一起相当于`BaseValue` 乘以 `2` (`50% increase + another 50% increase = 100% increase`). 这是来自 `GameplayPrediction.h` 的示例，`500` 基础速度的 `10%` 加速buff将为 `550`. 再加上 `10%` 的速度增益，那就是 `600`.

其次，该公式对于哪些值可以使用有一些未记录的规则，因为在设计时考虑到了Paragon.

`Multiply`乘法 和 `Divide` 除法 的乘加公式:
* `(No more than one value < 1) AND (Any number of values [1, 2))`
* `OR (One value >= 2)`

公式中的 `Bias` 基本上是减去 `[1, 2)`范围范围内数字的整数部位. 第一个 `Modifier`的 `Bias` 从起始`Sum`的值中减去 (在循环前设置 `Bias` 的值) 这就是为什么任何值本身都可以工作，以及为什么一个 `< 1`的值将会在范围 `[1, 2)`中工作.

一些乘法的例子 `Multiply`:  
Multipliers: `0.5`  
`1 + (0.5 - 1) = 0.5`, correct

Multipliers: `0.5, 0.5`  
`1 + (0.5 - 1) + (0.5 - 1) = 0`, 预期错误 `1`? 多个小于 `1` 的值对添加乘数没有意义. Paragon 被设计成仅使用乘法修改器的最大负值[greatest negative value for `Multiply` `Modifiers`](#cae-nonstackingge) 因此最多只有一个小于 `1` 的值乘以 `BaseValue`.

Multipliers: `1.1, 0.5`  
`1 + (0.5 - 1) + (1.1 - 1) = 0.6`, correct

Multipliers: `5, 5`  
`1 + (5 - 1) + (5 - 1) = 9`, 预期错误 `10`. 始终是修饰符的总和 - 修饰符的数量 + 1 `sum of the Modifiers - number of Modifiers + 1`.

许多游戏想要他们自己的 `Multiply` 和 `Divide` `Modifiers`在修改 `BaseValue`值前. 你可以通过修改 **引擎代码**  `FAggregatorModChannel::EvaluateWithBase()` 实现.

```c++
float FAggregatorModChannel::EvaluateWithBase(float InlineBaseValue, const FAggregatorEvaluateParameters& Parameters) const
{
	...
	float Multiplicitive = MultiplyMods(Mods[EGameplayModOp::Multiplicitive], Parameters);
	float Division = MultiplyMods(Mods[EGameplayModOp::Division], Parameters);
	...

	return ((InlineBaseValue + Additive) * Multiplicitive) / Division;
}
```

```c++
float FAggregatorModChannel::MultiplyMods(const TArray<FAggregatorMod>& InMods, const FAggregatorEvaluateParameters& Parameters)
{
	float Multiplier = 1.0f;

	for (const FAggregatorMod& Mod : InMods)
	{
		if (Mod.Qualifies())
		{
			Multiplier *= Mod.EvaluatedMagnitude;
		}
	}

	return Multiplier;
}
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-mods-gameplaytags"></a>
##### 4.5.4.2 Gameplay Tags on Modifiers

可以为每个[Modifier](#concepts-ge-mods) 设置`SourceTags` and `TargetTags` （`FGameplayEffectExecutionScopedModifierInfo`） . 它们和`GameplayEffect`的 [`Application Tag requirements`](#concepts-ge-tags) 工作原理类似. 因此只有在应用效果时才考虑tags. 例如，当有一个无限循环周期性的效果，仅在首次遇到时才考虑他们而不是每个周期都去执行他们.

`Attribute Based` Modifiers 也可以设置 `SourceTagFilter` 和 `TargetTagFilter`. 在确定以基于`Attribute Based` Modifier获取属性值时 , 这些过滤器用于排除该属性的某些修饰符. Modifiers 的源或目标中不包含过滤器所有tag的将会被排除.

这意味着: 源ASC和目标ASC的tags将会被 `GameplayEffects`捕获. 在创建`GameplayEffectSpec`时源ASC的tags被获取, 目标 ASC tags 在effect执行时被获取.在确定一个infinite 或 duration效果是否有资格执行时，将会拿得到的tags与过滤器中tags相比较.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-stacking"></a>
#### 4.5.5 Stacking Gameplay Effects
默认情况下`GameplayEffects` 将应用 `GameplayEffectSpec` 的新实例，这些新实例并不知道也不关心程序上先前存在的`GameplayEffectSpec`. `GameplayEffects` 可以设置成 stack 而不是直接添加一个新`GameplayEffectSpec`实例 , 当前存在的 `GameplayEffectSpec's` stack 数量会改变. Stacking 仅对 `Duration` 和 `Infinite` 类型`GameplayEffects`有效.

当前有两种堆栈类型: Aggregate by Source and Aggregate by Target.

| 堆栈类型       | 描述                                                                                                                          |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| Aggregate by Source | 目标上的每个源 ASC 有一个单独的堆栈实例。每个源可以应用 X 数量的堆栈.                     |
| Aggregate by Target | 无论源如何，目标上都只有一个堆栈实例。每个源可以应用一个堆栈，最高可达共享堆栈限制. |

堆栈还具有过期、持续时间刷新和周期重置的策略. 在 `GameplayEffect` 的Blueprint上有很多有用的 tooltips.

示例工程中包含一个自定义蓝图节点监听 `GameplayEffect` stack 改变. 用于更新HUD UMG Widget 上玩家拥有的被动护甲堆叠数量. 这个 `AsyncTask` 异步任务将会一直存在直到调用 `EndTask()`, 我们需要在UMG Widget的 `Destruct` 中调用. 参考 `AsyncTaskEffectStackChanged.h/cpp`.

![Listen for GameplayEffect Stack Change BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/gestackchange.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-ga"></a>
#### 4.5.6 Granted Abilities
`GameplayEffects` 可以向`ASCs` 中授予新的 [`GameplayAbilities`](#concepts-ga) . 只有 `Duration` 和 `Infinite` 类型 `GameplayEffects` 可以添授予新能力.

一个常见的用例是当你想要强迫其他玩家做一些事情，比如将他们从击退或拉扯中移动时. 你可以将一个自动激活的 `GameplayEffect` 赋予它们 (参考被动技能 [Passive Abilities](#concepts-ga-activating-passive) ，了解如何在授予能力时自动激活能力)从而对他们执行所需的操作.

设计者需要对`GameplayEffect`中选择赋予哪种类型的能力，什么等级，绑定到什么输入[input to bind](#concepts-ga-input)以及移除策略.

| 移除策略            | 解释                                                                                                                                                                     |
| -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Cancel Ability Immediately | 当授予该能力的 `GameplayEffect` 从目标中移除时，授予的能力将立即取消并移除.                                                   |
| Remove Ability on End      | 授予的能力可以完成，然后从Target中删除.                                                                                                   |
| Do Nothing                 | 从目标中移除授予的 `GameplayEffect` 不会影响授予的能力。目标永久具有该能力，直到稍后手动将其移除. |

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-tags"></a>
#### 4.5.7 Gameplay Effect Tags
`GameplayEffects` 携带多个 [`GameplayTagContainers`](#concepts-gt). 设计者可以编辑添加和删除每种类别的下的 `GameplayTagContainers`，最终结果会现在在编译时组合的 `GameplayTagContainer`中. `Added` tags 是当前 `GameplayEffect` 父类中没有的新 tag. `Removed` tags 是删除父类中包含的且当前子类中没有的tag.

| 类别                          | 描述                                                                                                                                                                                                                                                                                                                                                                        |
| --------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Gameplay Effect Asset Tags        | `GameplayEffect` 拥有的Tags. 他们本身不执行任何功能，仅用于描述 `GameplayEffect`的目的.                                                                                                                                                                                                                                        |
| Granted Tags                      | 存在于 `GameplayEffect` ，也提供给应用 `GameplayEffect`的 `ASC`. `GameplayEffect`移除时也会从 `ASC` 上移除. 仅 `Duration` 和 `Infinite` `GameplayEffects`类型上有效.                                                                                                                             |
| Ongoing Tag Requirements          | 一旦使用, 这个 tags 将决定 `GameplayEffect` 是打开还是关闭. 一个 `GameplayEffect` 可以关闭但仍然可以应用. 如果一个 `GameplayEffect` 由于未满足持续标签要求（Ongoing Tag Requirements）而关闭 , 但随后满足要求, `GameplayEffect` 会再次打开 并重新应用到它的 modifiers. 仅 `Duration` 和 `Infinite` `GameplayEffects`. |
| Application Tag Requirements      | 目标上的Tags 决定 `GameplayEffect` 是否可以被作用. 如果要求不满足，`GameplayEffect` 将不会被作用.                                                                                                                                                                                                                      |
| Remove Gameplay Effects with Tags | 成功应用此 `GameplayEffect` 后，目标上的`Asset Tags`或`Granted Tags`中具有任何这些标签的 GameplayEffect 将从目标中删除.                                                                                                                                                                                            |

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-immunity"></a>
#### 4.5.8 Immunity
`GameplayEffects` 可以基于[`GameplayTags`](#concepts-gt) 授予免疫力, 有效阻止其他 `GameplayEffects`影响. 虽然可以通过其他方式如`Application Tag Requirements`实现免疫, 当 `GameplayEffects` 被免疫时可以 使用该系统提供一个的一个委托 `UAbilitySystemComponent::OnImmunityBlockGameplayEffectDelegate`.

`GrantedApplicationImmunityTags` 检测源 `ASC` (包含来自源 `AbilityTags` ) 是否有指定tags. 这是一种根据特定角色或来源的标签提供免受所有 `GameplayEffects` 影响的方法.

`Granted Application Immunity Query` 检测传入的 `GameplayEffectSpec` 是否与任何查询匹配以阻止或允许其功能。.

在`GameplayEffect` Blueprint中有很多有用的tooltips.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-spec"></a>
#### 4.5.9 Gameplay Effect Spec
[`GameplayEffectSpec`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FGameplayEffectSpec/index.html) (`GESpec`) 可以看作是 `GameplayEffects`的实例化. 它们拥有所展示的 `GameplayEffect`的引用, 使用 level, 以及谁创建了它. 这些可以在应用前自由的创建和修改，不像 `GameplayEffects`由设计者在运行前创建. 当应用一个 `GameplayEffect`, `GameplayEffectSpec` 是从 `GameplayEffect` 创建的，这实际上是应用于目标的.

使用`UAbilitySystemComponent::MakeOutgoingSpec()`（`BlueprintCallable`）可以从`GameplayEffects`创建 `GameplayEffectSpecs` . `GameplayEffectSpecs` 不必立即应用. 通常将 `GameplayEffectSpec`传递给根据能力创建的炮弹，炮弹可以将其应用于稍后击中的目标. 当 `GameplayEffectSpecs` 被成功应用, 它们会返回一个新的结构体`FActiveGameplayEffect`.

值得注意的 `GameplayEffectSpec` 内容:
* The `GameplayEffect` class that this `GameplayEffect` was created from.
* The level of this `GameplayEffectSpec`. Usually the same as the level of the ability that created the `GameplayEffectSpec` but can be different.
* The duration of the `GameplayEffectSpec`. Defaults to the duration of the `GameplayEffect` but can be different.
* The period of the `GameplayEffectSpec` for periodic effects. Defaults to the period of the `GameplayEffect` but can be different.
* The current stack count of this `GameplayEffectSpec`. The stack limit is on the `GameplayEffect`.
* The [`GameplayEffectContextHandle`](#concepts-ge-context) tells us who created this `GameplayEffectSpec`.
* `Attributes` that were captured at the time of the `GameplayEffectSpec`'s creation due to snapshotting.
* `DynamicGrantedTags` that the `GameplayEffectSpec` grants to the Target in addition to the `GameplayTags` that the `GameplayEffect` grants.
* `DynamicAssetTags` that the `GameplayEffectSpec` has in addition to the `AssetTags` that the `GameplayEffect` has.
* `SetByCaller` `TMaps`.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-spec-setbycaller"></a>
##### 4.5.9.1 SetByCallers
`SetByCallers` 允许 `GameplayEffectSpec` 携带与`GameplayTag` 或 `FName` 有关的float数值. 它们被存储在`GameplayEffectSpec`中各自的`TMaps`: `TMap<FGameplayTag, float>` 和 `TMap<FName, float>`. 这些可以被用于`GameplayEffect`上的 `Modifiers` 或作为携带floats的通用手段. 通常传递ability内部生成的数值给 [`GameplayEffectExecutionCalculations`](#concepts-ge-ec) 或 [`ModifierMagnitudeCalculations`](#concepts-ge-mmc) 参考 `SetByCallers`.

| `SetByCaller` Use | Notes                                                                                                                                                                                                                                                                                                                                                                                                                               |
| ----------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Modifiers`       | 必须提前在 `GameplayEffect` class中定义. 仅可使用 `GameplayTag` 版. 如果在 `GameplayEffect` class定义了一个单在  `GameplayEffectSpec` 中并没有相应的Tag，float键值对, 游戏会有个运行时错误并返回 0. 在 `Divide` 操作时有很大风险. 参考 [`Modifiers`](#concepts-ge-mods). |
| Elsewhere         | 不需要在任何地方提前定义. 读取一个`GameplayEffectSpec`中并不存在的 `SetByCaller` 会返回开发者定义的默认值并弹出一个警告.                                                                                                                                                                                                                                      |

要在蓝图中定义 `SetByCaller` 值, 使用 Blueprint 节点并选择你期望的版本 (`GameplayTag` or `FName`):

![Assigning SetByCaller](https://github.com/tranek/GASDocumentation/raw/master/Images/setbycaller.png)

在蓝图中读取`SetByCaller` 值, 你需要在自己的Blueprint Library中定义节点.

在c++中定义 `SetByCaller`, 参考如下 (`GameplayTag` or `FName`):

```c++
void FGameplayEffectSpec::SetSetByCallerMagnitude(FName DataName, float Magnitude);
```
```c++
void FGameplayEffectSpec::SetSetByCallerMagnitude(FGameplayTag DataTag, float Magnitude);
```

在c++中读取 `SetByCaller` 值, 参考如下 (`GameplayTag` or `FName`):

```c++
float GetSetByCallerMagnitude(FName DataName, bool WarnIfNotFound = true, float DefaultIfNotFound = 0.f) const;
```
```c++
float GetSetByCallerMagnitude(FGameplayTag DataTag, bool WarnIfNotFound = true, float DefaultIfNotFound = 0.f) const;
```

我建议使用 `GameplayTag` 版本而不是 `FName`. 这可以防止蓝图中的拼写错误.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-context"></a>
#### 4.5.10 Gameplay Effect Context
[`GameplayEffectContext`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FGameplayEffectContext/index.html) 结构中保存有关于 `GameplayEffectSpec's` 发起者 和 [`TargetData`](#concepts-targeting-data). 这也是一个很好的子类信息，可以在[`ModifierMagnitudeCalculations`](#concepts-ge-mmc) / [`GameplayEffectExecutionCalculations`](#concepts-ge-ec), [`AttributeSets`](#concepts-as), 和 [`GameplayCues`](#concepts-gc)中传递任意数据.

要实现 `GameplayEffectContext`:

1. 继承 `FGameplayEffectContext`
1. 重载 `FGameplayEffectContext::GetScriptStruct()`
1. 重载 `FGameplayEffectContext::Duplicate()`
1. 重载 `FGameplayEffectContext::NetSerialize()` 如果你有新的需要同步的数据
1. 在子类中实现 `TStructOpsTypeTraits`
1. 重载 `AllocGameplayEffectContext()` 在你的 [`AbilitySystemGlobals`](#concepts-asg) class 并返回一个你子类的新的object

[GASShooter](https://github.com/tranek/GASShooter) 使用子类 `GameplayEffectContext` 去添加可以在`GameplayCues`中获取的 `TargetData`, 专门针对霰弹枪，因为它可以击中多个敌人.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-mmc"></a>
#### 4.5.11 Modifier Magnitude Calculation
[`ModifierMagnitudeCalculations`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayModMagnitudeCalculation/index.html) (`ModMagCalc` or `MMC`) 是功能强大的类， 用于`GameplayEffects`中 [`Modifiers`](#concepts-ge-mods). 它们的功能与 [`GameplayEffectExecutionCalculations`](#concepts-ge-ec)类似，但功能较弱，最重要的是它们可以预测[predicted](#concepts-p). 它们唯一的目的是从 `CalculateBaseMagnitude_Implementation()`返回一个float数. 你可以在蓝图或c++中继承重载它.

`MMCs` 可以在 `GameplayEffects`任何持续时间内使用 - `Instant`, `Duration`, `Infinite`, or `Periodic`.

`MMCs'` 优势在于它们能捕获`Source`上任何数量的`Attributes`值，并支持完全访问`GameplayEffectSpec`来读取`GameplayTags` and `SetByCallers`. `Attributes` 可以是快照，也可以不是. 创建 GameplayEffectSpec 时捕获快照属性，而应用 GameplayEffectSpec 时捕获非快照属性，当无限和持续时间 GameplayEffects 的属性更改时自动更新. 捕获 `Attributes` 会根据当前`ASC`上现有mods重新计算  `CurrentValue`. 此重新计算 **不会** 运行`AbilitySet`里的 [`PreAttributeChange()`](#concepts-as-preattributechange)，所以任何clamping操作此处都需要再做一次.

| Snapshot | Source or Target | Captured on `GameplayEffectSpec` | Automatically updates when `Attribute` changes for `Infinite` or `Duration` `GE`（自动更新） |
| -------- | ---------------- | -------------------------------- | -------------------------------------------------------------------------------- |
| Yes      | Source           | Creation（创建时）                | No                                                                               |
| Yes      | Target           | Application（应用时）             | No                                                                               |
| No       | Source           | Application                      | Yes                                                                              |
| No       | Target           | Application                      | Yes                                                                              |

`MMC` 生成的float值可以进一步被  `GameplayEffect's` `Modifier` 通过系数、前项、后项参数修改.

例如 `MMC` 捕获目标法力值`Attribute` 减少其中毒效果，其中减少量根据目标拥有的法力值和目标可能有的tag变化:
```c++
UPAMMC_PoisonMana::UPAMMC_PoisonMana()
{

	//ManaDef defined in header FGameplayEffectAttributeCaptureDefinition ManaDef;
	ManaDef.AttributeToCapture = UPAAttributeSetBase::GetManaAttribute();
	ManaDef.AttributeSource = EGameplayEffectAttributeCaptureSource::Target;
	ManaDef.bSnapshot = false;

	//MaxManaDef defined in header FGameplayEffectAttributeCaptureDefinition MaxManaDef;
	MaxManaDef.AttributeToCapture = UPAAttributeSetBase::GetMaxManaAttribute();
	MaxManaDef.AttributeSource = EGameplayEffectAttributeCaptureSource::Target;
	MaxManaDef.bSnapshot = false;

	RelevantAttributesToCapture.Add(ManaDef);
	RelevantAttributesToCapture.Add(MaxManaDef);
}

float UPAMMC_PoisonMana::CalculateBaseMagnitude_Implementation(const FGameplayEffectSpec & Spec) const
{
	// Gather the tags from the source and target as that can affect which buffs should be used
	const FGameplayTagContainer* SourceTags = Spec.CapturedSourceTags.GetAggregatedTags();
	const FGameplayTagContainer* TargetTags = Spec.CapturedTargetTags.GetAggregatedTags();

	FAggregatorEvaluateParameters EvaluationParameters;
	EvaluationParameters.SourceTags = SourceTags;
	EvaluationParameters.TargetTags = TargetTags;

	float Mana = 0.f;
	GetCapturedAttributeMagnitude(ManaDef, Spec, EvaluationParameters, Mana);
	Mana = FMath::Max<float>(Mana, 0.0f);

	float MaxMana = 0.f;
	GetCapturedAttributeMagnitude(MaxManaDef, Spec, EvaluationParameters, MaxMana);
	MaxMana = FMath::Max<float>(MaxMana, 1.0f); // Avoid divide by zero

	float Reduction = -20.0f;
	if (Mana / MaxMana > 0.5f)
	{
		// Double the effect if the target has more than half their mana
		Reduction *= 2;
	}
	
	if (TargetTags->HasTagExact(FGameplayTag::RequestGameplayTag(FName("Status.WeakToPoisonMana"))))
	{
		// Double the effect if the target is weak to PoisonMana
		Reduction *= 2;
	}
	
	return Reduction;
}
```

如果你没有在`MMC`的构造函数中将`FGameplayEffectAttributeCaptureDefinition` 添加到 `RelevantAttributesToCapture`但尝试去捕获 `Attributes`, 你会得到一个丢失Spec的错误. 如果你不需要捕获 `Attributes`, 你不需要添加任何东西到 `RelevantAttributesToCapture`.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-ec"></a>
#### 4.5.12 Gameplay Effect Execution Calculation
[`GameplayEffectExecutionCalculations`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayEffectExecutionCalculat-/index.html) (`ExecutionCalculation`, `Execution` (你会经常在插件的源码中看到这个单词), 或者 `ExecCalc`) 是 `GameplayEffects` 更改 `ASC`时非常有用的方法. 如 [`ModifierMagnitudeCalculations`](#concepts-ge-mmc), 它们可以捕获 `Attributes` 并选择性快照. 与 `MMCs`不同, 它们可以捕获多个 `Attribute` 并执行程序员想做的任何事. 这种强大功能和灵活性的缺点是它们[无法预测](#concepts-p) 且必须在 C++中实现.

`ExecutionCalculations` 仅可由 `Instant` 和 `Periodic` `GameplayEffects`使用. 任何名字带有 'Execute' 一词的通常都指的是这两种类型.

快照在创建`GameplayEffectSpec`时捕获`Attribute` 而非快照是在作用时捕获.  捕获 `Attributes` 会根据当前`ASC`上现有mods重新计算  `CurrentValue`. 此重新计算 **不会** 运行`AbilitySet`里的 [`PreAttributeChange()`](#concepts-as-preattributechange)，所以任何clamping操作此处都需要再做一次.

| Snapshot | Source or Target | Captured on `GameplayEffectSpec` |
| -------- | ---------------- | -------------------------------- |
| Yes      | Source           | Creation                         |
| Yes      | Target           | Application                      |
| No       | Source           | Application                      |
| No       | Target           | Application                      |

要设置`Attribute`捕获时, 我们遵循由Epic's ActionRPG Sample Project 制定的模式，定义一个结构体，保存并定义我们如何捕获属性并在结构体的构造函数中创建它的一个副本. 每个 `ExecCalc`都会有一个这样的结构. **注意:** ：每个结构体都需要一个唯一的名称，因为它们共享相同的命名空间。对结构使用相同的名称将导致捕获属性时出现错误行为 (通常会捕获错误的 `Attributes`值).

对 `Local Predicted（本地预测）`, `Server Only(仅服务器)`, 和 `Server Initiated（服务器实例化）` 的[`GameplayAbilities`](#concepts-ga),  `ExecCalc` 仅在Server被调用.

根据 `Source`源 和 `Target`目标上许多属性读取的复杂公式来计算受到的伤害是`ExecCalc`最常见的示例. 当前示例工程有一个简单的`ExecCalc` 读取 `GameplayEffectSpec's` [`SetByCaller`](#concepts-ge-spec-setbycaller)值计算伤害，然后根据从`Target`获取的护甲值减轻伤害. 参考 `GDDamageExecCalculation.cpp/.h`.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-ec-senddata"></a>
##### 4.5.12.1 Sending Data to Execution Calculations
除了捕获`Attributes`之外，还有几种方法可以将数据发送到 `ExecutionCalculation`.

<a name="concepts-ge-ec-senddata-setbycaller"></a>
###### 4.5.12.1.1 SetByCaller
任何 [`GameplayEffectSpec`设置的`SetByCallers`](#concepts-ge-spec-setbycaller) 都可在 `ExecutionCalculation`中直接读取.

```c++
const FGameplayEffectSpec& Spec = ExecutionParams.GetOwningSpec();
float Damage = FMath::Max<float>(Spec.GetSetByCallerMagnitude(FGameplayTag::RequestGameplayTag(FName("Data.Damage")), false, -1.0f), 0.0f);
```

<a name="concepts-ge-ec-senddata-backingdataattribute"></a>
###### 4.5.12.1.2 Backing Data Attribute Calculation Modifier
如果你想给`GameplayEffect`硬编码一些值, 你可以使用`CalculationModifier` 传递它们，该 CalculationModifier 使用捕获的属性之一作为支持数据。.

在此屏幕截图示例中，我们将 `50` 添加到捕获的伤害属性中。您还可以将其设置为 `Override` 以仅接受硬编码值.

![Backing Data Attribute Calculation Modifier](https://github.com/tranek/GASDocumentation/raw/master/Images/calculationmodifierbackingdataattribute.png)

The `ExecutionCalculation` reads this value in when it captures the `Attribute`.

```c++
float Damage = 0.0f;
// Capture optional damage value set on the damage GE as a CalculationModifier under the ExecutionCalculation
ExecutionParams.AttemptCalculateCapturedAttributeMagnitude(DamageStatics().DamageDef, EvaluationParameters, Damage);
```

<a name="concepts-ge-ec-senddata-backingdatatempvariable"></a>
###### 4.5.12.1.3 Backing Data Temporary Variable Calculation Modifier
如果要将值硬编码到 `GameplayEffect`，可以使用 `CalculationModifier` 传递它们，该 `CalculationModifier` 使用`Temporary Variable` or `Transient Aggregator`同C++中调用. `Temporary Variable` 与 `GameplayTag`关联.

在此屏幕截图示例中，我们将使用 `Data.Damage` `GameplayTag` 将`50`添加到 `Temporary Variable`.

![Backing Data Temporary Variable Calculation Modifier](https://github.com/tranek/GASDocumentation/raw/master/Images/calculationmodifierbackingdatatempvariable.png)

Add backing `Temporary Variables` to your `ExecutionCalculation`'s constructor:

```c++
ValidTransientAggregatorIdentifiers.AddTag(FGameplayTag::RequestGameplayTag("Data.Damage"));
```

`ExecutionCalculation` 使用类似捕获`Attribute`的特殊函数读取该值.

```c++
float Damage = 0.0f;
ExecutionParams.AttemptCalculateTransientAggregatorMagnitude(FGameplayTag::RequestGameplayTag("Data.Damage"), EvaluationParameters, Damage);
```

<a name="concepts-ge-ec-senddata-effectcontext"></a>
###### 4.5.12.1.4 Gameplay Effect Context
你可以使用[`GameplayEffectSpec`中的`GameplayEffectContext`](#concepts-ge-context)发送自定义数据到 `ExecutionCalculation`.

在`ExecutionCalculation` 中你可以从`FGameplayEffectCustomExecutionParameters`访问`EffectContext`.

```c++
const FGameplayEffectSpec& Spec = ExecutionParams.GetOwningSpec();
FGSGameplayEffectContext* ContextHandle = static_cast<FGSGameplayEffectContext*>(Spec.GetContext().Get());
```

如果你想修改 `GameplayEffectSpec` 或 `EffectContext`上的一些东西:

```c++
FGameplayEffectSpec* MutableSpec = ExecutionParams.GetOwningSpecForPreExecuteMod();
FGSGameplayEffectContext* ContextHandle = static_cast<FGSGameplayEffectContext*>(MutableSpec->GetContext().Get());
```

如果要修改`ExecutionCalculation`中的 `GameplayEffectSpec`需要格外小心. 参考注释 `GetOwningSpecForPreExecuteMod()`.

```c++
/** Non const access. Be careful with this, especially when modifying a spec after attribute capture. */
FGameplayEffectSpec* GetOwningSpecForPreExecuteMod() const;
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-car"></a>
#### 4.5.13 Custom Application Requirement
[`CustomApplicationRequirement`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayEffectCustomApplication-/index.html) (`CAR`) classes 给设计者提供了是否应用 `GameplayEffect` 的高级控制，而不是简单的 `GameplayTag` 检查. 这些可以在蓝图中重载`CanApplyGameplayEffect()` 实现或者在 C++ 中重载 `CanApplyGameplayEffect_Implementation()`.

何时使用`CARs`示例:
* `Target` 需要一些确定数量的 `Attribute`
* `Target` 需要一些确定数量的`GameplayEffect`堆栈

`CARs` 还可以做一些更高级的事比如检查该类型 `GameplayEffect` 实例是否已存在于`Target` 和修改已存在的实例上 [ duration](#concepts-ge-duration) 而不是应用一个新的实例(return false for `CanApplyGameplayEffect()`).

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-cost"></a>
#### 4.5.14 Cost Gameplay Effect
[`GameplayAbilities`](#concepts-ga) 有一个可选的 `GameplayEffect` 被专门设计为使用该能力的成本（the cost of the ability）. Costs 是指一个 `ASC`的多少 `Attribute` 才能激活该能力. 如果 `GA` 无法承担消耗`Cost GE`, 它们就不会被激活. 这个 `Cost GE` 需要是 `Instant` `GameplayEffect` 类型并且拥有一个或多个 `Modifiers` 去减少`Attributes`. 默认情况下, `Cost GEs` 是可以被预测的，建议保留该功能，这意味着不需要执行 `ExecutionCalculations`. `MMCs` 完全可以接受并鼓励复杂的 cost 计算.

刚开始, 你可能会为每个`GA`提供一个单独的`Cost GE`. 一种更先进的技术是为多个`GAs`重用一个`Cost GE` for multiple `GAs`，只需要修改从`Cost GE`创建的 `GameplayEffectSpec`  里 `GA`的特定数据(the cost value is defined on the `GA`). **仅对 `Instanced` 类型abalities有效.**

两种重用`Cost GE`的方法:

1. **使用一个 `MMC`.** T这是最简单的方法. 创建一个 [`MMC`](#concepts-ge-mmc)并从 `GameplayEffectSpec` 中获取的`GameplayAbility` 实例上读取cost值.

```c++
float UPGMMC_HeroAbilityCost::CalculateBaseMagnitude_Implementation(const FGameplayEffectSpec & Spec) const
{
	const UPGGameplayAbility* Ability = Cast<UPGGameplayAbility>(Spec.GetContext().GetAbilityInstance_NotReplicated());

	if (!Ability)
	{
		return 0.0f;
	}

	return Ability->Cost.GetValueAtLevel(Ability->GetAbilityLevel());
}
```

在这个例子中cost是我添加的`GameplayAbility`子类中的 `FScalableFloat` .
```c++
UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cost")
FScalableFloat Cost;
```

![Cost GE With MMC](https://github.com/tranek/GASDocumentation/raw/master/Images/costmmc.png)

2. **重载 `UGameplayAbility::GetCostGameplayEffect()`.** 重载此函数 并 [创建一个运行时 `GameplayEffect` ](#concepts-ge-dynamic) 来读取 `GameplayAbility`上的cost值.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-cooldown"></a>
#### 4.5.15 Cooldown Gameplay Effect
[`GameplayAbilities`](#concepts-ga) 拥有一个专门用来冷却技能的 `GameplayEffect` . Cooldowns 决定了使用后多久才能下次再使用. 如果一个 `GA` 还处于冷却状态，则不能被激活. `Cooldown GE` 必须为 `Duration` `GameplayEffect` 类型，每个ability slot或每个`GameplayAbility`都没有 `Modifiers` 且只有唯一一个 `GameplayTag` (如果你的游戏有分配给共享冷却时间的slot的可呼唤能力i)再 `GameplayEffect's` `GrantedTags` ("`Cooldown Tag`"). `GA` 真正检查的时是否存在`Cooldown Tag` 而不是`Cooldown GE`实例. 默认情况下, `Cooldown GEs` 是可预测的，建议保持该功能，这意味这不需要执行 `ExecutionCalculations`. `MMCs` 完全可以接受并鼓励复杂的 cooldown 计算.

刚开始, 你可能会为每个`GA`提供一个单独的 `Cooldown GE` . 一种更先进的技术是为多个`GAs`重用一个 `Cooldown GE`，只需要修改从`Cooldown GE`创建的 `GameplayEffectSpec`  里 `GA`的特定数据(the cooldown duration and the `Cooldown Tag` are defined on the `GA`). **仅对 `Instanced`类型 abilities有效.**

两种重用 `Cooldown GE`方式 :

1. **使用 [`SetByCaller`](#concepts-ge-spec-setbycaller).** 这是最简单的方式. 设置带有`GameplayTag`共享 `Cooldown GE` 时间到 `SetByCaller` . 在你的 `GameplayAbility` 子类中, 定义一个  float / `FScalableFloat` 类型 duration, 用于独有`Cooldown Tag`的 `FGameplayTagContainer`, 用于返回`Cooldown Tag` 和 `Cooldown GE's` tags 的临时`FGameplayTagContainer`.
```c++
UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FScalableFloat CooldownDuration;

UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FGameplayTagContainer CooldownTags;

// Temp container that we will return the pointer to in GetCooldownTags().
// This will be a union of our CooldownTags and the Cooldown GE's cooldown tags.
UPROPERTY(Transient)
FGameplayTagContainer TempCooldownTags;
```

然后重载 `UGameplayAbility::GetCooldownTags()` 返回 `Cooldown Tags` 任何存在的 `Cooldown GE's` tags的并集.
```c++
const FGameplayTagContainer * UPGGameplayAbility::GetCooldownTags() const
{
	FGameplayTagContainer* MutableTags = const_cast<FGameplayTagContainer*>(&TempCooldownTags);
	MutableTags->Reset(); // MutableTags writes to the TempCooldownTags on the CDO so clear it in case the ability cooldown tags change (moved to a different slot)
	const FGameplayTagContainer* ParentTags = Super::GetCooldownTags();
	if (ParentTags)
	{
		MutableTags->AppendTags(*ParentTags);
	}
	MutableTags->AppendTags(CooldownTags);
	return MutableTags;
}
```

最后, 重载 `UGameplayAbility::ApplyCooldown()` 来注入 `Cooldown Tags` 并添加 `SetByCaller` 到 `GameplayEffectSpec`的cooldown.
```c++
void UPGGameplayAbility::ApplyCooldown(const FGameplayAbilitySpecHandle Handle, const FGameplayAbilityActorInfo * ActorInfo, const FGameplayAbilityActivationInfo ActivationInfo) const
{
	UGameplayEffect* CooldownGE = GetCooldownGameplayEffect();
	if (CooldownGE)
	{
		FGameplayEffectSpecHandle SpecHandle = MakeOutgoingGameplayEffectSpec(CooldownGE->GetClass(), GetAbilityLevel());
		SpecHandle.Data.Get()->DynamicGrantedTags.AppendTags(CooldownTags);
		SpecHandle.Data.Get()->SetSetByCallerMagnitude(FGameplayTag::RequestGameplayTag(FName(  OurSetByCallerTag  )), CooldownDuration.GetValueAtLevel(GetAbilityLevel()));
		ApplyGameplayEffectSpecToOwner(Handle, ActorInfo, ActivationInfo, SpecHandle);
	}
}
```

在这张图中, cooldown的 duration `Modifier` 被设置为拥有`Data.Cooldown``Data Tag`的 `SetByCaller`. 在代码上`Data.Cooldown` 就是 `OurSetByCallerTag`.

![Cooldown GE with SetByCaller](https://github.com/tranek/GASDocumentation/raw/master/Images/cooldownsbc.png)

2. **使用 [`MMC`](#concepts-ge-mmc).** 除了将`SetByCaller`作为duration设置到`Cooldown GE`和 `ApplyCooldown`外，其他与上面的设置并无不同. 相反，将duration设置为 `Custom Calculation Class`并指向新创建的  `MMC`.
```c++
UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FScalableFloat CooldownDuration;

UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FGameplayTagContainer CooldownTags;

// Temp container that we will return the pointer to in GetCooldownTags().
// This will be a union of our CooldownTags and the Cooldown GE's cooldown tags.
UPROPERTY(Transient)
FGameplayTagContainer TempCooldownTags;
```

重载 `UGameplayAbility::GetCooldownTags()` 返回 `Cooldown Tags` 任何存在的 `Cooldown GE's` tags的并集.
```c++
const FGameplayTagContainer * UPGGameplayAbility::GetCooldownTags() const
{
	FGameplayTagContainer* MutableTags = const_cast<FGameplayTagContainer*>(&TempCooldownTags);
	MutableTags->Reset(); // MutableTags writes to the TempCooldownTags on the CDO so clear it in case the ability cooldown tags change (moved to a different slot)
	const FGameplayTagContainer* ParentTags = Super::GetCooldownTags();
	if (ParentTags)
	{
		MutableTags->AppendTags(*ParentTags);
	}
	MutableTags->AppendTags(CooldownTags);
	return MutableTags;
}
```

Finally, override `UGameplayAbility::ApplyCooldown()` to inject our `Cooldown Tags` into the cooldown `GameplayEffectSpec`.
```c++
void UPGGameplayAbility::ApplyCooldown(const FGameplayAbilitySpecHandle Handle, const FGameplayAbilityActorInfo * ActorInfo, const FGameplayAbilityActivationInfo ActivationInfo) const
{
	UGameplayEffect* CooldownGE = GetCooldownGameplayEffect();
	if (CooldownGE)
	{
		FGameplayEffectSpecHandle SpecHandle = MakeOutgoingGameplayEffectSpec(CooldownGE->GetClass(), GetAbilityLevel());
		SpecHandle.Data.Get()->DynamicGrantedTags.AppendTags(CooldownTags);
		ApplyGameplayEffectSpecToOwner(Handle, ActorInfo, ActivationInfo, SpecHandle);
	}
}
```

```c++
float UPGMMC_HeroAbilityCooldown::CalculateBaseMagnitude_Implementation(const FGameplayEffectSpec & Spec) const
{
	const UPGGameplayAbility* Ability = Cast<UPGGameplayAbility>(Spec.GetContext().GetAbilityInstance_NotReplicated());

	if (!Ability)
	{
		return 0.0f;
	}

	return Ability->CooldownDuration.GetValueAtLevel(Ability->GetAbilityLevel());
}
```

![Cooldown GE with MMC](https://github.com/tranek/GASDocumentation/raw/master/Images/cooldownmmc.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-cooldown-tr"></a>
##### 4.5.15.1 Get the Cooldown Gameplay Effect's Remaining Time
```c++
bool APGPlayerState::GetCooldownRemainingForTag(FGameplayTagContainer CooldownTags, float & TimeRemaining, float & CooldownDuration)
{
	if (AbilitySystemComponent && CooldownTags.Num() > 0)
	{
		TimeRemaining = 0.f;
		CooldownDuration = 0.f;

		FGameplayEffectQuery const Query = FGameplayEffectQuery::MakeQuery_MatchAnyOwningTags(CooldownTags);
		TArray< TPair<float, float> > DurationAndTimeRemaining = AbilitySystemComponent->GetActiveEffectsTimeRemainingAndDuration(Query);
		if (DurationAndTimeRemaining.Num() > 0)
		{
			int32 BestIdx = 0;
			float LongestTime = DurationAndTimeRemaining[0].Key;
			for (int32 Idx = 1; Idx < DurationAndTimeRemaining.Num(); ++Idx)
			{
				if (DurationAndTimeRemaining[Idx].Key > LongestTime)
				{
					LongestTime = DurationAndTimeRemaining[Idx].Key;
					BestIdx = Idx;
				}
			}

			TimeRemaining = DurationAndTimeRemaining[BestIdx].Key;
			CooldownDuration = DurationAndTimeRemaining[BestIdx].Value;

			return true;
		}
	}

	return false;
}
```

**注意:** 查询客户端的剩余冷却时间需要客户端能够接收复制的 `GameplayEffects`。这将取决于其 ASC 的 [复制模式](#concepts-asc-rm).

<a name="concepts-ge-cooldown-listen"></a>
##### 4.5.15.2 Listening for Cooldown Begin and End
你可以绑定事件到 `AbilitySystemComponent->OnActiveGameplayEffectAddedDelegateToSelf` 或 `AbilitySystemComponent->RegisterGameplayTagEvent(CooldownTag, EGameplayTagEventType::NewOrRemoved)`来监听cooldown开始. 我建议监听何时添加 `Cooldown GE`，因为你还可以访问到它的 `GameplayEffectSpec` . 由此你可以确定 `Cooldown GE` 是本地预测的还是服务器预测的.

你可以绑定事件到`AbilitySystemComponent->OnAnyGameplayEffectRemovedDelegate()` 或 `AbilitySystemComponent->RegisterGameplayTagEvent(CooldownTag, EGameplayTagEventType::NewOrRemoved)`来监听cooldown结束. 我建议监听何时 `Cooldown Tag` 被移除，因为当服务器修正corrected `Cooldown GE` 出现时, 尽管我们在冷却中他也会删除我们本地预测的那个导致 `OnAnyGameplayEffectRemovedDelegate()` 被触发. 在删除预测的`Cooldown GE`和应用服务器校正的`Cooldown GE`时 `Cooldown Tag` 不会改变.

**注意:** 在客户端上监听 `GameplayEffect`的添加和移除需要它们能接收到`GameplayEffects`的复制. 这将取决于其 ASC 的 [复制模式](#concepts-asc-rm).

示例工程包含一个自定义的蓝图节点用于监听cooldown的开始和结束. HUD UMG Widget 中使用它更新 Meteor's 的剩余冷却时间. 这个 `AsyncTask` 会一直存在直到调用 `EndTask()`, 我们需要在UMG Widget的 `Destruct` 调用该事件. 参考 [`AsyncTaskCooldownChanged.h/cpp`](Source/GASDocumentation/Private/Characters/Abilities/AsyncTaskCooldownChanged.cpp).

![Listen for Cooldown Change BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/cooldownchange.png)

<a name="concepts-ge-cooldown-prediction"></a>
##### 4.5.15.3 Predicting Cooldowns
目前还无法真正预测准确的Cooldowns. 当我们本地预测到`Cooldown GE`作用时我们可以开启UI上冷却计时，但`GameplayAbility's`的实际剩余冷却时间与服务器上剩余时间无关. 根据玩家的网络延迟，可能会出现本地的倒计时结束了但是服务器依然还处于冷却状态，这会阻止该功能重新激活直到服务器上倒计时结束.

示例工程在本地预测到冷却时间时将Meteor 的 UI 图标变灰，然后再服务器校正的`Cooldown GE`开始后再处理后续.

这样做的一个游戏结果是，高延迟的玩家在短冷却能力上的射速比低延迟的玩家低，这使他们处于劣势. Fortnite 避免了这种情况，因为他们的武器不使用cooldown `GameplayEffects`，而是自定义了一个 bookkeeping .

Epic 希望有一天在 GAS 的未来迭代中实现真正的预测冷却时间(当本地冷却时间到期但服务器仍处于冷却时间时，玩家可以激活 `GameplayAbility`) .

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-duration"></a>
#### 4.5.16 Changing Active Gameplay Effect Duration
为了修改`Cooldown GE` 或任何 `Duration` `GameplayEffect`的剩余冷却时间, 我们需要更改`GameplayEffectSpec`的 `Duration`, 更新它的 `StartServerWorldTime`,  `CachedStartServerWorldTime` 和 `StartWorldTime`, 并使用`CheckDuration()`做duration检查. 在服务器上执行此操作并将 `FActiveGameplayEffect` 标记为dirty 会将更改复制到客户端.
**注意:** 这确实涉及到 `const_cast` 并且可能不是 Epic 改变duration的预期方式, 但目前来说它很有效.

```c++
bool UPAAbilitySystemComponent::SetGameplayEffectDurationHandle(FActiveGameplayEffectHandle Handle, float NewDuration)
{
	if (!Handle.IsValid())
	{
		return false;
	}

	const FActiveGameplayEffect* ActiveGameplayEffect = GetActiveGameplayEffect(Handle);
	if (!ActiveGameplayEffect)
	{
		return false;
	}

	FActiveGameplayEffect* AGE = const_cast<FActiveGameplayEffect*>(ActiveGameplayEffect);
	if (NewDuration > 0)
	{
		AGE->Spec.Duration = NewDuration;
	}
	else
	{
		AGE->Spec.Duration = 0.01f;
	}

	AGE->StartServerWorldTime = ActiveGameplayEffects.GetServerWorldTime();
	AGE->CachedStartServerWorldTime = AGE->StartServerWorldTime;
	AGE->StartWorldTime = ActiveGameplayEffects.GetWorldTime();
	ActiveGameplayEffects.MarkItemDirty(*AGE);
	ActiveGameplayEffects.CheckDuration(Handle);

	AGE->EventSet.OnTimeChanged.Broadcast(AGE->Handle, AGE->StartWorldTime, AGE->GetDuration());
	OnGameplayEffectDurationChange(*AGE);

	return true;
}
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-dynamic"></a>
#### 4.5.17 Creating Dynamic Gameplay Effects at Runtime
运行时创建动态的`GameplayEffects`是高级内容. 你不应该经常这么做.

仅 `Instant` `GameplayEffects` 类型可以在 C++中被从头创建. `Duration` 和 `Infinite`类型 `GameplayEffects` 不可以运行时动态创建，因为当同步时，会找不到 `GameplayEffect` class 的定义. 为了实现这个功能， 你应该像在编辑器中那样创建一个原型archetype `GameplayEffect` . 然后在运行时自定义你需要的 `GameplayEffectSpec` 实例.

运行时创建的`Instant` `GameplayEffects`也可以从本地预测的 [local predicted](#concepts-p) `GameplayAbility`中调用. 然而，目前尚不清楚动态创建是否会产生副作用.

##### Examples

示例工程创建了一个当角色 在`AttributeSet`受到致命伤害时，将金币和经验返送回杀手的例子.

```c++
// Create a dynamic instant Gameplay Effect to give the bounties
UGameplayEffect* GEBounty = NewObject<UGameplayEffect>(GetTransientPackage(), FName(TEXT("Bounty")));
GEBounty->DurationPolicy = EGameplayEffectDurationType::Instant;

int32 Idx = GEBounty->Modifiers.Num();
GEBounty->Modifiers.SetNum(Idx + 2);

FGameplayModifierInfo& InfoXP = GEBounty->Modifiers[Idx];
InfoXP.ModifierMagnitude = FScalableFloat(GetXPBounty());
InfoXP.ModifierOp = EGameplayModOp::Additive;
InfoXP.Attribute = UGDAttributeSetBase::GetXPAttribute();

FGameplayModifierInfo& InfoGold = GEBounty->Modifiers[Idx + 1];
InfoGold.ModifierMagnitude = FScalableFloat(GetGoldBounty());
InfoGold.ModifierOp = EGameplayModOp::Additive;
InfoGold.Attribute = UGDAttributeSetBase::GetGoldAttribute();

Source->ApplyGameplayEffectToSelf(GEBounty, 1.0f, Source->MakeEffectContext());
```

第二个例子展示了在本地预测的`GameplayAbility`内创建一个运行时的  `GameplayEffect` . 使用自负风险 (参考代码注释)!

```c++
UGameplayAbilityRuntimeGE::UGameplayAbilityRuntimeGE()
{
	NetExecutionPolicy = EGameplayAbilityNetExecutionPolicy::LocalPredicted;
}

void UGameplayAbilityRuntimeGE::ActivateAbility(const FGameplayAbilitySpecHandle Handle, const FGameplayAbilityActorInfo* ActorInfo, const FGameplayAbilityActivationInfo ActivationInfo, const FGameplayEventData* TriggerEventData)
{
	if (HasAuthorityOrPredictionKey(ActorInfo, &ActivationInfo))
	{
		if (!CommitAbility(Handle, ActorInfo, ActivationInfo))
		{
			EndAbility(Handle, ActorInfo, ActivationInfo, true, true);
		}

		// Create the GE at runtime.
		UGameplayEffect* GameplayEffect = NewObject<UGameplayEffect>(GetTransientPackage(), TEXT("RuntimeInstantGE"));
		GameplayEffect->DurationPolicy = EGameplayEffectDurationType::Instant; // Only instant works with runtime GE.

		// Add a simple scalable float modifier, which overrides MyAttribute with 42.
		// In real world applications, consume information passed via TriggerEventData.
		const int32 Idx = GameplayEffect->Modifiers.Num();
		GameplayEffect->Modifiers.SetNum(Idx + 1);
		FGameplayModifierInfo& ModifierInfo = GameplayEffect->Modifiers[Idx];
		ModifierInfo.Attribute.SetUProperty(UMyAttributeSet::GetMyModifiedAttribute());
		ModifierInfo.ModifierMagnitude = FScalableFloat(42.f);
		ModifierInfo.ModifierOp = EGameplayModOp::Override;

		// Apply the GE.

		// Create the GESpec here to avoid the behavior of ASC to create GESpecs from the GE class default object.
		// Since we have a dynamic GE here, this would create a GESpec with the base GameplayEffect class, so we
		// would lose our modifiers. Attention: It is unknown, if this "hack" done here can have drawbacks!
		// The spec prevents the GE object being collected by the GarbageCollector, since the GE is a UPROPERTY on the spec.
		FGameplayEffectSpec* GESpec = new FGameplayEffectSpec(GameplayEffect, {}, 0.f); // "new", since lifetime is managed by a shared ptr within the handle
		ApplyGameplayEffectSpecToOwner(Handle, ActorInfo, ActivationInfo, FGameplayEffectSpecHandle(GESpec));
	}
	EndAbility(Handle, ActorInfo, ActivationInfo, false, false);
}
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-containers"></a>
#### 4.5.18 Gameplay Effect Containers
Epic的 [Action RPG Sample Project](https://www.unrealengine.com/marketplace/en-US/product/action-rpg) 实现了一个 `FGameplayEffectContainer`结构体. 这些不在普通的GAS中，但对于包含 `GameplayEffects` 和 [`TargetData`](#concepts-targeting-data)非常方便. 它自动做了一些工作，比如从`GameplayEffects`创建`GameplayEffectSpecs`，给`GameplayEffectContext`设置默认值. 使用 `GameplayAbility`的  `GameplayEffectContainer`并把它传递给生成的炮弹是非常简洁明了的. 我选择不在随附的示例中实现 `GameplayEffectContainers`以展示在普通的 GAS中没有它该如何工作, 但我强烈建议研究它们并考虑将它们添加到你自己的项目中。.

访问`GameplayEffectContainers`内部的`GESpecs` 做如添加 `SetByCallers`的操作, 展开 `FGameplayEffectContainer` 并通过`GESpecs`中数组的索引来访问它的引用. 这需要你提前知道你想获取内容的在 `GESpec`内数组中的索引 .

![SetByCaller with a GameplayEffectContainer](https://github.com/tranek/GASDocumentation/raw/master/Images/gecontainersetbycaller.png)

`GameplayEffectContainers` 还包含一个可选的有效方法 [targeting](#concepts-targeting-containers).

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga"></a>
### 4.6 Gameplay Abilities

<a name="concepts-ga-definition"></a>
#### 4.6.1 Gameplay Ability Definition
[`GameplayAbilities`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/UGameplayAbility/index.html) (`GA`) 是`Actor`在游戏中可以做的任何行为或技能. 不止一个 `GameplayAbility` 可以被同时激活比如跑步和射击. 这些都可以在 Blueprint 或 C++中实现.

`GameplayAbilities`示例:
* Jumping 跳
* Sprinting 跑
* Shooting a gun 射击
* Passively blocking an attack every X number of seconds 每 X 秒被动阻止一次攻击
* Using a potion 使用药水
* Opening a door 开门
* Collecting a resource 搜集资源
* Constructing a building 建造

不应该使用`GameplayAbilities`实现的:
* Basic movement input 基本的动作输入
* Some interactions with UIs - Don't use a `GameplayAbility` to purchase an item from a store. 一些UI交互

这些不是规定，只是个人建议，你的设计和实现可能有所不同.

`GameplayAbilities` 附带默认功能，能使用level修改属性的更改量或修改 `GameplayAbility` 的功能.

`GameplayAbilities` 在拥有的客户端 和/或 服务器上运行取决于 [`Net Execution Policy`](#concepts-ga-net) 但不是模拟代理 simulated proxies. `Net Execution Policy` 决定一个 `GameplayAbility` 是否可以本地预测 [predicted](#concepts-p). 它们拥有可选的[消耗与冷却](#concepts-ga-commit)的默认行为. `GameplayAbilities` 使用 [`AbilityTasks`](#concepts-at) 执行随时间推移发生的操作，例如事件，等待属性改变，等待玩家选择目标或者使用`Root Motion Source`移动`Character`. **模拟客户端将无法运行 `GameplayAbilities`**. 相反, 当服务器执行ability时，任何视觉上需要在模拟代理上播放的内容（例如蒙太奇动画）都通过`AbilityTasks` 或 [`GameplayCues`](#concepts-gc) 来同步/RPC处理， 来实现声音和粒子等装饰性内容.

所有的 `GameplayAbilities` 都会有自己的 `ActivateAbility()` 函数重载来实现相应的游戏逻辑. 还可以在`GameplayAbility`的完成或取消时在 `EndAbility()` 添加额外逻辑.

一个简单的`GameplayAbility`流程图:
![Simple GameplayAbility Flowchart](https://github.com/tranek/GASDocumentation/raw/master/Images/abilityflowchartsimple.png)


一个稍微复杂的 `GameplayAbility`流程图:
![Complex GameplayAbility Flowchart](https://github.com/tranek/GASDocumentation/raw/master/Images/abilityflowchartcomplex.png)

可以使用多个相互交互的（激活、取消等） `GameplayAbilities`来实现一个复杂的能力.

<a name="concepts-ga-definition-reppolicy"></a>
##### 4.6.1.1 Replication Policy
不要使用此选项. 这个名字会让人误解且你并不需要它. 默认情况下[`GameplayAbilitySpecs`](#concepts-ga-spec)会从服务器复制到拥有的客户端上. 如之前提到的, **`GameplayAbilities` 不会运行在模拟代理上**. 它们使用 `AbilityTasks` 和 `GameplayCues` 来 复制 或 RPC 处理模拟代理机上的视效变化. Epic的 Dave Ratti 表示他[希望将来删除此选项](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89).

<a name="concepts-ga-definition-remotecancel"></a>
##### 4.6.1.2 Server Respects Remote Ability Cancellation
这个选项常常会带来麻烦. 这表示如果客户端的`GameplayAbility` 结束了，先不管是正常结束还是取消，它都会强制服务器上的也结束，不管服务器上的是否执行完了. 后一个问题很重要，特别是对高延迟玩家使用本地预测`GameplayAbilities`. 通常你都会希望关掉此选项.

<a name="concepts-ga-definition-repinputdirectly"></a>
##### 4.6.1.3 Replicate Input Directly
开启此选项后不论press还是release事件都会同步给server. 如果你[将输入绑定到`ASC`](#concepts-ga-input), Epic建议不要使用此功能，而是依赖[`AbilityTasks`](#concepts-at) 内置的已有输入相关的  `Generic Replicated Events`.

Epic的注释:
```c++
/** Direct Input state replication. These will be called if bReplicateInputDirectly is true on the ability and is generally not a good thing to use. (Instead, prefer to use Generic Replicated Events). */
UAbilitySystemComponent::ServerSetInputPressed()
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-input"></a>
#### 4.6.2 Binding Input to the ASC
`ASC` 允许你直接将输入绑定到它身上，并在你授予这些输入时将其分配给`GameplayAbilities`. 分配给 `GameplayAbilities` 的输入在满足对应`GameplayTag`时会自动激活  `GameplayAbilities`. 只有分配了输入操作才能使用一些内置的`AbilityTasks`.

除了分配的操作可以激活 `GameplayAbilities`外, `ASC` 也可以接受通用的 `Confirm` 和 `Cancel` 输入. `AbilityTasks`使用这些特殊的输入来确认或取消一些事情比如[`Target Actors`](#concepts-targeting-actors).

要将输入绑定到 `ASC`, 您必须首先创建一个将输入操作名称转换为byte节的枚举. 枚举的名称必须与项目设置中输入操作名称完全对应. `DisplayName`修改不所谓.

摘自示例项目:
```c++
UENUM(BlueprintType)
enum class EGDAbilityInputID : uint8
{
	// 0 None
	None			UMETA(DisplayName = "None"),
	// 1 Confirm
	Confirm			UMETA(DisplayName = "Confirm"),
	// 2 Cancel
	Cancel			UMETA(DisplayName = "Cancel"),
	// 3 LMB
	Ability1		UMETA(DisplayName = "Ability1"),
	// 4 RMB
	Ability2		UMETA(DisplayName = "Ability2"),
	// 5 Q
	Ability3		UMETA(DisplayName = "Ability3"),
	// 6 E
	Ability4		UMETA(DisplayName = "Ability4"),
	// 7 R
	Ability5		UMETA(DisplayName = "Ability5"),
	// 8 Sprint
	Sprint			UMETA(DisplayName = "Sprint"),
	// 9 Jump
	Jump			UMETA(DisplayName = "Jump")
};
```

如果你把 `ASC` 挂在 `Character`上, 则在 `SetupPlayerInputComponent()` 包含绑定输入到 `ASC`的操作:
```c++
// Bind to AbilitySystemComponent
FTopLevelAssetPath AbilityEnumAssetPath = FTopLevelAssetPath(FName("/Script/GASDocumentation"), FName("EGDAbilityInputID"));
AbilitySystemComponent->BindAbilityActivationToInputComponent(PlayerInputComponent, FGameplayAbilityInputBinds(FString("ConfirmTarget"),
	FString("CancelTarget"), AbilityEnumAssetPath, static_cast<int32>(EGDAbilityInputID::Confirm), static_cast<int32>(EGDAbilityInputID::Cancel)));
```

如果你将  `ASC` 挂在 `PlayerState`上,  `SetupPlayerInputComponent()`内部存在潜在的竞争条件，其中`PlayerState` 可能尚未复制到客户端. 因此, 我建议在收到`OnRep_PlayerState()`和 `SetupPlayerInputComponent()` 时再将输入绑定. 只有`OnRep_PlayerState()` 是并不足够的，因为当 `PlayerController`告诉客户端调用`ClientRestart()`去创建 `InputComponent`前，`Actor`的`InputComponent`是为空的 . 示例项目演示了尝试通过布尔门控过程在两个位置进行绑定，因此它实际上只绑定输入一次.

**注意:** 在示例工程中枚举的 `Confirm` 和 `Cancel`并不匹配项目设置中输入操作的名称 (`ConfirmTarget` 和 `CancelTarget`), 但我们在  `BindAbilityActivationToInputComponent()`中提供了它们之间的映射. 这些特殊的操作本身并不匹配直到我们提供相应映射关系. 枚举中的所有其他输入必须与项目设置中的输入操作名称匹配.

对于仅由一个输入激活的 `GameplayAbilities` (它们会一直存在于同一个"slot" 如 MOBA), 我更喜欢向我的`UGameplayAbility` 子类中添加一个我可以自定义输入的变量. 然后在授予该能力时从 `ClassDefaultObject` 中读取.

<a name="concepts-ga-input-noactivate"></a>
##### 4.6.2.1 Binding to Input without Activating Abilities
如果你不想你的 `GameplayAbilities` 在按下某一个按键时自动激活，但仍希望它们绑定输入操作以便于 `AbilityTasks`一起使用, 你可以在你的`UGameplayAbility` 子类中添加一个bool变量, `bActivateOnInput`, 把默认值设为 `true` 然后重载 `UAbilitySystemComponent::AbilityLocalInputPressed()`.

```c++
void UGSAbilitySystemComponent::AbilityLocalInputPressed(int32 InputID)
{
	// Consume the input if this InputID is overloaded with GenericConfirm/Cancel and the GenericConfim/Cancel callback is bound
	if (IsGenericConfirmInputBound(InputID))
	{
		LocalInputConfirm();
		return;
	}

	if (IsGenericCancelInputBound(InputID))
	{
		LocalInputCancel();
		return;
	}

	// ---------------------------------------------------------

	ABILITYLIST_SCOPE_LOCK();
	for (FGameplayAbilitySpec& Spec : ActivatableAbilities.Items)
	{
		if (Spec.InputID == InputID)
		{
			if (Spec.Ability)
			{
				Spec.InputPressed = true;
				if (Spec.IsActive())
				{
					if (Spec.Ability->bReplicateInputDirectly && IsOwnerActorAuthoritative() == false)
					{
						ServerSetInputPressed(Spec.Handle);
					}

					AbilitySpecInputPressed(Spec);

					// Invoke the InputPressed event. This is not replicated here. If someone is listening, they may replicate the InputPressed event to the server.
					InvokeReplicatedEvent(EAbilityGenericReplicatedEvent::InputPressed, Spec.Handle, Spec.ActivationInfo.GetActivationPredictionKey());
				}
				else
				{
					UGSGameplayAbility* GA = Cast<UGSGameplayAbility>(Spec.Ability);
					if (GA && GA->bActivateOnInput)
					{
						// Ability is not active, so try to activate it
						TryActivateAbility(Spec.Handle);
					}
				}
			}
		}
	}
}
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-granting"></a>
#### 4.6.3 Granting Abilities
授予`ASC`一个新`GameplayAbility`并把它添加到 `ASC`的 `ActivatableAbilities` 列表，允许它们随意在满足[`GameplayTag` 要求](#concepts-ga-tags)时随意激活.

我们在服务器上授予 `GameplayAbilities`后他会自动将 [`GameplayAbilitySpec`](#concepts-ga-spec) 复制给对应客户端. 其他客户端 / 模拟代理不会收到 `GameplayAbilitySpec`.

示例项目中将`TArray<TSubclassOf<UGDGameplayAbility>>` 存储在它从中读取并在游戏开始时授予的`Character`类上:
```c++
void AGDCharacterBase::AddCharacterAbilities()
{
	// Grant abilities, but only on the server	
	if (Role != ROLE_Authority || !AbilitySystemComponent.IsValid() || AbilitySystemComponent->bCharacterAbilitiesGiven)
	{
		return;
	}

	for (TSubclassOf<UGDGameplayAbility>& StartupAbility : CharacterAbilities)
	{
		AbilitySystemComponent->GiveAbility(
			FGameplayAbilitySpec(StartupAbility, GetAbilityLevel(StartupAbility.GetDefaultObject()->AbilityID), static_cast<int32>(StartupAbility.GetDefaultObject()->AbilityInputID), this));
	}

	AbilitySystemComponent->bCharacterAbilitiesGiven = true;
}
```

在授予这些 `GameplayAbilities`, 我们使用`UGameplayAbility`类, ability level, 输入绑定, 以及 `SourceObject` 或者谁将此`GameplayAbility` 添加到 `ASC`的看来创建`GameplayAbilitySpecs`.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-activating"></a>
#### 4.6.4 Activating Abilities
如果一个 `GameplayAbility` 定义了输入操作, 在输入被按下且满足对应的 `GameplayTag`要求时它会自动激活. 这可能并不总是激活 `GameplayAbility`的理想方式. `ASC`提供了其他四种激活方式: 通过 `GameplayTag`, `GameplayAbility` class, `GameplayAbilitySpec` handle, and by an event. 通过事件激活 `GameplayAbility`需要你 [随事件传入相应数据](#concepts-ga-data).

```c++
UFUNCTION(BlueprintCallable, Category = "Abilities")
bool TryActivateAbilitiesByTag(const FGameplayTagContainer& GameplayTagContainer, bool bAllowRemoteActivation = true);

UFUNCTION(BlueprintCallable, Category = "Abilities")
bool TryActivateAbilityByClass(TSubclassOf<UGameplayAbility> InAbilityToActivate, bool bAllowRemoteActivation = true);

bool TryActivateAbility(FGameplayAbilitySpecHandle AbilityToActivate, bool bAllowRemoteActivation = true);

bool TriggerAbilityFromGameplayEvent(FGameplayAbilitySpecHandle AbilityToTrigger, FGameplayAbilityActorInfo* ActorInfo, FGameplayTag Tag, const FGameplayEventData* Payload, UAbilitySystemComponent& Component);

FGameplayAbilitySpecHandle GiveAbilityAndActivateOnce(const FGameplayAbilitySpec& AbilitySpec, const FGameplayEventData* GameplayEventData);
```
通过事件激活 `GameplayAbility`，对应的 `GameplayAbility` 必须先定义好自己的 `Triggers`. 分配一个`GameplayTag` 并为 `GameplayEvent`设置一个选项. 使用函数 `UAbilitySystemBlueprintLibrary::SendGameplayEventToActor(AActor* Actor, FGameplayTag EventTag, FGameplayEventData Payload)`发送事件. 

`GameplayAbility` `Triggers` 还可以在 `GameplayTag`添加或移除时激活 `GameplayAbility`.

**注意:** 当在蓝图中使用事件激活`GameplayAbility`时, 你必须使用 `ActivateAbilityFromEvent` 节点.

**注意:** 当`GameplayAbility`终止时不要忘记调用 `EndAbility()`,除非你有一个始终像被动能力一样运行的`GameplayAbility`.

**本地预测的** `GameplayAbilities`激活序列:
1. **Owning client** 调用 `TryActivateAbility()`
1. 调用 `InternalTryActivateAbility()`
1. 调用 `CanActivateAbility()` 并返回 `GameplayTag` 是否满足需求, 如果 `ASC` 满足消耗cost, 如果 `GameplayAbility` 不在冷却期, 如果当前没有其他实例处于活动状态
1. 调用 `CallServerTryActivateAbility()` 并向其传递生成的 预测密钥`Prediction Key`
1. 调用 `CallActivateAbility()`
1. 调用 `PreActivate()` Epic refers to this as "boilerplate init stuff"
1. 调用 `ActivateAbility()` 最终激活

**Server** 接收 `CallServerTryActivateAbility()`
1. 调用 `ServerTryActivateAbility()`
1. 调用 `InternalServerTryActivateAbility()` 
1. 调用 `InternalTryActivateAbility()`
1. 调用 `CanActivateAbility()` 并返回 `GameplayTag` 是否满足需求, 如果 `ASC` 满足消耗cost, 如果 `GameplayAbility` 不在冷却期, 如果当前没有其他实例处于活动状态
1. 调用 `ClientActivateAbilitySucceed()` 如果成功则告诉它更新它的 `ActivationInfo`（激活由服务器确认并调用`OnConfirmDelegate`广播）. 这与输入确认不同.
1. 调用 `CallActivateAbility()`
1. 调用 `PreActivate()` Epic refers to this as "boilerplate init stuff"
1. 调用 `ActivateAbility()` 最终激活

如果任何时候服务器激活失败，他会调用 `ClientActivateAbilityFailed()`, 立即终止客户端的 `GameplayAbility` 并恢复所有预测所做的改变.

<a name="concepts-ga-activating-passive"></a>
##### 4.6.4.1 Passive Abilities
实现一个自动激活且能持续运行的被动 `GameplayAbilities`,  当`GameplayAbility`被授予且`AvatarActor`被设置时会自动调用重载函数  `UGameplayAbility::OnAvatarSet()`并调用`TryActivateAbility()`.

我建议添加一个 `bool` 到你自定义的 `UGameplayAbility` 类中用来说明 `GameplayAbility` 是否可以被激活当被授予时. 示例项目添加了这个到盔甲的耐久度.

被动 `GameplayAbilities` 的 [`Net Execution Policy`](#concepts-ga-net) 都是 `Server Only`.

```c++
void UGDGameplayAbility::OnAvatarSet(const FGameplayAbilityActorInfo * ActorInfo, const FGameplayAbilitySpec & Spec)
{
	Super::OnAvatarSet(ActorInfo, Spec);

	if (bActivateAbilityOnGranted)
	{
		ActorInfo->AbilitySystemComponent->TryActivateAbility(Spec.Handle, false);
	}
}
```

Epic 将此函数描述为启动被动能力和执行 `BeginPlay` 操作的正确位置.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-activating-failedtags"></a>
##### 4.6.4.2 Activation Failed Tags

Abilities 有默认的逻辑告诉你为什么ability 激活失败. 要启用此功能，您必须设置与默认失败情况相对应的 GameplayTags.

添加这些 tags (或者你自己定义的名字) 到你的项目中:
```
+GameplayTagList=(Tag="Activation.Fail.BlockedByTags",DevComment="")
+GameplayTagList=(Tag="Activation.Fail.CantAffordCost",DevComment="")
+GameplayTagList=(Tag="Activation.Fail.IsDead",DevComment="")
+GameplayTagList=(Tag="Activation.Fail.MissingTags",DevComment="")
+GameplayTagList=(Tag="Activation.Fail.Networking",DevComment="")
+GameplayTagList=(Tag="Activation.Fail.OnCooldown",DevComment="")
```

把这些添加到 [`GASDocumentation\Config\DefaultGame.ini`](https://github.com/tranek/GASDocumentation/blob/master/Config/DefaultGame.ini#L8-L13):
```
[/Script/GameplayAbilities.AbilitySystemGlobals]
ActivateFailIsDeadName=Activation.Fail.IsDead
ActivateFailCooldownName=Activation.Fail.OnCooldown
ActivateFailCostName=Activation.Fail.CantAffordCost
ActivateFailTagsBlockedName=Activation.Fail.BlockedByTags
ActivateFailTagsMissingName=Activation.Fail.MissingTags
ActivateFailNetworkingName=Activation.Fail.Networking
```

现在无论何时一个ability激活失败了, 相应的GameplayTag 相应的信息会包含在output log messages 或显示在 `showdebug AbilitySystem` hud.
```
LogAbilitySystem: Display: InternalServerTryActivateAbility. Rejecting ClientActivation of Default__GA_FireGun_C. InternalTryActivateAbility failed: Activation.Fail.BlockedByTags
LogAbilitySystem: Display: ClientActivateAbilityFailed_Implementation. PredictionKey :109 Ability: Default__GA_FireGun_C
```

![Activation Failed Tags Displayed in showdebug AbilitySystem](https://github.com/tranek/GASDocumentation/raw/master/Images/activationfailedtags.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-cancelabilities"></a>
#### 4.6.5 Canceling Abilities
要从内部取消`GameplayAbility`, 你可以调用 `CancelAbility()`. 他会调用 `EndAbility()` 并将 `WasCancelled` 设置为 true.

要在外部取消`GameplayAbility`, `ASC`提供了如下方法:

```c++
/** Cancels the specified ability CDO. */
void CancelAbility(UGameplayAbility* Ability);	

/** Cancels the ability indicated by passed in spec handle. If handle is not found among reactivated abilities nothing happens. */
void CancelAbilityHandle(const FGameplayAbilitySpecHandle& AbilityHandle);

/** Cancel all abilities with the specified tags. Will not cancel the Ignore instance */
void CancelAbilities(const FGameplayTagContainer* WithTags=nullptr, const FGameplayTagContainer* WithoutTags=nullptr, UGameplayAbility* Ignore=nullptr);

/** Cancels all abilities regardless of tags. Will not cancel the ignore instance */
void CancelAllAbilities(UGameplayAbility* Ignore=nullptr);

/** Cancels all abilities and kills any remaining instanced abilities */
virtual void DestroyActiveState();
```

**注意:** 我发现如果你有一个`Non-Instanced`非实例化的 `GameplayAbilities`，`CancelAllAbilities`似乎没用. 看起来时遇到 `Non-Instanced` `GameplayAbility` 就放弃了. `CancelAbilities` 可以更好的处理 `Non-Instanced` `GameplayAbilities` ，这也是示例项目中使用的(Jump is a non-instanced `GameplayAbility`). Your mileage may vary.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-definition-activeability"></a>
#### 4.6.6 Getting Active Abilities
初学者经常会问 "我该如何获取激活的 ability?" 也许可以在上面设置变量或者取消它. 一次可以激活多个`GameplayAbility`所以就不存在一种 "active ability". 相反, 你必须通过 `ASC` 的 `ActivatableAbilities`列表 (granted `GameplayAbilities` that the `ASC` owns) 去搜索并找到符合你需要的[`Asset` or `Granted` `GameplayTag`](#concepts-ga-tags)要求的那个.

`UAbilitySystemComponent::GetActivatableAbilities()`返回 `TArray<FGameplayAbilitySpec>` 供你去遍历.

`ASC` 还提供了另一个有用的辅助函数，它接受 `GameplayTagContainer` 作为参数来搜索，而不是手动遍历 `GameplayAbilitySpecs`列表. `bOnlyAbilitiesThatSatisfyTagRequirements` 参数将仅返回满足`GameplayTag`要求且可以立即激活的 `GameplayAbilitySpecs`. 例如, 你有两种基础攻击 `GameplayAbilities`,一个使用武器另一个空拳, 哪个被正确的激活取决于你的装备`GameplayTag`被设置. 参考 Epic的函数注释获取更多内容.
```c++
UAbilitySystemComponent::GetActivatableGameplayAbilitySpecsByAllMatchingTags(const FGameplayTagContainer& GameplayTagContainer, TArray < struct FGameplayAbilitySpec* >& MatchingGameplayAbilities, bool bOnlyAbilitiesThatSatisfyTagRequirements = true)
```

一旦你获取你寻找的 `FGameplayAbilitySpec`时，你可以调用 `IsActive()`.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-instancing"></a>
#### 4.6.7 Instancing Policy
`GameplayAbility`的 `Instancing Policy` 决定在激活时是否以及如何实例化.

| `Instancing Policy`     | 描述                                                                                      | Example of when to use                                                                                                                                                                                                                                                                                                                                                                                             |
| ----------------------- | ------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Instanced Per Actor     | 每个 `ASC` 都只拥有一个 `GameplayAbility`示例，多次激活间可重复使用.    | 这可能是你使用最多的 `Instancing Policy`. 您可以将其用于任何能力，并在激活之间提供持久性. 开发者只需要在每次使用时手动重置任何变量.                                                                                                                                                               |
| Instanced Per Execution | 每次`GameplayAbility` 被激活时都创建一个 `GameplayAbility`的新实例. | 好处是每次激活 `GameplayAbilities`的变量都被重置. 它们的性能比 `Instanced Per Actor` 要差，因为它们每次都会创建新的的`GameplayAbilities`. 实例项目中并为使用该策略.                                                                                                                                 |
| Non-Instanced           | `GameplayAbility` 工作在它的`ClassDefaultObject`上. 不会创建实例.            | 这是三者中性能最好同时也是限制最多的策略. `Non-Instanced` `GameplayAbilities` 不可以缓存数据, 意味着没有动态变量，不能在 `AbilityTask` 做绑定. 最佳的使用方式是用来做简单的能力，比如 MOBA 或 RTS中小兵的攻击. 示例工程中 Jump `GameplayAbility` 是 `Non-Instanced`. |

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-net"></a>
#### 4.6.8 Net Execution Policy
`GameplayAbility`的 `Net Execution Policy` 决定了谁执行`GameplayAbility` 并以什么顺序.

| `Net Execution Policy` | 描述                                                                                                                                                                                                         |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Local Only`           | `GameplayAbility`只在当前客户端执行. 对只有本地装饰性改变的能力很有用. 单机游戏需要使用 `Server Only`.                                     |
| `Local Predicted`      | `Local Predicted` `GameplayAbilities` 现在本地客户端激活再传到服务器. 服务器会修正任何客户端预测错误的内容. c参考 [Prediction](#concepts-p). |
| `Server Only`          | `GameplayAbility` 只在服务器运行. 被动 `GameplayAbilities` 通常是 `Server Only`. 单机游戏也是使用该选项.                                                                  |
| `Server Initiated`     | `Server Initiated` `GameplayAbilities` 是服务器先激活然后再传给对应客户端. 我个人并未使用过.                                                                     | 

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-tags"></a>
#### 4.6.9 Ability Tags
`GameplayAbilities` 使用有内置逻辑的 `GameplayTagContainers`. 这些 `GameplayTags` 不可复制.

| `GameplayTag Container`     | 描述                                                                                                                                                                                   |
| --------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Ability Tags`              | `GameplayTags` that the `GameplayAbility` owns. These are just `GameplayTags` to describe the `GameplayAbility`.（仅描述使用）                                                                              |
| `Cancel Abilities with Tag` | Other `GameplayAbilities` that have these `GameplayTags` in their `Ability Tags` will be canceled when this `GameplayAbility` is activated. (取消当前技能)                                                 |
| `Block Abilities with Tag`  | Other `GameplayAbilities` that have these `GameplayTags` in their `Ability Tags` are blocked from activating while this `GameplayAbility` is active.   （阻止当前技能激活）                                       |
| `Activation Owned Tags`     | These `GameplayTags` are given to the `GameplayAbility's` owner while this `GameplayAbility` is active. Remember these are not replicated.    （激活时提供给owner，不会被复制）                                                |
| `Activation Required Tags`  | This `GameplayAbility` can only be activated if the owner has **all** of these `GameplayTags`.  （全满足该项tag要求时激活技能）                                                                                              |
| `Activation Blocked Tags`   | This `GameplayAbility` cannot be activated if the owner has **any** of these `GameplayTags`. （有任一该项中的tag时不允许激活）                                                                                                 |
| `Source Required Tags`      | This `GameplayAbility` can only be activated if the `Source` has **all** of these `GameplayTags`. The `Source` `GameplayTags` are only set if the `GameplayAbility` is triggered by an event. （对Source要求）|
| `Source Blocked Tags`       | This `GameplayAbility` cannot be activated if the `Source` has **any** of these `GameplayTags`. The `Source` `GameplayTags` are only set if the `GameplayAbility` is triggered by an event.  （对Source要求） |
| `Target Required Tags`      | This `GameplayAbility` can only be activated if the `Target` has **all** of these `GameplayTags`. The `Target` `GameplayTags` are only set if the `GameplayAbility` is triggered by an event. （对Target要求）|
| `Target Blocked Tags`       | This `GameplayAbility` cannot be activated if the `Target` has **any** of these `GameplayTags`. The `Target` `GameplayTags` are only set if the `GameplayAbility` is triggered by an event.  （对Target要求） |

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-spec"></a>
#### 4.6.10 Gameplay Ability Spec
授予`GameplayAbility`后，`GameplayAbilitySpec` 存在于 `ASC`上并定义了可激活的 `GameplayAbility` - `GameplayAbility` 类, level, 输入绑定, 必须与`GameplayAbility` 类分开保存的运行状态.

当一个 `GameplayAbility`在server上被授予时, 它会同步 `GameplayAbilitySpec`到对应的客户端上以便可以激活它.

激活 `GameplayAbilitySpec` 会创建一个 `GameplayAbility`实例 ( `Non-Instanced` `GameplayAbilities`模式不会) 取决于 `Instancing Policy`.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-data"></a>
#### 4.6.11 Passing Data to Abilities
`GameplayAbilities` 的一般样例是 `Activate->Generate Data->Apply->End`. 有时您需要根据现有数据采取行动. GAS 提供了一些将外部数据导入`GameplayAbilities`的选项:

| 方法                                          | 描述                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| ----------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Activate `GameplayAbility` by Event             | 使用包含数据负载的事件激活 `GameplayAbility`. 本地预测的 `GameplayAbilities`会将事件所带的数据从客户端复制到服务器. 将两个 `Optional Object` 或者 [`TargetData`](#concepts-targeting-data) 变量用于不适合现有变量的任意数据. 这样做的缺点是它会阻止您通过输入绑定激活该功能. 通过事件激活 `GameplayAbility`, `GameplayAbility`必须有设置自己的 `Triggers`. 分配一个 `GameplayTag` 并为 `GameplayEvent`选择一个选项. 使用`UAbilitySystemBlueprintLibrary::SendGameplayEventToActor(AActor* Actor, FGameplayTag EventTag, FGameplayEventData Payload)`发送事件. |
| Use `WaitGameplayEvent` `AbilityTask`           | 使用 `WaitGameplayEvent` `AbilityTask` 告诉 `GameplayAbility` 在激活后监听带有有效负载数据的事件. 事件有效负载及其发送过程与通过事件激活 `GameplayAbilities` 相同. 缺点是事件不会被`AbilityTask`同步复制，所以只能用在 `Local Only` 和 `Server Only` `GameplayAbilities`. 你可能需要实现自己的 `AbilityTask` 来同步这些事件负载数据.                                                                                                                                                                                                                                                                                               |
| Use `TargetData`                                | 自定义一个 `TargetData` 在服务器与客户端之间传递任意数据是非常好的方法.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| Store Data on the `OwnerActor` or `AvatarActor` | 使用保存在 `OwnerActor`, `AvatarActor`, 或其他 object 上可复制的变量的引用. 此方法最灵活，可与输入绑定激活的`GameplayAbilities` 一起使用. 然而, 它不保证数据在使用时会从复制同步. 您必须提前确保这一点 - 这意味着如果您设置复制变量然后立即激活 `GameplayAbility`，则由于潜在的数据包丢失，无法保证接收器上发生的顺序.                                                                                                                                                                                                                                   |

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-commit"></a>
#### 4.6.12 Ability Cost and Cooldown
`GameplayAbilities` 具有可选cost消耗和冷却时间的功能. Costs 是 `ASC` 必须有的一些预定义的 `Attributes` 来激活 `Instant` `GameplayEffect` ([`Cost GE`](#concepts-ge-cost))实现的 `GameplayAbility`. Cooldowns 是阻止 `GameplayAbility`在过期之前被再次激活的计时器，他是一种 `Duration` `GameplayEffect` ([`Cooldown GE`](#concepts-ge-cooldown)).

在 `GameplayAbility` 调用 `UGameplayAbility::Activate()`之前会先调用 `UGameplayAbility::CanActivateAbility()`. 这个函数检查`ASC` 是否能承担消耗 (`UGameplayAbility::CheckCost()`) 并确保 `GameplayAbility` 不处于冷却期 (`UGameplayAbility::CheckCooldown()`).

在`GameplayAbility` 调用 `Activate()`后, 它可以选择使用`UGameplayAbility::CommitAbility()`随时提交消耗 `UGameplayAbility::CommitCost()` 和冷却时间 `UGameplayAbility::CommitCooldown()`. 如果不同时发生开发者可以分别调用 `CommitCost()` 和 `CommitCooldown()`. 提交消耗和冷却会再次调用 `CheckCost()` 和 `CheckCooldown()`，这也是`GameplayAbility`与失败它们相关的最后机会. 激活`GameplayAbility`后所属 `ASC` 的`Attributes`可能会发生变化, 无法满足提交时的成本. 如果[prediction key](#concepts-p-key)在提交时有效， 提交消耗和冷却可以被[本地预测](#concepts-p) .

参考 [`CostGE`](#concepts-ge-cost) 和 [`CooldownGE`](#concepts-ge-cooldown) 获取实现详情.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-leveling"></a>
#### 4.6.13 Leveling Up Abilities
有两种方法可以提升ability:

| Level Up Method                            | Description                                                                                                                                                                                                      |
| ------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Ungrant and Regrant at the New Level       | Ungrant (remove) the `GameplayAbility` from the `ASC` and regrant it back at the next level on the server. This terminates the `GameplayAbility` if it was active at the time.(删除再添加)                                   |
| Increase the `GameplayAbilitySpec's` Level | On the server, find the `GameplayAbilitySpec`, increase its level, and mark it dirty so that replicates to the owning client. This method does not terminate the `GameplayAbility` if it was active at the time.（修改level等级） |

两者之间的主要区别是你是否希望在升级时取消当前已激活的 `GameplayAbilities`. 取决于不同类型的 `GameplayAbilities`你可能两种方法都会使用. 我建议添加一个 `bool` 到你的 `UGameplayAbility` 子类来说明使用哪种方式.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-sets"></a>
#### 4.6.14 Ability Sets
`GameplayAbilitySets` 是一种很方便的 `UDataAsset` 类，用于保存输入绑定和角色的启动 `GameplayAbilities` 列表，并具有授予 `GameplayAbilities` 的逻辑. 子类可以包含额外的逻辑或属性. Paragon 每个英雄有一个 `GameplayAbilitySet` 包含他们所有的`GameplayAbilities`.

我发现这个类并不是必要的，至少从我到目前为止所看到的来看是这样. 示例项目在 `GDCharacterBase` 及其子类内部处理 `GameplayAbilitySets` 的所有功能.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-batching"></a>
#### 4.6.15 Ability Batching
传统的 `Gameplay Ability` 生命周期涉及从客户端到服务器的至少两到三个RPC.

1. `CallServerTryActivateAbility()`
1. `ServerSetReplicatedTargetData()` (Optional)
1. `ServerEndAbility()`

如果 `GameplayAbility` 在一帧或一个原子分组中执行这些所有的操作，我们可以优化此工作流程，将所有的两个或三个RPC组合到一个RPC中. `GAS` 称这种RPC优化为 `Ability Batching`. 最常见使用该能力的例子是hitscan guns(命中扫描枪). Hitscan guns 激活后，发出一个射线检测并将结果 [`TargetData`](#concepts-targeting-data)发送给服务器 , 在一帧内结束这个原子组操作. [GASShooter](https://github.com/tranek/GASShooter) 实例工程中展示了这中使用方式.

Semi-Automatic guns（半自动枪）是将 `CallServerTryActivateAbility()`, `ServerSetReplicatedTargetData()` (the bullet hit result), 和 `ServerEndAbility()` 三个RPC合并为一个的最好示例.

Full-Automatic/Burst guns(全自动枪或者连发枪) 在第一颗子弹时合并 `CallServerTryActivateAbility()` 与 `ServerSetReplicatedTargetData()`为一个RPC. 后面的每个子弹会调用自己的 `ServerSetReplicatedTargetData()` RPC. 最后, 枪停火后会单独发一个独立的RPC `ServerEndAbility()`. 此场景也可以通过通过 [`Gameplay Event`](#concepts-ga-data) 激活该功能来实现，该 [`Gameplay Event`](#concepts-ga-data) 会将子弹的 `TargetData` 与 `EventPayload` 一起从客户端发送到服务器. 后一种方法的缺点是 `TargetData` 必须在ability外部生成，而batching approach则在功能内部生成.

`Ability Batching`默认情况下在[`ASC`](#concepts-asc)上是禁止的. 开启 `Ability Batching`需要重载 `ShouldDoServerAbilityRPCBatch()` 并返回 true:

```c++
virtual bool ShouldDoServerAbilityRPCBatch() const override { return true; }
```

现在 `Ability Batching` 开启了, 在激活你想合并的abilitie前, 你需要先创建一个 `FScopedServerAbilityRPCBatcher` struct. 这个特殊的结构将尝试在其范围内合并其后的任何abilities. 超出范围就不在合并了. `FScopedServerAbilityRPCBatcher`的工作原理是在每个可批处理的函数中包含特殊代码，拦截发送 RPC 的调用，并将消息打包到批处理结构中. 当超出 `FScopedServerAbilityRPCBatcher` 范围后, 它会自动将合并的结构体在 `UAbilitySystemComponent::EndServerAbilityRPCBatch()`RPC发送给服务器. 服务器接收 batch RPC 在 `UAbilitySystemComponent::ServerAbilityRPCBatch_Internal(FServerAbilityRPCBatch& BatchInfo)`. `BatchInfo` 参数将包含用于指示该能力是否应结束、激活时是否按下输入的标志以及可能包含的 `TargetData`. 这是一个很好的断点地方来确认你的合批是否生效. 或者, 使用 cvar `AbilitySystem.ServerRPCBatching.Log 1` 可以开启 batching logging.

此机制只能在C++中实现并只能通过`FGameplayAbilitySpecHandle`激活.

```c++
bool UGSAbilitySystemComponent::BatchRPCTryActivateAbility(FGameplayAbilitySpecHandle InAbilityHandle, bool EndAbilityImmediately)
{
	bool AbilityActivated = false;
	if (InAbilityHandle.IsValid())
	{
		FScopedServerAbilityRPCBatcher GSAbilityRPCBatcher(this, InAbilityHandle);
		AbilityActivated = TryActivateAbility(InAbilityHandle, true);

		if (EndAbilityImmediately)
		{
			FGameplayAbilitySpec* AbilitySpec = FindAbilitySpecFromHandle(InAbilityHandle);
			if (AbilitySpec)
			{
				UGSGameplayAbility* GSAbility = Cast<UGSGameplayAbility>(AbilitySpec->GetPrimaryInstance());
				GSAbility->ExternalEndAbility();
			}
		}

		return AbilityActivated;
	}

	return AbilityActivated;
}
```

GASShooter 对全自动、半自动枪重用了相同的 batched `GameplayAbility`，他们不会直接调用 `EndAbility()` (它是由仅本地能力在ability之外处理的，该能力管理玩家输入以及基于当前开火模式对批量能力的调用). 由于所有 RPCs 必须在 `FScopedServerAbilityRPCBatcher`范围内, 我提供了 `EndAbilityImmediately` 参数来控制/管理 本地功能是否应批处理 `EndAbility()` (半自动步枪), 或者不处理 `EndAbility()`  (全自动步枪)且 `EndAbility()`在后面自己的 RPC中调用.

GASShooter 暴露一个 Blueprint 节点来允许batching能力，上述仅限本地的ability使用该能力来触发批处理ability.

![Activate Batched Ability](https://github.com/tranek/GASDocumentation/raw/master/Images/batchabilityactivate.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-netsecuritypolicy"></a>
#### 4.6.16 Net Security Policy
`GameplayAbility`的 `NetSecurityPolicy` 决定执行在网络的哪一端. 它可以阻止客户端执行一些敏感的操作.

| `NetSecurityPolicy`     | 描述                                                                                                                                        |
| ----------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| `ClientOrServer`        | 没有安全要求. Client 或 server 都可以自由开启或结束.  	|
| `ServerOnlyExecution`   | 客户端请求执行会被忽略，但客户端可以请求取消或结束. |
| `ServerOnlyTermination` | 客户端请求取消或结束会被忽略，但可以请求执行.      |
| `ServerOnly`            | 服务器决定执行或者取消，客户端任何请求都被忽略.    	|

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-at"></a>
### 4.7 Ability Tasks

<a name="concepts-at-definition"></a>
### 4.7.1 Ability Task Definition
`GameplayAbilities` 仅在一帧中执行. 这本身并没有太大的灵活性. 为了执行随时间推移发生的操作或需要响应稍后某个时间点触发的委托我们使用 `AbilityTasks`.

GAS 附带了许多开箱即用的 `AbilityTasks` :
* Tasks for moving Characters with `RootMotionSource`     【角色移动】
* A task for playing animation montages	      【播放动画】
* Tasks for responding to `Attribute` changes			【修改属性】
* Tasks for responding to `GameplayEffect` changes【修改GE】
* Tasks for responding to player input【相应输入】
* and more

`UAbilityTask`在构造函数中硬编码了在游戏范围内最多同时运行1000个并发 `AbilityTasks`. 在设计类似RTS这种同时又数百个角色的游戏是需要记住这一点.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-at-definition"></a>
### 4.7.2 Custom Ability Tasks
你需要经常在C++中定义你自己的`AbilityTasks`. 示例项目附带两个自定义的`AbilityTasks`:
1. `PlayMontageAndWaitForEvent` 是默认`PlayMontageAndWait` 和 `WaitGameplayEvent` `AbilityTasks`的组合. 这允许动画蒙太奇将游戏事件从 `AnimNotify` 发送回启动它们的 `GameplayAbility`. 使用它在动画蒙太奇期间的特定时间触发动作.
1. `WaitReceiveDamage` 用于监听 `OwnerActor` 收到伤害. 在英雄收到伤害时，被动护甲层数减少.

`AbilityTasks` 由以下部分组成:
* 一个静态函数创建 `AbilityTask`的实例
* z在`AbilityTask` 完成是触发的一个代理
* 一个 `Activate()` 函数来激活job,绑定外部代理等.
* 一个 `OnDestroy()` 函数做清理，包含外部绑定的代理
* 绑定到的任何外部委托的回调函数
* 成员变量和任何内部辅助函数

**注意:** `AbilityTasks` 只能声明一种类型的输出委托. 所有输出委托都必须属于此类型，无论它们是否使用参数. 不使用传入默认值.

`AbilityTasks` 只运行在所属`GameplayAbility`的客户端或服务器; 但是，可以通过在 `AbilityTask` 构造中设置`bSimulatedTask = true;`将`AbilityTasks`设置为在模拟客户端simulated clients上运行, 重载 `virtual void InitSimulatedTask(UGameplayTasksComponent& InGameplayTasksComponent);`, 并设置要复制的任何成员变量. 这仅在极少数情况下有用，例如移动 `AbilityTasks`，你不想复制每个移动改变而是模拟整个移动的`AbilityTask`. 整个 `RootMotionSource` `AbilityTasks` 做的就是这些. 参考 `AbilityTask_MoveToLocation.h/.cpp`.

如果你在`AbilityTasks`的构造中将`bTickingTask = true;` 并重载 `virtual void TickTask(float DeltaTime);`则可让Task `Tick`. 当您需要跨帧平滑地对值进行 lerp 时，这非常有用. 参考 `AbilityTask_MoveToLocation.h/.cpp`.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-at-using"></a>
### 4.7.3 Using Ability Tasks
在C++中创建并激活一个 `AbilityTask`(From `GDGA_FireGun.cpp`):
```c++
UGDAT_PlayMontageAndWaitForEvent* Task = UGDAT_PlayMontageAndWaitForEvent::PlayMontageAndWaitForEvent(this, NAME_None, MontageToPlay, FGameplayTagContainer(), 1.0f, NAME_None, false, 1.0f);
Task->OnBlendOut.AddDynamic(this, &UGDGA_FireGun::OnCompleted);
Task->OnCompleted.AddDynamic(this, &UGDGA_FireGun::OnCompleted);
Task->OnInterrupted.AddDynamic(this, &UGDGA_FireGun::OnCancelled);
Task->OnCancelled.AddDynamic(this, &UGDGA_FireGun::OnCancelled);
Task->EventReceived.AddDynamic(this, &UGDGA_FireGun::EventReceived);
Task->ReadyForActivation();
```

我们可以使用蓝图节点创建 `AbilityTask`. 不需要调用 `ReadyForActivation()`. 它会自动被`Engine/Source/Editor/GameplayTasksEditor/Private/K2Node_LatentGameplayTaskCall.cpp`调用. `K2Node_LatentGameplayTaskCall` 也会自动调用 `BeginSpawningActor()` 和 `FinishSpawningActor()` 如果他们存在于你的 `AbilityTask` class (参考 `AbilityTask_WaitTargetData`). 重申一下, `K2Node_LatentGameplayTaskCall` 只对蓝图有效. 在C++我们需要手动调用 `ReadyForActivation()`, `BeginSpawningActor()`, 和 `FinishSpawningActor()`.

![Blueprint WaitTargetData AbilityTask](https://github.com/tranek/GASDocumentation/raw/master/Images/abilitytask.png)

在蓝图中或C++中，取消 `AbilityTask`只需要手动调用其上的 `EndTask()`就可以  (called `Async Task Proxy`).

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-at-rms"></a>
### 4.7.4 Root Motion Source Ability Tasks
GAS 附带了可以随时间推移移动 `Characters`的`AbilityTasks`，使用`Root Motion Sources`挂到角色的 `CharacterMovementComponent`实现击退、冲刺、复杂的跳跃等操作.

**注意:** 预测 `RootMotionSource` `AbilityTasks` 只对 4.19 和 4.25以上版本引擎有效. 4.20-4.24版本中存在bug; 然而, `AbilityTasks`在多人游戏或较小的网络修正后的单人游戏中可以完美运行. It is possible to cherry pick the [prediction fix](https://github.com/EpicGames/UnrealEngine/commit/94107438dd9f490e7b743f8e13da46927051bf33#diff-65f6196f9f28f560f95bd578e07e290c) from 4.25 into a custom 4.20-4.24 engine.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc"></a>
### 4.8 Gameplay Cues

<a name="concepts-gc-definition"></a>
#### 4.8.1 Gameplay Cue Definition
`GameplayCues` (`GC`) 执行于游戏无关的事情，比如音效、特效、相机抖动等. `GameplayCues` 通常是可以被预测 (除非明确只本地执行 `Executed`, `Added`, or `Removed` )和同步的.

我们通过发送带有 **强制父类名称**的相应 `GameplayTag`来触发`GameplayCues`,以及通过`ASC`向 `GameplayCueManager` 发送 (`Execute`, `Add`, or `Remove`) 事件. 实现 `IGameplayCueInterface` 接口的 `GameplayCueNotify` objects 和其他 `Actors`可以订阅基于 `GameplayCue's` `GameplayTag` (`GameplayCueTag`)的事件.

**注意:** 重申一下, `GameplayCue` `GameplayTags` 必须以 `GameplayCue`做父节点. 所以对于一个有效的 `GameplayCue` `GameplayTag`可能是 `GameplayCue.A.B.C`.

有两种`GameplayCueNotifies`类, `Static` 和 `Actor`. 它们响应不同的事件，不同类型的 `GameplayEffects` 可以触发它们。用您的逻辑覆盖相应的事件.

| `GameplayCue` Class                                                                                                                  | Event             | `GameplayEffect` Type    | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| ------------------------------------------------------------------------------------------------------------------------------------ | ----------------- | ------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [`GameplayCueNotify_Static`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayCueNotify_Static/index.html) | `Execute`         | `Instant` or `Periodic`  | Static `GameplayCueNotifies` operate on the `ClassDefaultObject` (meaning no instances) and are perfect for one-off effects like hit impacts.  (对CDO操作，适合一次性的效果)                                                                                                                                                                                                                                                                                                                                                                      |
| [`GameplayCueNotify_Actor`](https://docs.unrealengine.com/en-US/BlueprintAPI/GameplayCueNotify/index.html)                           | `Add` or `Remove` | `Duration` or `Infinite` | Actor `GameplayCueNotifies` spawn a new instance when `Added`. Because these are instanced, they can do actions over time until they are `Removed`. These are good for looping sounds and particle effects that will be removed when the backing `Duration` or `Infinite` `GameplayEffect` is removed or by manually calling remove. These also come with options to manage how many are allowed to be `Added` at the same time so that multiple applications of the same effect only start the sounds or particles once.（在添加时创建新实例，当时间结束或手动取消时移除，还有一些选项支持设置最大同时添加数量可以做到同时播放多次音效或特效只会响应一次） |

`GameplayCueNotifies` 从技术上讲可以响应任何事件，但这通常是我们使用它们的方式.

**注意:** 在使用 `GameplayCueNotify_Actor`时, 要判断 `Auto Destroy on Remove` 否则后面调用 `Add` 时 `GameplayCueTag`可能不会生效.

在使用 `ASC` 除`Full`外的 [Replication Mode](#concepts-asc-rm) 时, `Add` 和 `Remove` `GC` 事件会在（Listen Server）服务器上触发两次 - 一次是应用`GE` 另一次是客户端 "Minimal" `NetMultiCast` . 但是, `WhileActive` 事件仅会产生一次. 在客户端所有事件仅会触发一次.

示例工程使用`GameplayCueNotify_Actor`实现了眩晕和冲刺效果. 使用 `GameplayCueNotify_Static` 实现了用于FireGun 射弹影响. 这些 `GCs`可以进一步使用 [triggering them locally](#concepts-gc-local)优化来替代通过`GE`复制. 我在示例项目中向初学者展示了用法.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-trigger"></a>
#### 4.8.2 Triggering Gameplay Cues

当成功应用`GameplayEffect` (没有被tag或免疫阻挡)时,会从 `GameplayEffect` 内部填写应触发的所有 `GameplayCues` 的 `GameplayTags` .

![GameplayCue Triggered from a GameplayEffect](https://github.com/tranek/GASDocumentation/raw/master/Images/gcfromge.png)

`UGameplayAbility` 提供蓝图节点 `Execute`, `Add`, 或 `Remove` `GameplayCues`.

![GameplayCue Triggered from a GameplayAbility](https://github.com/tranek/GASDocumentation/raw/master/Images/gcfromga.png)

在 C++你可以直接调用 `ASC`中函数 (或者将他们暴露给蓝图在你的`ASC`子类 ):

```c++
/** GameplayCues can also come on their own. These take an optional effect context to pass through hit result, etc */
void ExecuteGameplayCue(const FGameplayTag GameplayCueTag, FGameplayEffectContextHandle EffectContext = FGameplayEffectContextHandle());
void ExecuteGameplayCue(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

/** Add a persistent gameplay cue */
void AddGameplayCue(const FGameplayTag GameplayCueTag, FGameplayEffectContextHandle EffectContext = FGameplayEffectContextHandle());
void AddGameplayCue(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

/** Remove a persistent gameplay cue */
void RemoveGameplayCue(const FGameplayTag GameplayCueTag);
	
/** Removes any GameplayCue added on its own, i.e. not as part of a GameplayEffect. */
void RemoveAllGameplayCues();
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-local"></a>
#### 4.8.3 Local Gameplay Cues
默认情况下，会复制从 `GameplayAbilities` 和 `ASC` 触发 `GameplayCues` 的公开函数. 每个 `GameplayCue` 事件都是一个RPC. 这会造成大量的 RPCs. GAS 还强制每次网络更新最多使用两个相同的`GameplayCue` RPCs . 我们需要尽可能的使用本地`GameplayCues` 来避免这个问题. 本地 `GameplayCues` 只有在个别客户端执行 `Execute`, `Add`, 或 `Remove` .

我们可以使用本地`GameplayCues`的场景:
* Projectile impacts（炮弹撞击）
* Melee collision impacts（近战碰撞）
* `GameplayCues` fired from animation montages（从动画中发出的）

你可以将本地`GameplayCue` 方法添加到你的`ASC` 子类:

```c++
UFUNCTION(BlueprintCallable, Category = "GameplayCue", Meta = (AutoCreateRefTerm = "GameplayCueParameters", GameplayTagFilter = "GameplayCue"))
void ExecuteGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

UFUNCTION(BlueprintCallable, Category = "GameplayCue", Meta = (AutoCreateRefTerm = "GameplayCueParameters", GameplayTagFilter = "GameplayCue"))
void AddGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

UFUNCTION(BlueprintCallable, Category = "GameplayCue", Meta = (AutoCreateRefTerm = "GameplayCueParameters", GameplayTagFilter = "GameplayCue"))
void RemoveGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);
```

```c++
void UPAAbilitySystemComponent::ExecuteGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters & GameplayCueParameters)
{
	UAbilitySystemGlobals::Get().GetGameplayCueManager()->HandleGameplayCue(GetOwner(), GameplayCueTag, EGameplayCueEvent::Type::Executed, GameplayCueParameters);
}

void UPAAbilitySystemComponent::AddGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters & GameplayCueParameters)
{
	UAbilitySystemGlobals::Get().GetGameplayCueManager()->HandleGameplayCue(GetOwner(), GameplayCueTag, EGameplayCueEvent::Type::OnActive, GameplayCueParameters);
	UAbilitySystemGlobals::Get().GetGameplayCueManager()->HandleGameplayCue(GetOwner(), GameplayCueTag, EGameplayCueEvent::Type::WhileActive, GameplayCueParameters);
}

void UPAAbilitySystemComponent::RemoveGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters & GameplayCueParameters)
{
	UAbilitySystemGlobals::Get().GetGameplayCueManager()->HandleGameplayCue(GetOwner(), GameplayCueTag, EGameplayCueEvent::Type::Removed, GameplayCueParameters);
}
```

从本地添加的`GameplayCue`就要本地移除，网络复制中添加的就要通过网络复制移除.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-parameters"></a>
#### 4.8.4 Gameplay Cue Parameters
`GameplayCues` 接收一个 `FGameplayCueParameters` 结构，其中包含 `GameplayCue` 的额外信息作为参数. 如果你从`GameplayAbility` 或 `ASC`中手动触发 `GameplayCue` , 你需要手动填充 `GameplayCueParameters` 结构体参数并将它传递给 `GameplayCue`. 如果是 `GameplayEffect`触发，以下参数会被`GameplayCueParameters`自动填充 :

* AggregatedSourceTags
* AggregatedTargetTags
* GameplayEffectLevel
* AbilityLevel
* [EffectContext](#concepts-ge-context)
* Magnitude (if the `GameplayEffect` has an `Attribute` for magnitude selected in the dropdown above the `GameplayCue` tag container and a corresponding `Modifier` that affects that `Attribute`)

当手动触发时， `GameplayCueParameters` 中的 `SourceObject`可能是一个传任意数据的好地方.

**注意:** 结构体中的一些变量如`Instigator` 可能已经在`EffectContext`中有了. `EffectContext` 还可能包含一下 `FHitResult` 变量标明生成 `GameplayCue`的世界坐标. 子类化 `EffectContext` 可能是给 `GameplayCues`传递更多数据的好方法, 尤其是通过`GameplayEffect`触发的效果.

有关详细信息，参阅 [`UAbilitySystemGlobals`](#concepts-asg) 中填充 `GameplayCueParameters` 结构的 3 个函数。它们是虚函数，可以重载它们以自动填充更多信息.

```c++
/** Initialize GameplayCue Parameters */
virtual void InitGameplayCueParameters(FGameplayCueParameters& CueParameters, const FGameplayEffectSpecForRPC &Spec);
virtual void InitGameplayCueParameters_GESpec(FGameplayCueParameters& CueParameters, const FGameplayEffectSpec &Spec);
virtual void InitGameplayCueParameters(FGameplayCueParameters& CueParameters, const FGameplayEffectContextHandle& EffectContext);
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-manager"></a>
#### 4.8.5 Gameplay Cue Manager
默认情况下，`GameplayCueManager` 将扫描整个游戏目录以查找 `GameplayCueNotify`，并在游戏开始时将它们加载到内存中。我们可以通过在`DefaultGame.ini`中设置来更改GameplayCueManager扫描的路径.

```
[/Script/GameplayAbilities.AbilitySystemGlobals]
GameplayCueNotifyPaths="/Game/GASDocumentation/Characters"
```

我们确实想让 `GameplayCueManager`扫描并找到所有的 `GameplayCueNotifies`; 但是我们不希望它在游戏中异步一个个都加载. 这会将每个 `GameplayCueNotify` 及其所有引用的声音和粒子放入内存中，无论它们是否在当前level中使用. 在一个如Paragon的大型项目中, 这可能会在内存中产生数百兆字节的不需要的资源，并导致启动时卡顿和游戏冻结.

在启动时异步加载每个 `GameplayCue` 的另一种方法是仅在游戏中触发时再异步加载 . 这可以减少不必要的内存使用和游戏卡住，同时异步加载每个 GameplayCue 以换取在游戏过程中第一次触发特定 `GameplayCue` 时可能出现的延迟效果. 对 SSDs来说这个延迟也是可以忽略的. 我在HDD上并未测试. 如果在 UE 编辑器中使用此选项，并且编辑器需要编译粒子系统，则在第一次加载 `GameplayCues` 期间可能会出现轻微的延迟和卡顿. 在粒子构建后就没问题了.

首先我们需要继承 `UGameplayCueManager` ，然后在 `DefaultGame.ini` 告诉 `AbilitySystemGlobals` 类使用我们自己的 `UGameplayCueManager`.

```
[/Script/GameplayAbilities.AbilitySystemGlobals]
GlobalGameplayCueManagerClass="/Script/ParagonAssets.PBGameplayCueManager"
```

在自定义的 `UGameplayCueManager` 子类中重载 `ShouldAsyncLoadRuntimeObjectLibraries()`.

```c++
virtual bool ShouldAsyncLoadRuntimeObjectLibraries() const override
{
	return false;
}
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-prevention"></a>
#### 4.8.6 Prevent Gameplay Cues from Firing
有时我们不希望 `GameplayCues` 被触发。例如，如果我们阻止攻击，我们可能不想播放附加到伤害 `GameplayEffect` 的命中效果，或者改为播放自定义效果. 我们可以在 [`GameplayEffectExecutionCalculations`](#concepts-ge-ec) 内调用 `OutExecutionOutput.MarkGameplayCuesHandledManually()` 然后手动给`Target` 或 `Source's` `ASC`发送`GameplayCue` 事件.

如果您不想在特定 `ASC` 上触发任何 `GameplayCues` , 你可以设置 `AbilitySystemComponent->bSuppressGameplayCues = true;`.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-batching"></a>
#### 4.8.7 Gameplay Cue Batching
每个`GameplayCue`都会触发 unreliable NetMulticast RPC. 在我们同时触发多个 `GC` 的情况下，有一些优化方法可以将它们压缩为一个 RPC 或通过发送更少的数据来节省带宽.

<a name="concepts-gc-batching-manualrpc"></a>
##### 4.8.7.1 Manual RPC
假设你有一把可以发射八颗子弹的霰弹枪. 就会有8个弹道和 `GameplayCues`攻击效果. [GASShooter](https://github.com/tranek/GASShooter) 采用一种惰性方法，将所有跟踪信息作为 [`TargetData`](#concepts-targeting-data) 存储到 [`EffectContext`](#concepts-ge-ec) 中，将它们合并到一个 RPC 中. 这就将8个RPC减少到1个, 在这个RPC中还是会发送大量数据(~500 bytes). 更优化的方法是发送带有自定义结构的 RPC，您可以在其中有效地对命中位置进行编码，或者可以为其提供随机种子数以在接收端重新创建/近似影响位置. 然后客户端解压这个自定义的结构体并在[本地执行  `GameplayCues`](#concepts-gc-local).

实现方法:
1. 声明一个自己的 `FScopedGameplayCueSendContext`. 这会抑制 `UGameplayCueManager::FlushPendingCues()` 直到它超出范围，这意味着所有 `GameplayCues` 将都会先排队 .
1. 重写 `UGameplayCueManager::FlushPendingCues()` ,可以基于某些自定义 `GameplayTag` 能一起合并的 `GameplayCues` 合并到您的自定义结构中，并将其 RPC 到客户端.
1. 客户端接收到自定义结构体解压并本地执行 `GameplayCues`.

如果 `GameplayCueParameters`提供的参数不能满足或者你有不想添加到`EffectContext`中的数据，比如伤害值，暴击，破甲值，是否是暴击等数据，你都可以使用这种方式.

https://forums.unrealengine.com/development-discussion/c-gameplay-programming/1711546-fscopedgameplaycuesendcontext-gameplaycuemanager

<a name="concepts-gc-batching-gcsonge"></a>
##### 4.8.7.2 Multiple GCs on one GE
`GameplayEffect` 上的所有 `GameplayCues` 已在一个 RPC 中发送. 默认情况下，不管`ASC`的 `Replication Mode`为何， `UGameplayCueManager::InvokeGameplayCueAddedAndWhileActive_FromSpec()` 都将在unreliable NetMulticast 中发送整个 `GameplayEffectSpec`（但转换为 FGameplayEffectSpecForRPC）. 根据`GameplayEffectSpec`不同可能会占用大量带宽. 我们可以设置 cvar `AbilitySystem.AlwaysConvertGESpecToGCParams 1`来优化这一点. 它会将 `GameplayEffectSpecs` 转换为 `FGameplayCueParameter` 并 RPC 替代原本整个 `FGameplayEffectSpecForRPC`. 这可能会减少带宽，但携带信息量也会减少，具体取决于 `GESpec` 是如何转换为 `GameplayCueParameters`的以及你的 `GCs` 需要什么信息.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-events"></a>
#### 4.8.8 Gameplay Cue Events
`GameplayCues` 响应特定的 `EGameplayCueEvents`:

| `EGameplayCueEvent` | Description                                                                                                                                                                                                                                                                                                                         |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `OnActive`          | Called when a `GameplayCue` is activated (added)（添加时激活）.                                                                                                                                                                                                                                                                                   |
| `WhileActive`       | Called when `GameplayCue` is active, even if it wasn't actually just applied (Join in progress, etc). This is not `Tick`! It's called once just like `OnActive` when a `GameplayCueNotify_Actor` is added or becomes relevant. If you need `Tick()`, just use the `GameplayCueNotify_Actor`'s `Tick()`. It's an `AActor` after all.（激活状态 未被应用也会调用，这不是Tick函数，它只会在变得相关时调用一次，Tick需要使用 `GameplayCueNotify_Actor`'s `Tick()` ） |
| `Removed`           | Called when a `GameplayCue` is removed. The Blueprint `GameplayCue` function that responds to this event is `OnRemove`. （移除）                                                                                                                                                                                                            |
| `Executed`          | Called when a `GameplayCue` is executed: instant effects or periodic `Tick()`. The Blueprint `GameplayCue` function that responds to this event is `OnExecute`.                                                                                                                                                                     |

`GameplayCue`在 `OnActive` 时可以做任何事，后来加入者错过了该函数也没关系. 使用 `WhileActive`在 `GameplayCue`实现你希望后来加入者看到的持续效果. 例如，如果您有一个用于 MOBA 塔爆炸的 GameplayCue，您可以将初始爆炸粒子系统和爆炸声音放入 OnActive 中，并将任何残留的持续火焰粒子或声音放入 WhileActive 中. 在这种情况下，后来加入者从 OnActive 重播初始爆炸是没有意义的，但您希望他们在 WhileActive 发生爆炸后看到地面上持续的、循环的火焰效果. `OnRemove` 需要清理在 `OnActive` 和 `WhileActive` 中添加的任何东西. `WhileActive` 在每次进入`GameplayCueNotify_Actor`相关范围时都会被调用. `OnRemove` 在离开`GameplayCueNotify_Actor`的相关范围时被调用.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-reliability"></a>
#### 4.8.9 Gameplay Cue Reliability

一般来说，`GameplayCues`应该被认为是不可靠的，因此不适合任何直接影响游戏玩法的事情.

**Executed `GameplayCues`:** 这些 `GameplayCues` 通过 unreliable multicasts 实现 并且始终时不可靠的.

**`GameplayCues` applied from `GameplayEffects`:**
* Autonomous proxy reliably receives `OnActive`, `WhileActive`, and `OnRemove`  
`FActiveGameplayEffectsContainer::NetDeltaSerialize()` calls `UAbilitySystemComponent::HandleDeferredGameplayCues()` to call `OnActive` and `WhileActive`. `FActiveGameplayEffectsContainer::RemoveActiveGameplayEffectGrantedTagsAndModifiers()` makes the call to `OnRemoved`.
* Simulated proxies reliably receive `WhileActive` and `OnRemove`  
`UAbilitySystemComponent::MinimalReplicationGameplayCues`'s replication calls `WhileActive` and `OnRemove`. The `OnActive` event is called by an unreliable multicast.

**`GameplayCues` applied without a `GameplayEffect`:**
* Autonomous proxy reliably receives `OnRemove`  
The `OnActive` and `WhileActive` events are called by an unreliable multicast.
* Simulated proxies reliably receive `WhileActive` and `OnRemove`  
`UAbilitySystemComponent::MinimalReplicationGameplayCues`'s replication calls `WhileActive` and `OnRemove`. The `OnActive` event is called by an unreliable multicast.

如果你想`GameplayCue` 中的一些东西 'reliable', 那么你就从 `GameplayEffect` 应用它并使用 `WhileActive` 和 `OnRemove` 分别添加、移除 FX.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-asg"></a>
### 4.9 Ability System Globals
[`AbilitySystemGlobals`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UAbilitySystemGlobals/index.html) 保存有关 GAS的全局信息.大部分变量可以在 `DefaultGame.ini`中配置. 一般来说你只需要知道它的存在，而不必与他打交道. 如果你想替换[`GameplayCueManager`](#concepts-gc-manager) 或 [`GameplayEffectContext`](#concepts-ge-context)为其子类, 你可以通过 `AbilitySystemGlobals`修改.

要使用`AbilitySystemGlobals`的子类, 需要在 `DefaultGame.ini`中修改:
```
[/Script/GameplayAbilities.AbilitySystemGlobals]
AbilitySystemGlobalsClassName="/Script/ParagonAssets.PAAbilitySystemGlobals"
```

<a name="concepts-asg-initglobaldata"></a>
#### 4.9.1 InitGlobalData()
从 UE 4.24 开始，现在需要调用 `UAbilitySystemGlobals::Get().InitGlobalData()` 才能使用 `TargetData`，否则您将收到与 `ScriptStructCache` 相关的错误，并且客户端将与服务器断开连接. 该方法在项目中仅需要调用一次. Fortnite 在 `UAssetManager::StartInitialLoading()` 中调用，Paragon 在 `UEngine::Init()`中调用. 如示例项目中所示, 我发现将其放在 `UAssetManager::StartInitialLoading() `中是一个好地方。我r认为此样板代码可以复制到你的项目中以避免 `TargetData` 出现问题.

如果你在使用`AbilitySystemGlobals` `GlobalAttributeSetDefaultsTableNames`时崩溃了, 你可能需要像Fortnite在 `AssetManager` 或 `GameInstance`中那样， 稍后调用 `UAbilitySystemGlobals::Get().InitGlobalData()`.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p"></a>
### 4.10 Prediction
GAS有开箱即用的客户端侧预测功能，但他不是万能的. GAS 的客户端侧预测意味着客户端不需要等待服务器允许即可激活`GameplayAbility` 并应用 `GameplayEffects`. 它可以“预测”服务器允许其执行的操作，并预测它将应用 `GameplayEffects` 的目标. 客户端告诉服务器执行 `GameplayAbility`的网络延迟时间后，服务器再告诉客户端它是否预测正确. 如果客户端预测有任何错误， 他都会“回滚”它的错误预测直到与服务器匹配.

GAS相关预测的代码再插件`GameplayPrediction.h`中.

Epic的心态是只预测你 “能逃脱惩罚” 的事情. 例如, Paragon 和 Fortnite并不预测伤害. 他们可能使用 [`ExecutionCalculations`](#concepts-ge-ec) 来计算伤害而者这是无法预测的. 这并不是说你无法尝试预测某些事情，例如伤害. 总而言之，如果你做了且没有错就行了.

> ... we are also not all in on a "predict everything: seamlessly and automatically" solution. We still feel player prediction is best kept to a minimum (meaning: predict the minimum amount of stuff you can get away with).

*Dave Ratti from Epic's comment from the new [Network Prediction Plugin](#concepts-p-npp)*

**可以被预测的:**
> * Ability activation
> *	Triggered Events
> *	GameplayEffect application:
>    * Attribute modification (EXCEPTIONS: Executions do not currently predict, only attribute modifiers)
>    * GameplayTag modification
> * Gameplay Cue events (both from within predictive gameplay effect and on their own)
> * Montages
> * Movement (built into UE's UCharacterMovement)

*不可被预测的:**
> * GameplayEffect removal
> * GameplayEffect periodic effects (dots ticking)

*From `GameplayPrediction.h`*

虽然我们可以预测 `GameplayEffect` 的应用, 但我们无法预测它的移除. 解决此限制的一种方法是预测当我们想要删除 `GameplayEffect` 时使用相反效果. 假设我们需要预测一个减速 40%. 我们可以通过设置一个加速 40%的buff假设它的移除. 然后同时移除两个 `GameplayEffects`. 这并不适合所有场景，仍然需要支持预测 `GameplayEffect` 删除。 Epic 的 Dave Ratti 表示希望将其添加到 GAS 的 [未来迭代](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89).

因为我们无法预测 `GameplayEffects` 的删除，所以我们无法完全预测 `GameplayAbility` 的冷却时间，并且没有反转这个效果. 服务器的复制`Cooldown GE` 会存在客户端上，任何尝试绕过此的尝试 (with `Minimal` replication mode for example) 都会被服务器拒绝. 这意味着具有较高延迟的客户端需要更长的时间来告诉服务器进行冷却并接收服务器`Cooldown GE` 的删除. 这意味着延迟较高的玩家的开火率会低于延迟较低的玩家，从而使他们在对抗延迟较低的玩家时处于劣势。 Fortnite 通过使用自定义bookkeeping而不是 Cooldown GE 来避免此问题.

关于预测伤害，我个人并不推荐它，尽管它是大多数人在开始使用 GAS 时首先尝试的事情之一。我特别不建议尝试预测死亡. 虽然可以预测伤害，但这样做很不好。如果你错误地预测了施加的伤害，玩家会看到敌人的生命值跳回. 如果你试图预测死亡，这可能会特别尴尬和令人沮丧。假设你错误地预测了一个角色的死亡，它开始ragdolling，但当服务器纠正它时，它会停止ragdolling并继续向你射击.

**注意:** `Instant`	`GameplayEffects` (like `Cost GEs`) 可以被你自己预测去改变 `Attributes` , 预测其他玩家的 `Instant` `Attribute` 改变将显示短暂的异常或 "blip" . 预测的`Instant` `GameplayEffects` 实际上被视为`Infinite` `GameplayEffects`，因此如果预测错误，它们可以回滚. 当服务器的 `GameplayEffect` 被应用时, 可能存在两个相同的 `GameplayEffect` 导致`Modifier`被应用两次或在短时间内根本不应用. 它最终会自行纠正，但有时玩家会注意到这一点.

GAS预测实现待解决的问题:
> 1. "Can I do this?" Basic protocol for prediction.
> 2. "Undo" How to undo side effects when a prediction fails.
> 3. "Redo" How to avoid replaying side effects that we predicted locally but that also get replicated from the server.
> 4. "Completeness" How to be sure we /really/ predicted all side effects.
> 5. "Dependencies" How to manage dependent prediction and chains of predicted events.
> 6. "Override" How to override state predictively that is otherwise replicated/owned by the server.

*From `GameplayPrediction.h`*

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p-key"></a>
#### 4.10.1 Prediction Key
GAS的预测时基于`Prediction Key`的概念，它是客户端在激活 `GameplayAbility`时生成的 整数 标识符.

* Client generates a prediction key when it activates a `GameplayAbility`. This is the `Activation Prediction Key`.（客户端激活时生成Key）
* Client sends this prediction key to the server with `CallServerTryActivateAbility()`. （发送服务器）
* Client adds this prediction key to all `GameplayEffects` that it applies while the prediction key is valid.（应用到GE上）
* Client's prediction key falls out of scope. Further predicted effects in the same `GameplayAbility` need a new [Scoped Prediction Window](#concepts-p-windows). （key有一定预测范围，超出范围需要新的预测窗口）

***

* Server receives the prediction key from the client.（服务器接收key）
* Server adds this prediction key to all `GameplayEffects` that it applies.（将key应用到所有GE）
* Server replicates the prediction key back to the client.（同步key给客户端）

***

* Client receives replicated `GameplayEffects` from the server with the prediction key used to apply them. If any of the replicated `GameplayEffects` match the `GameplayEffects` that the client applied with the same prediction key, they were predicted correctly. There will temporarily be two copies of the `GameplayEffect` on the target until the client removes its predicted one.（客户端接到服务器传来的复制GE并应用，复制的GE key与本地是一样的就预测准确了，目标上有两份GE副本，直到客户端删除预测的副本）
* Client receives the prediction key back from the server. This is the `Replicated Prediction Key`. This prediction key is now marked stale.（客户端接收服务器返回的预测key， 它现在已过时了）
* Client removes **all** `GameplayEffects` that it created with the now stale replicated prediction key. `GameplayEffects` replicated by the server will persist. Any `GameplayEffects` that the client added and didn't receive a matching replicated version from the server were mispredicted.（客户端删除所有过时key的GE， 服务器复制来的会保留，任何客户端添加且没收到服务同步来的就是预测错误的）

从激活预测密钥的`Activation`, 在 `GameplayAbilities` 中的原子指令“窗口”分组期间，预测密钥保证有效. 可以认为这仅在一帧内有效. 来自延迟 `AbilityTasks` 的任何回调将不再具有有效的预测键，除非 `AbilityTask` 具有生成新范围预测窗口的内置同步点 [Scoped Prediction Window](#concepts-p-windows).

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p-windows"></a>
#### 4.10.2 Creating New Prediction Windows in Abilities
想要从 `AbilityTasks`的回调中做更多预测, 我们需要使用新的范围预测密钥创建一个新的范围预测窗口. 这有时称为客户端和服务器之间的同步点. 一些 `AbilityTasks` 如输入相关的任务 则具有内置功能来创建新的范围预测窗口, 意味着 `AbilityTasks'` 回调中的原子代码有一个有效的范围预测键可供使用. 其他任务如 `WaitDelay` 没有内置代码来为其回调创建新的范围预测窗口. 如果您需要在没有内置代码来创建像 `WaitDelay` 这样的范围预测窗口的 `AbilityTask` 之后预测操作，我们必须使用带有 `OnlyServerWait` 选项的 `WaitNetSyncbilityTask` 手动执行此操作. 当客户端使用 `OnlyServerWait` 命中 `WaitNetSync` 时，它会根据 `GameplayAbility` 的激活预测密钥生成新的范围预测密钥，将其 RPC 到服务器，并将其添加到它应用的任何新 `GameplayEffects` 中. 当服务器使用 `OnlyServerWait` 命中 `WaitNetSync` 时，它会等待，直到从客户端收到新的范围预测密钥，然后再继续. 此作用域预测键与激活预测键执行相同的操作 - 应用于 `GameplayEffects` 并复制回客户端以标记为过时. 范围预测键在超出范围之前一直有效，超出意味着范围预测窗口已关闭。同样，只有原子操作（没有任何潜在的操作）可以使用新的作用域预测键.

你可以尽可能多的创建范围预测窗口.

如果您想将同步点功能添加到您自己的自定义 `AbilityTasks` 中，查看输入任务如何将 `WaitNetSync` `AbilityTask` 代码注入其中.

**注意:** 使用 `WaitNetSync` 时，这确实会阻止服务器的 `GameplayAbility` 继续执行，直到收到客户端的消息. 这可能会被恶意用户滥用，他们会破解游戏并故意延迟发送新的范围预测密钥. 虽然 Epic 很少使用 `WaitNetSync`，但如果你担心这一点，它建议你构建一个具有延迟的新版本的 `AbilityTask`​​，该延迟会在没有客户端的情况下自动继续.

示例项目在每次应用耐力成本时使用 Sprint `GameplayAbility` 中的 `WaitNetSync` 创建一个新的范围预测窗口，以便我们可以预测它。理想情况下，我们在应用成本和冷却时间时需要一个有效的预测密钥.

如果你的预测 `GameplayEffect` 在所属客户端上运行了两次，那你的预测密钥已过时，并且需要 "redo" . 您通常可以通过在应用 `GameplayEffect` 创建新的范围预测键之前放置带有 `OnlyServerWait` 的 `WaitNetSync` `AbilityTask` 来解决此问题.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p-spawn"></a>
#### 4.10.3 Predictively Spawning Actors
在客户端上预测生成 `Actors` 是高级内容. GAS并未提供开箱即用的功能 (the `SpawnActor` `AbilityTask` only spawns the `Actor` on the server). 关键点是在客户端和服务器上生成了一个可复制的 `Actor` .

如果 `Actor` 只是装饰性的或不用于任何游戏目的，则简单的解决方案是重写 `Actor` 的 `IsNetRelevantFor()` 函数以限制服务器复制到所属客户端I. 拥有的客户端将拥有其本地生成的版本，而服务器和其他客户端将拥有服务器的复制版本.
```c++
bool APAReplicatedActorExceptOwner::IsNetRelevantFor(const AActor * RealViewer, const AActor * ViewTarget, const FVector & SrcLocation) const
{
	return !IsOwnedBy(ViewTarget);
}
```

如果生成的 `Actor` 像需要预测伤害的子弹一样影响游戏玩法，那么您需要超出本文档范围的高级逻辑. 在 Epic Games 的 GitHub 上查看 UnrealTournament 如何预测性地生成子弹。他们仅在拥有的客户端上生成一个虚拟子弹，该虚拟子弹与服务器的复制子弹同步.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p-future"></a>
#### 4.10.4 Future of Prediction in GAS
`GameplayPrediction.h` 表示将来他们可能会添加预测 `GameplayEffect` 删除和定期 `GameplayEffects` 的功能.

Dave Ratti from Epic has [expressed interest](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89) in fixing the `latency reconciliation` problem for predicting cooldowns, disadvantaging players with higher latencies versus players with lower latencies.

The new [`Network Prediction` plugin](#concepts-p-npp) by Epic is expected to be fully interoperable with the GAS like the `CharacterMovementComponent` *was* before it.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p-npp"></a>
#### 4.10.5 Network Prediction Plugin
Epic 最近发起了一项计划，用新的网络预测插件替换 `CharacterMovementComponent` . 该插件仍处于早期阶段，但可以在虚幻引擎 GitHub 上访问. 现在判断该引擎将在哪个未来版本中首次亮相还为时过早.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-targeting"></a>
### 4.11 Targeting

<a name="concepts-targeting-data"></a>
#### 4.11.1 Target Data
[`FGameplayAbilityTargetData`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/FGameplayAbilityTargetData/index.html) 是用于通过网络传递的目标数据的通用结构. `TargetData` 持有 `AActor`/`UObject` 引用, `FHitResults`, 和其他通用的 location/direction/origin 信息. 但是，您可以对其进行子类化，以将任何您想要的内容放入其中，作为在 `GameplayAbilities` 中在客户端和服务器之间传递数据的简单方. 基础结构 `FGameplayAbilityTargetData` 不应该直接使用而是要继承使用. `GAS` 附带了一些开箱即用的 `FGameplayAbilityTargetData` 子类在 `GameplayAbilityTargetTypes.h`.

`TargetData` 通常由 [`Target Actors`](#concepts-targeting-actors)生成或 **手动创建** 并由  [`AbilityTasks`](#concepts-at) 和 [`GameplayEffects`](#concepts-ge) 通过 [`EffectContext`](#concepts-ge-context)使用. 由于位于 `EffectContext`中, [`Executions`](#concepts-ge-ec), [`MMCs`](#concepts-ge-mmc), [`GameplayCues`](#concepts-gc), 和其他 [`AttributeSet`](#concepts-as) 的后端函数可以访问`TargetData`.

我们通常不会直接传递 `FGameplayAbilityTargetData`, 而是使用[`FGameplayAbilityTargetDataHandle`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/FGameplayAbilityTargetDataHandle/index.html)，它有一个指向内部`FGameplayAbilityTargetData`的TArray指针. 该中间结构为 `TargetData` 的多态性提供支持.

一个继承`FGameplayAbilityTargetData`例子:
```c++
USTRUCT(BlueprintType)
struct MYGAME_API FGameplayAbilityTargetData_CustomData : public FGameplayAbilityTargetData
{
    GENERATED_BODY()
public:

    FGameplayAbilityTargetData_CustomData()
    { }

    UPROPERTY()
    FName CoolName = NAME_None;

    UPROPERTY()
    FPredictionKey MyCoolPredictionKey;

    // This is required for all child structs of FGameplayAbilityTargetData
    virtual UScriptStruct* GetScriptStruct() const override
    {
    	return FGameplayAbilityTargetData_CustomData::StaticStruct();
    }

	// This is required for all child structs of FGameplayAbilityTargetData
    bool NetSerialize(FArchive& Ar, class UPackageMap* Map, bool& bOutSuccess)
    {
	    // The engine already defined NetSerialize for FName & FPredictionKey, thanks Epic!
        CoolName.NetSerialize(Ar, Map, bOutSuccess);
        MyCoolPredictionKey.NetSerialize(Ar, Map, bOutSuccess);
        bOutSuccess = true;
        return true;
    }
}

template<>
struct TStructOpsTypeTraits<FGameplayAbilityTargetData_CustomData> : public TStructOpsTypeTraitsBase2<FGameplayAbilityTargetData_CustomData>
{
	enum
	{
        WithNetSerializer = true // This is REQUIRED for FGameplayAbilityTargetDataHandle net serialization to work
	};
};
```
将目标数据添加到handle:
```c++
UFUNCTION(BlueprintPure)
FGameplayAbilityTargetDataHandle MakeTargetDataFromCustomName(const FName CustomName)
{
	// Create our target data type, 
	// Handle's automatically cleanup and delete this data when the handle is destructed, 
	// if you don't add this to a handle then be careful because this deals with memory management and memory leaks so its safe to just always add it to a handle at some point in the frame!
	FGameplayAbilityTargetData_CustomData* MyCustomData = new FGameplayAbilityTargetData_CustomData();
	// Setup the struct's information to use the inputted name and any other changes we may want to do
	MyCustomData->CoolName = CustomName;
	
	// Make our handle wrapper for Blueprint usage
	FGameplayAbilityTargetDataHandle Handle;
	// Add the target data to our handle
	Handle.Add(MyCustomData);
	// Output our handle to Blueprint
	return Handle
}
```

为了获取值，需要进行类型安全检查，因为从handle的目标数据获取值的唯一方法是使用通用 C/C++ 转换，这不是类型安全的，可能会导致对象切片和崩溃. 对于类型检查，有多种方法可以执行此操作，两种常见的方法是:
- Gameplay Tag(s): 您可以使用子类层次结构，在该子类层次结构中，只要知道某个代码架构的功能发生，您就可以转换为基本父类型并获取其游戏标记，然后与继承类的转换标记进行比较.
- Script Struct & Static Structs: 您可以直接进行类比较（这可能涉及大量 IF 语句或创建一些模板函数）, 下面是这样做的一个示例，但基本上可以从任何 `FGameplayAbilityTargetData` 获取脚本结构（这是它作为 `USTRUCT` 的一个很好的优点，并且要求任何继承类在 `GetScriptStruct` 中指定结构类型）,比较一下是否是您要找的类型。下面是使用这些函数进行类型检查的示例:
```c++
UFUNCTION(BlueprintPure)
FName GetCoolNameFromTargetData(const FGameplayAbilityTargetDataHandle& Handle, const int Index)
{   
    // NOTE, there is two versions of this '::Get(int32 Index)' function; 
    // 1) const version that returns 'const FGameplayAbilityTargetData*', good for reading target data values 
    // 2) non-const version that returns 'FGameplayAbilityTargetData*', good for modifying target data values
    FGameplayAbilityTargetData* Data = Handle.Get(Index); // This will valid check the index for you 
    
    // Valid check we have something to use, null data means nothing to cast for
    if(Data == nullptr)
    {
       	return NAME_None;
    }
    // This is basically the type checking pass, static_cast does not have type safety, this is why we do this check.
    // If we don't do this then it will object slice the struct and thus we have no way of making sure its that type.
    if(Data->GetScriptStruct() == FGameplayAbilityTargetData_CustomData::StaticStruct())
    {
        // Here is when you would do the cast because we know its the correct type already
        FGameplayAbilityTargetData_CustomData* CustomData = static_cast<FGameplayAbilityTargetData_CustomData*>(Data);    
        return CustomData->CoolName;
    }
    return NAME_None;
}
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-targeting-actors"></a>
#### 4.11.2 Target Actors
`GameplayAbilities`使用 `WaitTargetData` `AbilityTask` 生成 spawn [`TargetActors`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/AGameplayAbilityTargetActor/index.html) 以可视化并捕获来自世界的目标信息. `TargetActors` 可能使用 [`GameplayAbilityWorldReticles`](#concepts-targeting-reticles) 展示当前目标. 确认后，目标信息将作为  [`TargetData`](#concepts-targeting-data) 返回，然后可以传递到  `GameplayEffects`.
 
`TargetActors` 基于 `AActor`，因此它们可以具有任何类型的可见组件来表示它们的目标位置和方式，例如静态网格物体或贴花. 静态网格物体可用于展示角色创建的可视化物体. 贴花被用来展示地面上的效果. 示例工程中使用 [`AGameplayAbilityTargetActor_GroundTrace`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/AGameplayAbilityTargetActor_Grou-/index.html) 在地图上显示贴画来展示流星落地效果. 有时他们也不需要显示任何东西。例如，对于 [GASShooter](https://github.com/tranek/GASShooter) 中使用的立即追踪到目标的直线的命中扫描枪来说，显示任何内容是没有意义的.

它们使用基本轨迹或碰撞重叠捕获目标信息，并根据`TargetActor`实现将结果转换为`FHitResults`或`AActor`数组. `WaitTargetData` `AbilityTask` 通过`TEnumAsByte<EGameplayTargetingConfirmation::Type> ConfirmationType` 参数决定何时确认目标. 当不使用 `TEnumAsByte<EGameplayTargetingConfirmation::Type::Instant` 时，`TargetActor` 通常在 `Tick()` 上执行跟踪/重叠，并根据其实现将其位置更新为 `FHitResult`. 虽然这会在 `Tick()` 上执行跟踪/重叠，但它通常并不可怕，因为它不会被复制，并且通常不会同时运行多个（尽管可以有多个）`TargetActor`. 请注意，它使用 `Tick()` 并且一些复杂的 `TargetActor` 可能会在上面做很多事情，就像 GASShooter 中火箭发射器的辅助功能一样. 而 `Tick()` 上的跟踪对客户端的响应非常灵敏，如果性能影响太大，您可以考虑降低 `TargetActor` 上的tick频率. 在 `TEnumAsByte<EGameplayTargetingConfirmation::Type::Instant`例子中, 立即生成`TargetActor`与 `TargetData`并销毁. `Tick()` 从未被调用. 

| `EGameplayTargetingConfirmation::Type` | 当目标确定后                                                                                                                                                                                                                                                                                                                                     |
| -------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Instant`                              | 无需额外逻辑与用户输入即可立即触发.                                                                                                                                                                                                                                                                   |
| `UserConfirmed`                        | 当用户将能力绑定到确认输入或通过调用 `UAbilitySystemComponent::TargetConfirm()` 确认时，就会触发. `TargetActor` 还将响应绑定的 `Cancel` 输入或调用 `UAbilitySystemComponent::TargetCancel()` 以取消.                              |
| `Custom`                               | GameplayTargeting Ability 负责通过调用 `UGameplayAbility::ConfirmTaskByInstanceName()` 来决定目标数据何时准备就绪. `TargetActor` 还将响应 `UGameplayAbility::CancelTask​​ByInstanceName()` 以取消.                                                                                              |
| `CustomMulti`                          | GameplayTargeting Ability 负责通过调用 `UGameplayAbility::ConfirmTaskByInstanceName()` 来决定目标数据何时准备就绪. `TargetActor` 还将响应 `UGameplayAbility::CancelTask​​ByInstanceName() `以取消。不应在数据生成时结束`AbilityTask` .                                       |

并非每个`TargetActor`都支持 EGameplayTargetingConfirmation::Type. 例如, `AGameplayAbilityTargetActor_GroundTrace` 不支持 `Instant` 确认.

`WaitTargetData` `AbilityTask` 接受 `AGameplayAbilityTargetActor` 类作为参数，并会在每次激活 `AbilityTask` 时生成一个实例，并在 `AbilityTask` 结束时销毁 `TargetActor`. `WaitTargetDataUsingActor` 的`AbilityTask`接收已经生成的`TargetActor`，但当`AbilityTask`结束时仍然会销毁它. 这两个技能任务的效率都很低，因为每次使用它们要么生成或一个已生成的 TargetActor. 它们非常适合原型设计，但在生产中，如果不断生成 `TargetData`（例如自动步枪的情况），你可能需要对其进行优化T. GASShooter 有一个自定义 [`AGameplayAbilityTargetActor`](https://github.com/tranek/GASShooter/blob/master/Source/GASShooter/Public/Characters/Abilities/GSGATA_Trace.h)子类和一个新的 and a new [`WaitTargetDataWithReusableActor`](https://github.com/tranek/GASShooter/blob/master/Source/GASShooter/Public/Characters/Abilities/AbilityTasks/GSAT_WaitTargetDataUsingActor.h) `AbilityTask` ，支持重用  `TargetActor` 而不必销毁它.

默认情况下，`TargetActor` 不被复制；但是，如果这在你的游戏中有意义，则可以让它们进行复制，以向其他玩家显示本地玩家的目标位置. 它们确实包含通过 `WaitTargetData` `AbilityTask` 上的 RPC 与服务器进行通信的默认功能. 
如果 `TargetActor`的`ShouldProduceTargetDataOnServer` 属性被设为 `false`, 那么客户端就会在确认后`UAbilityTask_WaitTargetData::OnTargetDataReadyCallback()`的`CallServerSetReplicatedTargetData()` RPC 它的 `TargetData`到server. 
如果 `ShouldProduceTargetDataOnServer` 设为 `true`, 客户端在`UAbilityTask_WaitTargetData::OnTargetDataReadyCallback()`中向服务器发送确认事件`EAbilityGenericReplicatedEvent::GenericConfirm`,服务器在收到RPC后将 进行追踪、重叠检查以在服务器上生成数据. 
如果客户端取消，它将在`UAbilityTask_WaitTargetData::OnTargetDataCancelledCallback`中向服务器发送通用取消事件
`EAbilityGenericReplicatedEvent::GenericCancel`. 
如你所见， `TargetActor` 和 `WaitTargetData` `AbilityTask` 上都有很多委托. `TargetActor`响应输入以生成和广播 `TargetData` 就绪、确认或取消委托. `WaitTargetData` 监听 `TargetActor`的 `TargetData` 准备就绪、确认和取消委托，并将该信息转发回 `GameplayAbility` 和 server. 如果你将`TargetData`发送到服务器，你可能需要在服务器上进行验证以确保`TargetData`看起来合理以防止作弊. 直接在服务器上生成 `TargetData` 可以完全避免此问题，但可能会导致拥有客户端的错误预测.

根据使用`AGameplayAbilityTargetActor` 的特定子类, 不同的 `ExposeOnSpawn` 参数会被暴露在`WaitTargetData` `AbilityTask` 节点上. 一些常见的参数包含:

| Common `TargetActor` Parameters | Definition                                                                                                                                                                                                                                                                                                               |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Debug                           | 如果为 `true`, 则每当`TargetActor`在非发布版本中执行时都会绘制  tracing/overlapping信息. 记住, 非`Instant` `TargetActors` 会在 `Tick()` 上执行追踪，因此这些绘制调用也在 `Tick()`.                                                        |
| Filter                          | [Optional] A special struct for filtering out (removing) `Actors` from the targets when the trace/overlap happens. Typical use cases are to filter out the player's `Pawn`, require targets be of a specific class. See [Target Data Filters](#concepts-target-data-filters) for more advanced use cases. |
| Reticle Class                   | [Optional] Subclass of `AGameplayAbilityWorldReticle` that the `TargetActor` will spawn.                                                                                                                                                                                                                                 |
| Reticle Parameters              | [Optional] Configure your Reticles. See [Reticles](#concepts-targeting-reticles).                                                                                                                                                                                                                                        |
| Start Location                  | A special struct for where tracing should start from. Typically this will be the player's viewpoint, a weapon muzzle, or the `Pawn`'s location.                                                                                                                                                                          |

对于默认`TargetActor` classes, `Actors` 仅当直接位于trace/overlap中时才是有效目标. 如果它们留下痕迹/重叠（它们移动或移开视线），它们就不再有效. 如果你希望 `TargetActor` 记住最后一个有效目标，则需要将此功能添加到自定义 TargetActor 类中. 我将这些称为持久目标，因为它们将持续存在，直到 `TargetActor` 收到确认或取消、`TargetActor` 在其跟踪/重叠中找到新的有效目标，或者目标不再有效（已销毁）. GASShooter 使用持久目标作为其火箭发射器的辅助能力的寻的火箭瞄准.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-target-data-filters"></a>
#### 4.11.3 Target Data Filters
使用 Make `GameplayTargetDataFilter` 和 `Make Filter Handle` 节点，你可以过滤掉玩家的 `Pawn` 或仅选择特定类. 如果你需要更多过滤设置，你可以继承`FGameplayTargetDataFilter`并重载 `FilterPassesForActor` 方法. 
```c++
USTRUCT(BlueprintType)
struct GASDOCUMENTATION_API FGDNameTargetDataFilter : public FGameplayTargetDataFilter
{
	GENERATED_BODY()

	/** Returns true if the actor passes the filter and will be targeted */
	virtual bool FilterPassesForActor(const AActor* ActorToBeFiltered) const override;
};
```

但是，这不能直接作用于`Wait Target Data` 节点，因为它需要 `FGameplayTargetDataFilterHandle`. 必须创建一个新的自定义 `Make Filter Handle` 几点来接收子类:
```c++
FGameplayTargetDataFilterHandle UGDTargetDataFilterBlueprintLibrary::MakeGDNameFilterHandle(FGDNameTargetDataFilter Filter, AActor* FilterActor)
{
	FGameplayTargetDataFilter* NewFilter = new FGDNameTargetDataFilter(Filter);
	NewFilter->InitializeFilterContext(FilterActor);

	FGameplayTargetDataFilterHandle FilterHandle;
	FilterHandle.Filter = TSharedPtr<FGameplayTargetDataFilter>(NewFilter);
	return FilterHandle;
}
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-targeting-reticles"></a>
#### 4.11.4 Gameplay Ability World Reticles
[`AGameplayAbilityWorldReticles`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/AGameplayAbilityWorldReticle/index.html) (`Reticles标线`) 在使用非`Instant`确认的 `TargetActor` 进行定位时可视化您的目标对象. `TargetActors` 负责所有`Reticles`的生成销毁生命周期. `Reticles` 是 `AActors` 因此它们可以使用任何类型的视觉组件进行表示. 如[GASShooter](https://github.com/tranek/GASShooter)所见到的一样，一种通用的实现是使用一个 `WidgetComponent` 用UMG Widget 在屏幕空间显示 (始终面向玩家的镜头). `Reticles` 不知道它们位于哪个`AActor` 上, 但你可以在自定义的`TargetActor上对该功能重写. `TargetActors` 通常在每个`Tick()`对 `Reticle`更新为目标的位置.

GASShooter 使用 `Reticles` 十字线来显示火箭发射器辅助能力的自导火箭的锁定目标. 敌人身上的红色指示器是十字线。类似的白色图像是火箭发射器的十字准线.
![Reticles in GASShooter](https://github.com/tranek/GASDocumentation/raw/master/Images/gameplayabilityworldreticle.png)

`Reticles` 对开发者提供了一些 `BlueprintImplementableEvents` (they're intended to be developed in Blueprints):

```c++
/** Called whenever bIsTargetValid changes value. */
UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void OnValidTargetChanged(bool bNewValue);

/** Called whenever bIsTargetAnActor changes value. */
UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void OnTargetingAnActor(bool bNewValue);

UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void OnParametersInitialized();

UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void SetReticleMaterialParamFloat(FName ParamName, float value);

UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void SetReticleMaterialParamVector(FName ParamName, FVector value);
```

`Reticles`可以使用`TargetActor`提供的 [`FWorldReticleParameters`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/FWorldReticleParameters/index.html) 进行配置. 默认 struct 只提供 `FVector AOEScale`变量. 虽然你可以继承这个结构体,但 TargetActor 将仅接受基本结构. 不允许使用默认 `TargetActors` 对其进行子类化似乎有点短视. 但是，如果创建自己的自定义 `TargetActor`，则可以提供自己的自定义标线参数结构，并在生成 `AGameplayAbilityWorldReticles` 时手动将其传递给 `AGameplayAbilityWorldReticles` .

`Reticles` 默认是不复制的, 如果你的游戏需要向其他玩家展示目标则可以进行复制.

`Reticles` 只会显示在具有默认 `TargetActors` 的当前有效目标上. 例如, 如果你使用 `AGameplayAbilityTargetActor_SingleLineTrace` 追踪目标,仅当敌人直接位于追踪路径中时  `Reticle` 才会出现. 如果你看向别处, 敌人就不再是有效目标且 `Reticle` 会消失. 如果你想 `Reticle`停止最后一个有效目标上, 你需要自定义自己的 `TargetActor` 来记住最后一个目标并让 `Reticle` 保存在上面. I refer to these as persistent targets as they will persist until the `TargetActor` receives confirmation or cancellation, the `TargetActor` finds a new valid target in its trace/overlap, or the target is no longer valid (destroyed).  GASShooter uses persistent targets for its rocket launcher's secondary ability's homing rockets targeting.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-targeting-containers"></a>
#### 4.11.5 Gameplay Effect Containers Targeting
[`GameplayEffectContainers`](#concepts-ge-containers) 提供了一种可选的、高效的生成 [`TargetData`](#concepts-targeting-data)方法. 当`EffectContainer` 应用于客户端和服务器时，此targeting会立即发生. 它比 [`TargetActors`](#concepts-targeting-actors)更高效，因为它在目标的CDO上运行 (没有 `Actors`的生成与销毁), 但它缺乏玩家输入，无需确认即可立即发生，无法取消，并且无法从客户端向服务器发送数据 (双端产生的数据). 它非常适合即时追踪和碰撞重叠. Epic的 [Action RPG Sample Project](https://www.unrealengine.com/marketplace/en-US/product/action-rpg) 包含两种 targeting  containers 方式- target the ability owner and pull `TargetData` from an event. 它还在蓝图中实现了一个功能，以在距播放器的一定偏移量（由子蓝图类设置）处进行即时球体跟踪. 你可以在C++或蓝图中重载  `URPGTargetType` 来创建你自己的的类型.

**[⬆ Back to Top](#table-of-contents)**

<a name="cae"></a>
## 5. Commonly Implemented Abilities and Effects

<a name="cae-stun"></a>
### 5.1 Stun
通常，对于眩晕，我们希望取消角色的所有活动 `GameplayAbilities`，防止新的 `GameplayAbility` 激活，并防止在眩晕期间移动. 示例项目的Meteor `GameplayAbility` 对击中目标施加眩晕效果.

为了取消激活的 `GameplayAbilities`, 在眩晕的[`GameplayTag`添加时](#concepts-gt-change)我们可以调用 `AbilitySystemComponent->CancelAbilities()` 取消.

在眩晕时为了阻止新的`GameplayAbilities` 激活,  `GameplayAbilities` 将眩晕 `GameplayTag` 放入 [`Activation Blocked Tags` `GameplayTagContainer`](#concepts-ga-tags).

阻止眩晕时移动, 我们重载 `CharacterMovementComponent's` `GetMaxSpeed()` 函数在有眩晕 `GameplayTag`时返回0.

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-sprint"></a>
### 5.2 Sprint
示例工程提供了如何冲刺 - 当 `Left Shift` 按住时跑的更快.

通过网络向服务器发送一个标志，`CharacterMovementComponent` 可以预测性的处理更快的速度. 参考 `GDCharacterMovementComponent.h/cpp` .

`GA` 处理并响应 `Left Shift` 输入, 告诉 `CMC` 开始和结束冲刺, 并在按下 `Left Shift` 是预测性补充耐力. 参考 `GA_Sprint_BP`.

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-ads"></a>
### 5.3 Aim Down Sights
示例项目以与冲刺完全相同的方式处理此问题，但降低而不是增加移动速度.

参考 `GDCharacterMovementComponent.h/cpp` 有关预测性降低移动速度的详细信息.

参考 `GA_AimDownSight_BP` 获取处理输入信息. 此处没有耐力消耗.

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-ls"></a>
### 5.4 Lifesteal
我在伤害计算 [`ExecutionCalculation`](#concepts-ge-ec)时处理吸血效果. `GameplayEffect` 会有一个类似`Effect.CanLifesteal`的 `GameplayTag`. `ExecutionCalculation` 检查是否在`GameplayEffectSpec`中有 `Effect.CanLifesteal` `GameplayTag`.如果这个 `GameplayTag` 存在, `ExecutionCalculation` [创建一个动态 `Instant` `GameplayEffect`](#concepts-ge-dynamic) 包含一定生命值返回到`Source'的 `ASC`.

```c++
if (SpecAssetTags.HasTag(FGameplayTag::RequestGameplayTag(FName("Effect.Damage.CanLifesteal"))))
{
	float Lifesteal = Damage * LifestealPercent;

	UGameplayEffect* GELifesteal = NewObject<UGameplayEffect>(GetTransientPackage(), FName(TEXT("Lifesteal")));
	GELifesteal->DurationPolicy = EGameplayEffectDurationType::Instant;

	int32 Idx = GELifesteal->Modifiers.Num();
	GELifesteal->Modifiers.SetNum(Idx + 1);
	FGameplayModifierInfo& Info = GELifesteal->Modifiers[Idx];
	Info.ModifierMagnitude = FScalableFloat(Lifesteal);
	Info.ModifierOp = EGameplayModOp::Additive;
	Info.Attribute = UPAAttributeSetBase::GetHealthAttribute();

	SourceAbilitySystemComponent->ApplyGameplayEffectToSelf(GELifesteal, 1.0f, SourceAbilitySystemComponent->MakeEffectContext());
}
```

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-random"></a>
### 5.5 Generating a Random Number on Client and Server
有时你需要在`GameplayAbility`内生成一个"随机"数值做类似子弹反冲和散步. 客户端和服务器可能需要产生相同的数字. 为了做到这些, 我们需要在 `GameplayAbility` 激活时设置相同的 `random seed`. 每次激活 `GameplayAbility` 时，你都需要设置随机种子，以防客户端错误预测激活并且其随机数序列与服务器的不同步.

| Seed Setting Method                                                          | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| ---------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Use the activation prediction key                                            | The `GameplayAbility` activation prediction key is an int16 guaranteed to be synchronized and available in both the client and server in the `Activation()`. You can set this as the `random seed` on both the client and the server. The downside to this method is that the prediction key always starts at zero each time the game starts and consistently increments the value to use between generating keys. This means each match will have the exact same random number sequence. This may or may not be random enough for your needs. （使用key作为seed,缺点是每次游戏开始预测都是从0开始）|
| Send a seed through an event payload when you activate the `GameplayAbility` | Activate your `GameplayAbility` by event and send the randomly generated seed from the client to the server via the replicated event payload. This allows for more randomness but the client could easily hack their game to only send the same seed value every time. Also activating `GameplayAbilities` by event will prevent them from activating from the input bind.（在激活参数中传递seed，但是客户端容易作弊发相同的seed，靠事件激活的无法从输入绑定激活）                                                                                                                                                                     |

如果您的随机偏差很小，大多数玩家不会注意到每个游戏的序列都是相同的，并且使用激活预测密钥作为随机种子应该适合. 如果你需要做一些防破解的事，可以使用 `Server Initiated` `GameplayAbility` 会更好，因为服务器可以创建预测密钥或生成随机种子以通过事件负载发送.

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-crit"></a>
### 5.6 Critical Hits
我在伤害计算 [`ExecutionCalculation`](#concepts-ge-ec)中处理暴击. `GameplayEffect`会有一个`GameplayTag` 类似 `Effect.CanCrit`. `ExecutionCalculation` 检测 `GameplayEffectSpec` 是否有 `Effect.CanCrit` `GameplayTag`. 如果这个 `GameplayTag` 存在, `ExecutionCalculation`生成一个与暴击几率对应的随机数(`Attribute` captured from the `Source`) 在成功时添加暴击伤害 (also an `Attribute` captured from the `Source`) . 由于我不预测伤害，因此我不必担心同步客户端和服务器上的随机数生成器，因为 `ExecutionCalculation` 只会在服务器上运行. 如果你想使用 `MMC`做一些伤害计算预测, 你需要从 `GameplayEffectSpec->GameplayEffectContext->GameplayAbilityInstance`获取随机种子的引用.

查看 [GASShooter](https://github.com/tranek/GASShooter) 做的爆头. 他们时相同的概念，只是它不依赖随机数，而是检查`FHitResult`中骨骼信息.

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-nonstackingge"></a>
### 5.7 Non-Stacking Gameplay Effects but Only the Greatest Magnitude Actually Affects the Target
Paragon中缓慢效果不i会叠加. 每个缓慢实例都正常应用并跟踪其生命周期，但只有最大程度的缓慢效果真正影响了 `Character`. GAS 通过`AggregatorEvaluateMetaData`为这个场景提供了开箱即用的功能. 查看 [`AggregatorEvaluateMetaData()`](#concepts-as-onattributeaggregatorcreated) 获取更多详情和实现.

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-paused"></a>
### 5.8 Generate Target Data While Game is Paused
如果你想从玩家等待`WaitTargetData` `AbilityTask`生成[`TargetData`](#concepts-targeting-data)时暂停游戏，我建议将暂停改为使用`slomo 0`.

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-onebuttoninteractionsystem"></a>
### 5.9 One Button Interaction System
[GASShooter](https://github.com/tranek/GASShooter) 实现了一个一键交互系统，玩家可以按下或按住“E”来与可交互对象进行交互，例如复活玩家、打开武器箱以及打开或关闭滑动门.

**[⬆ Back to Top](#table-of-contents)**

<a name="debugging"></a>
## 6. Debugging GAS
通常在调试 GAS 相关问题时，您想了解以下信息:
> * "What are the values of my attributes?"
> * "What gameplay tags do I have?"
> * "What gameplay effects do I currently have?"
> * "What abilities do I have granted, which ones are running, and which ones are blocked from activating?".

GAS 提供了两种在运行时回答这些问题的技术 - [`showdebug abilitysystem`](#debugging-sd) and hooks in the [`GameplayDebugger`](#debugging-gd).

**Tip:** Unreal Engine 喜欢优化 C++ 代码，这使得某些功能很难调试. 当深入跟踪代码时，您很少会遇到这种情况. 如果你将Visual Studio solution configuration 设置为 `DebugGame Editor` 仍然无法跟踪代码或检查变量, 你可以重载优化函数来禁用所有优化，使用定义在CoreMiscDefines.h中的  `UE_DISABLE_OPTIMIZATION` and `UE_ENABLE_OPTIMIZATION` macros or the ship variations. 这不能用于插件代码除非你重编这些源码. 这可能在内联函数上起作用，也可能不起作用，具体取决于它们的作用和位置。完成调试后请务必删除宏!

```c++
UE_DISABLE_OPTIMIZATION
void MyClass::MyFunction(int32 MyIntParameter)
{
	// My code
}
UE_ENABLE_OPTIMIZATION
```

**[⬆ Back to Top](#table-of-contents)**

<a name="debugging-sd"></a>
### 6.1 showdebug abilitysystem
输入 `showdebug abilitysystem` 在游戏的console. 此功能分为三个“页面”. 所有的三个界面都展示了你当前拥有的 `GameplayTags` . 在console输入 `AbilitySystem.Debug.NextCategory`在几个页面跳转.

第一个界面展示所有`Attributes`的 `CurrentValue` :
![First Page of showdebug abilitysystem](https://github.com/tranek/GASDocumentation/raw/master/Images/showdebugpage1.png)

第二个界面展示身上所有 `Duration` 和 `Infinite` `GameplayEffects` , their number of stacks, what `GameplayTags` they give, and what `Modifiers` they give.
![Second Page of showdebug abilitysystem](https://github.com/tranek/GASDocumentation/raw/master/Images/showdebugpage2.png)

第三个界面展示所有授予的 `GameplayAbilities` , whether they are currently running, whether they are blocked from activating, and the status of currently running `AbilityTasks`.
![Third Page of showdebug abilitysystem](https://github.com/tranek/GASDocumentation/raw/master/Images/showdebugpage3.png)

跳转目标 (denoted by a green rectangular prism around the Actor), use the `PageUp` key or `NextDebugTarget` console command to go to the next target and the `PageDown` key or `PreviousDebugTarget` console command to go to the previous target.

**Note:** 为了使系统信息能够根据当前选择的调试Actor进行更新, 你需要设置 `bUseDebugTargetFromHud=true` 在 `AbilitySystemGlobals` :
```
[/Script/GameplayAbilities.AbilitySystemGlobals]
bUseDebugTargetFromHud=true
```

**Note:** 为了 `showdebug abilitysystem` 正常运行，必须在设置GameMode中的 HUD class. 否则找不到该命令并返回“Unknown Command”.

**[⬆ Back to Top](#table-of-contents)**

<a name="debugging-gd"></a>
### 6.2 Gameplay Debugger
GAS 为游戏调试器添加了功能。使用撇号 (') 键访问游戏调试器. 按数字键盘上的 3 启用Ability类别. 根据你拥有的插件，类别可能会有所不同. 如果你的键盘没有像笔记本电脑那样的小键盘，那么可以在项目设置中更改键绑定.

想要查看其他角色的 `GameplayTags`, `GameplayEffects`, 和 `GameplayAbilities` 时. 不幸的是，它没有显示目标`Attributes`的`CurrentValue` .它将瞄准屏幕中央的任何角色. 你可以通过在编辑器的世界大纲中选择目标或查看不同的角色并再次按撇号（'）来更改目标. 当前检查的角色上方有最大的红色圆圈.

![Gameplay Debugger](https://github.com/tranek/GASDocumentation/raw/master/Images/gameplaydebugger.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="debugging-log"></a>
### 6.3 GAS Logging
GAS 源代码包含大量以不同详细级别生成的日志记录语句. 你可能很喜欢看见如 `ABILITY_LOG()` 语句. 默认详细级别为`Display`. 默认情况更高级别的日志不会再console中显示.

To change the verbosity level of a log category, type into your console:

```
log [category] [verbosity]
```

For example, to turn on `ABILITY_LOG()` statements, you would type into your console:
```
log LogAbilitySystem VeryVerbose
```

To reset it back to default, type:
```
log LogAbilitySystem Display
```

To display all log categories, type:
```
log list
```

Notable GAS related logging categories:

| Logging Category          | Default Verbosity Level |
| ------------------------- | ----------------------- |
| LogAbilitySystem          | Display                 |
| LogAbilitySystemComponent | Log                     |
| LogGameplayCueDetails     | Log                     |
| LogGameplayCueTranslator  | Display                 |
| LogGameplayEffectDetails  | Log                     |
| LogGameplayEffects        | Display                 |
| LogGameplayTags           | Log                     |
| LogGameplayTasks          | Log                     |
| VLogAbilitySystem         | Display                 |

See the [Wiki on Logging](https://unrealcommunity.wiki/logging-lgpidy6i) for more information.

**[⬆ Back to Top](#table-of-contents)**

<a name="optimizations"></a>
## 7. Optimizations

<a name="optimizations-abilitybatching"></a>
### 7.1 Ability Batching
[`GameplayAbilities`](#concepts-ga) that activate, optionally send `TargetData` to the server, and end all in one frame can be [batched to condense two-three RPCs into one RPC](#concepts-ga-batching). These types of abilities are commonly used for hitscan guns.

<a name="optimizations-gameplaycuebatching"></a>
### 7.2 Gameplay Cue Batching
If you're sending many [`GameplayCues`](#concepts-gc) at the same time, consider [batching them into one RPC](#concepts-gc-batching). The goal is to reduce the number of RPCs (`GameplayCues` are unreliable NetMulticasts) and send as little data as possible.

<a name="optimizations-ascreplicationmode"></a>
### 7.3 AbilitySystemComponent Replication Mode
By default, the [`ASC`](#concepts-asc) is in [`Full Replication Mode`](#concepts-asc-rm). This will replicate all [`GameplayEffects`](#concepts-ge) to every client (which is fine for a single player game). In a multiplayer game, set the player owned `ASCs` to `Mixed Replication Mode` and AI controlled characters to `Minimal Replication Mode`. This will replicate `GEs` applied on a player character to only replicate to the owner of that character and `GEs` applied on AI controlled characters will never replicate `GEs` to clients. [`GameplayTags`](#concepts-gt) will still replicate and [`GameplayCues`](#concepts-gc) will still be unreliable NetMulticast to all clients, regardless of the `Replication Mode`. This will cut down on network data from `GEs` being replicated when all clients don't need to see them.

<a name="optimizations-attributeproxyreplication"></a>
### 7.4 Attribute Proxy Replication
In large games with many players like Fortnite Battle Royale (FNBR), there will be a lot of [`ASCs`](#concepts-asc) living on always-relevant `PlayerStates` replicating a lot of [`Attributes`](#concepts-a). To optimize this bottleneck, Fortnite disables the `ASC` and its [`AttributeSets`](#concepts-as) from replicating altogether on **simulated player-controlled proxies** in the `PlayerState::ReplicateSubobjects()`. Autonomous proxies and AI controlled `Pawns` still fully replicate according to their [`Replication Mode`](#concepts-asc-rm). Instead of replicating `Attributes` on the `ASC` on the always-relevant `PlayerStates`, FNBR uses a replicated proxy structure on the player's `Pawn`. When `Attributes` change on the server's `ASC`, they are changed on the proxy struct too. The client receives the replicated `Attributes` from the proxy struct and pushes the changes back into its local `ASC`. This allows `Attribute` replication to use the `Pawn`'s relevancy and `NetUpdateFrequency`. This proxy struct also replicates a small white-listed set of `GameplayTags` in a bitmask. This optimization reduces the amount of data over the network and allows us to take advantage of pawn relevancy. AI controlled `Pawns` have their `ASC` on the `Pawn` which already uses its relevancy so this optimization is not needed for them.

> I’m not sure if it is still necessary with other server side optimizations that have been done since then (Replication Graph, etc) and it is not the most maintainable pattern.

*Dave Ratti from Epic's answer to [community questions #3](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89)*

<a name="optimizations-asclazyloading"></a>
### 7.5 ASC Lazy Loading
Fortnite Battle Royale (FNBR) has a lot of damageable `AActors` (trees, buildings, etc) in the world, each with an [`ASC`](#concepts-asc). This can add up in memory cost. FNBR optimizes this by lazily loading `ASCs` only when they're needed (when they first take damage by a player). This reduces overall memory usage since some `AActors` may never be damaged in a match.

**[⬆ Back to Top](#table-of-contents)**

<a name="qol"></a>
## 8. Quality of Life Suggestions

<a name="qol-gameplayeffectcontainers"></a>
### 8.1 Gameplay Effect Containers
[GameplayEffectContainers](#concepts-ge-containers) combine [`GameplayEffectSpecs`](#concepts-ge-spec), [`TargetData`](#concepts-targeting-data), [simple targeting](#concepts-targeting-containers), and related functionality into easy to use structures. These are great for transfering `GameplayEffectSpecs` to projectiles spawned from an ability that will then apply them on collision at a later time.

<a name="qol-asynctasksascdelegates"></a>
### 8.2 Blueprint AsyncTasks to Bind to ASC Delegates
To increase designer-friendly iteration times, especially when designing UMG Widgets for UI, create Blueprint AsyncTasks (in C++) to bind to the common change delegates on the `ASC` directly from your UMG Blueprint graphs. The only caveat is that they must be manually destroyed (like when the widget is destroyed) otherwise they will live in memory forever. The Sample Project includes three Blueprint AsyncTasks.

Listen for `Attribute` changes:

![Listen for Attributes Changes BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/attributeschange.png)

Listen for cooldown changes:

![Listen for Cooldown Change BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/cooldownchange.png)

Listen for `GE` stack changes:

![Listen for GameplayEffect Stack Change BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/gestackchange.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="troubleshooting"></a>
## 9. Troubleshooting

<a name="troubleshooting-notlocal"></a>
### 9.1 `LogAbilitySystem: Warning: Can't activate LocalOnly or LocalPredicted ability %s when not local!`
You need to [initialize the `ASC` on the client](#concepts-asc-setup).

**[⬆ Back to Top](#table-of-contents)**

<a name="troubleshooting-scriptstructcache"></a>
### 9.2 `ScriptStructCache` errors
You need to call [`UAbilitySystemGlobals::InitGlobalData()`](#concepts-asg-initglobaldata).

**[⬆ Back to Top](#table-of-contents)**

<a name="troubleshooting-replicatinganimmontages"></a>
### 9.3 Animation Montages are not replicating to clients
Make sure that you're using the `PlayMontageAndWait` Blueprint node instead of `PlayMontage` in your [GameplayAbilities](#concepts-ga). This [AbilityTask](#concepts-at) replicates the montage through the `ASC` automatically whereas the `PlayMontage` node does not.

**[⬆ Back to Top](#table-of-contents)**

<a name="troubleshooting-duplicatingblueprintactors"></a>
### 9.4 Duplicating Blueprint Actors is setting AttributeSets to nullptr
There is a [bug in Unreal Engine](https://issues.unrealengine.com/issue/UE-81109) that will set `AttributeSet` pointers on your classes to nullptr for Blueprint Actor classes that are duplicated from existing Blueprint Actor classes. There are a few workarounds for this. I've had success not creating bespoke `AttributeSet` pointers on my classes (no pointer in the .h, not calling `CreateDefaultSubobject` in the constructor) and instead just directly adding `AttributeSets` to the `ASC` in `PostInitializeComponents()` (not shown in the Sample Project). The replicated `AttributeSets` will still live in the `ASC's` `SpawnedAttributes` array. It would look something like this:

```c++
void AGDPlayerState::PostInitializeComponents()
{
	Super::PostInitializeComponents();

	if (AbilitySystemComponent)
	{
		AbilitySystemComponent->AddSet<UGDAttributeSetBase>();
		// ... any other AttributeSets that you may have
	}
}
```

In this scenario, you would read and set the values in the `AttributeSet` using the functions on the `ASC` instead of [calling functions on the `AttributeSet` made from the macros](#concepts-as-attributes).

```c++
/** Returns current (final) value of an attribute */
float GetNumericAttribute(const FGameplayAttribute &Attribute) const;

/** Sets the base value of an attribute. Existing active modifiers are NOT cleared and will act upon the new base value. */
void SetNumericAttributeBase(const FGameplayAttribute &Attribute, float NewBaseValue);
```

So the `GetHealth()` would look something like:

```c++
float AGDPlayerState::GetHealth() const
{
	if (AbilitySystemComponent)
	{
		return AbilitySystemComponent->GetNumericAttribute(UGDAttributeSetBase::GetHealthAttribute());
	}

	return 0.0f;
}
```

Setting (initializing) the health `Attribute` would look something like:

```c++
const float NewHealth = 100.0f;
if (AbilitySystemComponent)
{
	AbilitySystemComponent->SetNumericAttributeBase(UGDAttributeSetBase::GetHealthAttribute(), NewHealth);
}
```

As a reminder, the `ASC` only ever expects at most one `AttributeSet` object per `AttributeSet` class.

**[⬆ Back to Top](#table-of-contents)**

<a name="troubleshooting-unresolvedexternalsymbolmarkpropertydirty"></a>
### 9.5 unresolved external symbol UEPushModelPrivate::MarkPropertyDirty(int,int)

If you get a compiler error like:

```
error LNK2019: unresolved external symbol "__declspec(dllimport) void __cdecl UEPushModelPrivate::MarkPropertyDirty(int,int)" (__imp_?MarkPropertyDirty@UEPushModelPrivate@@YAXHH@Z) referenced in function "public: void __cdecl FFastArraySerializer::IncrementArrayReplicationKey(void)" (?IncrementArrayReplicationKey@FFastArraySerializer@@QEAAXXZ)
```

This is from trying to call `MarkItemDirty()` on a `FFastArraySerializer`. I've encountered this from updating an `ActiveGameplayEffect` such as when updating the cooldown duration.

```c++
ActiveGameplayEffects.MarkItemDirty(*AGE);
```

What's happening is that `WITH_PUSH_MODEL` is getting defined in more than one place. `PushModelMacros.h` is defining it as 0 while it's defined as 1 in multiple places. `PushModel.h` is seeing it as 1 but `PushModel.cpp` is seeing it as 0.

The solution is to add `NetCore` to your project's `PublicDependencyModuleNames` in the `Build.cs`.

**[⬆ Back to Top](#table-of-contents)**

<a name="troubleshooting-enumnamesarenowpathnames"></a>
### 9.6 Enum names are now represented by path name

If you get a compiler warning like:

```
warning C4996: 'FGameplayAbilityInputBinds::FGameplayAbilityInputBinds': Enum names are now represented by path names. Please use a version of FGameplayAbilityInputBinds constructor that accepts FTopLevelAssetPath. Please update your code to the new API before upgrading to the next release, otherwise your project will no longer compile.
```

UE 5.1 deprecated using `FString` in the constructor for `BindAbilityActivationToInputComponent()`. Instead, we must pass in an `FTopLevelAssetPath`.

Old, deprecated way:
```c++
AbilitySystemComponent->BindAbilityActivationToInputComponent(InputComponent, FGameplayAbilityInputBinds(FString("ConfirmTarget"),
	FString("CancelTarget"), FString("EGDAbilityInputID"), static_cast<int32>(EGDAbilityInputID::Confirm), static_cast<int32>(EGDAbilityInputID::Cancel)));
```

New way:
```c++
FTopLevelAssetPath AbilityEnumAssetPath = FTopLevelAssetPath(FName("/Script/GASDocumentation"), FName("EGDAbilityInputID"));
AbilitySystemComponent->BindAbilityActivationToInputComponent(InputComponent, FGameplayAbilityInputBinds(FString("ConfirmTarget"),
	FString("CancelTarget"), AbilityEnumAssetPath, static_cast<int32>(EGDAbilityInputID::Confirm), static_cast<int32>(EGDAbilityInputID::Cancel)));
```

See `Engine\Source\Runtime\CoreUObject\Public\UObject\TopLevelAssetPath.h` for more info.

**[⬆ Back to Top](#table-of-contents)**

<a name="acronyms"></a>
## 10. Common GAS Acronyms

| Name                                                                                                   | Acronyms            |
|------------------------------------------------------------------------------------------------------- | ------------------- |
| AbilitySystemComponent                                                                                 | ASC                 |
| AbilityTask                                                                                            | AT                  |
| [Action RPG Sample Project by Epic](https://www.unrealengine.com/marketplace/en-US/product/action-rpg) | ARPG, ARPG Sample   |
| CharacterMovementComponent                                                                             | CMC                 |
| GameplayAbility                                                                                        | GA                  |
| GameplayAbilitySystem                                                                                  | GAS                 |
| GameplayCue                                                                                            | GC                  |
| GameplayEffect                                                                                         | GE                  |
| GameplayEffectExecutionCalculation                                                                     | ExecCalc, Execution |
| GameplayTag                                                                                            | Tag, GT             |
| ModifierMagnitudeCalculation                                                                           | ModMagCalc, MMC     |

**[⬆ Back to Top](#table-of-contents)**

<a name="resources"></a>
## 11. Other Resources
* [Official Documentation](https://docs.unrealengine.com/en-US/Gameplay/GameplayAbilitySystem/index.html)
* Source Code!
   * Especially `GameplayPrediction.h`
* [Lyra Sample Project by Epic](https://unrealengine.com/marketplace/en-US/learn/lyra)
* [Action RPG Sample Project by Epic](https://www.unrealengine.com/marketplace/en-US/product/action-rpg)
* [Unreal Slackers Discord](https://unrealslackers.org/) has a text channel dedicated to GAS `#gameplay-ability-system`
   * Check pinned messages
* [GitHub repository of resources by Dan 'Pan'](https://github.com/Pantong51/GASContent)
* [YouTube Videos by SabreDartStudios](https://www.youtube.com/channel/UCCFUhQ6xQyjXDZ_d6X_H_-A)

<a name="resources-daveratti"></a>
### 11.1 Q&A With Epic Game's Dave Ratti

<a name="resources-daveratti-community1"></a>
#### 11.1.1 Community Questions 1
[Dave Ratti responses to the Unreal Slackers Discord Server community questions about GAS](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89):

1. How can we create scoped prediction windows on demand outside or irrespective of `GameplayAbilities`? For example, how can a fire and forget projectile locally predict a damage `GameplayEffect` when it hits an enemy?

> The PredictionKey system is not really meant to do this. Fundamentally this systems works by a client initiating a predictive action, telling the server about it with a key, and then both client and server running the same thing and associating predictive side effects with the given prediction key. For example, “I am predictively activating an ability” or “I have produced target data and am going to predictively run the part of the ability graph after the WaitTargetData task”.
>
> With this pattern, the PredictionKey “bounces” off the server and comes back to the client via UAbilitySystemComponent::ReplicatedPredictionKeyMap (replicated property). Once the key is replicated back from the server, the client is able to undo all of the locally predictive side effects (GameplayCues, GameplayEffects): the replicated versions *will be there* and if they aren’t then it was a misprediction. Knowing exactly when to undo the predictive side effects is crucial here: if you are too early you will see gaps, if you are too late you will have “double”. (Note this is referring to stateful prediction, like a looping GameplayCue of a duration based Gameplay Effect. “Burst” GameplayCues and instant Gameplay Effects are never “undone” or rolled back. They are just skipped on the client if there is a prediction key associated with them).
>
> To further hit home the point: it’s crucial that predictive action is something the server does not do on their own, but only does so when the client tells them to. So having a generic “Create a key on demand and tell the server so I can run something” does not work unless that “something” is something the server will only do once told to by the client.
>
> Backing up to the original question: something like a fire and forget projectile. Both Paragon and Fornite have projectile actor classes that use GameplayCues. However we do not use the Prediction Key system to do these. Instead we have a concept on Non-Replicated GameplayCues. GameplayCues that just fire off locally and are skipped by the server completely. Essentially all these are direct calls to UGameplayCueManager::HandleGameplayCue. They do not route through the UAbilitySystemComponent so no prediction key checks / early returns are made.
>
> The downside with non replicated GameplayCues is that, well, they are not replicated. So its up to the projectile class/blueprint to make sure the code paths that call these functions are running on everyone. We have for cues startup (called in BeginPlay), explosion, hit wall/character, etc.
>
> These type of events are already generated client side, so calling into a non replicated gameplay cue was no big deal. Complicated blueprints can be tricky, and are up to the author to make sure they understand what is running where.

2. When using a `WaitNetSync` `AbilityTask` with `OnlyServerWait` to create a scoped prediction window in a locally predicted `GameplayAbility`, could players potentially cheat by delaying their packets to the Server to control `GameplayAbility` timing since the Server is waiting for their RPC withtheir prediction key? Was this ever an issue in Paragon or Fortnite, and if so, what did Epic do to remedy it?

> Yes, this is a valid concern. Any ability blueprint running on the server that is waiting for a client “signal” is potentially vulnerable to lag switch type exploits.
>
> Paragon had a custom targeting task similar to UAbilityTask_WaitTargetData. In this task we had timeouts, or a “max delay” that we would wait on the client for instantaneous targeting modes. If the targeting mode was waiting for user confirmation (button press) then it would be ignored since the user is allowed to take his time. But for abilities that instantly confirmed targeting we would only wait a certain amount of time before either A) generating the target data server side or B) canceling the ability.
>
> We never had such mechanisms for WaitNetSync, which we used pretty sparingly.
>
> I don’t believe Fortnite makes use of anything like this though. The weapon abilities in Fortnite are special cased batched to a single fortnite-specific RPC: one RPC to activate the ability, provide target data, and end the ability. So weapon abilities are intrinsically not vulnerable to this in Battle Royale.
>
> My take is that this is something that could probably be solved system wide but I don’t see us making the change ourselves anytime soon. Spot fixing WaitNetSync to include a max delay for the case you mention is probably a reasonable task, but again - unlikely we will do this on our end in the immediate future.


3. Which `EGameplayEffectReplicationMode` did Paragon and Fortnite use and what are Epic’s recommendations for when to use each?

> Both games essentially use Mixed mode for their player controlled characters and Minimal for AI controlled (AI minions, jungle creeps, AI Husks, etc). This is what I would recommend most people using the system in a multiplayer game. The sooner into your project you set these, the better.
>
> Fortnite goes a few steps further with its optimizations. It actually does not replicate the UAbilitySystemComponent at all for simulated proxies. The component and attribute subobjects are skipped inside ::ReplicateSubobjects() on the owning fortnite player state class. We do push the bare minimum replicated data from the ability system component to a structure on the pawn itself (basically, a subset of attribute values and a white list subset of tags that we replicate down in a bitmask). We call this a “proxy”. On the receiving side we take the proxy data, replicated on the pawn, and push it back into ability system component on the player state. So you do have an ASC for each player in FNBR, it just doesn’t directly replicate: instead it replicates data via a minimal proxy struct on the pawn and then routes back to the ASC on receiving side. This is advantage since its A) a more minimal set of data B) takes advantage of pawn relevancy.
>
> I’m not sure if it is still necessary with other server side optimizations that have been done since then (Replication Graph, etc) and it is not the most maintainable pattern.


4. Since we cannot predict the removal of `GameplayEffects` as per `GameplayPrediction.h`, are there any strategies for mitigating the effects of latency on removing `GameplayEffects`? For example, when removing a movement speed slow, we currently have to wait for the Server to replicate the `GameplayEffect` removal resulting in a snap of the player’s character position.

> This is a tough one and I don’t have a good answer. We generally skirted around these problems with tolerances and smoothing. I totally agree that ability system and precise synchronization with the character movement system is not in a good place and something we do want to fix.
>
> I had a shelf of allowing predictive removal of GEs but could never work out all edge cases before having to move on. This doesn’t solve everything though since character movement still has an internal saved move buffer that does not know anything about the ability system and possible movement speed modifiers, etc. It is still possible to get into correction feedback loops even outside of not being able to predict the removal of GEs.
>
> If you think you have a case that is truly desperate, you are able to predictively add a GE that would inhibit your movement speed GEs. I’ve never done this myself but have theorized about it before. It may be able to help with a certain class of problem.


5. We know that the `AbilitySystemComponent` lives on the `PlayerState` in Paragon and Fortnite and on the `Character` in the Action RPG Sample. What are Epic’s internal rules, guidelines, or recommendations for where the AbilitySystemComponent should live, and what should its `Owner` be?

> In general I would say anything that does not need to respawn should have the Owner and Avatar actor be the same thing. Anything like AI enemies, buildings, world props, etc.
>
> Anything that does respawn should have the Owner and Avatar be different so that the Ability System Component does not need to be saved off / recreated / restored after a respawn. PlayerState is the logical choice it is replicated to all clients (where as PlayerController is not). The downside is PlayerStates are always relevant so you can run into problems in 100 player games (See notes on what FN did in question #3).


6. Is it viable to have several `AbilitySystemComponents` which have the same owner but different avatars (e.g. on pawn and weapon/items/projectiles with `Owner` set to `PlayerState`)?

> The first problem I see there would be implementing the IGameplayTagAssetInterface and IAbilitySystemInterface on the owning actor. The former may be possible: just aggregate the tags from all ASCs (but watch out - HasAllMatchingGameplayTags may be met only via cross ASC aggregation. It wouldn't be enough to just forward that calls to each ASC and OR the results together). But the later is even trickier: which ASC is the authoritative one? If someone wants to apply a GE - which one should receive it? Maybe you can work these out but this side of the problem will be the hardest: owners will multiple ASCs beneath them.
>
> Separate ASCs on the pawn and the weapon can make sense on its own though. E.g, distinguishing between tags the describe the weapon vs those that describe the owning pawn. Maybe it does make sense that tags granted to the weapon also “apply” to the owner and nothing else (E.g, attributes and GEs are independent but the owner will aggregate the owned tags like I describe above). This could work out, I am sure. But having multiple ASCs with the same owner may get dicey.


7. Is there a way to stop the Server from overwriting the cooldown duration of locally predicted abilities on the Owning Client? In scenarios of high latency, this would let the Owning Client "try" to activate the ability again when its local cooldown expires but it is still on cooldown on the Server. By the time the Owning Client's activation request reaches the Server over the network, the Server may be off cooldown or the Server might be able to queue the activation request for the remaining milliseconds that it has left. Otherwise as is, clients with higher latency have a longer delay before when they can reactivate an ability versus those with less latency. This is most apparent with very low cooldown abilities like a basic attack that can be less than one second of cooldown. If there isn't a way to stop the Server from overwriting the cooldown duration of locally predicted abilities, what is Epic's strategy for mitigating the effects of high latency on reactivating abilities? To word it another example-based way, how did Epic design Paragon's basic attacks and other abilities so that high latency players could attack or activate at the same speed as low latency players with local prediction?

> The short answer there is not a way to prevent this and Paragon definitely had the problem. Higher latency connections would have a lower ROF with basic attacks.
>
> I attempted to fix this by adding “GE reconciliation” where latency was taken into account when calculating GE duration. Essentially allowing the server to eat some of the total GE time so that the effective time of the GE client side would be 100% consistent with any amount of latency (though fluctuations could still cause issues). However I never got this working in a state that could ship and the project moved fast and we just never fully addressed it.
>
> Fortnite does its own bookkeeping for weapon firing rates: it does not use GEs for cooldowns on weapons. I would recommend this if this is a critical problem for your game.


8. What is Epic’s roadmap for the GameplayAbilitySystem plugin? Which features does Epic plan to add in 2019 and beyond?

> We feel that overall the system is pretty stable at this point and we don’t have anyone working on major new features. Bug fixes and small improvements occasionally are made for Fortnite or from UDN/pull requests, but that is it right now.
>
> Longer term, I think we will eventually do a “V2” or some big changes. We learned a lot from writing this system and feel we got a lot right and a lot wrong. I would love a chance to correct those mistakes and improve some of the fatal flaws that were pointed out above.
>
> If a V2 was to ever come, providing an upgrade path would be of utmost importance. We would never make a V2 and leave Fortnite on V1 forever: there would be some path or procedures that would automatically migrate as much as possible, though there would still almost certainly be some manual remaking required.
>
> The high priority fixes would be:
> * Better interoperability with the character movement system. Unifying client prediction.
> * GE removal prediction (question #4)
> * GE latency reconciliation (question #7)
> * Generalized network optimizations such as batching RPCs and proxy structures. Mostly the stuff that we’ve done for Fortnite but find ways to break it down into more generalized form, at least so that games can write their own game specific optimizations more easily.
>
> The more general refactor type of changes I would consider making:
> * I would like to look at fundamentally moving away from having GEs reference spreadsheet values directly, instead they would be able to emit parameters and those parameters could be filled by some higher level object that is bound to spreadsheet values. The problem with the current model is that GEs become unsharable due to their tight coupling with the curve table rows. I think a generalized system for parameterization could be written and be the underpinning of a V2 system.
> * Reduce number of “policies” on UGameplayAbility. I would remove ReplicationPolicy and InstancingPolicy. Replication is, imo, almost never actually needed and causes confusion. InstancingPolicy should be replaced instead by making FGameplayAbilitySpec a UObject that can be subclassed. This should have been the “non instantiated ability object” that has events and is blueprintable. The UGameplayAbility should be the “instanced per execution” object. It could be optional if you need to actually instantiate: instead “non instanced” abilities would be implemented via the new UGameplayAbilitySpec object. 
> * The system should provide more “middle level” constructs such as “filtered GE application container” (data drive what GEs to apply to which actors with higher level gameplay logic), “Overlapping volume support” (apply the “Filtered GE application container” based on collision primitive overlap events), etc. These are building blocks that every project ends up implementing in their own way. Getting them right is non trivial so I think we should do a better job providing some basic implementations. 
> * In general, reducing boilerplate needed to get your project up and running. Possibly a separate module “Ex library” or whatever that could provide things like passive abilities or basic hitscan weapons out of the box. This module would be optional but would get you up and running quickly.
> * I would like to move GameplayCues to a separate module that is not coupled with the ability system. I think there are a lot of improvements that could be made here.


> This is only my personal opinion and not a commitment from anyone. I think the most realistic course of action will be as new engine tech initiatives come through, the ability system will need to be updated and that will be a time to do this sort of thing. These initiatives could be related to scripting, networking, or physics/character movement. This is all very far looking ahead though so I cannot give commitments or estimates on timelines.

**[⬆ Back to Top](#table-of-contents)**

<a name="resources-daveratti-community2"></a>
#### 11.1.2 Community Questions 2
Community member [iniside](https://github.com/iniside)'s Q&A with Dave Ratti:

1. Is the support for decoupled fixed ticking planned? I'd like to
have Game Thread be fixed (like 30/60fps) and let the rendering thread
run wild. I ask if this is something we should expect in future or
not, to make some assumptions about how gameplay should work.
I ask mainly because there is now a fixed async tick for physics and
this poses a question how the rest of the system might work in the
future. I do not hide that having the ability to have fixed tick game
thread without also fixing tick rate of the rest of the engine would
be beyond awesome.

> There are no plans to decouple rendering frame rate and game thread tick frame rate. I think the ship has sailed on this ever happening due to the complexity of these systems and the requirement to preserve backwards compatibility with previous engine versions.
>
> Instead, the direction we've gone is to have an asynchronous "Physics Thread" which runs at a fixed tick rate, independent of the game thread. Things that need to run at a fixed rate can run here and the game thread / rendering can operate how they always have.
>
> It's worth clarifying that Network Prediction supports what it calls Independent Ticking and Fixed Ticking modes. My long term plan is to keep Independent Ticking roughly how it is today in Network Prediction where it runs on the game thread at variable frame rate and there is no "group/world" prediction, it's just the classic "clients predict their own pawn and owned actors" model. And Fixed Ticking would be what uses the async physics stuff and allows you to predict non client controlled/owned actors like physics objects and other clients/pawns/vehicles/etc.


2. Is there any plan on how the integration of Network Prediction will
look with the Ability System? Like for example, fixed frame ability
activation (so the server gets frames in which abilities were
activated and tasks executed instead of prediction keys)?

> Yes, the plan is to rewrite/remove the Ability System's prediction keys and replace them with Network Prediction constructs. The MockAbility examples in NetworkPredictionExtras show how this might work but they are more "hard coded" than what GAS will require. 
>
> The main idea would be that we remove the explicit client->server Prediction Key exchange in the ASC's RPCs. There would no longer be prediction windows or scoped prediction keys. Instead everything would be anchored around NetworkPrediction frames. The important thing is that client and server agree on when things happen. Examples would be:
>
> * When abilities were activated/ended/cancelled
> * When Gameplay Effects were applied/removed
> * Attribute values (what an attributes value was at frame X)
>
> I think this could be done generically at the ability system level. But actually making the user-defined logic inside a UGameplayAbility completely rollback-able would still take more work. We may end up having a subclass of UGameplayAbility that is fully rollbackable and has access to a more limited set of functionality or only Ability Tasks that are marked as rollback-friendly. Something like that. There are also many implications to animation events and root motion and how those are processed.
>
> Wish I had a more clear answer but it's really important we get the foundation right before touching GAS again. Movement and physics have to be solid before the higher level systems can be changed.


3. Is there a plan to move Network Prediction development toward the
main branch? Not gonna lie, I'd really like to check the latest code.
Regardless of it's state.

> We are working towards it. The system work is still all being done in NetworkPrediction (see NetworkPhysics.h) and the underlying async physics stuff should be all available (RewindData.h etc). But we also have use cases in Fortnite that we have been focused on that obviously can't be made public. We are working through bugs, performance optimizations, etc.
>
> For more context: when working on the early versions of this system, we were very focused on the "front end" of things - how state and simulations were defined and written. We learned a lot there. But as the async physics stuff has come online, we've been much more focused on just getting something real to work in this system, at the expense of throwing out some of our early abstractions. The goal here is to circle back when the real thing is working and reunifying things. E.g, get back to the "front end" and make the final version of that on top of the core pieces of tech we are working on now.


4. For some time on main branch there was a plugin for sending Gameplay
Messages (Looked like Event/Message Bus), but it was removed. Any
plans to restore it? With the Game Features/Modular Gameplay plugins,
having a generic Event Bus Dispatcher would be extremely useful.

> I think you are referring to the GameplayMessages plugin. This will probably come back at some point - the API isn't really finalized yet and the author didn't mean for it to be public yet. I agree it should be useful for modular gameplay design. But it's not really my area so I don't have much more information. 


5. I've been playing recently with async fixed physics and the results
are promising, though if there is going to be NP update in the future
I will probably just play around and wait, since to get it working I
still need to get entire engine into fixed tick and on the other hand
I try to keep physics at 33ms. Which does not make for a good
experience if everything is at 30 fps (:.

I have noticed there was some work on Async
CharacterMovementComponent, but not sure if this will be using Network
Prediction, or it is a separate effort?

Since I noticed it, I also went ahead and tried to implement my custom
async movement at fixed tick rate, which worked okay, but on top of it
I also needed to add a separate update for interpolation. The setup
was to run simulation tick on separate worker threads at fixed 33ms
update, do calculations, save result, and interpolate it at the game
thread to match current frame rate. Not perfect, but it got the job
done.

My question is, if this is something that might be easier to set up in
the future, as there is just quite a bit of boilerplate code to write,
(the interpolation part) and it's not particularly efficient to
interpolate each moving object individually.

The async stuff is really interesting, because it would allow you to
really run game simulation at fixed update rate (which would make
fixed thread unneeded) and have more predictable results. Is this
something that is intended going forward, or more of a benefit to
select systems? As far as I remember actor transforms are not updated
async and blueprints are not entirely thread safe. In other words is
it something that is planned to be supported at more of a framework
level or something that each game has to solve on it's own?

> Async CharacterMovementComponent
>
> This is basically an early prototype/experiment of porting CMC as it is to the physics thread. I don't view it as the future of CMC yet, but it could evolve into that. Right now there is no networking support so it's not something I would really follow. The people doing it are mostly concerned with measuring input latency that this system would add and how that could be mitigated.
>
> I still need to get entire engine into fixed tick and on the other hand I try to keep physics at 33ms. Which does not make for a good experience if everything is at 30 fps (:.
>
> The async stuff is really interesting, because it would allow you to really run game simulation at fixed update rate (which would make fixed thread unneeded) 
>
> Yes. The goal here is that with async physics enabled, you can run the engine at variable tick rate while the physics and "core" gameplay simulations can run at the fixed rate (such as character movement, vehicles, GAS, etc).
>
> These are the cvars that need to be set to enable this now: (I think you've figured this out)  
> `p.DefaultAsyncDt=0.03333`  
> `p.RewindCaptureNumFrames=64`
>
> Chaos does provide interpolation for the physics state (e.g, the transforms that get pushed back to the UPrimitiveComponent and are visible to the game code). There is a cvar now, `p.AsyncInterpolationMultiplier`, which controls that if you want to look at it. You should see smooth continuous motion of physics bodies without having to write any extra code. 
>
> If you want to interpolate non physics state, it is still up to you to do that right now. The example would be like a cool-down that you want to update (tick) on the async physics thread but see smooth continuous interpolation on the game thread so that every render frame the cool-down visualization is updated. We will get to this eventually but don't have examples yet.
>
> there is just quite a bit of boilerplate code to write,
>
> Yeah, so that has been a big general problem with the system up until now. We want to provide an interface that experienced programmers can use to maximize performance and safety (the ability to write gameplay code that "just works" predictively without tons of hazards and things you could-do-but-better-not). So something like CharacterMoverment might do a bunch of custom stuff to maximize its performance - e.g, writing templated code and doing batch updating, going wide, breaking the update loop into distinct phases etc. We want to provide a good "low level" interface into the async thread and rollback systems for this use case. And in this case too - it's still reasonable that the character movement system itself is extendable in its own way. For example providing a way to blueprint a custom movement mode and providing a blueprint API that is thread safe.
>
> But we recognize this is not acceptable for simpler gameplay objects that don't really need their own "system". Something more inline with Unreal is what is needed. E.g, using the reflection system, having general blueprint support, etc. There are examples of blueprints being used on other threads (see BlueprintThreadSafe keyword and what the animation system has been working towards). So I think there will be some form of this one day. But again, we aren't there yet.
>
> I realize you were just asking about interpolation but that is the general answer: right now we have you do everything manually like NetSerialize, ShouldReconcile, Interpolate, etc but eventually we'll have a way that is like "if you want to just use the reflection system, you don't have to manually write this stuff". We just don't want to *force* everyone to use the reflection system since that imposes other limitations that we think we don't want to take on the lowest levels of the system. 
>
> And then just to tie this back to what I said earlier - right now we are really focused on getting a few very specific examples working and performant and then we will turn attention back to the front end and making things friendly to use and iterate on, reducing boilerplate, etc for everybody else to use. 

**[⬆ Back to Top](#table-of-contents)**

<a name="changelog"></a>
## 12. GAS Changelog

This is a list of notable changes (fixes, changes, and new features) to GAS compiled from the official Unreal Engine upgrade changelog and from undocumented changes that I've encountered. If you've found something that isn't listed here, please make an issue or pull request.

<a name="changelog-5.3"></a>
### 5.3
* Crash Fix: Fixed a crash when trying to apply Gameplay Cues after a seamless travel.
* Crash Fix: Fixed a crash caused by GlobalAbilityTaskCount when using Live Coding.
* Crash Fix: Fixed UAbilityTask::OnDestroy to not crash if called recursively for cases like UAbilityTask_StartAbilityState.
* Bug Fix: It is now safe to call `Super::ActivateAbility` in a child class. Previously, it would call `CommitAbility`.
* Bug Fix: Added support for properly replicating different types of FGameplayEffectContext.
* Bug Fix: FGameplayEffectContextHandle will now check if data is valid before retrieving "Actors".
* Bug Fix: Retain rotation for Gameplay Ability System Target Data LocationInfo.
* Bug Fix: Gameplay Ability System now stops searching for PC only if a valid PC is found.
* Bug Fix: Use existing GameplayCueParameters if it exists instead of default parameters object in RemoveGameplayCue_Internal.
* Bug Fix: GameplayAbilityWorldReticle now faces towards the source Actor instead of the TargetingActor.
* Bug Fix: Cache trigger event data if it was passed in with GiveAbilityAndActivateOnce and the ability list was locked.
* Bug Fix: Support has been added for the FInheritedGameplayTags to update its CombinedTags immediately rather than waiting until a Save.
* Bug Fix: Moved ShouldAbilityRespondToEvent from client-only code path to both server and client.
* Bug Fix: Fixed FAttributeSetInitterDiscreteLevels from not working in Cooked Builds due to Curve Simplification.
* Bug Fix: Set CurrentEventData in GameplayAbility.
* Bug Fix: Ensure MinimalReplicationTags are set up correctly before potentially executing callbacks.
* Bug Fix: Fixed ShouldAbilityRespondToEvent from not getting called on the instanced GameplayAbility.
* Bug Fix: Gameplay Cue Notify Actors executing on Child Actors no longer leak memory when gc.PendingKill is disabled.
* Bug Fix: Fixed an issue in GameplayCueManager where GameplayCueNotify_Actors could be 'lost' due to hash collisions.
* Bug Fix: WaitGameplayTagQuery will now respect its Query even if we have no Gameplay Tags on the Actor.
* Bug Fix: PostAttributeChange and AttributeValueChangeDelegates will now have the correct OldValue.
* Bug Fix: Fixed FGameplayTagQuery from not showing a proper Query Description if the struct was created by native code.
* Bug Fix: Ensure that the UAbilitySystemGlobals::InitGlobalData is called if the Ability System is in use. Previously if the user did not call it, the Gameplay Ability System did not function correctly.
* Bug Fix: Fixed issue when linking/unlinking anim layers from UGameplayAbility::EndAbility.
* Bug Fix: Updated Ability System Component function to check the Spec's ability pointer before use.
* New: Added a GameplayTagQuery field to FGameplayTagRequirements to enable more complex requirements to be specified.
* New: Introduced FGameplayEffectQuery::SourceAggregateTagQuery to augment SourceTagQuery.
* New: Extended the functonality to execute and cancel Gameplay Abilities & Gameplay Effects from a console command.
* New: Added the ability to perform an "Audit" on Gameplay Ability Blueprints that will show information on how they're developed and intended to be used.
* Change: OnAvatarSet is now called on the primary instance instead of the CDO for instanced per Actor Gameplay Abilities.
* Change: Allow both Activate Ability and Activate Ability From Event in the same Gameplay Ability Graph.
* Change: AnimTask_PlayMontageAndWait now has a toggle to allow Completed and Interrupted after a BlendOut event.
* Change: ModMagnitudeCalc wrapper functions have been declared const.
* Change: FGameplayTagQuery::Matches now returns false for empty queries.
* Change: Updated FGameplayAttribute::PostSerialize to mark the contained attribute as a searchable name.
* Change: Updated GetAbilitySystemComponent to default parameter to Self.
* Change: Marked functions as virtual in AbilityTask_WaitTargetData.
* Change: Removed unused function FGameplayAbilityTargetData::AddTargetDataToGameplayCueParameters.
* Change: Removed vestigial GameplayAbility::SetMovementSyncPoint.
* Change: Removed unused replication flag from Gameplay tasks & Ability system components.
* Change: Moved some gameplay effect functionality into optional components. All existing content will automatically update to use components during PostCDOCompiled, if necessary.

https://docs.unrealengine.com/5.3/en-US/unreal-engine-5.3-release-notes/

<a name="changelog-5.2"></a>
### 5.2
* Bug Fix: Fixed a crash in the `UAbilitySystemBlueprintLibrary::MakeSpecHandle` function.
* Bug Fix: Fixed logic in the Gameplay Ability System where a non-Controlled Pawn would be considered remote, even if it was spawned locally on the server (e.g. Vehicles).
* Bug Fix: Correctly set activation info on predicted instanced abilities that were rejected by the server.
* Bug Fix: Fixed a bug that would cause GameplayCues to get stuck on remote instances.
* Bug Fix: Fixed a memory stomp when chaining calls to WaitGameplayEvent.
* Bug Fix: Calling the AbilitySystemComponent `GetOwnedGameplayTags()` function in Blueprint no longer retains the previous call's return values when the same node is executed multiple times.
* Bug Fix: Fixed an issue with GameplayEffectContext replicating a reference to a dynamic object that would never be replicated.
  * This prevented GameplayEffect from calling `Owner->HandleDeferredGameplayCues(this)` as `bHasMoreUnmappedReferences` would always be true.
* New: The [Gameplay Targeting System](https://docs.unrealengine.com/en-US/gameplay-targeting-system-in-unreal-engine/) is a way to create data-driven targeting requests.
* New: Added custom serialization support for GameplayTag Queries.
* New: Added support for replicating derived FGameplayEffectContext types.
* New: Gameplay Attributes in assets are now registered as searchable names on save, allowing for references to attributes to be seen in the reference viewer.
* New: Added some basic unit tests for the AbilitySystemComponent.
* New: Gameplay Ability System Attributes now respect Core Redirects. This means you can now rename Attribute Sets and their Attributes in code and have them load properly in assets saved with the old names by adding redirect entries to DefaultEngine.ini.
* Change: Allow changing the evaluation channel of a Gameplay Effect Modifier from code.
* Change: Removed previously unused variable `FGameplayModifierInfo::Magnitude` from the Gameplay Abilities Plugin.
* Change: Removed the synchronization logic between the ability system component and Smart Object instance tags.

https://docs.unrealengine.com/5.2/en-US/unreal-engine-5.2-release-notes/

<a name="changelog-5.1"></a>
### 5.1
* Bug Fix: Fixed issue where replicated loose gameplay tags were not replicating to the owner.
* Bug Fix: Fixed AbilityTask bug where abilities could be blocked from timely garbage-collection.
* Bug Fix: Fixed an issue when a gameplay ability listening to activate based on a tag would fail to be activated. This would happen if there were more than one Gameplay Ability listening to this tag, and the first one in the list was invalid or didn't have authority to activate.
* Bug Fix: Fixed GameplayEffects that use Data Registries correctly from warning on load and improved the warning text.
* Bug Fix: Removed code from UGameplayAbility that was incorrectly only registering the last instanced ability with the Blueprint debugger for breakpoints.
* Bug Fix: Fixed Gameplay Ability System Ability getting stuck if EndAbility was called during the lock inside ApplyGameplayEffectSpecToTarget.
* New: Added support for Gameplay Effects to add blocked ability tags.
* New: Added WaitGameplayTagQuery nodes. One is based off of the UAbilityTask and the other is of UAbilityAsync. This node specifies a TagQuery, and will trigger its output pin when the query becomes true or false, based on configuration.
* New: Modified AbilityTask debugging in Console Variables to enable debug recording and printing to log by default in non-shipping builds (with ability to hotfix on/off as needed).
* New: You can now set AbilitySystem.AbilityTask.Debug.RecordingEnabled to 0 to disable, 1 to enable in non-shipping builds, and 2 to enable all builds (including shipping).
* New: You can use AbilitySystem.AbilityTask.Debug.AbilityTaskDebugPrintTopNResults to only print the top N results in log (to avoid log spam).
* New: STAT_AbilityTaskDebugRecording can be used to test perf impact from these on-by-default debugging changes.
* New: Added a debug command to filter GameplayCue events.
* New: Added new debug commandsAbilitySystem.DebugAbilityTags, AbilitySystem.DebugBlockedTags, andAbilitySystem.DebugAttribute to the Gameplay Ability System.
* New: Added a Blueprint function to get a debug string representation of a Gameplay Attribute.
* New: Added a new Gameplay Task resource overlap policy to cancel existing tasks.
* Change: Now Ability Tasks should make sure to call Super::OnDestroy only after they do anything needed to the Ability pointer, as it will be nulled out after calling it.
* Change: Converted FGameplayAbilitySpec/Def::SourceObject to be a weak reference.
* Change: Made a Ability System Component reference in the Ability Task a weak pointer so Garbage Collection can delete it.
* Change: Removed redundant enum EWaitGameplayTagQueryAsyncTriggerCondition.
* Change: GameplayTasksComponent and AbilitySystemComponent now support the registered subobject API.
* Change: Added better logging to indicate why Gameplay Abilities failed to be activated.
* Change: Removed AbilitySystem.Debug.NextTarget and PrevTarget commands in favor of global HUD NextDebugTarget and PrevDebugTarget commands.

https://docs.unrealengine.com/5.1/en-US/unreal-engine-5.1-release-notes/

<a name="changelog-5.0"></a>
### 5.0

https://docs.unrealengine.com/5.0/en-US/unreal-engine-5.0-release-notes/

<a name="changelog-4.27"></a>
### 4.27
* Crash Fix: Fixed a root motion source issue where a networked client could crash when an Actor finishes executing an ability that uses a constant force root motion task with a strength-over-time modifier.
* Bug Fix: Fixed a regression in Editor loading time when using GameplayCues.
* Bug Fix: GameplayEffectsContainer's `SetActiveGameplayEffectLevel` method will no longer dirty FastArray if setting the same EffectLevel.
* Bug Fix: Fixed an edge case in GameplayEffect mixed replication mode where Actors not explicitly owned by the net connection but who utilize that connection from `GetNetConnection` will not received mixed replication updates.
* Bug Fix: Fixed an endless recursion occuring in GameplayAbility's class method `EndAbility` which was called by calling `EndAbility` again from `K2_OnEndAbility`.
* Bug Fix: GameplayTags Blueprint pins will no longer be silently cleared if they are loaded before tags are registered. They now work the same as GameplayTag variables, and the behavior for both can be changed with the ClearInvalidTags option in the Project Settings.
* Bug Fix: Improved thread safety of GameplayTag operations.
* New: Exposed SourceObject to GameplayAbility's `K2_CanActivateAbility` method.
* New: Native GameplayTags. Introducing a new `FNativeGameplayTag`, these make it possible to do one off native tags that are correctly registered and unregistered when the module is loaded and unloaded.
* New: Updated `GiveAbilityAndActivateOnce` to pass in FGameplayEventData parameter.
* New: Improved ScalableFloats in the GameplayAbilities plugin to support dynamic lookup of curve tables from the new Data Registry System. Added a ScalableFloat header for easier reuse of the generic struct outside the abilities plugin.
* New: Added code support for using the GameplayTag UI in other Editor customizations via GameplayTagsEditorModule.
* New: Modified UGameplayAbility's PreActivate method to optionally take in trigger event data.
* New: Added more support to filter GameplayTags in the Editor using a project-specific filter. `OnFilterGameplayTag` supplies the referencing property and the tag source, so you can filter tags based on what asset is requesting the tag.
* New: Added option to preserve the original captured SourceTags when GameplayEffectSpec's class method `SetContext` is called after initialization.
* New: Improved UI for registering GameplayTags from specific plugins. The new tag UI now lets you select a plugin location on disk for newly added GameplayTag sources.
* New: A new track has been added to Sequencer to allow for triggering notify states on Actors built using the GameplayAbiltiySystem. Like notifies, the GameplayCueTrack can utilize range-based events or trigger-based events.
* Change: Changed the GameplayCueInterface to pass GameplayCueParameters struct by reference.
* Optimization: Made several performance improvements to loading and regenerating the GameplayTag table were implemented so that this option would be optimized.

https://docs.unrealengine.com/en-US/WhatsNew/Builds/ReleaseNotes/4_27/

<a name="changelog-4.26"></a>
### 4.26
* GAS plugin is no longer flagged as beta.
* Crash Fix: Fixed a crash when adding a gameplay tag without a valid tag source selection.
* Crash Fix: Added the path string arg to a message to fix a crash in UGameplayCueManager::VerifyNotifyAssetIsInValidPath.
* Crash Fix: Fixed an access violation crash in AbilitySystemComponent_Abilities when using a ptr without checking it.
* Bug Fix: Fixed a bug where stacking GEs that did not reset the duration on additional instances of the effect being applied.
* Bug Fix: Fixed an issue that caused CancelAllAbilities to only cancel non-instanced abilities.
* New: Added optional tag parameters to gameplay ability commit functions.
* New: Added StartTimeSeconds to PlayMontageAndWait ability task and improved comments.
* New: Added tag container "DynamicAbilityTags" to FGameplayAbilitySpec. These are optional ability tags that are replicated with the spec. They are also captured as source tags by applied gameplay effects.
* New: GameplayAbility IsLocallyControlled and HasAuthority functions are now callable from Blueprint.
* New: Visual logger will now only collect and store info about instant GEs if we're currently recording visual logging data.
* New: Added support for redirectors on gameplay attribute pins in blueprint nodes.
* New: Added new functionality for when root motion movement related ability tasks end they will return the movement component's movement mode to the movement mode it was in before the task started.

https://docs.unrealengine.com/en-US/WhatsNew/Builds/ReleaseNotes/4_26/

<a name="changelog-4.25.1"></a>
### 4.25.1
* Fixed! UE-92787 Crash saving blueprint with a Get Float Attribute node and the attribute pin is set inline
* Fixed! UE-92810 Crash spawning actor with instance editable gameplay tag property that was changed inline

<a name="changelog-4.25"></a>
### 4.25
* Fixed prediction of `RootMotionSource` `AbilityTasks`
* [`GAMEPLAYATTRIBUTE_REPNOTIFY()`](#concepts-as-attributes) now additionally takes in the old `Attribute` value. We must supply that as the optional parameter to our `OnRep` functions. Previously, it was reading the attribute value to try to get the old value. However, if called from a replication function, the old value had already been discarded before reaching SetBaseAttributeValueFromReplication so we'd get the new value instead.
* Added [`NetSecurityPolicy`](#concepts-ga-netsecuritypolicy) to `UGameplayAbility`.
* Crash Fix: Fixed a crash when adding a gameplay tag without a valid tag source selection.
* Crash Fix: Removed a few ways for attackers to crash a server through the ability system.
* Crash Fix: We now make sure we have a GameplayEffect definition before checking tag requirements.
* Bug Fix: Fixed an issue with gameplay tag categories not applying to function parameters in Blueprints if they were part of a function terminator node.
* Bug Fix: Fixed an issue with gameplay effects' tags not being replicated with multiple viewports.
* Bug Fix: Fixed a bug where a gameplay ability spec could be invalidated by the InternalTryActivateAbility function while looping through triggered abilities.
* Bug Fix: Changed how we handle updating gameplay tags inside of tag count containers. When deferring the update of parent tags while removing gameplay tags, we will now call the change-related delegates after the parent tags have updated. This ensures that the tag table is in a consistent state when the delegates broadcast.
* Bug Fix: We now make a copy of the spawned target actor array before iterating over it inside when confirming targets because some callbacks may modify the array.
* Bug Fix: Fixed a bug where stacking GameplayEffects that did not reset the duration on additional instances of the effect being applied and with set by caller durations would only have the duration correctly set for the first instance on the stack. All other GE specs in the stack would have a duration of 1 second. Added automation tests to detect this case.
* Bug Fix: Fixed a bug that could occur if handling gameplay event delegates modified the list of gameplay event delegates.
* Bug Fix: Fixed a bug causing GiveAbilityAndActivateOnce to behave inconsistently.
* Bug Fix: Reordered some operations inside FGameplayEffectSpec::Initialize to deal with a potential ordering dependency.
* New: UGameplayAbility now has an OnRemoveAbility function. It follows the same pattern as OnGiveAbility and is only called on the primary instance of the ability or the class default object.
* New: When displaying blocked ability tags, the debug text now includes the total number of blocked tags.
* New: Renamed UAbilitySystemComponent::InternalServerTryActiveAbility to UAbilitySystemComponent::InternalServerTryActivateAbility.Code that was calling InternalServerTryActiveAbility should now call InternalServerTryActivateAbility.
* New: Continue to use the filter text for displaying gameplay tags when a tag is added or deleted. The previous behavior cleared the filter.
* New: Don't reset the tag source when we add a new tag in the editor.
* New: Added the ability to query an ability system component for all active gameplay effects that have a specified set of tags. The new function is called GetActiveEffectsWithAllTags and can be accessed through code or blueprints.
* New: When root motion movement related ability tasks end they now return the movement component's movement mode to the movement mode it was in before the task started.
* New: Made SpawnedAttributes transient so it won't save data that can become stale and incorrect. Added null checks to prevent any currently saved stale data from propagating. This prevents problems related to bad data getting stored in SpawnedAttributes.
* API Change: AddDefaultSubobjectSet has been deprecated. AddAttributeSetSubobject should be used instead.
* New: Gameplay Abilities can now specify the Anim Instance on which to play a montage.

https://docs.unrealengine.com/en-US/WhatsNew/Builds/ReleaseNotes/4_25/

<a name="changelog-4.24"></a>
### 4.24
* Fixed blueprint node `Attribute` variables resetting to `None` on compile.
* Need to call [`UAbilitySystemGlobals::InitGlobalData()`](#concepts-asg-initglobaldata) to use [`TargetData`](#concepts-targeting-data) otherwise you will get `ScriptStructCache` errors and clients will be disconnected from the server. My advice is to always call this in every project now whereas before 4.24 it was optional.
* Fixed crash when copying a `GameplayTag` setter to a blueprint that didn't have the variable previously defined.
* `UGameplayAbility::MontageStop()` function now properly uses the `OverrideBlendOutTime` parameter.
* Fixed `GameplayTag` query variables on components not being modified when edited.
* Added the ability for `GameplayEffectExecutionCalculations` to support scoped modifiers against "temporary variables" that aren't required to be backed by an attribute capture.
  * Implementation basically enables `GameplayTag`-identified aggregators to be created as a means for an execution to expose a temporary value to be manipulated with scoped modifiers; you can now build formulas that want manipulatable values that don't need to be captured from a source or target.
  * To use, an execution has to add a tag to the new member variable `ValidTransientAggregatorIdentifiers`; those tags will show up in the calculation modifier array of scoped mods at the bottom, marked as temporary variables—with updated details customizations accordingly to support feature
* Added restricted tag quality-of-life improvements. Removed the default option for restricted `GameplayTag` source. We no longer reset the source when adding restricted tags to make it easier to add several in a row. 
* `APawn::PossessedBy()` now sets the owner of the `Pawn` to the new `Controller`. Useful because [Mixed Replication Mode](#concepts-asc-rm) expects the owner of the `Pawn` to be the `Controller` if the `ASC` lives on the `Pawn`.
* Fixed bug with POD (Plain Old Data) in `FAttributeSetInitterDiscreteLevels`.

https://docs.unrealengine.com/en-US/WhatsNew/Builds/ReleaseNotes/4_24/

**[⬆ Back to Top](#table-of-contents)**

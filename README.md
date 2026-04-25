# GameCoreFramework (GCF)

![Unreal Engine](https://img.shields.io/badge/Unreal_Engine-5.7+-white.svg?logo=unrealengine&logoColor=white&color=0E1128)
![Version](https://img.shields.io/badge/Version-0.9.1_Beta-blue.svg)
![License](https://img.shields.io/badge/License-MIT-green.svg)

🌍 *Read this in other languages: [English](README.md) | [日本語 (Japanese)](README_ja.md)* *(Note: The English documentation is AI-translated from the original Japanese).*

> [!IMPORTANT]  
> **This repository is a portal for documentation and public relations.**  
> Because this framework contains code dependent on Epic Games' *Lyra Starter Game*, the actual source code is not hosted in this repository. In accordance with the Unreal Engine EULA, it is hosted in a **"Private Fork on the Epic Games network (Closed Environment)."**<br>
> Anyone who has linked their Epic Games and GitHub accounts can access it directly (no invitation required).<br>
> For details, please see [🚀 Source Code Access and Installation](#-source-code-access-and-installation).

## 📑 Table of Contents
- [🎯 Project Overview](#-project-overview)
- [💡 Core Design Philosophy](#-core-design-philosophy)
- [📚 Detailed Documentation](#-detailed-documentation)
- [🚀 Source Code Access and Installation](#-source-code-access-and-installation)
  - [Step 1: Link Epic Games and GitHub (Get Access)](#step-1-link-epic-games-and-github-get-access)
  - [Step 2: Clone the Repository (Clone and Play)](#step-2-clone-the-repository-clone-and-play)
  - [Step 3: Build the Project](#step-3-build-the-project)
  - [Integrating into Your Own Project](#integrating-into-your-own-project)
- [🎬 Demo & Samples](#-demo--samples)
- [📌 Project Status and Contribution](#-project-status-and-contribution)
- [💖 Credits & Acknowledgments](#-credits--acknowledgments)
- [🤖 AI Usage Policy](#-ai-usage-policy)
- [⚖ License](#-license)

## 🎯 Project Overview

This project is a highly extensible, loosely coupled modular game framework built upon the modern architecture of Unreal Engine 5 (inspired by Lyra Starter Game) and the design philosophy of the Gameplay Ability System (GAS).

I believe that "building a strong foundation, separating responsibilities into appropriate domains, and reusing them across multiple projects" is a critically important approach in game development.
This framework (Game Core Framework: GCF) is positioned as the **2nd layer (intermediate foundation)** in a "4-layer architecture" built on top of UE5's modern features (Modular Gameplay, GameFeatures, etc.).

### 🏗️ The 4-Layer Architecture

1. **Engine Layer (Unreal Engine)**  
   The infrastructure layer responsible for physics, rendering, and memory management. It holds no game-specific logic and is shared across all projects.
2. **Core Framework Layer (This Framework: GCF)**  
   An intermediate layer built on top of the engine, providing the "rules (grammar)" of game development. It intends to offer only an **abstract skeleton** — such as input routers and character interfaces — without any game-specific logic (Shooter, RPG, etc.). This is the "second foundation" I designed to be reusable across multiple projects.
3. **Modular Feature Layer (GameFeatures, etc.)**  
   "Swappable concrete packages" implemented according to the rules of the 2nd layer. These are modules packaged by independent domain (basic locomotion, firearms, magic, etc.), dynamically injected based on project requirements.
4. **Project / Experience Layer (The final game specifics)**  
   The assembly layer that combines the 3rd-layer modules (cartridges) and makes the final decision — for example, "enable 'Basic Locomotion' and 'Firearms' for this map" — to compose the game experience.

### 🧩 Why I Felt "GCF" Was Needed Instead of Just "Lyra"

I feel that the *Lyra Starter Game* provided by Epic Games is an excellent architectural reference. However, when trying to repurpose it as a general-purpose intermediate layer for a different genre, I found that it has a challenge: **"it leans too heavily toward the specifics of a shooter (Layer 3 & 4 elements are mixed into the core)."**

When trying to build a different genre of game on top of a Lyra base that is tightly coupled with TPS-specific logic and weapon concepts, I've experienced the architecture breaking down from the effort of stripping out the unwanted elements.
So I decided to build an abstract foundation (GCF) from scratch, **referencing only the essence of Lyra's design philosophy (modularization and data-driven design) while removing all game-specific concrete elements.**

### 🌟 The Vision I Am Aiming for with This Framework

I believe the ideal is to balance "development efficiency and high-quality game experiences" from a long-term perspective, rather than just chasing immediate deadlines.

* **Focus on project-specific "concrete" elements:**  
  As a well-maintained general-purpose intermediate layer is established, I believe teams can gradually free themselves from the burden of infrastructure construction and focus more exclusively on implementing the "fun" unique to their game.
* **Preventing reinvention through appropriate domain separation:**  
  I believe that by consciously carving out features by their "domain (responsibility)", this leads to the natural decoupling of the entire system and the reuse of knowledge across projects.
* **Accelerating iteration through modular composition:**  
  With domains separated and features organized as cartridges, non-programmers like planners and level designers can more easily swap and test features. I expect this will significantly accelerate the cycle of trial and error.

---

## 💡 Core Design Philosophy

- **Clear Separation of Concerns**
  By eliminating hardcoding in `ACharacter` and `APlayerController`, each feature is divided into independent components. This allows you to safely add and expand new features and characters without polluting the existing codebase.

- **Separation of Soul and Body**
  The responsibilities of the PlayerState (the persistent "Soul") and the Pawn (the temporary "Body") are clearly separated. This makes it possible to decompose Ability and Input bindings into persistent and temporary ones, achieving a flexible system that is not dictated by the current possession state.

- **Data-Driven Routing**
  A routing layer is established via DataAssets, enabling non-programmers (planners and artists) to link inputs with abilities and adjust behaviors without touching C++ code. This maximizes the iteration speed of the entire team.

- **Safe Asynchronous Lifecycle Management**
  Utilizing the `GameFrameworkComponentManager` (GFCM), the dependencies and initialization states (Feature States) of each component are strictly managed. By implementing a hybrid architecture of event-driven and state management, the system can always synchronize to the latest state. This prevents at the system level the frequent Unreal Engine initialization order issue where "referenced objects have not yet been spawned."

- **Performance-Oriented Tickless Design**
  Reliance on per-frame `Tick` processing is fundamentally eliminated. The system operates primarily through an Event-Driven model and state broadcasting. This suppresses CPU overhead on the Game Thread, maintaining high runtime performance.

---

## 📚 Detailed Documentation

> 💡 **Reference Article: Architecture Background**  
> Before diving into the code, I highly recommend reading the following technical article (originally in Japanese). It explains the core design philosophy of this framework—**"Loose coupling to enable emergent, unpredictable gameplay."** It covers the severe trade-offs encountered during development and the resulting architectural solutions  
> 🔗 [Why I am Obsessed with Loose Coupling and Component-Oriented Design in Game Development](https://zenn.dev/munimaru62o/articles/c6ed730c6e4c61?locale=en)

For details on the design philosophy and individual systems of GCF, please refer to the following documentation:

- **[Architecture Overview](Document/en/Architecture/overview.md)**
  Summarizes the core philosophy (e.g., Separation of Soul and Body) to systematically prevent "initialization race conditions" and "responsibility bloat" that frequently occur in multiplayer development, along with lessons learned from practical failures.

- **[GAS Integration and Ability Routing (Dual ASC & Router Pattern)](Document/en/Architecture/ability_system.md)**
  Explains the advanced "Dual ASC Architecture" where both the PlayerState and the Pawn have an ASC. It details the routing mechanism that eliminates tight coupling between inputs and abilities, dynamically switching the execution target based on tag prefixes.

- **[Input System (InputBridge & Manager Pattern)](Document/en/Architecture/input_system.md)**
  Details a robust manager design that queues requests until the state is ready and applies them safely all at once. This prevents input binding crashes caused by asynchronous loading during possession.

- **[Actor Control System (Interface-Driven Intent Dispatch & Opt-In Design)](Document/en/Architecture/control_system.md)**
  Explains the highly decoupled architecture based on an "Intent Bucket Relay." It clearly separates the player's "movement intent" from the Pawn's "physical behavior" using interfaces, where the input side Pushes the intent, the Pawn Caches it, and the physics engine (like Mover) Pulls and translates it.

---

## 🚀 Source Code Access and Installation

### Step 1: Link Epic Games and GitHub (Get Access)

The source code and the project itself are hosted on a **Private Fork on the Epic official network** （[https://github.com/munimaru62o/UnrealEngine.git](https://github.com/munimaru62o/UnrealEngine)）.

Access is not invitation-based. If you complete the account linking according to the **[instructions provided by Epic Games to download Unreal Engine source code](https://www.unrealengine.com/ue-on-github)** and join the Epic Games GitHub Organization (`@EpicGames/developers`), you will be able to access the URL directly.

### Step 2: Clone the Repository (Clone and Play)

Once the linking is complete, you can access and clone the dedicated branch (`gcf-release`) from the URL below.
*(Note: To maximize extensibility, this framework depends on Lyra's base plugins. However, to eliminate tedious manual copying steps, **all required plugin folders are bundled directly within the repository**. It is configured as a "Clone and Play" project!)*

```bash
# Example of making a lightweight clone of only the gcf-release branch
git clone -b gcf-release --single-branch https://github.com/munimaru62o/UnrealEngine.git
```

### Step 3: Build the Project

1. Right-click `GCF_SampleProject.uproject` at the root of the repository and select "Generate Visual Studio project files."
2. Open the generated `.sln` (or your IDE's project file) and build the project (e.g., Development Editor).
3. Once the editor launches, verify from `Edit > Plugins` that this plugin and its dependencies (GameplayAbilities, EnhancedInput, Mover, etc.) are Enabled.

---

### Integrating into Your Own Project

If you wish to introduce this framework into your own game project, simply copy the **`GameCoreFramework` plugin itself** located in the `Plugins` folder of the cloned repository, along with the bundled **Lyra dependency plugins (such as `CommonGame`)**, directly into your project's `Plugins` folder.

## 🎬 Demo & Samples

This project includes demo assets to help you verify its behavior.
It is designed so that you can dynamically switch abilities and behaviors simply by editing DataAssets, without altering any C++ code.

```text
GameCoreFramework/Content/Sample
├── Assets/         # Assets for Pawns and Particles
├── Blueprints/     # BP classes for Pawns and Abilities
├── DataAssets/     # The core of the data-driven design; DataAssets are defined here
├── Experiences/    # Experience definitions
└── Maps/           # Demo map definitions
└── UI/             # UI definitions for the Debug HUD
```

> **⚠️ Note on Experience Loading Functionality:**  
> Because this framework is built as a Minimum Viable Product (MVP) prioritizing the robustness of the core foundation, dynamic Experience switching via UI is not currently implemented. Assigning or changing the Experience is designed to be done solely via the "Default Gameplay Experience" setting within the target map's `World Settings`.

### 🖥️ Sample Video

https://github.com/user-attachments/assets/1ab546e9-f2b7-4c96-ad29-9a86f2c24af6

#### **💡 Highlights of the Video**
You can observe that the moment possession changes, the input bindings of the old Body are safely discarded, and **the new Pawn's `InputBinding` is dynamically updated.** It also demonstrates "safe synchronization of lifecycles," where Abilities granted to the new Body are immediately activated and routed.

#### 📊 Debug Information on Screen
- **Debug Input Info:** Displays currently active bound input actions and routing states in real-time.
- **Debug State Info:** Monitors GFCM's `InitState` (the initialization phase of each feature) and the current possession state.
- **Debug Log:** Outputs possession switch events and tag transmission logs via the Ability Router.

#### 🎮 Executing Possession and Target Selection
- Execute the `Interact` ability (a persistent ability on the Soul side) against an Actor highlighted (outlined) on the screen to transfer possession.
- Targets are dynamically selected based on the camera mode. (In TPS mode, it's based on the center of the screen; in Top-Down mode, it's based on the mouse cursor position).

#### 🤖 Variations of Playable Actors (Coexistence of Old/New Systems and Physics Engines)
To prove the decoupled nature of the architecture, the player seamlessly transitions between Pawns with completely different physics components.
- **White Mannequin:** Movement, Jump, and Crouch functions powered by this framework's proprietary `GCFCharacterMover`, based on UE5's next-gen `Mover` plugin.
- **Colored Mannequin:** Movement control using the legacy `CharacterMovementComponent`, plus the execution of dedicated abilities uniquely defined for the current "Body."
- **Sphere (Mover):** Tick-based movement control using the `Mover` plugin, viewed from a Top-Down camera.
- **Vehicle (Chaos Vehicle):** Authentic vehicle control using the `ChaosVehicleMovementComponent`, along with specific operations like Headlights and Handbrake.

### 🌐 Client Sample Video (Network Lag Simulation)

https://github.com/user-attachments/assets/f3c54c69-b6dc-4071-a949-bfde364a2029

#### **💡 Highlights of the Video**
You can observe that even in a poor network environment where communication delays cause "initialization order reversals" or "lifecycle desyncs during asynchronous loading (race conditions)," GFCM's strict state management safely absorbs these issues.

#### 📡 Test Environment (Intentional Network Latency)
A harsh network restriction frequently encountered in multiplayer is emulated on a Client connected to a Listen Server.
- **Latency (Ping):** 200 ms
- **Packet Loss:** 10%

It proves that the architecture functions robustly even under such severe lag conditions: possession transfers safely complete, and the occurrence of Null reference crashes or input binding failures is suppressed at the design level.

---

## 📌 Project Status and Contribution

This project is a demonstration of modern architecture design in Unreal Engine 5 and is published personally for **learning and reference purposes**.

Therefore, **we generally do not accept** Pull Requests (PRs) for "adding new features" or "large-scale changes that affect the core design."

If you resonate with the design philosophy of this framework, please feel free to take the necessary code and architectural ideas back to your own projects (within the scope of the MIT License)!

---

## 💖 Credits & Acknowledgments

- **[Lyra Starter Game](https://dev.epicgames.com/documentation/en-us/unreal-engine/lyra-sample-game-in-unreal-engine)** by Epic Games:
  Significantly influenced the utilization of modular design philosophies.
- **Unreal Engine Community**:
  Thanks to all the developers who share their best practices.

---

## 🤖 AI Usage Policy

All users are fully permitted and encouraged to use AI assistants to analyze the code and documentation. However, extracting data for model training or fine-tuning is strictly prohibited.

**⚠️ Important:** This repository contains code governed by the Epic Games EULA. Using this code as AI training data carries a risk of violating the license agreement.

For the complete policy and details regarding the affected files, please be sure to read [AI_POLICY.md](AI_POLICY.md).

---

## ⚖ License

The original code of this project is licensed under the **MIT License**.
See [LICENSE](LICENSE) for more details.

**⚠️ Notice Regarding Epic Games Content**
This project contains code and assets inspired by or derived from the "Lyra Starter Game." These Epic Games contents are subject to the Unreal Engine End User License Agreement (EULA).
For details on which files fall under which license, please be sure to check [NOTICE.md](NOTICE.md).

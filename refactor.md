```markdown
# Role: Senior Game Engine Architect

**Objective:** Architect a highly scalable **Data-Driven Vanilla JS Game Engine** using ES Modules, designed for long-term maintainability and easy version iteration.

## Context & Constraints
* **Platform:** HTML5 Canvas (No frameworks like Phaser/Three.js).
* **Assets:** NO external image or audio files. All assets must be **Procedurally Generated** via code (Canvas API & Web Audio API).
* **Input:** Must support both **Desktop (Keyboard)** and **Mobile (Touch/Virtual Buttons)** seamlessly.
* **Philosophy:** **Data-Driven over Hard-Coded.** Adding new characters, levels, or items should be done by editing a JSON-like configuration file, not by writing new Classes.

## 1. Directory Structure
Refactor the codebase into this strict separation of "Engine" (Logic) and "Data" (Content):

```text
/project-root
├── index.html          # Entry point (Canvas + Mobile UI Overlay)
├── style.css           # CSS for Mobile Controls & Layout
└── js/
    ├── main.js         # Bootstrapper (Initializes Engine)
    ├── engine/         # [CORE] Reusable Game Logic (The "Brain")
    │   ├── Game.js         # Manages Loop (RAF), Context, & State Switching
    │   ├── EventBus.js     # [CRITICAL] Pub/Sub system to decouple modules
    │   ├── InputSystem.js  # Maps Physical Inputs (Key/Touch) -> Logical Events
    │   └── Timer.js        # DeltaTime calculation for consistent physics
    ├── generators/     # [ASSETS] Procedural Factories
    │   ├── SpriteGen.js    # Draws shapes to Off-screen Canvas based on Config
    │   └── AudioSynth.js   # Generates SFX using Web Audio API based on Config
    ├── data/           # [CONTENT] The "Database" (Edit this to expand game)
    │   ├── assets.js       # Config for visual styles & sound parameters
    │   └── entities.js     # Config for Player/Enemy stats (speed, color, hp)
    ├── states/         # [FLOW] Game Scenes
    │   ├── BootState.js    # Pre-generates assets from 'data/' before game starts
    │   ├── MenuState.js
    │   └── PlayState.js
    └── entities/
        └── Actor.js        # A single, flexible Class configurable via Data

## 2. Architectural Guidelines

### A. The Event Bus (Decoupling)

* Implement a Pub/Sub system (`EventBus.js`).
* **Rule:** Components must not directly reference each other.
* *Example:* When the User presses "Space":
1. `InputSystem` detects keydown.
2. `InputSystem` emits event: `EventBus.emit('ACTION_JUMP')`.
3. `Actor` (Player) listening to `'ACTION_JUMP'` performs jump logic.
4. `AudioSynth` listening to `'ACTION_JUMP'` plays the jump sound.


### B. Data-Driven Entity System

* Do **not** create separate classes like `class FastHero extends Player`.
* Use **Composition** or **Configuration**.
* Create a single `class Actor`. It takes a `config` object in its constructor.
* The `entities.js` file should look like this:
```javascript
export const ACTOR_TYPES = {
    hero_fast: { speed: 10, color: 'red', shape: 'triangle', hp: 50 },
    hero_tank: { speed: 4, color: 'blue', shape: 'square', hp: 200 }
};


### C. Procedural Asset Pipeline (`BootState`)

* The game must not start immediately.
* In `BootState`, the engine must iterate through `data/assets.js`.
* Pass these configs to `SpriteGen` to create `OffscreenCanvas` images.
* Store these generated images in an `AssetCache` so they can be drawn instantly during gameplay (`PlayState`).

### D. Agnostic Input System

* The `InputSystem` must map multiple physical triggers to a single Logical Action.
* Support a configuration map:
```javascript
const CONTROLS = {
    MOVE_LEFT: ['ArrowLeft', 'KeyA', 'TouchPadLeft'],
    JUMP: ['Space', 'TouchButtonA']
};


## 3. Quality Assurance & Anti-Bug Protocols (CRITICAL)

**Context:** Previous iterations of this request resulted in **25+ Syntax Errors**, specifically missing closing braces `}` and truncated methods due to token limits.

**Strict Output Rules to Prevent Bugs:**

1. **Atomic File Generation:** Do NOT attempt to output all files in a single code block. Output **one file per code block**. If a file is long, break it into logical parts.
2. **Syntax Verification:** Before finishing a code block, you MUST verify that every opening `{`, `[`, and `(` has a matching closing counterpart.
3. **No "Lazy" Implementation:** Do not use comments like `// ... logic here` inside critical methods (e.g., `draw()` or `update()`). Write the complete code.
4. **Import Check:** Ensure every module used is explicitly imported at the top of the file.

**Reference of Forbidden Errors (Do Not Repeat):**

* *State.js / MenuState.js / BootState.js*: Missing closing braces at end of file.
* *Game.js*: Missing imports & method cut off in the middle.
* *AudioSystem.js*: Switch statements missing `break` or closing braces.

## 4. Execution Instructions

Please write the code for the **Core Foundation** of this architecture. I need the following files implemented. **Please output them one by one to ensure completeness:**

1. **`js/engine/EventBus.js`**: The simple but robust Pub/Sub class.
2. **`js/data/entities.js`**: A sample config file defining 2 distinct character types.
3. **`js/generators/SpriteGen.js`**: A function that takes a config (color/shape) and returns a generated Image/Canvas.
4. **`js/states/BootState.js`**: The logic to loop through data, generate sprites, and then switch to MenuState.
5. **`js/entities/Actor.js`**: The class that uses the config and generated sprite.

---

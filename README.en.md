# UTAKATA

[日本語](README.md) | [English](README.en.md)

> Your current emotion becomes an aquatic creature in this place.

UTAKATA is a **web experience that uses a momentary emotion as an entry point for discovering the waterside environments and wildlife of the region you are in**.

Tap the bubble-shaped `UTAKATA` logo floating underwater: its letters come apart into bubbles and rise to the surface. Enter a short description of how you feel. AI converts it—not into a “diagnosis,” but into **features used for visual expression**—and a fish is born from species in the area selected using coarse location data.

Watch the fish, learn its name, habitat, and conservation status, then release it back into the water. The goal is to connect a personal memory of emotion with the ecological memory of a place.

---

## Why

Even a large city like Tokyo contains varied aquatic environments, including the ocean, rivers, springs, and brackish waters. At the same time, primary information about rare species and local ecosystems is often found in government red lists, red data books, and survey reports—materials people do not often encounter in everyday life.

The Tokyo Metropolitan Government publishes the *Tokyo Red Data Book (Mainland), 2023*. Fifty freshwater fish species from mainland Tokyo alone are included on its red list. Instead of asking users to start by “reading” official data, UTAKATA creates an encounter in this order:

**your emotion → one fish → the region where that fish lives**

The product is not trying to “make lists of endangered species easier to read.”

**It aims to create an entry point for remembering local nature as something personal.**

---

## HACK STAGE STAGE3

HACK STAGE STAGE3 is a hybrid hackathon held from September 19 to 27, 2026, with the theme “Regional Revitalization × AI.” Its official page lists web development, social-problem solving, and mobile app development as tags.

For the HACK STAGE version of UTAKATA, the scope is initially limited to **Tokyo’s waterside environments**.

- Region: Tokyo
- Theme: aquatic ecosystems that are easy to overlook in the city
- AI’s role: structure emotion text into a profile for visual expression
- Core experience: a regional fish is born from an emotion; learn about it and release it
- Technical focus: WebGPU / TSL / GPU instancing / Hono / event-driven domain design

> Rules provided after participation is confirmed take precedence for implementation timing, prior development, and reuse of existing assets. Until those rules are confirmed, this repository focuses on requirements, design, and research.

---

## Product principles

UTAKATA **does not ask AI to infer a fish’s conservation status**.

Facts such as conservation category, habitat region, Japanese name, and scientific name are managed from primary sources such as the Tokyo Metropolitan Government and Japan’s Ministry of the Environment. AI only converts a user’s short emotion text into numerical values for visual effects and matching.

It also avoids claims without biological evidence, such as “this fish is for sad people.” The UI makes clear that the relationship between emotion and fish is a **poetic and visual mapping within UTAKATA**.

---

## Core experience — 60 seconds

```text
Underwater
  ↓
“UTAKATA” made of bubbles
  ↓ tap
The letters break apart into bubbles and rise to the surface
  ↓
“Describe how you feel right now in a few words.”
  ↓
Enter an emotion
  ↓
AI creates an EmotionProfile
  ↓
Narrow candidate fish using region + public data
  ↓
Match the emotion profile with each fish’s visual profile
  ↓
Bubbles gather and one fish is born
  ↓
Watch the fish
  ↓
Name / habitat / UTAKATA Level / official conservation category
  ↓
“Return it to the water”
  ↓
The fish swims into the distance and becomes part of the sea
  ↓
It remains in the encyclopedia
```

### Home

- The background is an **abstract underwater space**, not a fixed “ocean”
- Deep blue, light rays, floating particles, and a few bubbles
- A white bubble-shaped `UTAKATA` logo in the center
- The logo itself is the Enter button
- Help in the upper-right
- Encyclopedia in the lower-right

### Logo transition

The moment the user taps `UTAKATA`, the bubbles forming each letter lose their cohesion, sway slightly, then break apart and float upward.

The important idea is not a simple fade-out: **the act of using the interface becomes a moment of vanishing bubbles**.

### Emotion input

The expected input is one or two sentences.

Examples:

- “I kind of want to be quiet today.”
- “I did it. I feel amazing.”
- “I came to Tokyo, but I’m a little tired.”

UTAKATA does not provide medical or psychological diagnoses. Emotion input is treated as an expressive parameter for creating a fish.

### Fish birth

The fish does not appear fully formed at once.

```text
Small bubbles
  ↓
The outline becomes visible
  ↓
A translucent fish body
  ↓
Color, scales, and sheen appear
  ↓
The eyes catch the light
  ↓
The tail starts moving
  ↓
The fish begins to swim
```

The goal is to create **a few seconds in which the fish feels as if it is being born**, rather than simply displaying a generated result.

### Result card

Show only a small amount of information at first.

```text
Hotokedojō (Lefua echigonia)
UTAKATA LEVEL 5
“A fish closely associated with Tokyo’s springs and small streams”

[ Learn more ]
[ Return it to the water ]
```

Detailed information includes:

- Japanese name
- Scientific name
- Habitat
- Relationship to the region
- Official conservation category
- Information sources
- Meaning of the UTAKATA Level
- Photo / 3D model attribution

### Release

When the user taps “Return it to the water,” the fish becomes **an individual in UTAKATA’s underwater world**, rather than the user’s possession.

The MVP can work within the user’s own session. As a stretch goal, it can receive other users’ release events over WebSocket and add those fish to a school.

### Encyclopedia

The encyclopedia is not a “completionist game.” It is **a record of emotions and places**.

```text
2026/09/27  Tokyo
“I did it”
  ↓
Chikuzengobi
```

Users explicitly choose whether to save raw emotion text. It is not saved by default.

---

## Location design

Location data is not used to show precise locations of rare species.

### MVP

- Browser Geolocation API
- Use latitude and longitude locally to determine a region
- Send only a coarse region or habitat ID, such as `Tokyo`, `Tokyo Bay`, or `river`, to the server
- Do not persist raw latitude and longitude by default
- The experience remains available if location permission is denied, using “Explore Tokyo”

### Why use coarse location?

The experience should preserve this sense of distance:

**“The region of Tokyo where I am has these kinds of waters and living things,”**

rather than leading users to believe, “This fish lives in Akihabara.”

---

## Species data

### MVP candidates

Do not implement a large number of fish from the start. Begin with three to five species and give each one a carefully made 3D model, swimming behavior, and description.

Candidates:

- Japanese eight-barbel loach (Hotokedojō)
- Japanese rice fish (Minamimedaka)
- Edo goby (Edohaze)
- Tokyo Bay goby (Chikuzengobi)
- Japanese eel (Nihon-unagi)

The candidates are not final. Check the Tokyo Red List, Red Data Book, and official survey reports for Tokyo Bay and rivers. Select species based on **their connection to Tokyo, current conservation categories, ease of 3D modeling, differences in swimming behavior, and clarity in a demo**.

### Source policy

Keep at least the following fields for factual information in `Species`:

```ts
type Species = {
  id: string
  japaneseName: string
  scientificName: string
  habitatType: 'bay' | 'brackish' | 'river' | 'spring' | 'stream'
  regionIds: string[]
  officialStatus: string
  officialStatusSystem: string
  sourceUrl: string
  sourceUpdatedAt?: string
  story: string
  swimProfileId: string
  renderProfileId: string
}
```

Never save a generative AI answer directly as species data in the database.

---

## UTAKATA Level

Codes such as CR / EN / VU may not be clear to first-time users, so the experience includes a simplified `UTAKATA LEVEL`.

However, **it does not hide or replace the official category**.

Provisional proposal:

| UTAKATA | Example official category | Description |
|---|---|---|
| 5 | CR | At very high risk of disappearing |
| 4 | EN | At high risk of disappearing |
| 3 | VU | Numbers are declining and the risk is increasing |
| 2 | NT | Could be at greater risk in the future |
| ? | DD | There is not enough information to assess |
| LOST | EX / EW | Already lost, or no longer found in the wild |

Finalize the mapping based on the category definitions in the selected primary sources.

---

## Emotion → Fish matching

Do not ask AI to choose a fish directly. Use two stages.

### 1. AI: EmotionProfile

Gemini returns structured JSON from the short text.

```ts
type EmotionProfile = {
  valence: number      // -1 .. 1
  arousal: number      //  0 .. 1
  calmness: number     //  0 .. 1
  openness: number     //  0 .. 1
}
```

These are continuous values for visual expression, not a psychological diagnosis.

### 2. Domain: Species matching

First filter candidate fish by region, habitat, and data quality. Then compare their human-curated `RenderProfile` / `SwimProfile` against the EmotionProfile.

```text
Emotion text
  ↓ Gemini
EmotionProfile
  ↓
Region filter
  ↓
Habitat filter
  ↓
Curated species candidates
  ↓ deterministic scoring
Matched species
```

This approach means:

- AI hallucinations cannot introduce new fish species
- The same input can produce repeatable results
- The reason a fish was selected can be explained
- Changing the AI provider is less likely to break the Domain layer

---

# Technical architecture

## Stack

### Frontend

- React
- Vite
- TypeScript
- React Three Fiber
- Three.js `WebGPURenderer`
- TSL (Three.js Shading Language)
- Zustand (minimal UI state only)

### Backend

- Node.js
- Hono
- PostgreSQL
- `pg` / primarily raw SQL
- Gemini API
- WebSocket (sharing release events is a stretch goal)

### Testing

- Vitest
- React Testing Library
- Playwright

---

## Why Hono?

Hono is **not** the center of the application.

```text
HTTP Request
   ↓
Hono Router / Handler       ← Interface Adapter
   ↓
Application UseCase
   ↓
Domain
   ↓
Repository / AI Port / Event Port
   ↓
PostgreSQL / Gemini / WebSocket
```

As a rule, do not put SQL or direct Gemini calls inside handlers.

Hono’s role is to translate HTTP requests and responses.

---

## Monorepo proposal

```text
UTAKATA/
├─ apps/
│  ├─ web/
│  │  ├─ src/
│  │  │  ├─ features/
│  │  │  ├─ scenes/
│  │  │  ├─ shaders/
│  │  │  └─ ui/
│  │  └─ public/
│  │
│  └─ api/
│     └─ src/
│        ├─ routes/
│        ├─ adapters/
│        └─ infrastructure/
│
├─ packages/
│  ├─ domain/
│  │  ├─ emotion/
│  │  ├─ species/
│  │  ├─ fish/
│  │  └─ release/
│  │
│  ├─ application/
│  │  └─ usecases/
│  │
│  └─ contracts/
│     └─ api/
│
└─ docs/
   └─ adr/
```

---

## Event-driven domain

UTAKATA’s experience naturally divides around events:

```text
EmotionSubmitted
      ↓
EmotionProfileCreated
      ↓
SpeciesMatched
      ↓
FishBorn
      ↓
FishReleased
      ↓
FishArchived
```

**In-process domain events** are enough to start.

Do not add an external message broker such as Kafka to the MVP.

> Event-driven does not mean “add Kafka.”

First separate state transitions from side effects. Add only boundaries that could later be swapped for an Outbox / Queue / PubSub if the number of releases grows or the app is split into multiple services.

### Example

```ts
class ReleaseFish {
  constructor(
    private readonly fishRepository: FishRepository,
    private readonly events: DomainEventPublisher,
  ) {}

  async execute(input: ReleaseFishInput) {
    const fish = await this.fishRepository.findById(input.fishId)
    fish.release()

    await this.fishRepository.save(fish)
    await this.events.publish(new FishReleased(fish.id, fish.speciesId))
  }
}
```

Subscribers to `FishReleased` can independently add encyclopedia storage, WebSocket broadcast, analytics, and other behavior.

---

# WebGPU / fish rendering

## Renderer

Use Three.js `WebGPURenderer`, with TSL as the default for shader expression.

The Three.js documentation says that WebGPURenderer prioritizes WebGPU and can fall back to a WebGL 2 backend when unavailable. It is still experimental, however, and legacy `ShaderMaterial` / `RawShaderMaterial` / `EffectComposer` cannot be migrated as-is.

For that reason, start with **Node Material + TSL**.

React Three Fiber can initialize `WebGPURenderer` asynchronously through `Canvas`’s `gl` option.

---

## Fish layers

Use two rendering strategies for fish.

### Hero Fish

One fish born from an emotion and viewed up close.

```text
low/mid poly mesh
+ BaseColor
+ Normal
+ Roughness
+ body specular
+ translucent fins
+ eye material
+ species-specific swim deformation
+ caustics / underwater light
```

Do not try to achieve realism through polygon count alone. Spend the budget on **silhouette, scale normals, wet reflections, fins, eyes, and swimming behavior**.

### School Fish

Many fish living underwater after release.

```text
Shared Mesh
  ↓
Instancing
  ↓
Per-instance position / velocity / phase / species
  ↓
Vertex deformation
  ↓
Boids / spatial partition
  ↓
WebGPU compute (Stretch)
```

Dozens or hundreds of fish are enough for the MVP. Use GPU compute only if it becomes necessary to support a world where released fish continue to accumulate.

---

## Species-specific swimming

Do not animate every fish with the same sine wave.

```ts
type SwimProfile = {
  bodyFlexStart: number
  waveAmplitude: number
  waveFrequency: number
  tailAmplitude: number
  turnRate: number
  preferredDepth: number
  schooling: number
}
```

For example, a slender fish can pass a wave through its whole body, while a strong swimmer can move mostly through its rear body and tail. Make the differences visible with just three to five species.

---

## Underwater scene

Instead of placing a “photo of the ocean” in the background, layer cues that make the scene feel underwater:

- Depth fog
- Volumetric-like light rays
- Caustics
- Marine snow / suspended particles
- Small bubbles
- Subtle current drift
- Distance color attenuation
- Slow camera movement

Change the underwater environment slightly based on each species’ habitat.

```text
Tokyo Bay     → blue / sand / open space
Brackish      → slightly murky / currents
River         → greener / rocks / water flow
Spring        → bright / aquatic plants / shallow water
Mountain      → cold blue / rocks / fast current
```

The home screen should feel like “underwater remembered in a dream,” without belonging too strongly to any one habitat.

---

## Fallback strategy

The core experience should still work on devices that cannot use WebGPU.

```text
WebGPU available
  ├─ TSL NodeMaterial
  ├─ GPU compute school
  └─ higher fish count

WebGPU unavailable
  ├─ WebGL2 backend
  ├─ same core scene
  ├─ CPU/simple movement
  └─ lower fish count
```

GPU compute must not be a requirement for the MVP.

---

# Data / privacy

## Emotion text

By default:

- Use it for API processing
- Do not persist the raw text
- Derived EmotionProfile may be saved

Ask for explicit opt-in only if retaining the original text in the encyclopedia.

## Location

- Do not persist precise latitude / longitude
- Convert to a coarse region before use
- Keep the app usable when location permission is denied

## Species

- Require a source URL for official facts
- Track `updated_at` / version
- Do not use AI-generated conservation information

---

# MVP

## Must

- [ ] Underwater home
- [ ] Bubble-shaped UTAKATA logo
- [ ] Tap logo → break it into bubbles that rise
- [ ] Emotion input
- [ ] Gemini structured output
- [ ] At least three Tokyo fish species
- [ ] Coarse location
- [ ] Fish-birth effect
- [ ] Hero Fish 3D
- [ ] UTAKATA Level + official conservation category
- [ ] Links to primary sources
- [ ] Release
- [ ] Encyclopedia
- [ ] WebGPU / WebGL2 fallback
- [ ] Complete the judge demo end to end

## Stretch

- [ ] At least five fish species
- [ ] WebGPU compute Boids
- [ ] Receive other users’ FishReleased events over WebSocket
- [ ] Shared ocean
- [ ] Sound
- [ ] Background changes by habitat
- [ ] Procedural caustics
- [ ] Photo mode

## Not in MVP

- Implementing 200 species
- Realistic ocean-fluid simulation
- Precise map of habitats
- Social networking
- User accounts
- Payments
- Kafka
- Microservices
- Biological reasoning by AI

---

# Success criteria

At HACK STAGE, the priority is not “using lots of technology.” It is **communicating the value in 30 seconds and helping users remember one Tokyo fish after trying it**.

### Product

- A first-time user can reach emotion input without an explanation
- A fish is born within 60 seconds and the user understands its connection to the region
- After the experience, the user remembers at least one species name or feature

### Visual

- The moment the logo is tapped communicates the character of UTAKATA
- The fish looks like a living creature, not just a “3D model”
- The underwater experience is the focus, not the UI

### Technical

- Domain / Application are independent of Hono and Gemini
- Official species data and AI output are not mixed
- The core flow works even when WebGPU is unavailable
- A three-minute demo runs without requiring a reload

---

# After HACK STAGE — Gekiiku CAMP / broader version

HACK STAGE focuses on Tokyo. Applications such as Gekiiku CAMP can expand to other regions.

```text
Region
├─ Tokyo
│  ├─ Tokyo Bay
│  ├─ River
│  └─ Spring
├─ Osaka
├─ Kyoto
├─ Aichi
└─ ...
```

The long-term goal is for people traveling or attending a conference in another place to open UTAKATA and **meet the aquatic life of that location**.

Each change of place adds memories of emotions and local ecosystems to the encyclopedia.

---

# Research references

## Event

- HACK STAGE STAGE3 — CraftStadium  
  https://www.craftstadium.com/hackathon/hackstaga3

## Biodiversity / conservation

- Tokyo Red Data Book (Mainland), 2023  
  https://www.kankyo.metro.tokyo.lg.jp/nature/animals_plants/red_data_book/400100a20230424184941875
- Tokyo’s Important Wildlife Species for Conservation (Mainland), 2020 revision  
  https://www.kankyo.metro.tokyo.lg.jp/nature/animals_plants/red_data_book/400100a20230424184008472
- Ministry of the Environment: Publication of the Fifth Red List  
  https://www.env.go.jp/press/press_03312.html

## Graphics

- Three.js — WebGPURenderer  
  https://threejs.org/manual/en/webgpurenderer
- Three.js — WebGPU Post-Processing  
  https://threejs.org/manual/en/webgpu-postprocessing.html
- React Three Fiber — Canvas / WebGPU  
  https://github.com/pmndrs/react-three-fiber/blob/master/docs/API/canvas.mdx
- WebGL Aquarium  
  https://github.com/WebGLSamples/WebGLSamples.github.io/tree/master/aquarium

## Backend

- Hono — Node.js  
  https://hono.dev/docs/getting-started/nodejs
- Hono — RPC  
  https://hono.dev/docs/guides/rpc
- Hono — WebSocket  
  https://hono.dev/docs/helpers/websocket

---

## Status

**Planning / research phase.**

Requirements, design, and implementation are tracked separately through Issues. MVP implementation will begin after the HACK STAGE rules are confirmed.

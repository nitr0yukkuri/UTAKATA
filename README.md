# UTAKATA

> いまの感情が、この場所の水のいきものになる。

UTAKATA は、**自分の一瞬の感情を入口に、いまいる地域の水辺と生きものを知るためのWeb体験**です。

水中に浮かぶ泡の `UTAKATA` ロゴを押すと、文字がほどけて泡になり、水面へ昇っていきます。短い感情を入力すると、AIがその感情を“診断”ではなく**表現用の特徴量**へ変換し、位置情報から絞り込んだ地域の魚の中から、1匹の魚が生まれます。

生まれた魚を眺め、名前・生息環境・保全状況を知り、最後に水中へ放す。個人的な感情の記憶と、その土地の生態系の記憶を重ねることを狙います。

---

## Why

東京のような大都市にも、海・川・湧水・汽水域など多様な水環境があります。一方で、希少種や地域の生態系に関する一次情報は、行政のレッドリストやレッドデータブック、調査資料にまとまっているものの、日常の体験として触れる機会は多くありません。

東京都は「東京都レッドデータブック（本土部）2023」を公開しており、本土部の淡水魚類だけでも50種がレッドリストに掲載されています。UTAKATAでは、こうした公的データを“読む”ところから始めるのではなく、**自分の感情 → 1匹の魚 → その魚が暮らす地域**という順番で出会いをつくります。

このプロダクトで解きたいのは「絶滅危惧種の一覧をもっと見やすくすること」ではありません。

**地域の自然を、自分ごととして覚える入口をつくること**です。

---

## HACK STAGE STAGE3

HACK STAGE STAGE3 は「地方創生 × AI」を掲げた、2026年9月19日〜27日のハイブリッド型ハッカソンです。公式ページでは、Web開発・社会課題解決・モバイルアプリ開発のタグが付いています。

UTAKATA の HACK STAGE 版では、まず **東京の水辺** に範囲を絞ります。

- 地域: 東京
- テーマ: 都市の中で見落とされがちな水辺の生態系
- AIの役割: 感情テキストを表現用プロフィールへ構造化
- 体験の核: 感情から地域の魚が生まれ、知り、放す
- 技術の核: WebGPU / TSL / GPU instancing / Hono / event-driven domain design

> 実装開始時期・事前開発の可否・既存資産の再利用範囲は、参加確定後に案内されるルールを優先します。このRepositoryでは、ルール確認までは要件定義・設計・調査を中心に扱います。

---

## Product principle

UTAKATA は、**AIに魚の保全状況を推測させません。**

保全カテゴリ・生息地域・和名・学名などの事実は、東京都や環境省などの一次情報から取得・管理します。AIが担当するのは、ユーザーの短い感情テキストを、演出とマッチングに使える数値へ変換するところだけです。

また、「悲しい人にはこの魚」のような生物学的根拠のない説明はしません。感情と魚の対応は、あくまで**UTAKATA内の詩的・視覚的なマッピング**であることをUI上でも明示します。

---

## Core experience — 60 seconds

```text
水中
  ↓
泡でできた「UTAKATA」
  ↓ tap
文字が崩れ、泡になって水面へ昇る
  ↓
「いまの気持ちを、ひとこと。」
  ↓
感情を入力
  ↓
AIが EmotionProfile を生成
  ↓
地域 + 公的データから候補魚を絞る
  ↓
感情プロフィールと魚の演出プロフィールをマッチング
  ↓
泡が集まり、1匹の魚が生まれる
  ↓
魚を眺める
  ↓
名前 / 生息環境 / UTAKATA Level / 公式保全カテゴリ
  ↓
「水へかえす」
  ↓
魚が奥へ泳ぎ、海の一部になる
  ↓
図鑑に残る
```

### Home

- 背景は「海」固定ではなく、**抽象化した水中空間**
- 深い青、光芒、浮遊粒子、わずかな泡
- 中央に、白い泡で構成された `UTAKATA`
- ロゴそのものがEnterボタン
- Helpは右上
- 図鑑は右下

### Logo transition

`UTAKATA` を押した瞬間に、各文字を構成する泡が接着を失い、少し揺れたあと上方向へ分解・浮上します。

単なるフェードアウトではなく、**「泡沫が消える」ことを操作そのものにする**のが重要です。

### Emotion input

入力は1〜2文程度を想定します。

例:

- 「なんか今日は静かにしてたい」
- 「やりきった。めちゃくちゃ気持ちいい」
- 「東京来たけどちょっと疲れた」

UTAKATA は医療・心理診断を行いません。感情入力は魚を生むための表現パラメータとして扱います。

### Fish birth

いきなり完成した魚を表示しません。

```text
小さな泡
  ↓
輪郭が見える
  ↓
透明な魚体
  ↓
色・鱗・光沢が入る
  ↓
目が光を拾う
  ↓
尾が動く
  ↓
泳ぎ出す
```

「生成結果を表示」ではなく、**魚が生まれたと感じる数秒**をつくります。

### Result card

最初に見せる情報は少なくします。

```text
ホトケドジョウ
UTAKATA LEVEL 5
「東京の湧水や細流と関わりの深い魚」

[ もっと知る ]
[ 水へかえす ]
```

詳細では以下を表示します。

- 和名
- 学名
- 生息環境
- 地域との関係
- 公式の保全カテゴリ
- 情報源
- UTAKATA Levelの意味
- 写真・3Dモデルの出典

### Release

「水へかえす」を押すと、その魚はユーザーの所有物ではなく**UTAKATAの水中世界の一個体**になります。

MVPでは自分のセッション内だけでも成立します。StretchではWebSocketで他ユーザーの放流イベントを受信し、水中の魚群へ追加します。

### Encyclopedia

図鑑は「コンプリートゲーム」ではなく、**感情と地域の記憶**です。

```text
2026/09/27  東京
「やりきった」
  ↓
チクゼンハゼ
```

生の感情テキストを保存するかは明示的にユーザーが選べるようにし、デフォルトでは保存しません。

---

## Location design

位置情報は、希少種の正確な生息地点を表示するためには使いません。

### MVP

- Browser Geolocation API
- 緯度経度はその場で地域判定に使う
- サーバーへ送るのは `Tokyo` / `Tokyo Bay` / `river` などの粗い地域・環境ID
- 生の緯度経度は原則保存しない
- 位置情報を拒否しても `東京を体験する` で利用可能

### Why coarse location

「秋葉原にこの魚がいる」と誤認させるのではなく、

**“いまいる東京という地域には、こういう水辺と生きものがある”**

という距離感を守ります。

---

## Species data

### MVP candidate

最初から多数の魚を実装しません。まず3〜5種に絞り、1種ごとの3D・泳ぎ・説明を作り込みます。

候補:

- ホトケドジョウ
- ミナミメダカ
- エドハゼ
- チクゼンハゼ
- ニホンウナギ

候補は確定ではありません。東京都レッドリスト・レッドデータブック・東京湾や河川の公的調査資料を確認し、**東京との関係、現在の保全カテゴリ、3D化のしやすさ、泳ぎ方の差、デモでの伝わりやすさ**から選定します。

### Source policy

`Species` の事実情報は、最低でも以下を保持します。

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

生成AIの回答を、そのまま生物データとしてDBへ保存することは禁止します。

---

## UTAKATA Level

CR / EN / VU などの記号だけでは初見で伝わりにくいため、体験上の簡易表示として `UTAKATA LEVEL` を置きます。

ただし、**公式カテゴリを隠したり置き換えたりはしません。**

暫定案:

| UTAKATA | 公式カテゴリの例 | 表現 |
|---|---|---|
| 5 | CR | 消えてしまう危険がとても高い |
| 4 | EN | 消えてしまう危険が高い |
| 3 | VU | 数が減り、危険が大きくなっている |
| 2 | NT | 今後、危険が高まる可能性がある |
| ? | DD | 情報が足りず判断できない |
| LOST | EX / EW | すでに失われた、または野生では確認できない |

最終マッピングは、採用する一次情報のカテゴリ定義に合わせて確定します。

---

## Emotion → Fish matching

AIに魚を直接選ばせるのではなく、2段階に分けます。

### 1. AI: EmotionProfile

Geminiには、短いテキストから構造化JSONだけを返してもらいます。

```ts
type EmotionProfile = {
  valence: number      // -1 .. 1
  arousal: number      //  0 .. 1
  calmness: number     //  0 .. 1
  openness: number     //  0 .. 1
}
```

これは心理診断ではなく、演出のための連続値です。

### 2. Domain: Species matching

候補魚はまず地域・生息環境・データ品質で絞ります。その後、あらかじめ人間が設定した `RenderProfile` / `SwimProfile` と EmotionProfile を比較します。

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

これにより、

- AIの幻覚で魚種が増えない
- 同じ入力に再現性を持たせられる
- なぜその魚になったか説明できる
- AI providerを交換してもDomainを壊しにくい

という利点があります。

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
- Zustand（必要最小限のUI state）

### Backend

- Node.js
- Hono
- PostgreSQL
- `pg` / 生SQLを基本にする
- Gemini API
- WebSocket（放流イベントの共有はStretch）

### Testing

- Vitest
- React Testing Library
- Playwright

---

## Why Hono

Honoを“アプリケーションの中心”にはしません。

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

Handlerの中にSQLやGemini呼び出しを直接書かないことを原則にします。

Honoの役割はHTTPとの変換です。

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

UTAKATAの体験は「何かが起きた」ことを起点に自然に分けられます。

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

最初は**プロセス内Domain Event**で十分です。

Kafkaなどの外部message brokerはMVPに入れません。

> Event-driven = Kafkaを入れること、ではない。

まず状態遷移と副作用を分離します。将来、放流数が増えたり複数サービスへ分割したときに、Outbox / Queue / PubSubへ差し替えられる境界だけ用意します。

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

`FishReleased` を購読する側は、図鑑保存、WebSocket broadcast、analyticsなどを独立して追加できます。

---

# WebGPU / fish rendering

## Renderer

Three.jsの `WebGPURenderer` を使い、shader表現はTSLを基本にします。

Three.js公式ドキュメントでは、WebGPURendererはWebGPUを優先し、利用できない環境ではWebGL 2 backendへfallbackできます。一方でまだexperimentalで、従来の `ShaderMaterial` / `RawShaderMaterial` / `EffectComposer` はそのまま移植できません。

そのため、最初から **Node Material + TSL** 前提で組みます。

React Three Fiberは、`Canvas` の `gl` からasyncに `WebGPURenderer` を初期化できます。

---

## Fish layers

魚は2種類の描画戦略に分けます。

### Hero Fish

感情から生まれ、ユーザーが近距離で見る1匹。

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

ポリゴン数だけでリアルさを出そうとせず、**シルエット・鱗の法線・濡れた反射・ヒレ・目・泳ぎ**に予算を使います。

### School Fish

放流後、水中に存在する多数の魚。

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

MVPでは数十〜数百匹でも十分です。GPU computeは「使うこと」ではなく、放流個体が増え続ける世界を成立させる必要が出たときに使います。

---

## Species-specific swimming

すべての魚を同じsin波で動かしません。

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

例えば、細長い魚は体全体に波を通し、遊泳力の高い魚は後半〜尾を中心に動かすなど、3〜5種でも差が見えるようにします。

---

## Underwater scene

「海の写真」を背景に置くのではなく、3Dで水中を感じる手がかりを積み重ねます。

- depth fog
- volumetric-like light rays
- caustics
- marine snow / suspended particles
- small bubbles
- subtle current drift
- distance color attenuation
- slow camera movement

魚の生息環境によって、水中空間を少し変えます。

```text
Tokyo Bay     → 青 / 砂 / 開放感
Brackish      → 少し濁る / 流れ
River         → 緑寄り / 石 / 水流
Spring        → 明るい / 水草 / 浅い
Mountain      → 冷たい青 / 岩 / 速い流れ
```

ホームはどれにも属しすぎない「記憶の中の水中」にします。

---

## Fallback strategy

WebGPUが使えない端末でもコア体験は壊しません。

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

GPU computeが必須の機能は、MVPの成立条件にしません。

---

# Data / privacy

## Emotion text

デフォルト:

- API処理に利用
- raw textは永続保存しない
- derived EmotionProfileのみ保存可能

図鑑へ原文を残す場合のみ明示的なopt-inを取ります。

## Location

- precise latitude / longitudeは永続保存しない
- coarse regionへ変換してから扱う
- 位置情報拒否でも利用可能

## Species

- 公式情報にはsource URLを必須
- `updated_at` / versionを管理
- AI生成した保全情報を採用しない

---

# MVP

## Must

- [ ] 水中ホーム
- [ ] 泡のUTAKATAロゴ
- [ ] ロゴtap → 泡へ分解して上昇
- [ ] 感情入力
- [ ] Gemini structured output
- [ ] 東京の魚3種以上
- [ ] coarse location
- [ ] 魚が生まれる演出
- [ ] Hero Fish 3D
- [ ] UTAKATA Level + 公式保全カテゴリ
- [ ] 一次情報リンク
- [ ] 放流
- [ ] 図鑑
- [ ] WebGPU / WebGL2 fallback
- [ ] judge demoを通しで完走できる

## Stretch

- [ ] 魚5種以上
- [ ] WebGPU compute Boids
- [ ] 他ユーザーのFishReleasedをWebSocketで受信
- [ ] shared ocean
- [ ] 音響
- [ ] habitatごとの背景切替
- [ ] procedural caustics
- [ ] photo mode

## Not MVP

- 200種実装
- リアルな海洋流体シミュレーション
- 生息地点の精密マップ
- SNS
- ユーザーアカウント
- 課金
- Kafka
- microservices
- AIによる生物学的推論

---

# Success criteria

HACK STAGEで最も重要なのは「技術をたくさん積んだこと」ではなく、**30秒で価値が伝わり、触ったあとに東京の魚を1匹覚えていること**です。

### Product

- 初見ユーザーが説明なしで感情入力まで到達できる
- 60秒以内に魚が生まれ、地域との関係を理解できる
- 体験後に最低1種の名前または特徴を覚えている

### Visual

- ロゴを押した瞬間にUTAKATAらしさが伝わる
- 魚が「3Dモデル」ではなく「生き物」に見える
- UIより水中体験が主役

### Technical

- Domain/ApplicationがHonoやGeminiから独立
- 公式生物データとAI出力が混ざらない
- WebGPU非対応でもコア導線が動く
- 3分デモ中にreload不要

---

# After HACK STAGE — 技育CAMP / broader version

HACK STAGEでは東京に絞り、技育CAMPなど次の応募では地域を広げます。

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

最終的には、旅行やカンファレンスで別の土地へ行ったとき、UTAKATAを開くだけで、**その場所の水の生きものと出会える**状態を目指します。

場所が変わるたびに、感情の記憶と地域の生態系が図鑑に増えていきます。

---

# Research references

## Event

- HACK STAGE STAGE3 — CraftStadium  
  https://www.craftstadium.com/hackathon/hackstaga3

## Biodiversity / conservation

- 東京都レッドデータブック（本土部）2023  
  https://www.kankyo.metro.tokyo.lg.jp/nature/animals_plants/red_data_book/400100a20230424184941875
- 東京都の保護上重要な野生生物種（本土部）2020年見直し版  
  https://www.kankyo.metro.tokyo.lg.jp/nature/animals_plants/red_data_book/400100a20230424184008472
- 環境省 第5次レッドリスト公表状況  
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
- Craig Reynolds — Boids  
  https://www.red3d.com/cwr/boids/

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

Issue単位で要件・設計・実装を分離し、HACK STAGEのルール確認後にMVP実装へ入ります。

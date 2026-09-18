# Malilion Novel Library｜碼力獅小說典藏館 Spec

> Project: **Malilion Novel Library**  
> Domain: `novel.malilion.com`  
> Positioning: Multi-novel 3D digital library + long-form web reader  
> Initial title: 《九重燼 / Embers of the Ninefold Court》  
> Reference direction: MengTo / complete-shelf 的 3D 書架、精裝書材質與開書互動概念；本專案重新設計自己的程式、材質與出版識別。

---

## 1. Product Vision

建立一座可持續收錄多部小說 IP 的數位藏書館。

核心體驗：

```text
3D Library
  ↓
3D Bookshelf
  ↓
Book Inspection
  ↓
Book Opening
  ↓
Preview Pages
  ↓
HTML Novel Reader
```

3D 負責「選書與開書的儀式感」，真正的長篇閱讀使用可搜尋、可選字、可分享、可無障礙操作的 HTML Reader。

---

## 2. Architecture Principle

> **平台與作品分離。**

《九重燼》不是平台本身，而是第一套藏書。

新增新小說時，原則上只新增：

- Work metadata
- Volume metadata
- Chapter metadata
- Markdown content
- Book theme / cover assets

不應修改：

- Shelf Core
- Reader Core
- Three.js Book Core
- Content Loader Core

第二部作品以 **Project Lily** 驗證 Multi-Novel 架構。

---

## 3. Repository / Domain

Repository：

```text
github.com/malilion/novel-library
```

Production：

```text
https://novel.malilion.com
```

---

## 4. Recommended Stack

```text
Vue 3
Vite
TypeScript
Three.js
Pinia
Markdown
Vercel
```

Markdown loader 建議以 Vite `import.meta.glob()` 建立 lazy content registry。

---

## 5. Information Architecture

```text
Malilion Novel Library
│
├── 3D Library / Home
├── Continue Reading
├── Collections
├── Search
├── Works
│   ├── 九重燼
│   │   ├── 藏鋒
│   │   ├── 入局
│   │   ├── 九重
│   │   ├── 人物志
│   │   ├── 皇室列傳
│   │   ├── 深情特典
│   │   ├── 番外
│   │   └── 異聞錄
│   ├── Project Lily
│   └── Future Works...
└── Reader
```

---

## 6. URL Design

```text
/
 /works
 /works/embers-of-the-ninefold-court
 /read/embers-of-the-ninefold-court/volume-1/chapter-01
```

每一部作品與每一章都必須有真正可分享的 HTML URL，不能只存在 Canvas / WebGL 狀態裡。

---

## 7. 3D Shelf

首頁主要體驗為 3D 書架。

支援：

- Mouse wheel
- Arrow keys
- Previous / Next
- Touch swipe
- Position indicators
- Click book
- Keyboard accessible HTML fallback

Selected book 應有克制的前移／亮度／材質提示。

書架數量增加後支援 collection / universe 切換，不把所有書擠在單一無限列。

---

## 8. 3D Hardcover Model

每一本書由可重用幾何組成：

- Front board
- Back board
- Spine
- Hinges
- Page block
- Endpapers
- Headbands
- Bookmark
- Foil accents
- Contact shadow

需求：

- width / height / thickness metadata-driven
- front / spine / back artwork 分離
- back cover text 不鏡像
- shared geometry / materials
- PBR 材質
- 避免過度圓角與玩具感

---

## 9. Interaction State Machine

```text
IDLE
 ↓
SHELF_BROWSING
 ↓
BOOK_SELECTED
 ↓
BOOK_PULLING
 ↓
BOOK_INSPECTION
 ↓
BOOK_OPENING
 ↓
BOOK_PREVIEW
 ↓
READER
 ↓
BOOK_CLOSING
 ↓
RETURN_TO_SHELF
```

主要 transition 使用 time-based deterministic interpolation。

Shelf → Detail → Shelf 的 first / final pose 必須精確，reparent 不得出現 last-frame jump。

---

## 10. Book Inspection

選書後顯示：

- 3D 書本
- 書名
- 卷名
- Canon
- 章數
- 簡介
- 開始閱讀
- 查看目錄
- 打開書本

支援：

- Orbit
- Rotate
- Zoom
- Pan
- Front / Spine / Back inspection

Desktop hover front cover 時可微開 5–10°；Click / Tap 完整開書。

---

## 11. 3D Preview Pages

3D 翻頁不承擔完整長篇閱讀。

每本書約 5–10 preview pages：

1. 扉頁
2. 書名頁
3. 卷首語
4. 世界觀 / 編輯介紹
5. 目錄
6. 第一章 Preview
7. 開始閱讀 CTA

支援向前／向後 drag，active page 可 bend / twist / settle，翻頁完成後不能彈回。

---

## 12. HTML Novel Reader

Desktop：

```text
TOC | ARTICLE | READER TOOLS
```

Mobile：

```text
ARTICLE
[Prev] [TOC] [Next]
```

基本功能：

- Markdown render
- Chapter TOC
- Previous / Next
- Canon badge
- Reading progress
- Bookmark
- Continue Reading
- Search-ready semantic HTML
- Direct URL
- Browser back / forward

---

## 13. Reader Appearance

支援：

- Font size
- Line height
- Paragraph spacing
- Content width
- Serif / Sans Serif
- Light
- Sepia
- Dark

設定先使用 localStorage 保存。

---

## 14. Reading State

MVP 使用 localStorage：

```json
{
  "work": "embers-of-the-ninefold-court",
  "volume": "volume-2",
  "chapter": "chapter-18",
  "progress": 0.48,
  "lastReadAt": "ISO-8601"
}
```

支援：

- last work
- last volume
- last chapter
- scroll progress
- bookmarks
- reader preferences
- versioned storage schema

未來若需會員同步，再接後端服務。

---

## 15. Multi-Novel Content Structure

推薦：

```text
content/
├── jiuzhongjin/
│   ├── work.json
│   ├── 卷一_藏鋒/
│   ├── 卷二_入局/
│   ├── 卷三_九重/
│   ├── 人物志/
│   ├── 皇室列傳/
│   ├── 深情特典/
│   ├── 番外/
│   └── 異聞錄/
├── project-lily/
│   ├── work.json
│   └── ...
└── future-work/
```

---

## 16. Work Schema

Example：

```json
{
  "id": "embers-of-the-ninefold-court",
  "title": "九重燼",
  "englishTitle": "Embers of the Ninefold Court",
  "author": "Malilion",
  "status": "completed",
  "genre": ["架空歷史", "政治", "穿越", "宮廷"],
  "description": "現代歷史學者穿越成大晟九皇子蕭承淵……",
  "theme": "imperial",
  "defaultCanon": "novel"
}
```

---

## 17. Core Data Types

平台至少需要：

- `Work`
- `Volume`
- `Chapter`
- `Canon`
- `BookTheme`
- `ReadingProgress`

所有 Shelf / Reader / Detail UI 由 metadata 驅動。

---

## 18. Canon System

《九重燼》原生支援：

- NOVEL CANON
- CHARACTER LORE
- ROYAL CHRONICLES
- GAMEVERSE EXTRA
- GAMEVERSE IF
- ROMANCE IF

Canon Badge 在：

- Book Detail
- TOC / Collection
- Reader

都要顯示，避免 IF / Gameverse 被誤認為小說唯一正史。

---

## 19. 《九重燼》First Collection

第一版 8 Books：

| # | Book | Canon |
|---|---|---|
| 1 | 藏鋒 | Novel Canon |
| 2 | 入局 | Novel Canon |
| 3 | 九重 | Novel Canon |
| 4 | 人物志 | Character Lore |
| 5 | 皇室列傳 | Royal Chronicles |
| 6 | 深情特典 | Romance IF |
| 7 | 番外 | Gameverse Extra |
| 8 | 異聞錄 | Gameverse IF |

---

## 20. 《九重燼》Book Art Direction

### 藏鋒
墨黑 × 深青 × 暗金；鳳尾玉、暗龍紋。

### 入局
深紅 × 玄黑 × 金；棋局、詔書、玄沙。

### 九重
帝王黑 × 暗金 × 血紅；九重宮門、帝印。

### 人物志
象牙白 × 墨 × 淡金；六女主剪影、人物印章。

### 皇室列傳
玄黑 × 帝王金；皇族、宮闕。

### 深情特典
梅紅 × 煙紫 × 月白；梅、月、絲帶。

### 番外
茶褐 × 舊紙 × 青墨；典藏手稿。

### 異聞錄
深紫 × 黑 × 銀；裂痕、平行世界。

整套需共享統一出版識別，但每本具有獨立辨識度。

---

## 21. Content Sync

《九重燼》內容 Source of Truth：

```text
malilion/the-Ninth-Prince/novel/
```

Novel Library 不應人工維護另一份逐漸漂移的小說版本。

第一階段可提供可重跑 sync/import script，並記錄：

- source repository
- source commit SHA
- sync timestamp

後續可由 GitHub Actions 自動同步。

---

## 22. Second Work Validation

以 **Project Lily** 驗證：

新增第二部作品時，不修改：

- Shelf Core
- Reader Core
- Three Book Core
- Content Loader Core

只新增：

- metadata
- Markdown
- theme
- covers / assets

此條件成立才算真正 Multi-Novel Ready。

---

## 23. Collections / Search

作品增加後支援：

- Original Novels
- Interactive Novel Adaptations
- Romance
- Historical
- Fantasy
- Short Stories

也支援 Universe：

- 九重燼 Universe
- Project Lily Universe

搜尋至少預留：

- Works
- Chapters
- Characters
- Keywords

---

## 24. Mobile Strategy

Desktop：
- Full 3D Shelf
- Full inspection

Mobile：
- Light 3D / Book Carousel
- 降低同時存在的高成本模型與 texture
- Reader 優先

避免在手機同時渲染大量高面數精裝書。

---

## 25. Performance

- nearby book loading
- LOD
- texture lazy load
- shared geometry
- shared materials
- texture atlas
- AVIF / WebP
- 不在首頁載入全作品正文
- Three.js chunk lazy load if practical

---

## 26. Accessibility

3D Canvas 不能成為唯一入口。

必須有同步 HTML：

- Work list
- Book selection
- Reader links

並支援：

- Keyboard
- Screen reader
- accessible names
- focus states
- status announcements
- `prefers-reduced-motion`

Reduced Motion 模式可取消 camera fly / page physics，改用直接切換或淡入淡出。

---

## 27. SEO

每部小說與每章都提供：

- title
- description
- canonical
- Open Graph
- Schema.org Book
- Article / Chapter structured data
- sitemap
- robots

全文不能只存在 WebGL Canvas。

---

## 28. Audio (Optional)

可加入：

- 木質書架
- 布面摩擦
- 書本抽出
- 封面開啟
- 紙張翻頁
- 書本闔上
- 極輕環境音

Audio 必須：
- user gesture 後啟動
- 有 mute / volume
- 不妨礙 Reader

---

## 29. Reference Boundary

可參考 `MengTo/complete-shelf` 的：

- 3D shelf browsing
- book inspection
- book opening
- preview page interaction
- editorial presentation
- physical material feeling

不直接複製：

- 原始 `index.html`
- textures
- book cover designs
- visual assets

建立 Malilion 自己的數位出版視覺系統。

---

## 30. Phases

### Phase 1 — Foundation & 3D Shelf
- project foundation
- schemas
- Three.js scene
- reusable book model
- continuous shelf

### Phase 2 — Book Interaction
- shelf/detail transition
- inspection
- cover opening
- preview page turn

### Phase 3 — Reader
- Markdown loader
- HTML reader
- reader settings
- progress/bookmarks

### Phase 4 — 九重燼
- 8 books metadata
- Canon badge
- cover system
- full content import

### Phase 5 — Multi Novel Platform
- Project Lily
- collections/search
- SEO/A11y/performance/release

---

## 31. MVP Cut

優先完成：

1. Foundation
2. Schema
3. Three.js Scene
4. Book Model
5. Shelf
6. Shelf → Detail
7. Markdown Loader
8. HTML Reader
9. 九重燼 8 Books
10. 九重燼 Content Import

MVP 成品：

> **3D 書架 +《九重燼》+ 正式小說 Reader**

---

## 32. Definition of Done

平台完成的標準不是「可以看到《九重燼》」。

而是：

> **新增一部小說只需要新增 metadata、Markdown 與 Book Theme，不需要修改核心程式碼。**

Project Lily 能依此方式加入、擁有獨立視覺、獨立閱讀路徑與獨立進度後，Malilion Novel Library 的核心架構才算成立。

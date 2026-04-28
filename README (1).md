<div align="center">

# 🍽️ SplitEasy

### Pincéri számla-szétosztó és fizetési demó — egyetlen HTML fájlban

*Drag & drop számlamegosztás, külön-külön fizetési módok, élő asztalkezelés.*

</div>

---

## 📖 Mi ez?

A **SplitEasy** egy interaktív pincér-app demó, amely bemutatja, hogyan zajlik egy modern étteremben a rendelésfelvételtől az asztal lezárásáig az egész folyamat — különös figyelmet fordítva arra a fájdalompontra, amit minden vendéglátós ismer: **a számla szétosztására és a külön fizetésre**.

Az egész alkalmazás egyetlen `SplitEasy_app.html` fájlban él. Nincs build folyamat, nincs `node_modules`, nincs backend. Megnyitod a böngészőben és megy.

---

## ✨ Funkciók

### 🪑 Asztalnézet
- 8 asztal kezelése egyetlen képernyőn
- Két állapot: **Szabad** (új vendég fogadható) és **Foglalt** (rendelés nyitva)
- Foglalt asztalokon valós idejű információ: vendégek száma, eltelt idő, aktuális számlaösszeg
- A 3. és 5. asztal demó-szándékkal előre feltöltve van rendeléssel

### 📝 Rendelésfelvétel
- 4 kategória: **Előétel · Főétel · Ital · Desszert**
- 17 előre felvett étel és ital, emojikkal és magyar árakkal (Ft)
- Vendégszám-választó
- Élő kosár-összegzés a felvett tételekkel és teljes összeggel

### 🎯 Drag & Drop számla-szétosztás
- Tételek húzása ujjal vagy egérrel — **érintés és kurzor egyaránt működik**
- Maximum **4 párhuzamos számla**, mindegyik saját színkóddal
- Egy tétel áthúzható egyik számláról a másikra, vagy vissza a "szétosztatlan" zónába
- Élő összegzés minden számlán
- Üres számla nem mehet tovább a fizetéshez — beépített validáció

### 💳 Külön fizetés
- Számlánként választható fizetési mód: **bankkártya · készpénz · QR-kód**
- Fizetés rögzítése egyenként, vizuális visszaigazolással
- Az asztal lezárása csak akkor engedélyezett, ha minden számla ki van fizetve

### 📊 Lezárási összefoglaló
- Tranzakciós napló időbélyeggel, fizetési móddal, összeggel
- Statisztikák: befizetett összeg, számlák száma, fizetési idő, használt fizetési módok
- Az asztal automatikusan visszaáll **Szabad** állapotba

### 🎨 Design részletek
- Sötét, mély tengerészkék paletta (`#050c18` alapon) teal/lila/borostyán akcentekkel
- Egyedi tipográfia: **Fraunces** (display), **Plus Jakarta Sans** (UI), **JetBrains Mono** (számok)
- Szubtilis háttér-grádiensek és rácsmintázat
- Folyamatos animációk és átmenetek
- Toaszt-értesítések minden fontos akcióhoz
- Welcome modal az első indításnál, amely végigvezet a 3 lépésen

---

## 🚀 Gyors indítás

```bash
# 1. Klónozd a repót
git clone https://github.com/MatraBendi/spliteasy.git
cd spliteasy

# 2. Nyisd meg a böngészőben
open SplitEasy_app.html        # macOS
start SplitEasy_app.html       # Windows
xdg-open SplitEasy_app.html    # Linux
```

Vagy egyszerűen tedd egy webszerverre — pl. GitHub Pages, Netlify, Vercel — és megosztható a link.

> **Tipp:** lokális fejlesztéshez bármelyik egyszerű HTTP szerver megteszi:
> ```bash
> python3 -m http.server 8000
> # majd nyisd meg: http://localhost:8000/index.html
> ```

---

## 🛣️ Felhasználói folyamat

```
┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐
│ Asztalok │ ──▶│ Rendelés │ ──▶│Szétosztás│ ──▶│  Fizetés │ ──▶│ Lezárva  │
└──────────┘    └──────────┘    └──────────┘    └──────────┘    └──────────┘
   szabad         menü +          drag & drop      bankkártya     összegző +
   vagy           kosár           számlákra        készpénz       statisztika
   foglalt        + vendégszám    (max 4)          QR-kód         + log
```

A folyamatot a felső navigációban breadcrumb is jelzi, és bármikor visszaléphetsz.

### Gyors demó-útvonal
1. Koppints a **3. asztalra** (már van bent rendelés)
2. Húzd a tételeket a számlákra (kettéosztáshoz adj hozzá új számlát a `+ Új számla` gombbal)
3. Válassz minden számlához fizetési módot, rögzítsd a fizetést
4. Zárd le az asztalt → megjelenik a teljes összegző

---

## 🧱 Technológia

| Réteg          | Megoldás                                                            |
|----------------|---------------------------------------------------------------------|
| **HTML**       | Egyetlen fájl, szemantikus jelölés                                  |
| **CSS**        | Vanilla CSS, design tokenek CSS változókkal, reszponzív breakpointok |
| **JavaScript** | Vanilla JS (`'use strict'`), nincs külső függőség                   |
| **Drag & Drop**| Pointer Events API — egér + érintés ugyanazzal a kóddal             |
| **Tipográfia** | Google Fonts (Fraunces, Plus Jakarta Sans, JetBrains Mono)          |
| **Build**      | **Nincs**. Nincs Webpack, nincs Vite, nincs `package.json`.         |

### Fájlszerkezet
```
spliteasy/
├── index.html    ← minden logika, stílus, markup itt
└── README.md             ← ez a fájl
```

---

## 🏗️ Architektúra

Az app **single-page**, **state-driven** módon működik. Egyetlen `state` objektum tárolja az összes információt, és minden képernyőváltás vagy interakció után újra-renderelődik a teljes UI.

### State modell (egyszerűsítve)
```js
state = {
  screen: 'tables' | 'order' | 'split' | 'payment' | 'closed',
  currentTableId: number | null,
  tables: { 1: {...}, 2: {...}, ..., 8: {...} },
  orderCart: [{ menuId, qty }],
  orderGuests: number,
  bills: [[uid, uid], [uid], ...],   // számlák tömbjei
  billsPaid: [{ method, amount, time } | null, ...],
  closedSummary: { ... } | null
}
```

### Render ciklus
- `goTo(screen)` → frissíti a `state.screen`-t és meghívja a `render()`-t
- `render()` → kiválasztja a megfelelő `renderX()` függvényt és HTML-t generál
- Drag & drop képernyőn külön rárakja a pointer eseményeket az `attachDragAndDrop()`-pal

### Drag & drop logika
A `pointerdown` / `pointermove` / `pointerup` eseményeken alapul, `setPointerCapture`-rel — emiatt **egységesen működik egéren és touch-on**. A húzott elemnek külön "ghost" másolata készül, amely követi a kurzort, miközben a `document.elementFromPoint()` méri, hogy melyik dropzone fölött lebeg éppen.

---

## 🎨 Testreszabás

### Étlap módosítása
A menü a JavaScript blokk elején, a `MENU` konstansban van:
```js
const MENU = [
  { id: 'ma1', cat: 'Főétel', emoji: '🍔', name: 'Hamburger', price: 2890 },
  // ... új tételeket ide
];
```
Új kategória esetén add hozzá a `CATS` tömbhöz is.

### Színek
A `:root`-ban definiált CSS változókat írd át:
```css
:root {
  --teal-400: #2dd4bf;   /* fő akcentszín */
  --plum-500: #8b5cf6;   /* másodlagos */
  --amber-400: #fbbf24;  /* figyelemfelkeltő */
  /* ... */
}
```

### Asztalok száma
A `createInitialState()` függvényben a `for (let i = 1; i <= 8; i++)` ciklus értékét állítsd át.

### Fizetési módok
A `PAY_METHODS` tömbhöz adj új objektumot `id`, `label` és `ico` mezővel.

---

## 🌐 Reszponzivitás

- **Desktop**: széles grid layout, oldalsó "szétosztatlan" panel + jobbra a számlák
- **Tablet** (~720px alatt): a navigációs breadcrumb elrejtődik, a layout átszerveződik
- **Mobil**: minden vertikálisan stackel, a drag & drop ujjal is használható

---

## ♿ Hozzáférhetőség

- Szemantikus HTML elemek (`<button>`, `<header>`, `<main>`, `<nav>`)
- Magas kontrasztú szövegszínek a sötét háttéren
- A `viewport` meta beállítva a megfelelő mobil-rendereléshez
- Billentyűzet-fókusz a gombokon működik (a böngésző alapértelmezett stílusával)

> Még nem 100% AA-megfelelő — fejlesztési ötletek a [Roadmap](#️-roadmap) résznél.

---

## 🗺️ Roadmap

Lehetséges következő lépések (ha tovább szeretnéd vinni a projektet):

- [ ] Borravaló-kalkulátor minden számlához
- [ ] Számla PDF/email generálás (előnézet legalább)
- [ ] Több pincér támogatása (pincér-választó a navigációban)
- [ ] Asztalfoglalás-előzmények
- [ ] Statisztika dashboard (napi forgalom, top tételek)
- [ ] Sötét/világos téma váltó
- [ ] i18n — angol/német nyelv hozzáadása
- [ ] LocalStorage perzisztencia (jelenleg minden frissítéskor reseteli az állapotot)
- [ ] Billentyűzet-shortcutok (pl. `R` = új számla, `Esc` = vissza)
- [ ] WCAG AA audit + fix

---

## 📸 Képernyők

> *Tipp: készíts screenshotokat az alábbi képernyőkről és tedd be ide!*

| Képernyő        | Tartalom                                       |
|-----------------|-----------------------------------------------|
| Asztalnézet     | 8 asztal grid, szabad/foglalt állapotok        |
| Rendelés        | Kategória pillek, menü grid, kosár oldalt      |
| Szétosztás      | Drag & drop számlák, "szétosztatlan" panel     |
| Fizetés         | Számlakártyák fizetési módokkal                 |
| Lezárás         | Sikeres pipa + tranzakciós napló + statok      |

---



## 📄 Licenc

[MIT](LICENSE) — szabadon használható és módosítható.

---

<div align="center">

**SplitEasy** · Készült demó célokra · 2026


</div>

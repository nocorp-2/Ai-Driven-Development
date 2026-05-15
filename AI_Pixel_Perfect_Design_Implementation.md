# 🎨 Pixel-Perfect верстка: Полное руководство

Руководство по достижению пиксель-в-пиксель соответствия дизайну, используя правильный флоу, контракты и верификацию.

---

## 📋 Принцип: Планирование > Кодирование

Как и в AI Dev: **правильный контракт и спеки дают лучший результат чем быстрое кодирование**.

Не спешишь прямо в CSS — сначала зафиксируй контракт с дизайном.

---

## 🏗️ Фаза 0: Подготовка (Контракт + Спеки)

### 1.1 Импорт дизайна и анализ

```
Задача: Понять точные требования перед кодированием
```

**Необходимо:**
- [ ] Экспорт макета из Figma/Adobe XD/Sketch в высоком разрешении (3x или @3x)
- [ ] Определение брейкпоинтов (desktop, tablet, mobile)
- [ ] Экспорт всех компонентов отдельно
- [ ] Снятие размеров всех элементов (через inspector в дизайнерском ПО)

**Выходные артефакты:**
- `design-spec.md` — контракт с дизайном
- `measurements.json` — все размеры, отступы, шрифты, цвета
- Скриншоты для каждого брейкпоинта

### 1.2 Создание Design Contract

**Пример `design-spec.md`:**

```markdown
# Design Contract: Hero Section

## Scope
- Desktop (1920px), Tablet (768px), Mobile (375px)
- No animations (v1), interactions (v2)

## Measurements
- Container width: 1920px
- Padding: 60px top/bottom, 40px left/right
- Background: #F5F5F5
- Text: Roboto, 48px, Bold, #333333

## Colors (exact HEX)
- Primary background: #F5F5F5
- Text primary: #333333
- Text secondary: #666666
- Accent: #2E7D32

## Typography
- Heading: Roboto 48px / Line-height: 1.2 / Weight: 700
- Body: Roboto 16px / Line-height: 1.5 / Weight: 400

## Spacing Grid
- Base unit: 8px
- All margins/paddings: multiples of 8px (8, 16, 24, 32, 40, 48, 56, 60, 64...)

## Non-scope
- Animations
- Hover states (v1)
- Accessibility optimizations
- Print styles

## Verification criteria
- Screenshot comparison: 100% pixel match on desktop
- Color picker validation: HEX match on all colors
- Layout grid: All elements on 8px grid
```

### 1.3 DAG планирования верстки

```
Design Export & Analysis
    ↓
Spec Agreement (design-spec.md)
    ↓
Setup (HTML structure, CSS variables)
    ↓
Core Layout (flexbox/grid, container)
    ↓
Typography (fonts, sizes, line-heights)
    ↓
Spacing (margins, paddings on grid)
    ↓
Colors & Visual (backgrounds, borders, shadows)
    ↓
Components Styling (buttons, cards, etc)
    ↓
Pixel-Perfect Verification
    ↓
Cross-browser Testing
    ↓
Final QA
```

**Принцип:** Строго последовательно, без скачков. Каждый этап = верификация.

---

## 🎯 Фаза 1: Setup и переменные

### 2.1 CSS Custom Properties (Design Tokens)

Создай переменные ДО кодирования стилей:

```css
:root {
  /* Colors */
  --color-primary: #2E7D32;
  --color-bg-primary: #F5F5F5;
  --color-text-primary: #333333;
  --color-text-secondary: #666666;
  --color-border: #E0E0E0;
  --color-shadow: rgba(0, 0, 0, 0.1);
  
  /* Typography */
  --font-family: 'Roboto', sans-serif;
  --font-size-base: 16px;
  --font-size-h1: 48px;
  --font-size-h2: 36px;
  --font-size-body: 16px;
  --font-size-small: 14px;
  
  --font-weight-regular: 400;
  --font-weight-medium: 500;
  --font-weight-bold: 700;
  
  --line-height-tight: 1.2;
  --line-height-normal: 1.5;
  --line-height-loose: 1.8;
  
  /* Spacing (8px grid) */
  --spacing-xs: 8px;
  --spacing-sm: 16px;
  --spacing-md: 24px;
  --spacing-lg: 32px;
  --spacing-xl: 40px;
  --spacing-2xl: 48px;
  --spacing-3xl: 60px;
  
  /* Border radius */
  --radius-sm: 4px;
  --radius-md: 8px;
  --radius-lg: 12px;
  
  /* Shadows */
  --shadow-sm: 0 1px 3px var(--color-shadow);
  --shadow-md: 0 4px 8px var(--color-shadow);
  --shadow-lg: 0 12px 24px var(--color-shadow);
  
  /* Layout */
  --container-max-width: 1920px;
  --container-padding-desktop: 40px;
  --container-padding-tablet: 24px;
  --container-padding-mobile: 16px;
}

/* Ensure exact colors */
html {
  font-size: 16px; /* base unit */
}
```

### 2.2 HTML Структура (семантична и модульна)

```html
<body>
  <main class="hero-section">
    <div class="container">
      <div class="hero-content">
        <h1 class="hero-title">Heading</h1>
        <p class="hero-description">Description text</p>
      </div>
    </div>
  </main>
</body>
```

**Правило:** Структура = 1:1 слоям дизайна, без лишних обертко.

---

## 🎨 Фаза 2: Layout (Flexbox/Grid)

### 3.1 Container Setup

```css
.container {
  max-width: var(--container-max-width);
  margin: 0 auto;
  padding-left: var(--container-padding-desktop);
  padding-right: var(--container-padding-desktop);
  
  /* Important: ensure no hidden overflow */
  box-sizing: border-box;
  width: 100%;
}

@media (max-width: 1024px) {
  .container {
    padding-left: var(--container-padding-tablet);
    padding-right: var(--container-padding-tablet);
  }
}

@media (max-width: 480px) {
  .container {
    padding-left: var(--container-padding-mobile);
    padding-right: var(--container-padding-mobile);
  }
}
```

### 3.2 Section Layout

```css
.hero-section {
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  
  /* From spec: 60px top/bottom padding */
  padding-top: var(--spacing-3xl);
  padding-bottom: var(--spacing-3xl);
  
  background-color: var(--color-bg-primary);
  box-sizing: border-box;
}

.hero-content {
  width: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: var(--spacing-md); /* 24px между элементами */
  text-align: center;
}
```

**Проверка:** Отступы должны быть **ровно как в spec**.

---

## 🔤 Фаза 3: Typography (Точные размеры)

### 4.1 Шрифты и размеры

```css
/* From spec: Roboto 48px Bold */
.hero-title {
  font-family: var(--font-family);
  font-size: var(--font-size-h1); /* 48px */
  font-weight: var(--font-weight-bold); /* 700 */
  line-height: var(--line-height-tight); /* 1.2 */
  margin: 0;
  color: var(--color-text-primary);
  
  /* Letter spacing: если есть в дизайне */
  letter-spacing: -0.5px; /* пример */
}

.hero-description {
  font-family: var(--font-family);
  font-size: var(--font-size-body); /* 16px */
  font-weight: var(--font-weight-regular); /* 400 */
  line-height: var(--line-height-normal); /* 1.5 */
  color: var(--color-text-secondary);
  margin: 0;
  max-width: 600px; /* если ширина текста ограничена */
}
```

**Критическое:** Font-size и line-height должны быть **ровно как в макете**.

### 4.2 Проверка Typography

```bash
# Используй DevTools Inspector:
# 1. Выбери элемент (F12 → Elements)
# 2. Посмотри Computed styles
# 3. Сравни с design-spec.md:
#    - font-size: 48px ✓
#    - font-weight: 700 ✓
#    - line-height: 1.2 ✓
#    - color: #333333 ✓
```

---

## 🌈 Фаза 4: Colors & Visual (Точные цвета)

### 5.1 Color Verification

```css
.hero-section {
  /* From spec: #F5F5F5 */
  background-color: var(--color-bg-primary); /* #F5F5F5 */
}

/* ВАЖНО: Не использовать расчеты или полупрозрачность вместо точных цветов! */
/* ПЛОХО: background-color: rgba(245, 245, 245, 0.95); */
/* ХОРОШО: background-color: #F5F5F5; */
```

**Метод проверки (Color Picker):**

```javascript
// В консоли браузера:
const elem = document.querySelector('.hero-section');
const color = window.getComputedStyle(elem).backgroundColor;
console.log(color); // rgb(245, 245, 245) или rgba(...)

// Конвертируй RGB в HEX:
// rgb(245, 245, 245) = #F5F5F5 ✓
```

### 5.2 Borders и Shadows

```css
.card {
  border: 1px solid var(--color-border); /* #E0E0E0 */
  border-radius: var(--radius-md); /* 8px */
  
  /* Shadow from spec */
  box-shadow: var(--shadow-md); /* 0 4px 8px rgba(0,0,0,0.1) */
}

/* ВАЖНО: тень НЕ должна быть сложнее чем в дизайне */
/* Если в дизайне simple shadow 4px 8px, не добавляй 3 слоя теней */
```

---

## 📐 Фаза 5: Spacing & Grid (8px подход)

### 6.1 Margin & Padding на 8px сетке

```css
/* Все отступы = multiples of 8px */
.hero-title {
  margin-bottom: var(--spacing-md); /* 24px = 8px × 3 ✓ */
}

.hero-description {
  margin-bottom: var(--spacing-lg); /* 32px = 8px × 4 ✓ */
}

/* ПЛОХО:
.hero-title {
  margin-bottom: 25px; ✗ (не кратно 8)
}
*/
```

### 6.2 Проверка Grid Alignment

```javascript
// Быстрая проверка в DevTools Console:
const elem = document.querySelector('.hero-title');
const style = window.getComputedStyle(elem);
const margin = parseInt(style.marginBottom);
console.log(margin % 8 === 0 ? '✓ On grid' : '✗ Off grid');
```

---

## ✅ Фаза 6: Pixel-Perfect Verification

### 7.1 Screenshot Comparison

**Инструменты:**
- **Figma:** File → Export → 3x PNG
- **Browser DevTools:** F12 → More tools → Screenshots
- **Diff Tools:** pixelmatch, looks-same, percyio

**Процесс:**

```bash
# 1. Экспорт из Figma в высокого качества
# design-screenshot.png (3x resolution)

# 2. Скриншот своей верстки
# my-implementation.png (same resolution)

# 3. Overlay сравнение в редакторе:
# - Figma overlays
# - VS Code extension: "Figma for VS Code"
# - Online tool: https://www.diffchecker.com/image-compare
```

### 7.2 Element-by-element Inspection

```javascript
// Chrome DevTools — наложение сеток

// 1. Откройте DevTools (F12)
// 2. Settings → Experiments → включите "Grid overlay"
// 3. Выбери элемент → покажи grid overlay
// 4. Сравни с дизайном

// Или используй плагин:
// https://chrome.google.com/webstore/detail/css-grid-overlay
```

### 7.3 Checklist верификации

```markdown
## Desktop (1920px)

- [ ] Container width: 1920px
- [ ] Container padding: 40px (left/right)
- [ ] Hero padding: 60px (top/bottom)
- [ ] Title font-size: 48px
- [ ] Title color: #333333
- [ ] Description color: #666666
- [ ] Background: #F5F5F5
- [ ] All elements on 8px grid
- [ ] No overflow hidden
- [ ] Button states match spec
- [ ] Spacing between elements exact

## Tablet (768px)

- [ ] Responsive: padding/margins adjusted
- [ ] Typography: readable, no overflow
- [ ] Layout: stacked correctly
- [ ] Touch targets: min 44px

## Mobile (375px)

- [ ] All text readable
- [ ] Touch-friendly spacing
- [ ] No horizontal scroll
- [ ] Images responsive

## Cross-browser

- [ ] Chrome
- [ ] Firefox
- [ ] Safari
- [ ] Edge
```

---

## 🔄 Фаза 7: Iteration & Lessons Learned

### 8.1 Issue Tracking

Если нашел отличие от дизайна:

```markdown
## Issue: Button padding incorrect

**Current:** padding: 12px 16px
**Expected (from spec):** padding: 8px 16px
**Difference:** 4px (1px в пиксельном масштабе)

**Fix:**
```css
.button {
  padding: 8px 16px; /* was 12px */
}
```

**Lesson learned:** 
- Всегда проверяй button states в spec перед кодированием
- Добавить в design-spec.md все варианты (default, hover, active)
```

### 8.2 Memory Bank Update

```markdown
# Lessons: Pixel-Perfect Workflow

## ✓ Что сработало:
- CSS Custom Properties для всех значений
- DAG подход (layout → typography → colors → spacing)
- 8px grid для всех отступов
- design-spec.md как контракт

## ✗ Что не сработало:
- Assumption о цветах без точного экспорта HEX
- Skip typography spec → потом переделывал
- Trying всех браузеров в конце → нашел проблемы в 50%

## → Новый процесс:
- [ ] export-spec.md (ДО кодирования)
- [ ] define CSS variables
- [ ] verify on each commit
- [ ] test cross-browser на каждом этапе
```

---

## 🚀 Production Checklist

Перед деплоем:

```markdown
## Code Quality
- [ ] CSS variables defined for all values
- [ ] No hardcoded px/colors in component CSS
- [ ] DRY — no duplicate rules
- [ ] Minified CSS in production

## Design Compliance
- [ ] Screenshot match 100% on primary breakpoint
- [ ] All colors verified (HEX match)
- [ ] Typography exact (size, weight, line-height)
- [ ] Spacing on 8px grid
- [ ] No visual regressions vs previous version

## Performance
- [ ] Font loading optimized (preload, WOFF2)
- [ ] Images optimized (WebP, responsive sizes)
- [ ] CSS critical path identified
- [ ] Paint performance acceptable

## Accessibility
- [ ] Color contrast: WCAG AA minimum
- [ ] Focus states visible
- [ ] Semantic HTML
- [ ] Alt text for images

## Cross-browser
- [ ] Desktop: Chrome, Firefox, Safari, Edge
- [ ] Mobile: iOS Safari, Chrome Android
- [ ] No layout shift (CLS < 0.1)

## Responsive
- [ ] Desktop: 1920px
- [ ] Tablet: 768px
- [ ] Mobile: 375px
- [ ] Tested on real devices
```

---

## 📊 Template: design-spec.md

```markdown
# Design Contract: [Component Name]

## Scope
- Breakpoints: Desktop (1920px), Tablet (768px), Mobile (375px)
- Animation: None (v1) / Defined (v2)
- Interaction: Hover, Click, Focus states

## Container
- Width: 1920px
- Max-width: 1920px
- Padding: 40px (left/right), 60px (top/bottom)
- Background: #F5F5F5

## Typography
| Element | Size | Weight | Line-height | Color |
|---------|------|--------|------------|-------|
| H1 | 48px | 700 | 1.2 | #333333 |
| Body | 16px | 400 | 1.5 | #666666 |

## Colors (exact HEX)
- Primary BG: #F5F5F5
- Text Primary: #333333
- Text Secondary: #666666
- Accent: #2E7D32
- Border: #E0E0E0
- Shadow: rgba(0,0,0,0.1)

## Spacing (8px grid)
- All margins/padding are multiples of 8px
- Base gap between elements: 24px

## Borders & Shadows
- Border radius: 8px
- Box shadow: 0 4px 8px rgba(0,0,0,0.1)

## Responsive adjustments
| Breakpoint | Changes |
|------------|---------|
| Tablet (768px) | Container padding: 24px; Font-size: -2px |
| Mobile (375px) | Container padding: 16px; Font-size: -4px |

## Verification criteria
- [ ] Screenshot pixel-match
- [ ] All HEX colors exact
- [ ] Typography on-spec
- [ ] Layout on 8px grid
- [ ] No overflow hidden
```

---

## 🎓 Итоговый DAG Workflow

```
1. Design Export & Spec Creation
         ↓
2. CSS Variables Definition
         ↓
3. HTML Structure (semantic, modular)
         ↓
4. Layout (flexbox/grid, containers)
         ↓
5. Typography (sizes, weights, line-heights)
         ↓
6. Colors (exact HEX, no gradients unless spec)
         ↓
7. Spacing (8px grid, verify all margins/padding)
         ↓
8. Visual Details (borders, shadows, radius)
         ↓
9. Responsive (tablet, mobile breakpoints)
         ↓
10. Pixel-Perfect Verification (screenshot compare)
         ↓
11. Cross-browser Testing
         ↓
12. Performance & Accessibility Check
         ↓
13. Lessons Learned → Memory Bank Update
```

---

## 💡 Ключевые принципы

1. **Контракт перед кодом** — design-spec.md = истина
2. **CSS Variables для всех** — no hardcoding
3. **Строгий DAG** — каждый этап = верификация
4. **8px Grid everywhere** — spacing predictable
5. **Screenshot comparison** — не на глаз
6. **Lessons learned** — каждый раз апдейт процесса
7. **Cross-browser рано** — не в конце
8. **Performance matters** — оптимизация параллельно

---

**Помни:** Pixel-Perfect верстка = дисциплина + процесс, не одноразовый спринт. 🎯

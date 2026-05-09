# Design Guidelines — Vercel/Stripe-light v1

Единый визуальный язык для всех корпоративных дашбордов и админок Zoloto585.
Документ оформлен как ТЗ для дизайнера/разработчика: можно отдавать целиком в новый проект и получать узнаваемо-«наш» интерфейс без отдельной пере-инсценировки.

---

## 1. Эстетическая платформа

### 1.1 Вижн

«Vercel/Stripe-light» с тёплым нейтралом и amber-акцентом.
Ни лоск маркетингового лендинга, ни «энтерпрайз 2008» — ровный продакшн-инструмент: hairline-бордеры, плотные таблицы, спокойные тени, типографика без декоративности, цвет включается только семантически.

### 1.2 Принципы (порядок не случаен)

1. **Density first.** Списки и таблицы — основная рабочая поверхность. Высота строки 32–36 px, inline-actions, без card-обёртки над таблицей. «Воздух» — не цель, цель — обзорность.
2. **Severity at a glance.** Цвет = семантика. Зелёный/красный нельзя «для красоты». `dead` — единственное место для красного. `at_risk` — amber-600. Норма — нейтральный ink.
3. **Hairlines, not boxes.** Разделители — `1px solid var(--hairline)`. Большие тени — только для всплывающих слоёв (модал, popover, dropdown).
4. **Numbers are data.** Все числа в таблицах и KPI — `font-variant-numeric: tabular-nums`. Никакого прыгания колонок на смене значения.
5. **Public-facing — другой регистр.** Когда страницу видит сотрудник, не админ (формы подтверждения, magic-link, ошибки): крупные шрифты, кнопки, минимум хрома, ноль навигации. Должно заполняться за 30 секунд с телефона.
6. **Tokens or nothing.** Никаких inline `#hex`, никаких `padding: 14px` с цифры. Только `var(--token)`. Если нужного токена нет — добавить токен, потом использовать.
7. **Reduced motion respected.** Любая анимация выключается через `@media (prefers-reduced-motion: reduce)`. По умолчанию — никаких bounce/parallax.

### 1.3 Anti-patterns

| Не надо | Почему |
|---|---|
| Tinting всей строки таблицы по статусу | Шумит, пользователь сканирует не значение, а полосу |
| Decorative gradients на surface | Конфликтует с amber-акцентом и data-color |
| Round-corners > 12 px на кнопках/инпутах | Визуальный регистр «лендинг», а не «инструмент» |
| Несколько уровней теней одновременно | Поверхность теряет глубину; одна тень — один слой |
| Разный отступ в соседних карточках | Сетка 4 px нарушается — глаз цепляется |
| Свой шрифт «потому что красиво» | Только Geist + JetBrains Mono. Других не вводим |

---

## 2. Технологический стек (обязательный)

| Слой | Решение | Зачем именно так |
|---|---|---|
| Framework | **Nuxt 3** (Vue 3 + Nitro) | SSR-first, file-based routing, единый рантайм |
| UI Kit | **PrimeVue** или **Nuxt UI v3** | Готовые таблицы/формы/модалки/тосты с overridable-палитрой (далее будут приводиться примеры на Nuxt UI) |
| Стилизация компонентов | **CSS Modules** (`<style module>`) | Изоляция без runtime, без прокинутых классов в DOM |
| Иконки | **`@nuxt/icon` + Lucide** (`i-lucide-*`) | Единый набор, нет смеси Heroicons/Phosphor |
| Шрифты | **`@nuxt/fonts`**, self-hosted | CSP-safe, никаких CDN, семейства фиксированы |
| Графики | **`vue-chartjs`** (минимум) | Только sparkline/agg-графики; кастомные визуализации — SVG |
| Анимация | CSS-transitions через токены | Без motion-библиотек |
| Состояние клиента | **Pinia** | Стандарт экосистемы Vue |
| Серверный кэш | **TanStack Query** (`@tanstack/vue-query`) | Кэш+инвалидация+optimistic updates без велосипедов |

**Запрещено:** css-in-js, Bootstrap, Element Plus, Vuetify, Material UI, кастомные иконпаки кроме Lucide.

---

## 3. Палитра и токены

Все токены живут в `app/assets/css/tokens.css` под `:root`. В компонентах — только `var(--token)`. Полный набор — ниже.

### 3.1 Warm neutral ramp

Тёплая нейтраль (warm gray / stone) — основа. **Никогда** не используем чистый `#000`/`#fff` для текста — только `--ink-1`/`--surface`.

| Token | Hex | Назначение |
|---|---|---|
| `--neutral-0` | `#FFFFFF` | Surface (карточки, modals) |
| `--neutral-50` | `#FAFAF7` | Page background (warm off-white) |
| `--neutral-100` | `#F5F4EE` | Surface-2 (hover-row, footer карточки) |
| `--neutral-150` | `#EFEDE5` | Surface-3 (active state) |
| `--neutral-200` | `#E5E2DA` | Hairline (1px бордеры) |
| `--neutral-300` | `#D6D2C7` | Hairline-strong (формы, кнопки secondary) |
| `--neutral-400` | `#A8A49A` | Ink-4 (вспомогательные иконки, placeholder) |
| `--neutral-500` | `#7A7672` | Ink-3 (мета, subtitle, labels) |
| `--neutral-600` | `#555148` | Ink-плотный (переходные оттенки) |
| `--neutral-700` | `#3A3733` | Ink-2 (вторичный текст, цифры) |
| `--neutral-800` | `#1F1D1A` | Тёмный заголовок |
| `--neutral-900` | `#0E0D0B` | Ink-1 (основной текст, заголовки) |

### 3.2 Amber accent ramp

Один-единственный акцентный цвет проекта. Используем для primary-кнопок, focus-ring, активных вкладок, heatmap-buckets и `at_risk`-health.

| Token | Hex |
|---|---|
| `--amber-50` | `#FFF8EC` |
| `--amber-100` | `#FFEAC2` |
| `--amber-200` | `#FFD994` |
| `--amber-300` | `#F5C56A` |
| `--amber-400` | `#E2A638` |
| `--amber-500` | `#D08A1B` (primary) |
| `--amber-600` | `#A26F14` (hover/at-risk) |
| `--amber-700` | `#7E5510` (active/pressed) |
| `--amber-800` | `#5C3D0B` (selection-fg) |

### 3.3 Семантические статусы

| Token | Hex | Использование |
|---|---|---|
| `--success-50` / `--success-500` / `--success-700` | `#E8F4EE` / `#2E7D5C` / `#205A41` | Положительная дельта, success-toast. **Не** healthy-индикатор по умолчанию |
| `--danger-50` / `--danger-500` / `--danger-700` | `#FCEAEA` / `#B11C1C` / `#851515` | Только под dead/destructive |
| `--warning-50/500/700` | алиасы amber | Предупреждения = amber |
| `--info-50` / `--info-500` / `--info-700` | `#ECF1F8` / `#2E5BB1` / `#1E3F7E` | Нейтральные info-баджи |

### 3.4 Поверхности и ink (semantic)

```css
--bg:        var(--neutral-50);   /* фон страницы */
--surface:   var(--neutral-0);    /* карточки/модалки */
--surface-2: var(--neutral-100);  /* hover-state, table-header bg */
--surface-3: var(--neutral-150);  /* active-state */
--hairline:  var(--neutral-200);
--hairline-strong: var(--neutral-300);

--ink-1: var(--neutral-900);  /* заголовки, primary-текст */
--ink-2: var(--neutral-700);  /* вторичный текст */
--ink-3: var(--neutral-500);  /* мета, subtitle, labels */
--ink-4: var(--neutral-400);  /* placeholder, тонкие иконки */
```

### 3.5 Focus / selection

```css
--focus-ring: 0 0 0 3px rgb(208 138 27 / 0.28);
--selection-bg: var(--amber-100);
--selection-fg: var(--amber-800);
```

`:focus-visible` → `box-shadow: var(--focus-ring)`. Никогда не отключаем outline без замены на ring.

### 3.6 Health aliases

Семантические алиасы — чтобы health/severity не привязывался к конкретной палитре в компонентах:

```css
--health-healthy:    var(--success-700);
--health-at-risk:    var(--amber-600);
--health-dead:       var(--danger-500);
--health-healthy-bg: var(--success-50);
--health-at-risk-bg: var(--amber-50);
--health-dead-bg:    var(--danger-50);
```

### 3.7 Heatmap (если есть тепловая карта активности)

```css
--heatmap-0: var(--neutral-100);  /* пустая ячейка */
--heatmap-1: var(--amber-50);
--heatmap-2: var(--amber-100);
--heatmap-3: var(--amber-300);
--heatmap-4: var(--amber-500);
```

Bucket вычисляется чистой функцией от значения (часов/событий). Bucket-границы задаются продуктово, не дизайнерски.

### 3.8 Dark theme (опционально, M5+)

Активируется через `<html data-theme="dark">`. Переопределяем семантические токены, ramp оставляем как есть:

| Token | Dark value |
|---|---|
| `--bg` | `#0F0F0E` |
| `--surface` | `#16161A` |
| `--ink-1` | `#F4F1EA` |
| `--ink-2` | `#B5B0A4` |
| `--ink-3` | `#6F6B62` |
| `--hairline` | `#26241F` |

---

## 4. Типографика

### 4.1 Семейства

```css
--font-display: 'Geist', system-ui, -apple-system, sans-serif;
--font-ui:      'Geist', system-ui, -apple-system, sans-serif;
--font-mono:    'JetBrains Mono', ui-monospace, 'SF Mono', Menlo, monospace;
```

- **Geist** — единственный sans для UI и заголовков. Self-hosted через `@nuxt/fonts`.
- **JetBrains Mono** — для ID, email, timestamps, токенов, monospace-значений в таблицах и tooltip.
- *Опциональный display-serif* — если в проекте есть hero-карточка с именем/брендом, можно подключить **Fraunces** (variable serif) и присвоить `--font-display: 'Fraunces', Georgia, serif`. По умолчанию display = Geist.

**Никаких** Roboto, Inter, Open Sans, Montserrat, кастомных шрифтов из брендбука.

### 4.2 Шкала размеров (14-px base)

```css
--text-xs:   12px;
--text-sm:   13px;
--text-base: 14px;   /* body */
--text-md:   15px;
--text-lg:   18px;
--text-xl:   22px;
--text-2xl:  28px;   /* page H1 */
--text-3xl:  36px;
--text-4xl:  48px;   /* hero */
```

### 4.3 Line-height

```css
--leading-tight:   1.1;     /* заголовки, KPI-числа */
--leading-snug:    1.25;    /* subtitle, описания */
--leading-normal:  1.5;     /* body */
--leading-relaxed: 1.625;   /* длинные параграфы */
```

### 4.4 Weights и tracking

```css
--weight-regular:  400;
--weight-medium:   500;   /* кнопки, лейблы, навигация */
--weight-semibold: 600;   /* заголовки, KPI, акцентные числа */
--weight-bold:     700;   /* по запросу, редко */

--tracking-tight:  -0.014em;  /* заголовки, кнопки */
--tracking-normal: 0;
--tracking-wide:   0.06em;    /* uppercase-eyebrow, table-headers */
```

### 4.5 Базовый body-стиль

```css
body {
  font-family: var(--font-ui);
  font-size: var(--text-base);
  line-height: var(--leading-normal);
  font-feature-settings: 'tnum' 1, 'cv11' 1, 'ss01' 1;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-rendering: optimizeLegibility;
}
```

### 4.6 Утилиты

```css
.font-display { font-family: var(--font-display); }
.font-ui      { font-family: var(--font-ui); }
.font-mono    { font-family: var(--font-mono); font-feature-settings: 'tnum' 1; }
.tnum         { font-variant-numeric: tabular-nums; }  /* обязательно для таблиц/KPI */
.tracking-tight { letter-spacing: var(--tracking-tight); }
.tracking-wide  { letter-spacing: var(--tracking-wide); }
```

### 4.7 Иерархия по ролям

| Роль | Размер | Вес | Цвет | Tracking |
|---|---|---|---|---|
| Page H1 | `--text-2xl` | semibold | `--ink-1` | tight |
| Section H2 | `--text-lg` | semibold | `--ink-1` | tight |
| Subtitle / описание | `--text-md` | regular | `--ink-3` | normal |
| Eyebrow (over-title) | `--text-xs` | medium, UPPERCASE | `--ink-3` | wide |
| Body | `--text-base` | regular | `--ink-1` | normal |
| Caption / meta | `--text-sm` | regular | `--ink-3` | normal |
| Table header | `--text-xs` | medium, UPPERCASE | `--ink-3` | wide |
| Table cell | `--text-sm` | regular | `--ink-1` | normal |
| KPI value | `--text-3xl` | semibold | `--ink-1` | tight, tnum |
| KPI label | `--text-xs` | medium, UPPERCASE | `--ink-3` | wide |
| Mono (ID/email/timestamp) | `--text-xs` | regular | `--ink-2` | — |

---

## 5. Сетка, отступы, скругления, тени

### 5.1 Spacing scale (4-px base)

```css
--space-0:0; --space-1:4px; --space-2:8px; --space-3:12px;
--space-4:16px; --space-5:20px; --space-6:24px; --space-7:28px;
--space-8:32px; --space-10:40px; --space-12:48px; --space-14:56px;
--space-16:64px; --space-20:80px; --space-24:96px;
```

Любая «магия» вне шкалы — баг. Padding/gap/margin берутся только отсюда.

### 5.2 Layout-токены

```css
--row-h-comfortable: 36px;   /* таблица по умолчанию */
--row-h-compact:     32px;   /* плотный режим */
--container-max:     1280px;
--container-pad-x:   var(--space-8);
--container-pad-y:   var(--space-8);
--header-h:          56px;
```

### 5.3 Radii

```css
--radius-xs:   4px;   /* индикаторы */
--radius-sm:   6px;   /* кнопки, инпуты, small-карточки */
--radius-md:   8px;   /* карточки, KPI */
--radius-lg:   12px;  /* модалы */
--radius-xl:   16px;  /* hero-блоки */
--radius-pill: 999px; /* badges, dot-индикаторы, аватары */
```

### 5.4 Тени (мягкие, многослойные)

```css
--shadow-xs: 0 1px 1px rgb(15 13 11 / 0.04);
--shadow-sm: 0 1px 2px rgb(15 13 11 / 0.05), 0 1px 3px rgb(15 13 11 / 0.06);
--shadow-md: 0 4px 8px -2px rgb(15 13 11 / 0.06), 0 2px 4px -1px rgb(15 13 11 / 0.04);
--shadow-lg: 0 12px 24px -8px rgb(15 13 11 / 0.10), 0 4px 8px -2px rgb(15 13 11 / 0.06);
--shadow-xl: 0 24px 48px -12px rgb(15 13 11 / 0.18);
--shadow-inset: inset 0 1px 0 rgb(255 255 255 / 0.6), inset 0 -1px 0 rgb(15 13 11 / 0.04);
```

Правило применения:
- `xs` — кнопки secondary, инпуты, KPI-карточки.
- `sm` — обычные карточки.
- `md` — hover на interactive-карточке.
- `lg` — popover, dropdown.
- `xl` — модалы.

### 5.5 Motion

```css
--ease-out:    cubic-bezier(0.2, 0.8, 0.2, 1);
--ease-in-out: cubic-bezier(0.4, 0, 0.2, 1);
--ease-spring: cubic-bezier(0.34, 1.56, 0.64, 1);
--duration-instant: 60ms;
--duration-fast:    120ms;   /* hover, focus */
--duration-base:    180ms;   /* state transition */
--duration-slow:    280ms;   /* modal in/out, reveal */
--duration-reveal:  400ms;   /* sweep heatmap, скан данных */
```

`prefers-reduced-motion` глушит всё:

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation: none !important;
    transition: none !important;
  }
}
```

---

## 6. Layout-shell (default)

Дашборд = `header(sticky) + main(container)`.

- **Header**: высота 56 px, `position: sticky; top: 0; z-index: 20`. Фон — полупрозрачный `color-mix(in oklab, var(--bg) 92%, transparent)` + `backdrop-filter: saturate(140%) blur(8px)`. Низ — hairline.
- **Brand-mark** слева: 24×24 квадрат, `linear-gradient(135deg, --amber-100, --amber-300)`, 1 px `--amber-200`, `--radius-sm`, glyph 14×14. Текст: имя проекта (semibold, ink-1) + suffix (regular, ink-3).
- **Nav**: горизонтальная, item = 32 px высоты, `--text-sm`, medium, ink-3 → ink-1 на hover, активный — `surface-2` фон + amber-600 у иконки.
- **Account-блок** справа: имя (ink-2) + кнопка «Выйти» с `i-lucide-log-out`.
- **Main → container**: `max-width: 1280px; margin: 0 auto; padding: 32px 32px 64px`.

Public-layout (`/confirm/:token` и подобные) — без header'а, центральная карточка `max-width: ~480px`, акцент на тип и кнопку.

---

## 7. Компоненты

Все базовые компоненты живут в `app/components/ui/` под префиксом `Ui*`:
`UiAppButton`, `UiAppCard`, `UiAppBadge`, `UiDataTable`, `UiKpiCard`, `UiPageHeader`, `UiEmptyState`, `UiHealthPill`, `UiSegmentedControl`, `UiSkeleton`, `UiStat`. Кастом UI кита — поверх Nuxt UI, не вместо: модалки, toast, popover берём из `@nuxt/ui`.

### 7.1 UiAppButton

Варианты × размеры × иконки.

| Variant | Поведение |
|---|---|
| `primary` | amber-500 fg ink-1; hover → amber-400; active → amber-600 (fg #fff); inset-highlight + `--shadow-xs` |
| `secondary` (default) | surface + `--hairline-strong`; hover → surface-2 + neutral-400; active → surface-3 |
| `ghost` | прозрачный + ink-2; hover → surface-2 + ink-1 |
| `danger` | danger-500 fg #fff; hover → danger-700; active → danger-700 + 0.5px translateY |

| Size | Высота | Padding-x | Font | Radius |
|---|---|---|---|---|
| `sm` | 28 | 10 | `--text-sm` | `--radius-sm` |
| `md` (default) | 32 | 12 | `--text-base` | `--radius-sm` |
| `lg` | 40 | 16 | `--text-md` | `--radius-md` |

Свойства: `loading` (показать `i-lucide-loader-circle` со спином и блокировать), `block` (100% width), `leadingIcon`, `trailingIcon`, `disabled` (opacity .5 + cursor-not-allowed). Focus-visible — `var(--focus-ring)`.

**Кейс использования:** primary — единственное «главное действие» на странице (Сохранить, Отправить, Создать). Всё остальное — secondary. Destructive — `danger`. Inline-actions в строке таблицы — `ghost size=sm`.

### 7.2 UiAppCard

```ts
interface Props {
  padded?: boolean      // default true; false для кейса с UiDataTable
  interactive?: boolean // hover-эффект
  elevation?: 'flat' | 'sm' | 'md' // default 'sm'
}
```

- Background: `--surface`, border `1px solid --hairline`, radius `--radius-md`, `overflow: hidden`.
- Header (`#header`): padding 16/20, низ-hairline, `min-height: 52px`, flex-space-between с gap.
- Body: padding 20 (если `padded`).
- Footer (`#footer`): padding 12/20, верх-hairline, фон `--surface-2`, justify-end, gap 8.
- `interactive` → translateY(-1px) + `--shadow-md` + `--hairline-strong` на hover.

**Не делаем** карточку-обёртку поверх таблицы. Таблицы внутри `UiAppCard padded={false}` — норма.

### 7.3 UiAppBadge

Pill-форма, 22/26 px высоты.

| Tone | bg / fg / border |
|---|---|
| `neutral` (default) | `neutral-100` / `ink-2` / `neutral-200` |
| `amber` | `amber-50` / `amber-700` / `amber-200` |
| `success` | `success-50` / `success-700` / `color-mix(success-500 30%)` |
| `danger` | `danger-50` / `danger-700` / `color-mix(danger-500 30%)` |
| `info` | `info-50` / `info-700` / `color-mix(info-500 30%)` |

Опции: `dot` (6×6 круг `currentColor` слева), `size: 'sm' | 'md'`. `font-feature-settings: 'tnum' 1`.

### 7.4 UiHealthPill (паттерн для статусов «жив / полужив / мёртв»)

`healthy / at_risk / dead / unknown`. Цвета берутся из `--health-*`-aliases. Dot — 7×7 с `box-shadow: 0 0 0 2px color-mix(currentColor 22%, transparent)` (мягкий «ореол»).

В таблице, где статус — самая важная колонка, можно дублировать его 6 px вертикальной полосой слева у ячейки (severity at a glance), но **не** заливать всю строку.

### 7.5 UiPageHeader

```ts
interface Props {
  title: string
  subtitle?: string
  eyebrow?: string  // UPPERCASE label над title
}
// slot: actions (кнопки справа)
```

- Layout: flex space-between, `align-items: flex-start`, gap 24, низ-hairline, `padding-bottom: 24`, `margin-bottom: 32`.
- Eyebrow: text-xs UPPERCASE, ink-3, tracking-wide.
- Title: text-2xl, semibold, ink-1, tracking-tight, leading-tight.
- Subtitle: text-md, ink-3, leading-snug, max-width 64ch.
- Actions: flex gap 8, flex-shrink 0.

**Правило:** на каждой странице есть ровно один `UiPageHeader`. Eyebrow — категория («Кампания», «Пользователь», «Провайдер»), title — конкретика.

### 7.6 UiDataTable

Generic-таблица с slot-cells, sticky-thead, density.

```ts
interface Column {
  key: string
  label: string
  width?: string        // CSS width
  align?: 'start' | 'end' | 'center'
  mono?: boolean        // моноширинная ячейка
}
interface Props<T> {
  columns: Column[]
  rows: T[]
  rowKey?: keyof T
  density?: 'compact' | 'comfortable'  // default comfortable
  empty?: string                       // empty-state text
}
// slots: cell-{key} для кастомного рендера
```

Стили:
- `border-collapse: separate; border-spacing: 0; font-size: --text-sm`.
- `thead`: sticky top, bg `--surface-2`, header-cell `--text-xs` UPPERCASE tracking-wide ink-3, `border-bottom: 1px solid --hairline`, padding `12 16`.
- `tbody tr`: hover → `--surface-2` (transition `--duration-fast`).
- `td`: border-bottom hairline, height `--row-h-comfortable | --row-h-compact`, padding `0 16`, vertical-middle, `white-space: nowrap`.
- Mono-ячейка: `font-mono`, text-xs, ink-2.
- Пустое состояние: `padding: 40 16; text-align: center; color: ink-3`.

**Inline-actions** в правой колонке — `align: 'end'`, кнопки `ghost size=sm`, минимум места.

### 7.7 UiKpiCard

Карточка-метрика для дашборда.

```ts
interface Props {
  label: string
  value: string | number
  suffix?: string             // «₽», «ч», «%»
  delta?: string              // «+12»
  deltaTone?: 'up' | 'down' | 'flat'
  icon?: string               // i-lucide-*
  accent?: boolean            // верхняя amber-полоска (главная метрика страницы)
}
```

- Min-height 132 px, padding 20, radius `--radius-md`, `--shadow-xs`.
- Label: text-xs UPPERCASE tracking-wide ink-3 medium.
- Value: text-3xl semibold ink-1 tracking-tight tnum.
- Suffix: text-md ink-3 medium, baseline с value.
- Delta: text-sm medium с иконкой trending-up/-down/minus, цвет — success-500 / danger-500 / ink-3.
- `accent` — псевдоэлемент `::before height: 2px` с amber-градиентом сверху.

**Правило:** на дашборде ≤ 4 KPI в ряд, ≤ 1 c `accent`. Не «всё одинаково важно» — это не KPI.

### 7.8 UiEmptyState

Универсальная заглушка «нет данных / пусто / ошибка».

- Иконка 22×22 в круге 56×56 (`surface-2` + hairline + ink-3) — для compact 18×18 в 40×40.
- Title: text-md semibold ink-1, tracking-tight.
- Description: text-sm ink-3, max-width 48ch, leading-snug.
- Actions slot снизу.
- Padding: `48 24` (default) / `32 16` (compact).

Дефолтная иконка — `i-lucide-inbox`. Семантические варианты подбираем сами: `mail-x`, `users-x`, `database-x`, `wifi-off`, `alert-triangle`.

### 7.9 Формы

Высота инпутов = высоте кнопки md = 32 px. Делается одним override-блоком в `main.css`:

```css
[data-component="input"] input,
[data-component="textarea"] textarea {
  height: 32px;
  padding: 0 12px;
  font-size: var(--text-base);
  font-family: var(--font-ui);
  color: var(--ink-1);
  background: var(--surface);
  border: 1px solid var(--hairline-strong);
  border-radius: var(--radius-sm);
  box-shadow: var(--shadow-xs);
  transition:
    border-color var(--duration-fast) var(--ease-out),
    box-shadow var(--duration-fast) var(--ease-out);
}
input:focus, textarea:focus {
  outline: none;
  border-color: var(--amber-500);
  box-shadow: var(--focus-ring);
}
```

`UFormField label` — text-sm ink-2 medium, margin-bottom 6px.

`USelect / UCombobox` — те же 32 px и amber-focus, селектор: `button[role="combobox"], button[aria-haspopup="listbox"]`.

Textarea — height auto, min-height 80, padding 8/12.

### 7.10 Иконки

- Источник: **Lucide** через `i-lucide-*`.
- Размеры по контексту: 14 px (inline в кнопке/nav-item), 16 px (header-icon в KPI), 18–22 px (empty-state).
- Цвет — `currentColor`. Если иконка несёт семантику (success/danger), задаём цвет токеном на родителе.
- Стандартные алиасы для Nuxt UI прописываются в `app.config.ts` (см. секцию 11.3).

### 7.11 Toast / Modal / Popover

- Берём из `@nuxt/ui` или `primevue`. Не пишем свои.
- Toast: `useToast().add({ title, description, color })`. Цвета: `success` (зелёный, **только** успех операции), `error`, `warning` (= amber), `info`.
- Modal: `<UModal v-model:open="open" title="…">` с слотами `body` / `footer`. Footer-кнопки выравниваем `flex justify-end gap-2`.
- Tone copy в title/description: глагол + объект, без многоточий. `«Кампания принудительно закрыта»`, не `«Готово!»`.

---

## 8. Паттерны

### 8.1 Loading

- Кнопка: `loading` prop → spinner `i-lucide-loader-circle` (700 ms linear infinite) вместо leading-icon, label с opacity .85, `disabled`.
- Списки: `UiSkeleton` (хеш-полосы) или явное `«Загрузка…»` в empty-cell таблицы, **не** spinner-overlay поверх контента.
- TanStack Query `isLoading` → skeleton; `isFetching` (фоновое обновление) — не блокируем UI.

### 8.2 Optimistic / mutation

- Mutation на «маленькие» действия (resend, copy, claim) — без overlay. Кнопка показывает `loading`, по успеху toast + invalidate.
- Mutation на «опасные» (force-close, delete) — открывают `UModal` с явным подтверждением и текстом последствий.

### 8.3 Inline-actions vs ActionMenu

- ≤ 2 inline-actions в строке — рендерим явно (`ghost sm`).
- 3+ — прячем в `UDropdownMenu` с триггером `i-lucide-more-horizontal`.
- Destructive в меню — отдельной секцией внизу, danger-tone.

### 8.4 Severity в таблицах

- Status-колонка использует `UiAppBadge dot` или `UiHealthPill`.
- Сортировка по severity — по умолчанию: dead → at_risk → healthy → unknown.
- Для самой критичной колонки можно добавить 6 px вертикальную цветную полосу слева у ячейки. **Никогда** не красим строку целиком.

### 8.5 Numbers

- Все числа в таблицах и KPI: класс `tnum` или `font-feature-settings: 'tnum' 1`.
- Денежные/тысячные форматируем через `Intl.NumberFormat('ru-RU')`.
- Дельта в KPI: знак `+` для положительной (`Intl.NumberFormat('ru-RU', { signDisplay: 'always' })`).

### 8.6 Mono-данные

ID, токены, email-в-таблицах, timestamps, magic-link, hash — `font-mono`, `text-xs`, `ink-2`. Рядом с такими значениями всегда есть `i-lucide-copy` (если copy-action имеет смысл).

### 8.7 Public-facing страницы (magic-link форма, ошибки)

- Layout `public.vue` или `confirm.vue` — без сайд-навигации, центральная колонка `max-width: ~480 px`, padding-y `--space-12`.
- Title `--text-3xl` semibold, subtitle `--text-md` ink-3.
- Кнопка primary `lg`, `block`.
- Минимум полей, явные lables, hint-ы под полем.
- Цель: «заполнить за 30 секунд с телефона».

### 8.8 Dark mode

- Активация: `<html data-theme="dark">`. Переключатель — UI-toggle в header (опционально) или `prefers-color-scheme: dark`.
- Только семантические токены (`--bg`, `--surface`, `--ink-*`, `--hairline`) переопределяются. Amber/danger ramps остаются.
- В компонентах **никогда** не пишем `color: #FFF` — всегда `color: var(--ink-1)`.

### 8.9 Спецслучаи цвета

| Кейс | Решение |
|---|---|
| Подтверждение (success-toast) | `color: success` |
| Health "живой" в таблице | нейтральный ink-2 (без зелёного tint) |
| Подсветка положительной дельты | success-500 (но не как alert, а как точка) |
| Зелёный «сохранено» под формой | **нет**. Пишем «Сохранено» ink-2 + check-icon, без фона |

### 8.10 Контейнеры

- Страничный wrap: `max-width: 1280; margin: 0 auto; padding: 32 32 64`.
- Между секциями — `margin-bottom: 32` (`--space-8`) или `gap: 24` (`--space-6`) у flex/grid.
- Внутри секции — `gap: 16` или `gap: 12`.

---

## 9. Доступность

- Контрасты: ink-1 на bg ≥ 12:1; ink-3 на bg ≥ 4.5:1; amber-500 на ink-1 — проверить ≥ 4.5:1 для текста на кнопке.
- Все интерактивные элементы — клавиатурно достижимы; `:focus-visible` показывает amber-ring всегда (никаких `outline: none` без замены).
- Иконки-кнопки имеют `aria-label`.
- В `UModal` — фокус-trap из коробки от Nuxt UI; не ломаем.
- `prefers-reduced-motion: reduce` — обязательно.
- Lang: `<html lang="ru">`.

---

## 10. Стилизация: правила гигиены

1. **CSS Modules** для каждого компонента: `<style module>`, классы — camelCase, обращение через `$style.`.
2. **Никаких** глобальных классов вне `tokens.css`/`main.css`. Никаких scoped-CSS «по случаю».
3. Tailwind-утилиты — допустимы для одноразовой расстановки в `<template>`; но любое визуальное правило, использующее цвет/тень/радиус, должно идти через CSS Modules + var(--token).
4. **Inline `style="..."`** — только для динамических значений, которые нельзя выразить классом (`width: ${pct}%`, computed-position).
5. Никаких `!important` кроме `prefers-reduced-motion` overrides и Nuxt UI-проникающих override'ов в `main.css`.
6. `z-index` — фиксированная шкала: 0 (поток), 1 (sticky thead), 10 (toast), 20 (header), 30 (popover/dropdown), 40 (modal-backdrop), 50 (modal-content).

---

## 11. Стартовый чеклист нового проекта

### 11.1 Скаффолд

```bash
npx nuxi@latest init <project>
cd <project>
npm i @nuxt/ui @nuxt/icon @nuxt/fonts @tanstack/vue-query pinia
```

### 11.2 `nuxt.config.ts`

```ts
export default defineNuxtConfig({
  modules: ['@nuxt/ui', '@nuxt/icon', '@nuxt/fonts', '@pinia/nuxt'],
  css: ['~/assets/css/main.css'],
  app: { head: { htmlAttrs: { lang: 'ru' } } },
  fonts: {
    families: [
      { name: 'Geist', provider: 'google' },
      { name: 'JetBrains Mono', provider: 'google' },
      // { name: 'Fraunces', provider: 'google' },  // опционально для display
    ],
  },
  icon: { mode: 'svg', class: 'icon', size: '1em' },
  routeRules: { '/': { redirect: '/dashboard' } },  // не через navigateTo в setup
})
```

### 11.3 `app.config.ts`

```ts
export default defineAppConfig({
  ui: {
    colors: {
      primary: 'amber',
      neutral: 'stone',
    },
    icons: {
      arrowLeft: 'i-lucide-arrow-left',
      arrowRight: 'i-lucide-arrow-right',
      check: 'i-lucide-check',
      chevronDoubleLeft: 'i-lucide-chevrons-left',
      chevronDoubleRight: 'i-lucide-chevrons-right',
      chevronDown: 'i-lucide-chevron-down',
      chevronLeft: 'i-lucide-chevron-left',
      chevronRight: 'i-lucide-chevron-right',
      chevronUp: 'i-lucide-chevron-up',
      close: 'i-lucide-x',
      ellipsis: 'i-lucide-ellipsis',
      external: 'i-lucide-arrow-up-right',
      file: 'i-lucide-file',
      folder: 'i-lucide-folder',
      folderOpen: 'i-lucide-folder-open',
      loading: 'i-lucide-loader-circle',
      minus: 'i-lucide-minus',
      plus: 'i-lucide-plus',
      search: 'i-lucide-search',
    },
  },
})
```

### 11.4 Файлы

```
app/
├─ assets/css/
│  ├─ tokens.css      ← скопировать из reference, не редактировать без причины
│  └─ main.css        ← @import tailwind + @nuxt/ui + tokens + form-overrides
├─ components/ui/     ← скопировать UiAppButton/Card/Badge/DataTable/KpiCard/PageHeader/EmptyState/HealthPill
├─ layouts/
│  ├─ default.vue     ← header(sticky) + main(container)
│  └─ public.vue      ← без navigation, центральная колонка
└─ app.config.ts
```

### 11.5 Smoke-проверки перед мержем фичи

- [ ] Все цвета через `var(--token)`. `grep -rE '#[0-9a-fA-F]{3,6}'` в `app/` пусто (кроме tokens.css).
- [ ] В таблицах `font-feature-settings: 'tnum' 1` или класс `.tnum` на колонках с числами.
- [ ] `:focus-visible` отрисован amber-ring (поверьте Tab-ом по странице).
- [ ] Nav-link с `active-class` имеет `--surface-2` и amber-600 у иконки.
- [ ] Зелёный цвет (`--success-*`) **не** используется как health-индикатор «живой» в списке.
- [ ] Красный (`--danger-*`) встречается только на dead/destructive.
- [ ] `<html lang="ru">`.
- [ ] `prefers-reduced-motion: reduce` гасит transitions/animations.
- [ ] Корень `/` редиректит через `routeRules`, не через `navigateTo` в setup-страницы.

---

## 12. Что отдавать дизайнеру

Файлы для Figma-библиотеки:

1. **Color styles** — все токены из секции 3 (warm-neutral 0..900, amber 50..800, success/danger/info/warning, semantic surfaces & ink, health aliases, heatmap).
2. **Text styles** — комбинации (size × weight × tracking × line-height) из таблицы 4.7. Имена стилей повторяют роли: `Page H1`, `Section H2`, `Subtitle`, `Eyebrow`, `Body`, `Caption`, `Table Header`, `Table Cell`, `KPI Value`, `KPI Label`, `Mono`.
3. **Effect styles** — пять теней `shadow-xs..xl` + `shadow-inset` + `focus-ring` (как outer-stroke).
4. **Spacing** — variable-collection 4 px scale.
5. **Radii** — `xs/sm/md/lg/xl/pill`.
6. **Components** (master + variants):
   - Button (4 variant × 3 size × loading × disabled × icon-leading/trailing).
   - Card (elevation flat/sm/md, with/without header/footer, padded/non-padded).
   - Badge (5 tones × 2 sizes × dot on/off).
   - HealthPill (4 health × 2 sizes).
   - PageHeader (with/without eyebrow, with/without subtitle, with/without actions).
   - DataTable (compact/comfortable, с sticky-header, со status-cell, с mono-cell, с inline-actions).
   - KpiCard (with/without delta, with/without accent, with/without icon, with/without suffix).
   - EmptyState (default/compact, с actions / без).
   - Form Input / Textarea / Select (default/hover/focus/disabled/error).
   - Modal (header + body + footer + danger-variant).
7. **Templates / Pages**:
   - Default layout (header + container).
   - Public layout (центральная карточка).
   - Dashboard (4 KPI + 1–2 секции с DataTable).
   - List page (PageHeader + filters bar + DataTable + pagination).
   - Detail page (PageHeader с eyebrow + tabs/sections).
   - Empty / Loading / Error состояния каждого шаблона.

Везде использовать только variables (variable collections в Figma) — никаких hardcoded-цветов в компонентах.

---

## 13. Что НЕ делать в гайдлайне

- Не добавлять второй акцентный цвет (синий, зелёный, фиолетовый). Один продукт = один акцент.
- Не вводить glass-морфизм / неон / градиенты на surface.
- Не делать «brutalist» вариант (толстые рамки, тени-блоки). Это другой визуальный язык.
- Не использовать иконки из других наборов (Phosphor/Heroicons/Tabler) даже точечно.
- Не менять шрифт. Если нужен контраст — используйте display-режим Geist (semibold + tight tracking) или включайте Fraunces только для hero.
- Не отказываться от `prefers-reduced-motion`.
- Не закладывать density меньше compact (28 px) — нечитаемо.

---

**Конец документа.** Любое отклонение нужно либо аргументировать в PR-описании со ссылкой на бизнес-кейс, либо вернуть в этот гайд как новое правило.

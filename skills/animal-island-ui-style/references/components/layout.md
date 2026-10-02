# Layout components — props reference

Props/types below are copied from the library source. In an npm-installed project, the installed package's TypeScript declarations (`dist/types/index.d.ts`) are the ground truth — prefer exploring them when in doubt.

## Card

```ts
type CardType = 'default' | 'dashed';

type CardColor =
    | 'default' | 'app-pink' | 'purple' | 'app-blue' | 'app-yellow' | 'app-orange' | 'app-teal'
    | 'app-green' | 'app-red' | 'lime-green' | 'yellow-green' | 'brown' | 'warm-peach-pink';
type CardPattern = 'none' | CardColor; // decorative overlay; same 13 values as CardColor

interface CardProps extends React.HTMLAttributes<HTMLDivElement> {
    type?: CardType; // default 'default'
    color?: CardColor; // default 'default'
    pattern?: CardPattern; // default 'none'
    hoverable?: boolean; // default false (no hover); true → cursor pointer + translateY(-2px) on hover
    children?: React.ReactNode;
}
```

Color → background / text: `default` rgb(247,243,223) / #725d42, `app-pink` #f8a6b2 / #fff, `purple` #b77dee / #fff, `app-blue` #889df0 / #fff, `app-yellow` #f7cd67 / #725d42, `app-orange` #e59266 / #fff, `app-teal` #82d5bb / #fff, `app-green` #8ac68a / #fff, `app-red` #fc736d / #fff, `lime-green` #d1da49 / #3d5a1a, `yellow-green` #ecdf52 / #725d42, `brown` #9a835a / #fff, `warm-peach-pink` #e18c6f / #fff.

```tsx
<Card>Default parchment card (read-only, no hover)</Card>
<Card hoverable>Interactive card (hover lifts -2px, cursor pointer)</Card>
<Card type="dashed">Draft / empty-state container</Card>
<Card color="app-yellow">Notification</Card>
<Card color="app-blue" pattern="app-pink">With decorative pattern overlay</Card>
```

Need a chapter/section heading? Use `<Title>` (ribbon banner). `Card type="title"` does not exist.

## Title

```ts
type TitleSize = 'small' | 'middle' | 'large';

type TitleColor =
    | 'default' | 'app-pink' | 'purple' | 'app-blue' | 'app-yellow' | 'app-orange' | 'app-teal'
    | 'app-green' | 'app-red' | 'lime-green' | 'yellow-green' | 'brown' | 'warm-peach-pink';

interface TitleProps {
    children: React.ReactNode; // REQUIRED
    size?: TitleSize; // default 'middle'
    color?: TitleColor; // default 'default'
    variant?: 'layer' | 'ribbon' | 'tab'; // default 'layer'
    className?: string;
    style?: React.CSSProperties;
}
```

```tsx
<Title>Chapter One</Title>
<Title size="large" color="app-yellow">Notification</Title>
<Title variant="layer" color="app-blue">Layer note</Title>
<Title variant="tab" color="lime-green">Corner tab</Title>
```

Renders an island-style ribbon banner (swallowtail clip-path ends + fold-shadow triangles + raised front). Uses the same 13 island palette as `Card.color`; `size` scales the whole ribbon via `em` units (small 14px / middle 20px / large 28px base). `variant` picks a same-family decoration that shares the `--rf / --rb / --rk / --rt` palette: `layer` (default, double-layer note: back sheet offset upper-left), `ribbon` (banner), or `tab` (corner tab: 135° gradient corner cut + dark fold triangle); every `.color-*` override applies equally to all three.

**Not supported:** no `level` (`h1..h6`) — renders as an inline-block `<div>`; no `bordered`; no `code` / `mark` / `underline` / `delete` modifiers (this is a decorative banner, NOT a generic typography-heading component).

## Divider

```ts
type DividerType =
    | 'dashed-brown'
    | 'thin'
    | 'hairline'
    | 'wave-yellow'
    | 'squiggle';

interface DividerProps {
    type?: DividerType; // default 'dashed-brown'
    icon?: IconComponent; // single-icon connected divider, a React icon component (wins over type)
    iconSize?: number;  // default 24
    iconGap?: number;   // gap width holding a centered 4x2px connector, default 8
    className?: string;
    style?: React.CSSProperties;
}
```

```tsx
<Divider />
<Divider type="thin" />      // 1px solid hairline, #e8dec7
<Divider type="hairline" />  // 1px dense dashed, #d5c3a2
<Divider type="wave-yellow" /> // yellow wavy line, #f5d04a
<Divider type="squiggle" />    // theme-teal squiggle, seamless tiled, #19c8b9
<Divider icon="Fish" />        // icon + centered connector, tiled to fill width
```

Height 12px. Purely decorative dashed rule drawn with `linear-gradient` (12px rhythm, 50% on / 50% off) — no image assets. `thin` is a 1px solid rule in `#e8dec7` (height 1px); `hairline` is a 1px dense dashed rule in `#d5c3a2` (6px rhythm); `wave-yellow` is a yellow (#f5d04a) repeating wavy line (40px period, ±7px amplitude, round line-cap); `squiggle` is a theme-teal (#19c8b9) squiggle tiled at a fixed 120px width (`repeat-x`, viewBox 0 0 120 10) whose ends meet at the same height with a horizontal tangent, so tiles join seamlessly without stretching. When `icon` is set, renders a single-icon connected divider (Footer-style tiling, `aria-hidden`): `[icon][gap]` units repeat to fill the width, sized by `iconSize` (default 24) / `iconGap` (default 8), each gap holding a short 4×2px brown connector centered between the two icons; the last icon ends the row without a trailing connector, so both ends are icons. Cycle count recalculated on resize via `ResizeObserver`. No `orientation` / `dashed` / `plain` / children — for a vertical separator, use a CSS `border-left` on adjacent elements.

## Collapse

```ts
interface CollapseProps {
    question: React.ReactNode; // REQUIRED — header
    answer: React.ReactNode; // REQUIRED — body
    defaultExpanded?: boolean; // default false
    disabled?: boolean; // default false
    className?: string;
    style?: React.CSSProperties;
}
```

```tsx
<Collapse question="What is Animal Island?" answer="A cozy React UI kit." />
<Collapse defaultExpanded question="FAQ #1" answer={<p>Long rich content…</p>} />
```

Uses a pure CSS grid-row transition — no JS height measurement, safe for SSR. Single panel only — no `accordion` / `items` group API; render multiple `<Collapse>` siblings if you need a list.

## Tabs

```ts
interface TabItem {
    key: string;
    label: React.ReactNode;
    children: React.ReactNode;
}

interface TabsProps {
    items: TabItem[]; // REQUIRED
    defaultActiveKey?: string; // default: first tab
    activeKey?: string; // controlled mode
    onChange?: (key: string) => void;
    className?: string;
    style?: React.CSSProperties;
    leafAnimation?: boolean; // default true — active-tab leaf wiggle
}
```

```tsx
// Uncontrolled
<Tabs
    items={[
        { key: 'tab1', label: '鱼类', children: <p>鲈鱼、鲷鱼...</p> },
        { key: 'tab2', label: '昆虫', children: <p>蝴蝶、蜻蜓...</p> },
    ]}
    defaultActiveKey="tab1"
/>;

// Controlled
const [activeKey, setActiveKey] = useState('tab1');
<Tabs items={items} activeKey={activeKey} onChange={setActiveKey} />;
```

Supports both controlled and uncontrolled modes, with a smooth fade animation on tab switch.

**Not supported:** no `tabPosition` (always top), no `type="card"` / `type="editable-card"`, no `tabBarExtraContent`, no closable tabs.

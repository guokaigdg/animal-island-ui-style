# General components — props reference

Props/types below are copied from the library source. In an npm-installed project, the installed package's TypeScript declarations (`dist/types/index.d.ts`) are the ground truth — prefer exploring them when in doubt.

## Button

```ts
type ButtonType = 'primary' | 'default' | 'dashed' | 'text' | 'link';
type ButtonSize = 'small' | 'middle' | 'large';
type ButtonHTMLType = 'submit' | 'reset' | 'button';

interface ButtonProps extends Omit<React.ButtonHTMLAttributes<HTMLButtonElement>, 'type'> {
    type?: ButtonType; // default 'default'
    size?: ButtonSize; // default 'middle'
    danger?: boolean; // default false
    ghost?: boolean; // default false
    block?: boolean; // default false
    loading?: boolean; // default false — renders diagonal-stripe animation
    disabled?: boolean; // default false
    icon?: React.ReactNode;
    htmlType?: ButtonHTMLType; // default 'button'
    children?: React.ReactNode;
}
```

```tsx
<Button type="primary" onClick={save}>Save</Button>
<Button type="primary" danger loading>Deleting…</Button>
<Button type="dashed" icon={<PlusIcon />} size="large" block>Add</Button>
<Button type="text">Cancel</Button>
```

## Typewriter

```ts
interface TypewriterProps {
    children?: React.ReactNode; // ANY ReactNode — preserves element structure, classNames, inline styles
    speed?: number; // ms per char, default 90
    trigger?: unknown; // change this value to restart animation (e.g. modal openCount)
    autoPlay?: boolean; // default true (false = show full immediately)
    onDone?: () => void;
}
```

```tsx
<Typewriter speed={60} onDone={() => setStep(2)}>
  <p>Hello, <strong>traveler</strong>.</p>
  <p>Welcome to the island.</p>
</Typewriter>

// Restart on modal open:
<Typewriter trigger={openCount}>{dialogueText}</Typewriter>
```

Renders NO wrapper element; zero layout impact. Recursively truncates the ReactNode by char count while preserving tree structure.

## Cursor

```ts
interface CursorProps {
    children?: React.ReactNode;
    className?: string;
    style?: React.CSSProperties;
    type?: 'default' | 'raindrop'; // default 'default' — bold-outline rounded arrow / blue raindrop
    forceAll?: boolean; // default true — force the custom cursor on every descendant
}
```

Wrap the region where you want a game finger cursor:

```tsx
<Cursor>
    <App />
</Cursor>

{/* or a blue raindrop cursor */}
<Cursor type="raindrop">
    <App />
</Cursor>
```

**Text selection.** `default` also restyles selected text in its subtree to `#e9c46a` on `#2b2620`. The dark text is required, not stylistic: the body color `#794f27` on that background is 4.25:1 and misses AA for body text. Carry the two variants as custom properties (`--animal-selection-bg` / `--animal-selection-fg`) that raindrop overwrites with `Highlight` / `HighlightText` — **never** as `.animal-cursor:not(.animal-cursor--raindrop) ::selection`, which paints a raindrop nested inside a default (descendant combinators match at any depth) and leaves a top-level raindrop with no selection styling at all. Custom properties resolve against the nearest declaring ancestor, so nesting always resolves correctly.

The arrow is a **bold-outline rounded pointer**: the classic seven-vertex silhouette scaled `1.36` into a 32×32 canvas, every corner rounded (tip `3.1`, tail `2.8`, notches `1.2`), and filled and stroked by one closed path with a stroke ~15% of the shape width. The heavy outline around a small bright core *is* the style — keep it. The path must read `L Q L Q …`: straight `L` edges between corners, one `Q` per corner. A pure `Q` chain that spans corner-to-corner also renders each edge, and the vertex control points bow the long ones by ~1 unit. The tip is deliberately blunt so it still reads against the heavy stroke, and the left edge is tilted slightly for a hand-placed feel. **The arrow is a strict mirror.** Only vertices `①``②``③``④` are free; `⑤``⑥``⑦` are each the reflection of `④``③``②` across the line through the tip at `61.62°` from horizontal (`28.38°` from vertical). So `①→②` and `①→⑦` are both `23.19` and `②→③` and `⑥→⑦` are both `7.87` — never fix either by hand. Editing a free vertex forces its partner to move; pushing a partner past the axis breaks the pairing. Two dead ends: shortening the arm by sliding notch `⑥` right (it leaves the mirror position), and any symmetry about the *vertical* — both long edges are ~23 units on a 32-wide canvas, so a vertical mirror would put the left edge at negative x. Do not tune this axis against the ink centroid: a classic arrow is asymmetric by construction and even a maximally extended arm only reaches `14.1` against a canvas centre of `16`. Judge left-right balance by eye at real 32px. When editing `cursor.css`, express stroke weight as a ratio of shape width, not a fixed pixel value, and recompute it if the scale changes — a too-thin stroke reads as a line drawing, a too-thick one swallows the bright core. The **raindrop stays plain geometric art** (path + highlight ellipse). The artwork lives in the `--animal-cursor-img` / `--animal-cursor-hotspot` custom properties on `.animal-cursor`, so changing it means editing those two declarations, not the four mode rules.

With `forceAll` (default `true`) every descendant gets the custom cursor via `cursor: var(--animal-cursor-img) var(--animal-cursor-hotspot) !important` (hotspot `6 2` for the arrow, `16 6` for the raindrop); the custom properties inherit, so descendants resolve the right variant on their own. Set `forceAll={false}` for scoped mode: only the container shows the custom cursor while links/buttons keep `pointer`, text inputs keep `text`, and disabled elements keep `not-allowed`. Do NOT nest multiple `<Cursor>`. `style` is for layout only — do not try to override the cursor URL via inline style.

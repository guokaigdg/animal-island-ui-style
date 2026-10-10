# CountUp — props reference

Props/types below are copied from the library source. In an npm-installed project, the installed package's TypeScript declarations (`dist/types/index.d.ts`) are the ground truth — prefer exploring them when in doubt.

## CountUp

Score-screen counter: animates `start` → `end` over `duration` seconds on `requestAnimationFrame`. No third-party timer or easing library — the easing curves and number formatting are local. Shares Countdown's two shells (`default` / `island`), three sizes and cream-gradient digit plate, so a score screen and a deadline screen read as one family.

```ts
type CountUpSize = 'small' | 'middle' | 'large';
type CountUpVariant = 'default' | 'island';
type CountUpEasingName = 'linear' | 'easeInCubic' | 'easeOutCubic' | 'easeInOutCubic' | 'easeOutExpo';
type CountUpEasingFunction = (progress: number) => number;   // 0→1 in, 0→1 out
type CountUpEasing = CountUpEasingName | CountUpEasingFunction;
interface CountUpCelebrateOptions { text?: React.ReactNode; }   // sticker copy; omit to render no sticker
interface CountUpRenderState { value: number; reset: (newStartAt?: number) => void; }
interface CountUpCompleteResult { shouldRepeat?: boolean; delay?: number; newStartAt?: number; }

interface CountUpProps extends Omit<React.HTMLAttributes<HTMLDivElement>, 'prefix' | 'children'> {
    start?: number;                // default 0
    end: number;                   // REQUIRED — target value (the score)
    duration?: number;             // seconds, default 2; 0 jumps straight to end
    isCounting?: boolean;          // default false; false pauses keeping the value, true resumes
    decimalPlaces?: number;        // default max(decimals(start), decimals(end))
    decimalSeparator?: string;     // default '.'
    thousandsSeparator?: string;   // default '' (no grouping)
    easing?: CountUpEasing;        // default 'easeOutCubic'; unknown names fall back to it
    formatter?: (value: number) => React.ReactNode;   // highest priority, overrides all formatting
    updateInterval?: number;       // seconds between value updates; 0 = every frame (default)
    prefix?: React.ReactNode;      // e.g. '¥'
    suffix?: React.ReactNode;      // e.g. '分'
    size?: CountUpSize;            // default 'middle'
    variant?: CountUpVariant;      // default 'default'
    bordered?: boolean;            // default false — 1.5px plate border
    celebrate?: boolean | CountUpCelebrateOptions;   // default false — celebration on completion
    onUpdate?: (value: number) => void;              // every frame
    onComplete?: (elapsedTime: number) => void | CountUpCompleteResult;
    children?: (state: CountUpRenderState) => React.ReactNode;   // render prop, replaces the number
}
```

```tsx
<CountUp isCounting end={1320} duration={2} thousandsSeparator="," suffix="分" />

{/* celebration; pass text for a sticker with any copy */}
<CountUp isCounting end={score} celebrate />
<CountUp isCounting end={score} celebrate={{ text: '完美！' }} />
<CountUp isCounting end={score} celebrate={{ text: '当当！' }} />

{/* replay with a key — the documented recipe of the reference library */}
<CountUp key={round} isCounting end={score} />

{/* render prop: take over rendering; keep focusable elements outside (the plate is aria-hidden) */}
<CountUp isCounting end={86} duration={1.2}>
    {({ value, reset }) => {
        resetRef.current = reset;
        return <span>{Math.round(value)}</span>;
    }}
</CountUp>
<button onClick={() => resetRef.current?.()}>replay</button>
```

Notes:

- **`end` is required** — there is no count-to-infinity mode. `isCounting` is the play/pause switch and defaults to `false`, so nothing animates until it is set.
- **Replay rules**: changing `start` / `end` / `duration` restarts from `start`; a finished counter re-runs when `isCounting` flips `false → true`; otherwise use `key` or the render prop's `reset(newStartAt?)`. Shifting `start` and `end` together continues from the previous total, which is the accumulation pattern (`start={Math.max(0, total - 15)} end={total}` for a +15 pickup).
- **Pausing keeps elapsed progress** — resuming continues instead of restarting. `duration={0}`, and any environment without `requestAnimationFrame`, settle on the value immediately.
- **`celebrate` is opt-in and purely decorative** (the whole layer is `aria-hidden`): two-beat plate bounce + two shockwave rings + four CSS sparkles. Pass `celebrate={{ text: '…' }}` to add a sticker carrying any copy; it auto-hides after 900ms. The sticker overflows above the plate, so a parent with `overflow: hidden` clips it. Honors `prefers-reduced-motion`.
- **A11y**: the root is `role="status"`; the animated digits are `aria-hidden`, and a visually-hidden span carries the default-formatted number only once the animation is not running — the final score is announced, never the per-frame values. `prefix` / `suffix` are decorative and excluded from that text. The digit plate is `aria-hidden` as a whole, so the `children` render prop must stay presentational — keep focusable elements (e.g. a replay button) outside the component, e.g. by storing the `reset` it hands you in a ref.

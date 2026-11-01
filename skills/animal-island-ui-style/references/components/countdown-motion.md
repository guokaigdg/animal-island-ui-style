# Countdown motion options

Import these types from `animal-island-ui`. `shake`, `endingAnimation` and `finishAnimation` accept objects as well as their original boolean shortcuts. `shake` also keeps its numeric seconds shortcut.

# Declarations

Copied from `src/components/Countdown/motion.ts`; defaults and units are documented on each field.

```ts
/** 抖动提醒的阈值、节奏和幅度，时间单位为毫秒，距离单位为 px，角度单位为度。 */
export interface CountdownShakeOptions {
    /** 开始抖动的剩余秒数，默认 5；非正数关闭 */
    seconds?: number;
    /** 加强阶段的剩余秒数，默认 3；0 关闭加强 */
    urgentSeconds?: number;
    /** 普通阶段周期，默认 1000ms，最小 16ms */
    duration?: number;
    /** 加强阶段周期，默认 650ms，最小 16ms */
    urgentDuration?: number;
    /** 普通阶段水平位移，默认 1.5px */
    distance?: number;
    /** 加强阶段水平位移，默认 2.5px */
    urgentDistance?: number;
    /** 普通阶段旋转角度，默认 0.6 度 */
    rotation?: number;
    /** 加强阶段旋转角度，默认 0.9 度 */
    urgentRotation?: number;
    /** CSS 缓动函数，默认沿用组件库令牌 */
    easing?: string;
}

/** 数字轻压回弹参数；偏移为正时向下，为负时向上。 */
export interface CountdownBounceOptions {
    /** 单次回弹时长，默认 350ms */
    duration?: number;
    /** 轻压缩放比例，默认 0.94 */
    pressScale?: number;
    /** 弹起缩放比例，默认 1.05 */
    popScale?: number;
    /** 轻压纵向偏移，默认 1px */
    pressOffset?: number;
    /** 弹起纵向偏移，默认 -2px */
    popOffset?: number;
}

/** 扩散波纹参数；波纹独立于数字，不占布局空间。 */
export interface CountdownRippleOptions {
    /** 波纹数量，默认 2；取整并限制在 0–10，0 关闭 */
    count?: number;
    /** 每道波纹时长，默认 600ms */
    duration?: number;
    /** 相邻波纹延迟，默认 150ms */
    delay?: number;
    /** 波纹颜色，默认继承提醒色，再回退到组件库主色 */
    color?: string;
    /** 初始透明度，默认 0.45，范围 0–1 */
    opacity?: number;
    /** 线宽，默认 2px */
    width?: number;
    /** 波纹与数字组的外间距，默认 5px */
    offset?: number;
    /** 波纹圆角，默认 16px */
    radius?: number;
    /** 起始缩放比例，默认 0.98 */
    startScale?: number;
    /** 结束横向缩放比例，默认 1.08 */
    scaleX?: number;
    /** 结束纵向缩放比例，默认 1.35 */
    scaleY?: number;
}

/** 最后几秒与归零共用的视觉配置；两处可分别配置。 */
export interface CountdownEffectOptions {
    /** 动效保留时间，默认 900ms；自动延长以覆盖全部子动画 */
    duration?: number;
    /** 数字回弹，默认 true；false 关闭，对象自定义 */
    bounce?: boolean | CountdownBounceOptions;
    /** 扩散波纹，默认 true；false 关闭，对象自定义 */
    ripple?: boolean | CountdownRippleOptions;
    /** CSS 缓动函数，默认沿用组件库令牌 */
    easing?: string;
    /** 系统减少动态效果时的替代反馈，默认 fade；none 完全关闭 */
    reducedMotion?: 'fade' | 'none';
    /** 替代淡入时长，默认 250ms */
    fadeDuration?: number;
    /** 替代淡入的起始透明度，默认 0.65，范围 0–1 */
    fadeOpacity?: number;
}

/** 最后几秒循环回弹与波纹，可与颜色提醒及抖动组合。 */
export interface CountdownEndingAnimationOptions extends CountdownEffectOptions {
    /** 开始播放的剩余秒数，默认 5；非正数关闭 */
    seconds?: number;
    /** 循环间隔，默认 1000ms；至少为 16ms，且不短于单次完整动效 */
    interval?: number;
}
```

# Timing and composition

- `endingAnimation` repeats while `0 < displayedSeconds <= seconds`. `true` means the last 5 seconds, every 1000ms. Disabling it or resetting beyond the threshold removes the current effect. Each repetition preserves existing digit-strip DOM nodes.
- `finishAnimation` plays once when a positive countdown reaches zero. Initial expired deadlines and enabling the prop after completion do not replay it. A fresh countdown can finish again.
- The two effects use independent visual options. Defaults match the original completion effect: 350ms rebound, two 600ms ripples staggered by 150ms, 900ms retention.
- Retention is at least the longest enabled child animation (including all ripple delays and the reduced-motion fade). The ending interval is at least this retention and 16ms, so a cycle cannot clip the previous one.
- Shake lives inside the bounce layer; enabling both composes their transforms. Normal and urgent shake phases are independently configurable; `urgentSeconds: 0` disables escalation.
- A ripple's `color` overrides reminder color; without it, the active reminder color wins, then the primary token. `reminder` still independently controls digits and enabled tile borders.
- Loop timers stop offscreen or while the document is hidden. Resuming starts a fresh visual cycle without replaying missed seconds; the real deadline continues advancing.
- Reduced motion always removes shaking, recurring bounces and ripples. Completion alone may fade (`reducedMotion: 'fade'`) or stay static (`'none'`). It never forces motion against the OS setting.
- Non-finite object values use defaults; negative unsigned values clamp to zero. Numeric `shake={NaN}`/`Infinity` keeps the old disabled behavior. Numeric fields cap at 60000 except opacity (0–1), rotation (0–360) and count (integer 0–10); signed offsets also allow -60000. CSS easing/color strings accept CSS variables; empty strings fall back to defaults.

See [countdown.md](countdown.md) for the component API and [canonical motion specs](https://github.com/guokaigdg/animal-island-ui/blob/main/docs/design-system/components/countdown-motion.md) for the visual contract.

# Chart interaction declarations

Copied verbatim from `src/components/Chart/types.ts`. See [Chart](charts.md) for full props.

```ts
import type React from 'react';
import type { ChartDataEvent, ChartType } from 'animal-island-ui';

/** 提示中的一行数据，比例按当前可见数据计算。 */
export interface ChartTooltipItem extends ChartDataEvent {
    /** 图形颜色 */
    color: string;
    /** 已格式化的数值，包含坐标或 K 线信息 */
    formattedValue: string;
    /** 饼/环/玫瑰占比，或漏斗相对首阶段比例；范围口径为百分数 */
    percentage?: number;
    /** 漏斗相对上一个可见阶段的转化率，百分数；分母为零时省略 */
    conversion?: number;
}

/** 自定义提示的上下文，不包含内部绘图节点。 */
export interface ChartTooltipContext {
    /** 图表类型 */
    type: ChartType;
    /** 当前分类或项目名称 */
    name: string;
    /** 当前可见系列或单点数据 */
    items: readonly ChartTooltipItem[];
    /** 是否通过点击或键盘锁定 */
    locked: boolean;
}

/** 数据提示配置，兼容原有布尔开关。 */
export interface ChartTooltipOptions {
    /** axis 聚合同分类/同时间/同雷达维度；其余图表回退单点，默认 axis */
    trigger?: 'axis' | 'item';
    /** 显示坐标辅助线，默认 true */
    crosshair?: boolean;
    /** 显示占比与漏斗转化率，默认 true */
    showPercentage?: boolean;
    /** 自定义提示内容；返回 null 时隐藏提示 */
    render?: (context: ChartTooltipContext) => React.ReactNode;
}
```

# Behavior

- `tooltip={true}` defaults to `{ trigger: 'axis', crosshair: true, showPercentage: true }`; `false` hides the panel and crosshair.
- Category charts compare visible series at the same data index. Time compares exact x timestamps; radar compares the same dimension. Scatter, bubble and other non-category types use item tooltips. Missing values stay missing. Cartesian plots resolve nearby categories; radar resolves the nearest visible dimension by angle inside its circular plot, including zero-valued points.
- Pie/ring/rose percentages use the sum of currently drawn values. Funnel uses the first visible stage and previous visible stage; a zero denominator omits the corresponding rate. Percentages use 0–100 units and may exceed 100 for increasing funnel values.
- `highlight={false}` disables emphasis and dimming. `selectable={false}` disables locking but keeps tooltips, callbacks and keyboard access.
- Clicking a mark locks the reading; clicking it again, an empty plot area, the release button or Escape unlocks. Arrow/Home/End navigation also unlocks and moves focus. Hidden/removed data, changed chart identity and loading clear selection.
- Data updates with the same identity refresh a locked value. Use unique series names and immutable arrays.
- Custom `render` receives original data, formatted values, colors, applicable percentages and lock state; null hides the panel.
- Tooltip placement measures content and respects local horizontal scrolling. Hover panels do not intercept pointer clicks. Lock a reading to scroll long content inside the panel.

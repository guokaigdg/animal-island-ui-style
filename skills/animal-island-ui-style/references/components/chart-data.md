# Chart data declarations

Copied verbatim from `src/components/Chart/types.ts`. See [Chart](charts.md) for usage.

```ts
/** 图表类型；沿用 uCharts 常见类型名称，time 为按时间坐标排列的折线。 */
export type ChartType =
    | 'column'
    | 'bar'
    | 'line'
    | 'area'
    | 'mount'
    | 'mix'
    | 'scatter'
    | 'bubble'
    | 'pie'
    | 'ring'
    | 'rose'
    | 'radar'
    | 'arcbar'
    | 'gauge'
    | 'funnel'
    | 'word'
    | 'candle'
    | 'map'
    | 'time';

/** 普通数值点；散点/气泡/时序使用 x、y，其他图表使用 value。 */
export interface ChartPoint {
    /** 数据名称，用于提示、扇区图例和地图区域匹配 */
    name?: string;
    /** 数值 */
    value?: number;
    /** 横坐标；time 类型使用毫秒时间戳 */
    x?: number;
    /** 纵坐标 */
    y?: number;
    /** 气泡正数权重，面积与权重成正比，默认 1；非正数不绘制 */
    size?: number;
    /** 单点颜色，优先于系列色 */
    color?: string;
}

/** 单根 K 线，开盘、收盘应位于最低与最高价之间。 */
export interface ChartCandle {
    /** 名称，通常为日期 */
    name?: string;
    /** 开盘值 */
    open: number;
    /** 收盘值 */
    close: number;
    /** 最低值 */
    low: number;
    /** 最高值 */
    high: number;
}

/** null 表示缺测；非有限数和不完整坐标也按缺测处理。 */
export type ChartDatum = number | null | ChartPoint | ChartCandle;

/** 一个数据系列；名称在同一图表内应唯一。 */
export interface ChartSeries {
    /** 图例与提示使用的系列名 */
    name: string;
    /** 数值、坐标或 K 线数据 */
    data: readonly ChartDatum[];
    /** 系列颜色 */
    color?: string;
    /** 仅 mix 使用，默认 column；共用同一数值轴 */
    type?: 'column' | 'line' | 'area';
}

/** 地图区域，使用同一平面坐标系，纵坐标向上；可提供多个闭合轮廓。 */
export interface ChartMapRegion {
    /** 与数据点 name 对应的区域名 */
    name: string;
    /** 多边形轮廓；相交/嵌套轮廓按 evenodd 填充规则绘制 */
    polygons: readonly (readonly (readonly [number, number])[])[];
}

/** 点击数据点返回的原始数据及位置。 */
export interface ChartDataEvent {
    /** 图表类型 */
    type: ChartType;
    /** 原始系列下标 */
    seriesIndex: number;
    /** 原始数据下标 */
    dataIndex: number;
    /** 系列名称 */
    seriesName: string;
    /** 数据名称 */
    name: string;
    /** 数值；K 线为收盘值 */
    value: number;
    /** 调用方传入的原始数据 */
    datum: ChartDatum;
}
```

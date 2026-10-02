# Data display components — props reference

Props/types below are copied from the library source. In an npm-installed project, the installed package's TypeScript declarations (`dist/types/index.d.ts`) are the ground truth — prefer exploring them when in doubt.

Covers: Table, Pagination, CodeBlock, Tag.

## Table

```ts
interface TableColumn<T = Record<string, unknown>> {
    title: React.ReactNode;
    dataIndex?: keyof T;
    render?: (value: unknown, record: T, index: number) => React.ReactNode;
    width?: string | number;
    align?: 'left' | 'center' | 'right';
    fixed?: 'left' | 'right';
    style?: React.CSSProperties;
}
interface TableProps<T = Record<string, unknown>> {
    columns?: TableColumn<T>[]; // default []
    dataSource?: T[]; // default []
    rowKey?: string | ((record: T) => string); // default 'key'
    striped?: boolean; // default true
    showHeader?: boolean; // default true
    rowClassName?: string | ((record: T, index: number) => string);
    onRow?: (record: T, index: number) => React.HTMLAttributes<HTMLTableRowElement>;
    loading?: boolean; // default false
    emptyText?: React.ReactNode; // default '暂无数据'
    scroll?: { x?: number | string; y?: number | string };
    pagination?: false | PaginationProps; // default false — object enables client-side paging
    className?: string;
    style?: React.CSSProperties;
}
```

```tsx
<Table
    columns={[
        { title: '名称', dataIndex: 'name', width: 160 },
        { title: '价格', dataIndex: 'price', align: 'right' },
        { title: '操作', render: (_, r) => <Button size="small">买</Button> },
    ]}
    dataSource={items}
    rowKey="id"
/>
```

> **Not supported:** no built-in `sorter`/`filters`/column-search, `rowSelection`, `expandable`/nested rows, `summary`, `bordered` toggle (always borderless), virtual scroll. `scroll.x/y` only enable native overflow scrolling. Client-side paging IS built in via `pagination={{ ... }}` (see Pagination below) — for server-side paging slice `dataSource` yourself.

## Pagination

```ts
interface PaginationProps {
    total: number; // REQUIRED
    current?: number; // controlled; defaultCurrent defaults to 1
    pageSize?: number; // controlled; defaultPageSize defaults to 10
    onChange?: (page: number, pageSize: number) => void;
    onShowSizeChange?: (current: number, size: number) => void; // size change only
    showSizeChanger?: boolean; // default false — page-size popover; pageSizeOptions default [10,20,50,100]
    showQuickJumper?: boolean; // default false — "跳至 <input> 页"
    showTotal?: boolean; // default false — "共 N 条"
    disabled?: boolean; // default false
    className?: string;
    style?: React.CSSProperties;
}
```

```tsx
<Pagination total={85} defaultCurrent={3} showTotal showSizeChanger pageSizeOptions={[10, 20, 50]} />
<Pagination total={500} current={page} pageSize={20} showQuickJumper onChange={(p) => setPage(p)} />
<Table columns={columns} dataSource={data} pagination={{ defaultPageSize: 5, showTotal: true }} />
```

Notes: DatePicker visual language — ghost 32px circles (transparent bg) hover to `#e6f9f6` + teal text; active page teal `#19c8b9` solid circle with white text; size trigger & jumper are cream `#fffbe7` capsules. Page run: first + last always visible, current ±1 neighbourhood, `···` when `pageCount > 7`. `current`/`pageSize` are controlled when passed, else internal. The size changer is a self-contained popover (click-outside/Escape closes). a11y: `<nav aria-label="分页">`, `aria-current="page"`, native disabled.

## CodeBlock

```ts
interface CodeBlockProps {
    code: string; // REQUIRED — raw source string
    style?: React.CSSProperties; // merged on top of the dark preset
    className?: string;
    copyable?: boolean; // default true
    onCopy?: (code: string) => void;
}
```

```tsx
<CodeBlock code={`import { Button } from 'animal-island-ui';\n\n<Button type="primary">Go</Button>`} />
<CodeBlock code={src} style={{ borderRadius: 5, backgroundColor: '#242c46' }} />
```

> Renders a `<pre>` with built-in JSX/TS tokenizer and a top-right copy button. No `language` prop, line numbers or word-wrap. Default theme: bg `#2b2118`, border `1px solid #3d3028`, radius 20px, font-size 14, line-height 1.7.

## Tag

```ts
type TagSize = 'small' | 'medium' | 'large';
type TagVariant = 'solid' | 'outlined' | 'dashed' | 'soft';
type TagColor = 'default' | 'app-pink' | 'purple' | 'app-blue' | 'app-yellow' | 'app-orange' | 'app-teal' | 'app-green' | 'app-red' | 'lime-green' | 'yellow-green' | 'brown' | 'warm-peach-pink';
interface TagProps {
    children?: React.ReactNode;
    size?: TagSize; // default 'medium'
    variant?: TagVariant; // default 'soft'
    color?: TagColor; // default 'default'
    closable?: boolean; // default false
    onClose?: (e: React.MouseEvent<HTMLElement>) => void;
    onClick?: (e: React.MouseEvent<HTMLElement>) => void; // enables clickable + keyboard a11y
    disabled?: boolean; // default false
    className?: string;
    style?: React.CSSProperties;
}
```

```tsx
<Tag>默认标签</Tag>
<Tag color="app-pink" variant="solid">已选</Tag>
<Tag color="app-teal" variant="outlined">草稿</Tag>
<Tag closable onClose={(e) => console.log('closed')}>可关闭</Tag>
<Tag color="app-blue" onClick={() => alert('clicked')}>可点击</Tag>
<Tag disabled>禁用</Tag>
```

Notes: color palette exactly matches `Card` — 12 brand colors + 1 default. `solid` saturated bg + white text; `outlined`/`dashed` same color text + border on transparent; `soft` pastel bg + deeper same-hue text, no border; `default` is the parchment-pill neutral (`rgb(247,243,223)` bg, `#8f734f` text). Sizes (class `size-{size}`): small 24 / medium 32 / large 40px, font 12/14/16, all `border-radius: 999px`, `font-weight: 600`, 1.5px transparent border. `closable` renders a × button (`aria-label="close"`, 16×16 circle bg, click is `stopPropagation`'d). `onClick` upgrades to `<span role="button" tabIndex={0}>` with Enter/Space support. `disabled` = `opacity: 0.5` + `pointer-events: none`.

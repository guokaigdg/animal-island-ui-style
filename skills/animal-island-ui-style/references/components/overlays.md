# Overlay components — props reference

Props/types below are copied from the library source. In an npm-installed project, the installed package's TypeScript declarations (`dist/types/index.d.ts`) are the ground truth — prefer exploring them when in doubt.

## Modal

```ts
interface ModalProps {
    open: boolean; // REQUIRED
    title?: React.ReactNode; // heading text — NOT the <Title> component
    width?: number | string; // default 520
    maskClosable?: boolean; // default true
    footer?: React.ReactNode | null; // null = hide footer
    onClose?: () => void;
    onOk?: () => void;
    children?: React.ReactNode;
    className?: string;
    typeSpeed?: number; // default 80 (ms/char for built-in typewriter)
    typewriter?: boolean; // default true — body plays typewriter on open
}
```

```tsx
const [open, setOpen] = useState(false);
<Modal
    open={open}
    title="Confirm"
    onClose={() => setOpen(false)}
    onOk={() => {
        submit();
        setOpen(false);
    }}
>
    Proceed to delete this island?
</Modal>;
```

Notes:

- Modal already ships the required SVG blob `<clipPath id="animal-modal-clip">` internally.
- `prefers-reduced-motion: reduce` drops the panel's `scale(0.92) → scale(1)` zoom-in — that is exactly the kind of motion the setting exists to suppress. The panel then renders at its resting `scale(1)`, so it never appears mid-flight or invisible. The mask's plain opacity fade is not motion and is kept.
- To disable the typewriter animation for dynamic content: `typewriter={false}`.
- Custom footer: pass `footer={<><Button>...</Button></>}` or `footer={null}` to hide.
- `title` accepts a `ReactNode` — pass plain text. Do NOT pass `<Title>` here.
- **Modal chrome is not selectable; only the body is.** The mask, header, title, close × and footer carry `user-select: none`, the body carries `user-select: text` (content stays copyable). Keep it that way in any custom skin: without it, double-clicking the title selects a word, and the warm `::selection` colour paints a stray highlight inside the popup.

## Drawer

```ts
type DrawerPlacement = 'left' | 'right' | 'top' | 'bottom';

interface DrawerProps {
    open: boolean; // REQUIRED
    title?: React.ReactNode; // heading text — plain text, NOT <Title>
    placement?: DrawerPlacement; // default 'right'
    width?: number | string; // for left/right, default 378
    height?: number | string; // for top/bottom, default 300
    maskClosable?: boolean; // default true
    pushBackground?: boolean; // default true — signature depth effect (background sinks)
    footer?: React.ReactNode | null; // null / undefined = no footer rendered
    onClose?: () => void;
    children?: React.ReactNode;
    className?: string;
    maskStyle?: React.CSSProperties;
}
```

```tsx
const [open, setOpen] = useState(false);
<Drawer open={open} title="岛屿设置" onClose={() => setOpen(false)}>
    打开时背景内容会下沉 + 缩放 + 降亮，突出抽屉主体，形成景深层次。
</Drawer>;
```

Notes:

- `pushBackground` (default `true`) is the signature effect: when open, every non-fixed direct child of `document.body` gets `transform: scale(0.94)`, `border-radius: 14px` and `overflow: hidden` over 0.36s `cubic-bezier(0.2, 0, 0.2, 1)`, making the drawer float above a "sunken" page. Fixed-position elements (other overlays, portals) are auto-excluded, as are `[data-animal-drawer-ignore]` and the component's own portal wrapper. Set `pushBackground={false}` for a normal flat-mask drawer.
- **Do not add `filter: blur()` to the pushed background.** `filter` is a non-composited property: it forces a fresh composited layer plus a per-pixel gaussian blur over the whole viewport for every background element. The symptom is a visible stall on the *first* open (layers are cold and must be rasterized from scratch) while later opens feel fine. The depth read comes from `scale` + rounded corners + the dimming mask; a 1px blur contributes almost nothing visually. Pair this with `will-change: transform` on the pushed elements so layer promotion happens before the transition starts rather than mid-animation.
- `prefers-reduced-motion: reduce` disables **both** the background push (sunken scale) **and** the panel's own slide-in transition — the same treatment `Modal` gives its zoom-in. The panel lands directly on `transform: none`, so it never stops mid-flight or invisible. Keep the mask's opacity transition: plain opacity is not motion.
- The mask uses a light black `rgba(0,0,0,0.18)` (lighter than Modal's 0.35) so the sunken background remains visible — this is what makes the depth readable.
- Drawer has NO built-in typewriter (unlike Modal). Pass any content directly.
- Default footer is **none** (unlike Modal's 取消/确定). Pass `footer={<><Button>...</Button></>}` for confirm-style actions.
- A close × button is rendered in the header when `title` is provided. Esc / mask click also close.
- **Panel chrome is not selectable; only the body is.** The mask, header, title, close × and footer carry `user-select: none`, the body carries `user-select: text` (content stays copyable). Keep it that way in any custom skin: without it, double-clicking the title selects a word, and double-clicking the trigger to open the drawer drops a selection onto the sliding-in panel, which the warm `::selection` colour paints as a stray highlight inside the popup.
- a11y: `role="dialog"`, `aria-modal="true"`, `aria-labelledby` on title, focus trap + focus restore (same pattern as Modal).

## Tooltip

```ts
type TooltipPlacement =
    | 'top'
    | 'top-start'
    | 'top-end'
    | 'bottom'
    | 'bottom-start'
    | 'bottom-end'
    | 'left'
    | 'left-start'
    | 'left-end'
    | 'right'
    | 'right-start'
    | 'right-end';

type TooltipTrigger = 'hover' | 'focus' | 'click';
type TooltipVariant = 'default' | 'island';

interface TooltipProps {
    title: React.ReactNode; // REQUIRED — tooltip body
    children: React.ReactElement; // REQUIRED — single trigger element
    placement?: TooltipPlacement; // default 'top'
    trigger?: TooltipTrigger; // default 'hover'
    variant?: TooltipVariant; // default 'default'
    bordered?: boolean; // default true
    className?: string;
    style?: React.CSSProperties;
}
```

```tsx
<Tooltip title="Save your island"><Button type="primary">Save</Button></Tooltip>
<Tooltip title="More info" placement="right" trigger="click">
  <ChatIcon />
</Tooltip>
<Tooltip title="Game-style bubble" variant="island"><span>?</span></Tooltip>
```

> `children` must be a SINGLE React element capable of receiving event/ref props (do not pass strings or fragments). `variant="island"` renders a transparent container (no border, no shadow) whose inner content carries its own organic bubble — it is NOT the Modal blob clip-path.
>
> **Not supported:** no `open` / `defaultOpen` (uncontrolled visibility only — driven by `trigger`), no `onOpenChange`, no `mouseEnterDelay` / `mouseLeaveDelay`, no arrow toggle, no `getPopupContainer`, no `color`. The bubble color is fixed by `variant`.

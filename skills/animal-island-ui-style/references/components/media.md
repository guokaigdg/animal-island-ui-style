# Media components — props reference

Props/types below are copied from the library source. In an npm-installed project, the installed package's TypeScript declarations (`dist/types/index.d.ts`) are the ground truth — prefer exploring them when in doubt.

Covers: Image, Avatar, Carousel.

## Image

```ts
type ImageColor = 'white' | 'default' | 'app-pink' | 'purple' | 'app-blue' | 'app-yellow' | 'app-orange' | 'app-teal' | 'app-green' | 'app-red' | 'lime-green' | 'yellow-green' | 'brown' | 'warm-peach-pink';
interface ImageProps extends Omit<React.ImgHTMLAttributes<HTMLImageElement>, 'src' | 'alt' | 'width' | 'height' | 'onLoad' | 'onError'> {
    src: string; // REQUIRED
    alt?: string; // default '' — empty means decorative
    width?: number | string;
    height?: number | string;
    color?: ImageColor; // default 'white'; others are the Card pattern base colours (pastel, no dots)
    variant?: 'default' | 'bordered' | 'stamp'; // default 'default'; 'stamp' = postage-stamp border
    stampYear?: string; // variant='stamp' only — issue year, top-right
    lazy?: boolean; // default false — native loading="lazy"
    preview?: boolean; // default true — click opens a lightbox
    onLoad?: (e: React.SyntheticEvent<HTMLImageElement>) => void;
    onError?: (e: React.SyntheticEvent<HTMLImageElement>) => void;
}
```

```tsx
<Image src="/photo.png" alt="岛屿风景" width={200} height={150} />
<Image src="/photo.png" alt="粉色" color="app-pink" />
<Image src="/photo.png" alt="懒加载" lazy />
<Image src="/photo.png" alt="预览" width={200} height={130} preview />
<Image src="/photo.png" alt="邮票（带年份）" width={240} height={176} variant="stamp" stampYear="2026" />
```

> Renders a `<img>` in a fixed mat frame (12px padding, 8px radius, soft shadow, no border, `overflow: hidden`). The image stays `opacity: 0` until `onLoad` fades it in; on error a built-in placeholder renders (`role="img"` + `aria-label`). With `preview` the frame becomes a `<button>`; clicking opens a portaled lightbox (`role="dialog"` + `aria-modal`, name from `alt`) — close via ESC, mask, or close button; focus moves to the close button on open and is restored on close.

## Avatar

```ts
type AvatarShape = 'circle' | 'square';
type AvatarSize = 'small' | 'middle' | 'large';
interface AvatarProps extends Omit<React.HTMLAttributes<HTMLSpanElement>, 'onError'> {
    shape?: AvatarShape; // default 'circle'
    size?: number | AvatarSize; // default 'middle' — 32/40/48px; any px number allowed
    src?: string; // image URL; falls back to icon/text on load error
    alt?: string; // img alt (a11y)
    icon?: React.ReactNode; // placeholder icon; default UserIcon
    gap?: number; // default 4 — text/icon inset from the edge; oversized text auto-shrinks
    onError?: () => boolean; // load-fail callback; return false to keep the img
    children?: React.ReactNode; // text avatar, or a naive-icons component for an icon avatar
}
interface AvatarGroupProps extends React.HTMLAttributes<HTMLDivElement> {
    maxCount?: number; // collapse excess into a "+N" badge
    maxStyle?: React.CSSProperties; // styling for the "+N" badge
    size?: number | AvatarSize; // injected into children that don't set their own
    shape?: AvatarShape; // injected into children that don't set their own
    gap?: number; // default 8 — overlap between avatars
    children?: React.ReactNode;
}
```

```tsx
<Avatar>岛</Avatar>
<Avatar icon={<UserIcon />} />
<Avatar><FishIcon /></Avatar>{/* naive-icons component as children → icon avatar */}
<Avatar src="/u.png" alt="岛民" />
<Avatar size={64}>64</Avatar>
<Avatar src="/broken.png" onError={() => false}>兜底</Avatar>

<AvatarGroup maxCount={3} size="small" shape="square" gap={12}>
    <Avatar src="/a.png" alt="A" />
    <Avatar>勤</Avatar>
    <Avatar>劳</Avatar>
    <Avatar src="/d.png" alt="D" />
</AvatarGroup>
```

Notes: image avatars render an `<img>` (`object-fit: cover`); on `error` the component falls back to `icon` (default naive-icons `UserIcon`) or text `children` unless `onError` returns `false`. Passing a naive-icons component as `children` (element type is a function component) creates an icon avatar — same rendering path as `icon`; plain text/number children are text avatars. Text/icon avatars sit on a light-teal `--animal-primary-color-bg` background with teal content and a 2px cream border (`--animal-bg-color`) — a "sticker" look; circles use `border-radius: 999px`, squares 8px (matches the Image mat). `gap` drives automatic font shrinking via `useLayoutEffect` measurement (available width = `size - gap*2`) — text avatars only, icon avatars skip the shrink path. Numeric sizes derive font size as `size * 0.4`. The group stacks avatars with `margin-left: calc(-1 * var(--avatar-group-gap))`; a 2px cream gap ring separates them. `Avatar.Group` is also available as a static property. a11y: image avatars use `alt`; the bare default icon gets `role="img"` + `aria-label="avatar"`; text/icon content is readable as-is.

## Carousel

```ts
interface CarouselProps extends Omit<React.HTMLAttributes<HTMLElement>, 'onChange'> {
    children: React.ReactNode; // REQUIRED; each direct child is one slide
    activeIndex?: number;
    defaultActiveIndex?: number; // default 0
    onChange?: (index: number) => void;
    autoplay?: boolean; // default false
    interval?: number; // default 3000
    loop?: boolean; // default true
    showArrows?: boolean; // default true
    showDots?: boolean; // default true
    pauseOnHover?: boolean; // default true; focus also pauses
}
```

```tsx
<Carousel autoplay aria-label="岛屿照片">
    <img src="/beach.jpg" alt="海滩" />
    <img src="/plaza.jpg" alt="广场" />
</Carousel>
```

Supports controlled/uncontrolled indexes, CSS fade transitions, looping, arrows, dots and ArrowLeft/ArrowRight/Home/End navigation. Autoplay automatically renders a pause/resume control and pauses on hover/focus. Set a useful `aria-label`; every slide receives a position/count label automatically.

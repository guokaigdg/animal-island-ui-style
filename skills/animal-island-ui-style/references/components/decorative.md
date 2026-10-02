# Decorative components — props reference

Props/types below are copied from the library source. In an npm-installed project, the installed package's TypeScript declarations (`dist/types/index.d.ts`) are the ground truth — prefer exploring them when in doubt.

## Footer

```ts
interface FooterProps {
    text?: string; // copyright text — default 'All Rights Reserved.'
    year?: number; // year — default current year (dynamically fetched)
    className?: string;
    style?: React.CSSProperties;
}
```

```tsx
<Footer />                   {/* © 2026 All Rights Reserved. */}
<Footer text="Acme Ltd." />  {/* custom text */}
<Footer text="Acme" year={2020} /> {/* custom year */}
```

Renders a copyright bar `© {year} {text}`, centered, `color: #807d75`, `font-size: 12px`, `padding: 16px 0`. The `year` defaults to the current year; the `text` defaults to `All Rights Reserved.`. Both are overridable, and styling can be customized via `style` / `className`.

## Background

```ts
type BackgroundType = 'default' | 'grid' | 'dots-dark-green' | 'sprinkles' | 'sweet-corner' | 'coffee-break'
    | 'dots-pink' | 'dots-purple' | 'dots-blue' | 'dots-yellow' | 'dots-orange' | 'dots-teal'
    | 'dots-green' | 'dots-red' | 'dots-lime-green' | 'dots-yellow-green' | 'dots-brown' | 'dots-warm-peach-pink';

interface BackgroundProps extends React.HTMLAttributes<HTMLDivElement> {
    type?: BackgroundType; // default 'default'
    children?: React.ReactNode;
}
```

```tsx
<Background style={{ height: 200 }} />
<Background type="dots-dark-green" style={{ height: 200 }} />
<Background type="sprinkles" style={{ minHeight: 200, padding: 24 }}>
    <p>Content renders above the pattern</p>
</Background>
```

Most types are pattern-only wallpapers with no image assets: `default` is cream `rgb(247,243,223)` with beige polka dots; `grid` is a 24px grid of hairline box rules; `dots-dark-green` is a two-layer offset polka-dot pattern on green `#bfe3bf`; `sprinkles` scatters 6-color cylindrical candy sprinkles (capsule rods with highlight shading) on frosting `#fdf3e3` — three mutually-prime inline-SVG tiles make the scatter read as random with no visible repeat; the other 12 `dots-*` types are pastel polka wallpapers whose base color matches Card's `pattern-*` series (`dots-pink`, `dots-purple`, `dots-blue`, `dots-yellow`, `dots-orange`, `dots-teal`, `dots-green`, `dots-red`, `dots-lime-green`, `dots-yellow-green`, `dots-brown`, `dots-warm-peach-pink`). `sweet-corner` / `coffee-break` draw the 2560×1440 transparent-background scene SVGs (same as Progress `fill` scene variants) with `cover` sizing over a cream base — the SVG URL is injected as `backgroundImage` from the component. No fixed height — set `height` / `min-height` via `style`.


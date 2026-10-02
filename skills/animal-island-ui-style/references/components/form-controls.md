# Form control components — props reference

Props/types below are copied from the library source. In an npm-installed project, the installed package's TypeScript declarations (`dist/types/index.d.ts`) are the ground truth — prefer exploring them when in doubt.

## Input

```ts
type InputSize = 'small' | 'middle' | 'large';

interface InputProps extends Omit<React.InputHTMLAttributes<HTMLInputElement>, 'size' | 'prefix'> {
    size?: InputSize; // default 'middle'
    prefix?: React.ReactNode;
    suffix?: React.ReactNode;
    allowClear?: boolean; // default false
    status?: 'error' | 'warning';
    shadow?: boolean; // default false — when true, render the 3D pixel-stack shadow
    onChange?: React.ChangeEventHandler<HTMLInputElement>;
    onClear?: () => void;
}
```

```tsx
<Input placeholder="Your name" allowClear size="large" prefix={<SearchIcon />} value={q} onChange={e => setQ(e.target.value)} status="error" disabled />
```

## Switch

```ts
type SwitchSize = 'small' | 'default';

interface SwitchProps {
    checked?: boolean; // controlled
    defaultChecked?: boolean; // default false
    size?: SwitchSize; // default 'default'
    disabled?: boolean; // default false
    loading?: boolean; // default false
    checkedChildren?: React.ReactNode;
    unCheckedChildren?: React.ReactNode;
    onChange?: (checked: boolean) => void;
    className?: string;
}
```

```tsx
<Switch defaultChecked onChange={v => console.log(v)} />
<Switch size="small" checkedChildren="ON" unCheckedChildren="OFF" loading disabled />
```

## Checkbox

```ts
type CheckboxSize = 'small' | 'middle' | 'large';

interface CheckboxOption {
    label: React.ReactNode;
    value: string | number;
    disabled?: boolean; // disable this option only
}

interface CheckboxProps {
    options: CheckboxOption[]; // REQUIRED
    value?: Array<string | number>; // controlled
    defaultValue?: Array<string | number>; // default []
    size?: CheckboxSize; // default 'middle'
    disabled?: boolean; // default false — disables all
    direction?: 'horizontal' | 'vertical'; // default 'horizontal'
    onChange?: (values: Array<string | number>) => void;
    className?: string;
    style?: React.CSSProperties;
}
```

```tsx
<Checkbox options={[{ label: '🌊 海滩', value: 'beach' }, { label: '🌳 森林', value: 'forest' }, { label: '🦀 螃蟹', value: 'crab', disabled: true }]} defaultValue={['beach']} />
<Checkbox options={options} value={values} onChange={setValues} direction="vertical" size="large" />
```

Group-level `disabled` disables every item; per-option `disabled` disables a single row. A checked box fills with `#19c8b9`. No indeterminate state, no standalone `<Checkbox.Single>` — group-only via `options`.

## Radio

```ts
type RadioSize = 'small' | 'middle' | 'large';

interface RadioOption {
    label: React.ReactNode;
    value: string | number;
    disabled?: boolean;
}

interface RadioProps {
    options: RadioOption[]; // REQUIRED
    value?: string | number; // controlled
    defaultValue?: string | number; // uncontrolled
    size?: RadioSize; // default 'middle'
    disabled?: boolean; // default false — disables all
    direction?: 'horizontal' | 'vertical'; // default 'horizontal'
    onChange?: (value: string | number) => void;
    className?: string;
    style?: React.CSSProperties;
}
```

```tsx
const [v, setV] = useState<string | number>('zh');
<Radio value={v} onChange={setV} options={[{ label: '中文', value: 'zh' }, { label: 'English', value: 'en' }, { label: '日本語', value: 'ja', disabled: true }]} />;
```

Implements WAI-ARIA roving tabindex (Arrow / Home / End keyboard navigation). Single-select counterpart to `Checkbox`. **Not supported:** no `optionType="button"` / `buttonStyle` / indeterminate / nested groups / standalone per-`<Radio>` (group-only via `options`).

## Select

```ts
type SelectOption = { key: string; label: string };

interface SelectProps {
    options: SelectOption[]; // REQUIRED
    value: string; // REQUIRED — controlled-only
    onChange: (key: string) => void; // REQUIRED
    placeholder?: string; // default '请选择'
    disabled?: boolean; // default false
}
```

```tsx
const [lang, setLang] = useState('zh');
<Select value={lang} onChange={setLang} options={[{ key: 'zh', label: '简体中文' }, { key: 'en', label: 'English' }, { key: 'ja', label: '日本語' }]} placeholder="Choose language" />;
```

- **Controlled only** — `value` / `onChange` required, no `defaultValue`. Dropdown auto-flips (top/bottom, left/right); click-outside closes. No `className` / `style` / `renderOption`; style via the descendant `.wrapper`. **Not supported:** no `multiple` / `tags` / `showSearch` / `loading` / `allowClear` / `optionLabelProp` / `notFoundContent`.

## Rate

```ts
type RateSize = 'small' | 'middle' | 'large';

interface RateProps extends Omit<React.HTMLAttributes<HTMLDivElement>, 'onChange' | 'defaultValue'> {
    value?: number; // controlled
    defaultValue?: number; // default 0
    count?: number; // default 5 — number of stars
    size?: RateSize; // default 'middle'
    readonly?: boolean; // default false — display only: no hover preview, no click, inputs disabled
    allowClear?: boolean; // default true — clicking the current star again clears the value to 0
    onChange?: (value: number) => void;
}
```

```tsx
const [score, setScore] = useState(3);
<Rate value={score} onChange={setScore} size="large" />
<Rate defaultValue={4} count={10} />
<Rate defaultValue={5} readonly />
```

Hovering previews the rating up to the hovered star; `readonly` keeps the same gold stars but removes hover and click. Keyboard: arrows ±1 star, Home/End jump to the ends, roving tabindex sits on the checked star. `role="radiogroup"`, one `role="radio"` per star labeled `{n} 星`. **Not supported:** no half stars / `allowHalf`, no custom `character`, no `tooltips`.

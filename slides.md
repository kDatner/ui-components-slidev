---
canvasWidth: 1024
layout: intro
highlighter: shiki
---

# Design Systems at Scale

How to avoid stumbling over your own product

---

# Outline

What's wrong with the way things are?

Why do we have `antd` and `mui`?

What is our styling solution?

How do we handle hybrid/complex/one-off cases?

---

# Requirements

How do we solve these issue in the long run?

 - Primitives
 - Composition
 - Modularity 

---

# Primitives

The legendary "LEGO" blocks your PM dreams about

```tsx {all|1-3|5-14|16-19}
export function Button(props: ButtonProps) {
  return <button {...props} />
}

export function NavBar(props: NavBarProps) {
  // insert magic here
  return (
  <nav>
    {renderMainLinks(props)}
    {renderSubLinks(props)}
    {renderOtherStuff(props)}
  </nav>
  )
}

export function Table(props: TableProps) {
 // 2987 line of table logic here
 return <table>{..entireTableHere}</table>
}
```

---

# Primitives

What he actually wants

```tsx
// @filename: Menu.tsx
export function useMenu() {}

export function Root() {}

export function Input() {}

export function Trigger() {}

export function List() {}

export function ListItem() {}
```

```tsx
// @filename: MyMenu.tsx
import * as Menu from '@ui/primitives/Menu'

export function MyMenu() {}
```

<v-click>

**This is not about separation of concerns**

</v-click>

---

# Composition

A form of abstraction that is flexible and lean

```tsx {all|10|11-13|all}
// @filename: MyMenu.tsx
import * as Menu from '@ui/primitives/Menu'

export function MyMenu(props: MyMenuProps) {
  const {items} = props
  const menu = Menu.useMenu()

  return (
    <Menu.Root>
      <Menu.Trigger>select</Menu.Trigger>
      <Menu.List>
        {items.map(it => <Menu.ListItem value={it} onSelect={menu.handleSelect}>{it.name}</Menu.ListItem>)}
      </Menu.List>
    <Menu.Root>
  )
}
```

---

# Composition

A form of abstraction that is flexible and lean

```tsx 
// @filename: prefab/Menu.tsx
import * as Menu from '@ui/primitives/Menu'

export function Menu(props: MenuProps) {
  // "unique" logic
  return (
  <Menu.Root>
    {props.children}
  </Menu.Root>
  )
}

export function Trigger(props: TriggerProps) {
  return (
  <Menu.Trigger>
    {props.children}
  </Menu.Trigger>
  )
}
```

---

# Composition

A form of abstraction that is flexible and lean

```tsx
// @filename: Leads.tsx
import * as MyMenu from '@ui/prefab/Menu'
import { useMenu } from '@ui/primitives/Menu'

export function Leads(props: LeadsProps) {
  const [leads] = useCurrentLeads()
  const menu = useMenu()

  return (
    <MyMenu.Root>
      <MyMenu.Trigger>select lead</MyMenu.Trigger>
      <MyMenu.List>
        {items.map(it => <MyMenu.ListItem value={it} onSelect={menu.handleSelect}>{it.name}</MyMenu.ListItem>)}
      </MyMenu.List>
    <MyMenu.Root>
  )
}
```
---

# Composition

A form of abstraction that is flexible and lean

```tsx {all|7}
// @filename: Leads.tsx
import * as MyMenu from '@ui/prefab/MyMenu'
import * as Menu from '@ui/primitives/Menu'

function SpecificTrigger() {
  // some logic specific to leads
  return <Menu.Trigger />
}

export function Leads(props: LeadsProps) {
  const [leads] = useCurrentLeads()
  const menu = Menu.useMenu()

  return (
    <MyMenu.Root>
      <SpecificTrigger />
      <MyMenu.List>
        {items.map(it => <MyMenu.ListItem value={it} onSelect={menu.handleSelect}>{it.name}</MyMenu.ListItem>)}
      </MyMenu.List>
    <MyMenu.Root>
  )
}
```

---

# Modularity

```ts {maxHeight:'100px'}
module.exports = {
  // ...
  plugins: [
    plugin(function ({ addBase, addComponents, addUtilities, theme }) {
      addComponents({
        '.menu': {
          padding: theme('spacing.6'),
        },
        '.menu-primary': {
          backgroundColor: theme('color.white')
          '& .menu-trigger': {
            backgroundColor: theme('colors.primary.200'),
            '&:hover': {
              backgroundColor: theme('colors.primary.200'),
            }
          }
        },
        '.menu-trigger': {
          // ...
        }
      })
    })
  ]
}
```

---

# Modularity

Separation of style and substance

```tsx 
// @filename: prefab/Menu.tsx
import * as Menu from '@ui/primitives/Menu'
import clsx from 'clsx'

export function Menu(props: MenuProps) {
  const { variant = "primary" } = props
  // "unique" logic
  return (
  <Menu.Root className={clsx("menu", {
    "menu-primary": variant === "primary",
    "menu-secondary": variant === "secondary",
    "menu-ghost": variant === "ghost"
  })}>
    {props.children}
  </Menu.Root>
  )
}
```

---

# Modularity

Separation of style and substance

```tsx {7}
// @filename: Leads.tsx
import * as MyMenu from '@ui/prefab/MyMenu'
import * as Menu from '@ui/primitives/Menu'

function SpecificTrigger() {
  // some logic specific to leads
  return <Menu.Trigger className="menu-trigger" />
}

export function Leads(props: LeadsProps) {
  const [leads] = useCurrentLeads()
  const menu = Menu.useMenu()

  return (
    <MyMenu.Root>
      <SpecificTrigger />
      <MyMenu.List>
        {items.map(it => <MyMenu.ListItem value={it} onSelect={menu.handleSelect}>{it.name}</MyMenu.ListItem>)}
      </MyMenu.List>
    <MyMenu.Root>
  )
}
```

---
layout: iframe-right

url: https://daisyui.com/
---

# Mature example

This is a proven concept

<v-clicks>

  - Scales
  - Robust
  - Flexible
  - Fast

</v-clicks>

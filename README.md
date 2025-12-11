[![npm version](https://img.shields.io/npm/v/@itrocks/notifications?logo=npm)](https://www.npmjs.org/package/@itrocks/notifications)
[![npm downloads](https://img.shields.io/npm/dm/@itrocks/notifications)](https://www.npmjs.org/package/@itrocks/notifications)
[![GitHub](https://img.shields.io/github/last-commit/itrocks-ts/notifications?color=2dba4e&label=commit&logo=github)](https://github.com/itrocks-ts/notifications)
[![issues](https://img.shields.io/github/issues/itrocks-ts/notifications)](https://github.com/itrocks-ts/notifications/issues)
[![discord](https://img.shields.io/discord/1314141024020467782?color=7289da&label=discord&logo=discord&logoColor=white)](https://25.re/ditr)

# notifications

Visual management of a notification list: recent, hidden/visible, read/unread.

*This documentation was written by an artificial intelligence and may contain errors or approximations.
It has not yet been fully reviewed by a human. If anything seems unclear or incomplete,
please feel free to contact the author of this package.*

## Installation

```bash
npm i @itrocks/notifications
```

## Usage

`@itrocks/notifications` provides two DOM‑oriented helpers:

- `notification(item: HTMLElement)` – prepares and animates a single
  notification item when it is added to the list.
- `notifications(list: HTMLOListElement | HTMLUListElement)` – wires a
  whole list so that clicks toggle visibility of the list and the
  read/unread state of each notification.

You are expected to provide your own HTML structure (usually an
`<ol>`/`<ul>` list) and CSS. The package only manipulates classes such as
`new`, `visible`, and `read` that you can style however you like.

### Minimal example

```html
<ol id="notifications">
  <li>First notification</li>
</ol>
```

```ts
import { notification, notifications } from '@itrocks/notifications'

const list = document.getElementById('notifications') as HTMLOListElement

// Enable interactions (toggle visibility, read/unread)
notifications(list)

// Initialize the existing item (limit list size, mark as new, auto‑clear
// the "new" highlight after a few seconds)
notification(list.firstElementChild as HTMLElement)
```

### Complete example with dynamic items

In a typical application, notifications are added over time while the
user interacts with the system.

```html
<button id="notify">Add notification</button>

<ul id="notifications" class="notifications">
  <!-- items will be added dynamically -->
</ul>
```

```ts
import { notification, notifications } from '@itrocks/notifications'

const list   = document.getElementById('notifications') as HTMLUListElement
const button = document.getElementById('notify') as HTMLButtonElement

// Activate notification list behaviour
notifications(list)

let counter = 1

button.addEventListener('click', () => {
  const li = document.createElement('li')
  li.textContent = `Notification #${counter++}`

  list.prepend(li)

  // Apply notification behaviour to the newly added item
  notification(li)
})
```

With this setup:

- Clicking the list itself (`<ul id="notifications">`) toggles the
  `visible` class to show or hide notifications.
- Clicking an individual `<li>` toggles its `read` state and clears the
  `new` highlight.
- Only the five most recent notifications are kept; older ones are
  automatically removed when new items are added.

## API

### `function notification(item: HTMLElement): void`

Prepare and animate a single notification item when it is added to the
list.

#### Behaviour

- If the item is empty (no non‑whitespace `innerHTML`), it is removed
  from the DOM.
- The item is ensured to have a parent list; if there is no parent,
  nothing is done.
- The parent list is trimmed so that it never contains more than **five
  items**; older items are removed from the end.
- The CSS class `new` is added to the item, then automatically removed
  after about **7 seconds**. You can use this class to highlight fresh
  notifications.

#### Parameters

- `item` – the `<li>` (or similar) element representing the
  notification.

#### Return value

- `void` – all effects are applied directly to the DOM.

---

### `function notifications(list: HTMLOListElement | HTMLUListElement): void`

Attach click‑based behaviour to a notifications list.

#### Behaviour

Once initialised, the function adds a single `click` listener to the
provided list:

- **Show / hide list** – clicking directly on the `<ol>`/`<ul>` element
  toggles the `visible` class on that list. You can style this to expand
  or collapse the notification area.
- **Mark as read / unread** – clicking any element inside the list will
  find the closest parent `<li>` and toggle its read state:
  - if the item already has the `read` class, it is removed (the
    notification becomes unread again),
  - otherwise, `read` is added and `new` is removed.

#### Parameters

- `list` – the notifications container (`<ol>` or `<ul>`) that holds
  the individual notification items.

#### Return value

- `void` – behaviour is attached via DOM events.

## Typical use cases

- Implement a small notification drop‑down in a web application header
  that shows recent events (new messages, system alerts, etc.).
- Highlight the latest notification with a temporary visual effect using
  the `new` class, without manually handling timers.
- Keep the interface clean by automatically limiting the list to the
  most recent five notifications.
- Provide a simple read/unread toggle for each notification item just by
  clicking it, without wiring individual event handlers.
- Control the visibility of the entire notifications panel using a
  single `visible` CSS class.

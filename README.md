# Change Detection Deep Dive

A hands-on Angular 18 app for exploring **when** and **why** components re-render. Each component exposes a `debugOutput` getter that logs to the console whenever its template bindings are re-evaluated, so you can correlate UI updates with change detection runs.

## What this project demonstrates

- **Zoneless change detection** — enabled in `main.ts` via `provideExperimentalZonelessChangeDetection()`. There is no `zone.js`-driven automatic patching of async tasks; updates flow from signals, events, and other explicit notification paths.
- **`ChangeDetectionStrategy.OnPush`** — used on the counter, messages, and child components. The root `AppComponent` keeps the default strategy for comparison.
- **Signals** — counter state (`count`), message list (`MessagesService`), and form input (`enteredText`) use Angular signals so updates are tracked precisely.
- **Component tree** — two feature areas side by side:
  - **Counter** — increment/decrement with a nested `InfoMessageComponent` (clickable info line).
  - **Messages** — add messages via a form; list reads from a root-injected `MessagesService`.

## Project structure

```
src/app/
├── app.component.*          # Root shell (default change detection)
├── counter/                 # OnPush + signals; includes info-message child
├── info-message/            # OnPush child used inside counter
└── messages/
    ├── messages.service.ts  # Signal-based message store
    ├── messages.component.*
    ├── messages-list/       # Displays allMessages()
    └── new-message/         # Form to add messages
```

## Experiments to try

1. **Open the browser console** and interact with the counter and messages UI. Watch which `[ComponentName] "debugOutput" binding re-evaluated.` lines appear.
2. **Increment/decrement** — see which components in the counter subtree refresh.
3. **Click the info message** — observe whether parent/sibling components re-check.
4. **Add a message** — trace updates through `NewMessageComponent`, `MessagesListComponent`, and ancestors.
5. **Wait ~4–5 seconds after load** — `CounterComponent` runs `setTimeout` callbacks (one sets `count` to the same value, one only logs). Compare what still triggers binding re-evaluation under zoneless + OnPush.

Debug lines are also rendered in the UI (`.debug-output` paragraphs) so you can see which sections updated without relying only on the console.

## Getting started

**Prerequisites:** Node.js and npm.

```bash
npm install
npm start
```

Open [http://localhost:4200/](http://localhost:4200/).

### Other scripts

| Command        | Description                    |
|----------------|--------------------------------|
| `npm start`    | Dev server (`ng serve`)        |
| `npm run build`| Production build to `dist/`    |
| `npm test`     | Unit tests (Karma + Jasmine)   |

## Stack

- Angular 18 (standalone components, no NgModules)
- TypeScript 5.4
- Experimental zoneless change detection
- RxJS (dependency; core demos use signals)

## Further reading

- [Angular change detection](https://angular.dev/guide/components/advanced-configuration#changedetectionstrategy)
- [Signals](https://angular.dev/guide/signals)
- [Angular CLI](https://angular.dev/tools/cli)

# Habit Tracker

A React application for recording daily habits and reviewing progress one week at a time. This project demonstrates components, state, hooks, shared data, forms, and browser storage.

## What the app does

- Add a habit by entering its name.
- Mark habits as completed or undo completion for today or a past date.
- View a Monday-to-Sunday week and navigate to earlier weeks.
- See how many habits are completed today.
- Display a streak of consecutive completed days ending today. If today is incomplete, the streak is zero.
- Delete habits you no longer want to track.
- Keep habits after refreshing the page using browser local storage.

Future dates are disabled, and week navigation stops at the current week. Data stays in the current browser; there is no backend, account system, or device sync. Clearing the browser's site data removes saved habits.

## What we used and why

| Tool | Purpose in this project |
| --- | --- |
| React | Builds the interface from reusable components and updates it when state changes. |
| TypeScript | Defines habit data, component props, and function parameters to catch mistakes during development. |
| Vite | Runs the development server, updates the page during development, and creates the production build. |
| Tailwind CSS | Styles layout, spacing, colours, and button states through utility classes in JSX. |
| date-fns | Calculates week boundaries, formats dates, compares calendar days, and helps calculate streaks. |
| tailwind-merge | Resolves conflicting Tailwind classes when custom styles are passed to the reusable Button component. |
| Browser localStorage | Saves habits between visits without needing a database. |
| ESLint | Checks TypeScript and React code for common mistakes and hook rule violations. |
| React Compiler | Enabled through the Vite/Babel configuration to automatically optimize supported React code. |

## Main concepts used

### 1. Components and JSX

The interface is divided into small functions that return JSX:

- `App` arranges the page and selects the visible week.
- `Header` shows today's progress and week navigation.
- `HabitForm` handles adding habits.
- `HabitList` and `HabitItem` display habits and daily completion buttons.
- `Button` provides shared styling with primary, secondary, and delete variants.

This separation makes each part easier to understand, reuse, and change.

### 2. Props and callbacks

Props pass information from a parent component to a child. For example, `App` passes `visibleDates` to `Header` and `HabitList`. It also passes `onPrev` and `onNext` callbacks so the header can request a week change.

### 3. State with useState

State stores values that can change and cause the interface to update. `App` stores the selected week offset, and `HabitForm` stores the input text. Habit data is managed by a custom storage hook.

### 4. Controlled forms and events

The habit input uses `value` and `onChange`, so React controls its text. Submitting the form calls `preventDefault()` to avoid a page reload. Empty or whitespace-only names are rejected, and the input clears after adding a habit.

### 5. Context and custom hooks

`HabitProvider` shares habits and the `addHabit`, `toggleHabit`, and `deleteHabit` functions with its descendants. Components access them through `useHabits()` instead of passing the same data through every intermediate component.

`useLocalStorage<T>()` is a reusable custom hook that combines React state with browser storage. Its generic type parameter lets the caller specify the shape of the saved data.

### 6. Effects and cleanup

`useLocalStorage` uses `useEffect` to save data whenever the stored value or storage key changes.

`App` also contains a demonstration effect that logs the current week offset when the document is clicked. It removes the listener during cleanup before the effect runs again or the component unmounts.

### 7. Immutable updates and array methods

Habit updates create new arrays and objects instead of modifying existing state directly:

- Spread syntax adds a habit or completion date.
- `map()` updates the matching habit and renders lists of components.
- `filter()` removes habits or completion dates and counts completed habits.
- `some()` checks whether a habit was completed on a particular day.

Functional updates such as `setHabits(current => ...)` calculate the next value from the previous state. Each habit gets an ID from `crypto.randomUUID()`, which is also used as its React list key.

### 8. Conditional rendering and derived data

The app displays an empty-state message when there are no habits, shows a streak only when it is nonzero, and changes button styles according to completion status.

Today's completion count, the visible dates, and streaks are calculated from existing data rather than stored as separate state.

### 9. Saving and restoring dates

Habits are saved under the `Habits` local-storage key with `JSON.stringify()`. JSON converts dates to strings, so a custom reviver passed to `JSON.parse()` uses `parseISO()` to restore date values when loading them. If reading or parsing storage fails, the hook starts with an empty habit list.

### 10. TypeScript types and reusable props

The `Habit` type describes each record:

```ts
type Habit = {
  id: string
  name: string
  completions: Date[]
}
```

Component prop types describe the data each component needs. `Button` extends `ComponentProps<"button">`, allowing normal button attributes such as `disabled`, `onClick`, and `children` alongside its custom `variant` prop.

## Project structure

```text
src/
  App.tsx                    Page layout and selected week
  main.tsx                   React entry point and CSS import
  index.css                  Tailwind import and global colours
  components/
    Button.tsx               Reusable button variants
    HabitForm.tsx            New habit input and submission
    HabitList.tsx            Habit cards, completion buttons, and streaks
    Header.tsx               Today's progress and week navigation
  context/
    HabitProvider.tsx        Shared habit state and update functions
    useHabits.ts             Habit types, context, and access hook
  hooks/
    useLocalStorage.ts       Persistent state and date restoration
```

## Run locally

With Node.js and npm installed, open a terminal in the project directory and run:

```bash
npm install
npm run dev
```

Open the local URL printed in the terminal. Save edited files to trigger development updates, and keep the server running while working. Press **Ctrl+C** in that terminal to stop it.

## Other commands

| Command | What it does |
| --- | --- |
| `npm run build` | Checks TypeScript and creates the production files in `dist/`. |
| `npm run preview` | Serves the production build locally after running the build command. |
| `npm run lint` | Checks the code with ESLint. |


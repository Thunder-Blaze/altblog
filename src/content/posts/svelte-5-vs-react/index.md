---
title: Getting Started with Svelte 5 (and Why You Might Not Need React)
published: 2025-08-24
image: 'cover.jpg'
description: A deep dive into the structure,  reactivity, runes, stores, and load functions in Svelte 5, and how it compares to React.
tags: [ svelte, react, javascript, frontend ]
category: Frameworks
draft: false
---

# Getting Started with Svelte 5: Why It Feels Different from React

For most developers, the path into modern frontend development goes something like this: you learn a little HTML and CSS, sprinkle in some JavaScript, then get told that *“if you want to be taken seriously, you need React.”*

React has become the default. Its component-based structure, hook system, and virtual DOM reconciliation process have shaped the way millions of developers think about web interfaces. But that dominance also means many developers assume React’s structure is simply *how frontend frameworks work*.

Svelte - especially in its latest version, **Svelte 5** - politely disagrees. Its structure, reactivity model, and approach to state management are designed around a compiler-first philosophy rather than a runtime-first philosophy. To really appreciate why that matters, let’s unpack what these frameworks are actually made of.

---

## The Structure of a React Application

In React, an application is a tree of **components**, each represented by a function (or class, historically). Components return a description of UI in JSX, which React turns into a virtual DOM tree. Whenever state changes, React re-renders the component, compares the new virtual DOM with the old one, and applies minimal updates to the real DOM.

This means React’s structure has three major layers:

1. **Component Functions** - the building blocks, each describing what the UI should look like.
2. **Hooks and State Management** - `useState`, `useReducer`, `useContext`, and a host of third-party libraries that hold and manage application state.
3. **Runtime Reconciliation** - the virtual DOM diffing algorithm that determines how to update the actual DOM based on state changes.

The advantage of this structure is familiarity and flexibility: every piece is declarative, and React handles the mechanics of change. The disadvantage is weight. Every app ships React itself plus the runtime that makes reconciliation work, even if you are only building a simple counter.

---

## The Structure of a Svelte 5 Application

Svelte flips the model. Instead of carrying a runtime into the browser, **Svelte compiles components into plain JavaScript at build time**. This alters the structure of an application in a fundamental way:

1. **Components** are still the unit of composition, but instead of returning virtual DOM, they are compiled into direct DOM operations. When you declare `<button>{count}</button>`, the compiler generates the exact DOM update instructions needed to increment the text when `count` changes.
2. **Runes** replace hooks. A rune like `$state(0)` declares state. A rune like `$derived(expr)` computes a value from other state. `$effect(() => { ... })` defines what React would call a “side effect” - code that runs in response to data changes but doesn’t itself produce data for the UI. Importantly, because runes are compile-time hints, the compiler wires dependencies automatically.
3. **Reactivity** is not a runtime guessing game. In React, you declare dependencies manually in `useEffect` or `useMemo`. Forgetting or mis-declaring them is a common source of bugs. In Svelte, reactivity is **fine-grained and explicit**: if `count` is used inside `$derived` or `$effect`, the compiler knows exactly when to update.

This structure means a Svelte 5 application has no virtual DOM, no reconciliation layer, and significantly less runtime code. What ships to the browser is essentially optimized vanilla JavaScript customized for your component tree.

---

## What is Reactivity ?

The term **“reactivity”** describes how a framework updates the UI when data changes.  

In **React**, reactivity is powered by state and re-renders. You call `setState`, React re-runs the component, then the Virtual DOM decides what needs updating. This works well, but every change re-executes the component function, even if only one value changed.  

In **Svelte**, reactivity is built into the compiler. Assigning to a variable (`count++`) directly updates only the DOM nodes that depend on it. No Virtual DOM, no component re-runs - just precise updates.  

The difference is subtle but important: React reactivity is **runtime-driven**, while Svelte reactivity is **compile-time optimized**.

---

## What Side Effects Actually Mean

The phrase **“side effect”** gets thrown around casually in frontend development, but let’s pin it down. A *side effect* is any action your code performs that reaches outside of its own scope and alters something external. Examples include logging to the console, updating localStorage, starting an interval timer, or making a network request.

In React, side effects live in `useEffect`. You declare dependencies, React runs the effect when they change, and you may return a cleanup function. In practice, this can be error-prone: forgetting dependencies leads to stale values, while adding too many creates unnecessary re-runs.

In Svelte 5, side effects live inside `$effect`. The difference is subtle but important: the compiler analyzes your effect and automatically tracks which values it depends on. You do not manually declare dependencies, so there is less room for mistakes. Moreover, because `$effect` is reserved only for side effects, not computations, it reduces the temptation to use it as a catch-all. Computed values belong in `$derived`, keeping data transformations pure and effects truly external.

---

## State and Data Across the App

Both frameworks need a way to manage state across components. In React, this typically means `useContext` or an external state library like Redux, Zustand, etc. In Svelte, the answer is **stores**.

A store is simply a reactive object with a `subscribe` method. Svelte provides a small set out of the box:

* **`writable`**: holds a value that can be updated.
* **`readable`**: holds a value but can only be updated internally (useful for derived or system state).
* **`derived`**: computes a new store based on others.

The compiler adds sugar: prefixing a store variable with `$` inside a component automatically subscribes and unsubscribes it. That’s why you can write `{$count}` inside a template and get the live value.

The line between runes and stores is clear: runes are for *local component state*, stores are for *shared global or cross-component state*.

---

## The Role of Data Loading

One area where SvelteKit - the application framework around Svelte - diverges sharply from React’s ecosystem is **data loading**. In React, data loading conventions vary by framework. Next.js, for example, uses server components, route handlers, and `getServerSideProps` or `getStaticProps` (depending on the version you’re working with). It’s powerful but can feel fragmented.

SvelteKit simplifies this with a single concept: the **`load` function**. Every route or layout can export a `load` function, which fetches or computes data before rendering. Crucially, there are two flavors:

* **`+page.ts` / `+layout.ts`**: runs on both client and server, perfect for public data or anything that must survive client-side navigation.
* **`+page.server.ts` / `+layout.server.ts`**: runs only on the server, ideal for accessing databases, filesystems, or secret environment variables.

This clear separation removes guesswork. When you read a SvelteKit codebase, you know exactly what runs on the server and what may run in the browser.

---

## A Practical Comparison

Let’s compare two mental models.

**React’s Model**

* Structure: components return virtual DOM, React runtime diffs and updates real DOM.
* State: hooks manage local state; external libraries manage global state.
* Side effects: `useEffect` runs external code, with manually declared dependencies.
* Data loading: depends on framework, with patterns that vary between Next.js, Remix, and others.

**Svelte 5’s Model**

* Structure: components compile into direct DOM operations; no runtime reconciliation layer.
* State: runes (`$state`, `$derived`, `$effect`) handle local reactive state and side effects.
* Shared state: stores (`writable`, `readable`, `derived`) provide reactive values across components.
* Data loading: SvelteKit’s `load` functions, explicit and scoped to routes.

This difference in structure is not just academic. It changes the weight of the code you ship, the likelihood of bugs from dependency mismatches, and the mental overhead required to understand what your app is doing at any given time.

---

## Why This Matters

Choosing between React and Svelte is not about loyalty, it’s about trade-offs. ***React*** gives you the safety of an enormous ecosystem, countless libraries, and a familiar structure used by millions of developers. ***Svelte 5*** offers a compiler-driven model that produces smaller bundles, faster updates, and code that directly mirrors your mental model of reactivity.

If you want predictability, React is the well-traveled highway. If you want efficiency, simplicity, and a more expressive syntax, Svelte is the smaller, faster road that feels almost suspiciously smooth.

The real takeaway is not that one will “kill” the other - React’s ecosystem is too large for that. The takeaway is that Svelte offers a different structure, one where reactivity is explicit, side effects are cleanly separated, and the compiler takes on more of the work so the browser can do less.

---


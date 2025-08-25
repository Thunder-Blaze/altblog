---
title: Getting Started with Svelte 5 (and Why I switched from React)
published: 2025-08-24
image: 'cover.jpg'
description: A deep dive into the structure, reactivity, runes, stores, and load functions in Svelte 5, and how it compares to React.
tags: [ svelte, react, javascript, frontend ]
category: Frameworks
draft: false
---

# Introduction

So, most developers start their web development journey with the common roadmap of HTML (Hyper Text Markup Language) -> CSS (Cascading Style Sheets) -> JS (JavaScript). And they stop there, the ones serious about Web Development put their foot into the swamp of React and I was one of them too.

[React](https://react.dev), developed by Meta itself, is the most popular framework for creating web applications. Its component-based structure, hook system, and [virtual DOM](https://legacy.reactjs.org/docs/faq-internals.html) reconciliation process have shaped the way millions of developers think about web interfaces. But that dominance also means many developers assume React’s structure is simply *how frontend frameworks work*.

It all started when I was finding ways to create a higly fast and efficient Anilist Client (Zafkiel) and at that moment I came accross Svelte which was known for its blazing fast speed boasting a feature rich compiler that did almost everything that you don't want to deal with.

[Svelte](https://svelte.dev/) - A Framework that is spreading like wildfire. Created By [Rich Harris](https://github.com/rich-harris). Its structure, reactivity model, and approach to state management are designed around a compiler-first philosophy rather than a runtime-first philosophy. To really appreciate why that matters, let’s dive into the details.

# Structure of a React Application

In React, the application is a tree of **components** starting from the root and all the way to the leaf nodes. A component in react is generally a function (class in past)  which returns a subtle mix of HTML and Javascript namely **JSX**. React uses this output to create what we so call [**Virtual DOM**](https://legacy.reactjs.org/docs/faq-internals.html), whenever state changes react uses its diffing algorithm to compare the virtual DOMs and selectively update/re-render only the components which have recieved change in real DOM.

So, React’s structure has three major layers:

1. **Components** - the fundamental building block of the application, describing structure and look of the UI.
2. **Hooks and States** - `useState`, `useReducer`, `useContext`, and a other third-party libraries that hold and manage application state
3. **Runtime Reconciliation** - the virtual DOM diffing algorithm that determines how to update the actual DOM based on state changes.

The advantage of this structure is familiarity and flexibility. The disadvantage is it's weight. React handles the mechanics of change for you but at the cost of shipping the runtime that makes reconciliation work. Even if you are only building a simple counter, it still sends the whole runtime to the browser.

---

# The Structure of a Svelte 5 Application

Svelte really changed the game, rather than requiring a runtime for the identification of changed Nodes and selective re-render of those components, **svelte compiles its components into plain javascript at build time** which is totally 360 of React in way.

Parts of the Svelte's Structure:

1. **Components** are still the unit of composition, but instead of returning virtual DOM, they are compiled into direct DOM operations. When you declare `<button>{count}</button>`, the compiler generates the exact DOM update instructions needed to increment the text when `count` changes.
2. **Runes** replace hooks. A rune like `$state(0)` declares state. A rune like `$derived(expr)` computes a value from other state. `$effect(() => { ... })` defines what React would call a "side effect" - code that runs in response to data changes but doesn’t itself produce data for the UI. Importantly, because runes are compile-time hints, the compiler wires dependencies automatically.
3. **Reactivity** is not a runtime guessing game. In React, you declare dependencies manually in `useEffect` or `useMemo`, *(Introduction of **React Compiler** did fixed some things but it needs more work)*. Forgetting or mis-declaring them is a common source of bugs *(I just faced them a while ago and believe me you don't want to deal with them)*. In Svelte, reactivity is **fine-grained and explicit**: if `count` is used inside `$derived` or `$effect`, the compiler knows exactly when to update.

This structure means a Svelte 5 application has no virtual DOM, no reconciliation layer, and significantly less runtime code. What the browser gets is essentially optimized vanilla JavaScript customized for your component tree.

---

## What is Reactivity ?

The term ***"reactivity"*** describes how a framework updates the UI when the data changes.  

In **React**, reactivity is powered by state and re-renders. You call `setState`, React re-runs the component, then the Virtual DOM decides what needs updating with its diffing algorithm. This works well, but every change re-executes the component function, even if only one value changed which is very frustating when you're calling an API, you'll need to properly manage it and use caching or something to prevent additional caches.  

In **Svelte**, reactivity is built into the compiler. Assigning to a variable (`count++`) directly updates only the DOM nodes that depend on it. No Virtual DOM, no component re-runs - just precise updates.  

The difference is subtle but important: React reactivity is ***runtime-driven***, while Svelte reactivity is ***compile-time optimized*** (very less is left for the browser to do when using svelte, which is one of the reason svelte is much faster).

> Source: [Reactivity in Modern Web Development](https://medium.com/approved-tech/understanding-reactivity-and-state-management-in-modern-web-development-c3b7a354a515)

---

## What Side Effects Actually Mean

A **side effect** is any action your code performs that reaches outside of its own scope *(by this I mean it reaches out of the useEffect scope)* and alters something external. For example logging to the console, updating localStorage, starting an interval timer, or making a network request.

In React, side effects are contained in `useEffect`. You declare dependencies, React runs the effect when they change, and you may return a cleanup function. This is very error-prone in practice: forgetting dependencies leads to stale/unchanged values *(The most dangerous part is forgetting even writing the empty array [], if this happens then it keeps re-running on and on)*, while adding too many creates unnecessary re-runs.

In Svelte 5, side effects are inside `$effect`. The difference is subtle but important: the compiler analyzes your effect and automatically tracks which values it depends on. You do not manually declare dependencies, so there is less room for mistakes *(Almost no room)*. Moreover, because `$effect` is reserved only for side effects, not computations, it reduces the cases in which value is computed inside `$effect`. Computed values belong in `$derived`, keeping data transformations and effects isolated but still connected.

> Source: [`$effect`](https://svelte.dev/docs/svelte/$effect) and [`useEffect`](https://react.dev/reference/react/useEffect)

---

# State and Data Across the App

Both frameworks need a way to manage state across components. In React, this generally means using `useContext` API or an external state library like [Redux](https://redux.js.org/), [Zustand](https://zustand.docs.pmnd.rs/), etc but Svelte, gives us the inbuilt solution i.e. the **stores**.

A store is simply a reactive object with a `subscribe` method *(Subscribe just refers to the 'change this when the store value changes')*. Svelte provides a 3 types of stores by default (more can be made easily):

- **`writable store`**: contains a value that can be updated and read.
- **`readable store`**: contains a value that can be read but can only be updated internally (useful for derived or system state).
- **`derived store`**: computes a new store based on others.

One of the things that I like is that there is very less boilerplate code compared to Redux, etc and prefixing a store variable with `$` inside a component automatically subscribes and unsubscribes it. That’s why you can write `{$count}` inside a template and get the live value.

The line between runes and stores is clear: runes are for *local component state*, stores are for *shared global or cross-component state*.

> Source: [stores](https://svelte.dev/docs/svelte/stores)

---

# The Role of Data Loading

## SvelteKit
[SvelteKit](https://svelte.dev/docs/kit/introduction) is a modern, full-stack framework built on top of Svelte that makes it easy to create fast web apps. It handles routing, server-side rendering, API endpoints, and static site generation all *(almost everything you might need)* in singular unified system.

One of the areas where SvelteKit differenciates highly from React’s ecosystem is **data loading**. In React, data loading conventions vary by framework. Next.js, for example, uses server components, route handlers, and `getServerSideProps` or `getStaticProps` (depending on the version you’re working with). It’s powerful but can feel fragmented.

SvelteKit simplifies this very easily with a single concept: the **`load` function**. Every route or layout can export a `load` function, which fetches or computes data before rendering *(SvelteKit also prerenders these load functions by default)*. They render on client or server depending on the filename:

* **`+page.ts` / `+layout.ts`**: runs on both client and server, perfect for public data or anything that must survive client-side navigation.
* **`+page.server.ts` / `+layout.server.ts`**: runs only on the server, ideal for accessing databases, filesystems, or secret environment variables.

This clear separation improves readability and understanding. When you read a SvelteKit codebase, you know exactly what runs on the server and what may run in the browser.

> Source: [load functions](https://svelte.dev/docs/kit/load)

---

These differences in structure affect a big deal. It changes the weight of the code you ship, the possibility of bugs from dependency mismatches, and the brain damage required to understand what your app is doing at any given time.

---

# Why All This Matters

Choosing between React and Svelte is not about loyalty, it’s about trade-offs. ***React*** gives you the safety of an enormous ecosystem, countless libraries, and a familiar structure used by millions of developers. ***Svelte 5*** offers a compiler-driven model that produces smaller bundles, faster updates, and code that directly mirrors your mental model of reactivity.

If you want predictability, React is the well-traveled highway. If you want efficiency, simplicity, and a more expressive syntax, Svelte is the smaller, faster road that feels almost suspiciously smooth.

The real takeaway is not that one will "kill" the other - React’s ecosystem is too large for that. The takeaway is that Svelte offers a different structure, one where reactivity is explicit, side effects are cleanly separated, and the compiler takes on more of the work so the browser can do less.

> Both React and Svelte documentations are really good for learning and the final choice still depends on you, for creating big applications with minimal effort using pre-made packages is doable in React while in Svelte you might need to do what those libraries are doing yourself because of low popularity but its steadily increasing.

Point is, after using Svelte in one of my own projects *(mentioned below)* I have come to love it, and I don't think I'll be going back to React anytime soon until I require React's large ecosystem and Zafkiel can surivive without that or so I belive, only time will tell if I will switch to React if the complexity of the things I'm creating becomes very high (in those cases I'd prefer using pre made packages) but I'll be avoiding doing that while sticking with svelte as long as I can.

---

Check out a blazing fast site made in Svelte - [Rasiel](https://rasiel.thunderblaze.tech)
This site doesn't even have caching, just see its speed and loading times.

::github{repo="E31n/Rasiel"}

---
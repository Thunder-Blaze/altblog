---
title: Zafkiel - An Anilist Client
description: My journey building Zafkiel, a lightweight and customizable anilist client with Svelte, Shadcn, Tauri, and Rust.
published: 2025-08-24
image: 'cover.jpg'
category: Projects
tags: [ svelte, rust, tauri, anilist ]
draft: false
---

# Zafkiel - Building a Native Anilist Client with Svelte, Tauri, and Rust

![Preview](preview.png)

Over the past few months, I’ve been working on a project very close to my heart: **Zafkiel**, a desktop anilist client built with **Svelte, Tauri, and Rust**. The goal has always been simple yet ambitious - to create a fast, lightweight, and customizable app for browsing and tracking anime, one that feels **native, modern, and open to the community**.

Zafkiel is at a stage where I can reflect on why I started it, the choices I made, and the direction it’s heading.

---

## Why Zafkiel?

Existing anime tracking platforms and apps are powerful, but I often found them lacking in either **performance, design flexibility, or openness**. Many desktop clients are simply Electron wrappers, which work but can feel heavy and slow.

I was also inspired by the creative minds behind the creation of **Dantotsu**, an Android application for watching anime, reading manga and light novels with **multi-sourcing**, **extensions** for more features, etc. Sadly it was taken down recently by DMCA but many of its forks are still there. And I also came across a cross-platform Dantotsu ([**Dartotsu**](https://github.com/aayush2622/Dartotsu)) but it was still in much development and it used flutter so the app was really slow and bad in performance.

That is why I decided to create my own client with the following features in mind:

* **Lightweight** - fast startup and minimal resource usage.
* **Beautiful** - a modern design with multiple themes, fully customizable.
* **Community Friendly** - easy to modify, extend, and contribute to.
* **Cross-platform** - consistent performance on Linux, Windows, and macOS.
* **Multi-Sourced** - should have multiple sources and switching between them (*I'll expand on this further later*).
* **Extensible** - should provide features like extensions which can be installed or uninstalled.

That vision became [**Zafkiel**](https://date-a-live.fandom.com/wiki/Zafkiel) - named after Kurumi Tokisaki’s angel in [***Date A Live***](https://www.imdb.com/title/tt2575684/) (*My favorite character btw from this Series*), symbolizing control over time. It felt like the right metaphor for an app meant to give users **control over their anime experience**.

---

## Choosing the Stack

### Frontend: Svelte + Shadcn & Tweakcn

I chose **Svelte** for the frontend because of its **speed, simplicity, reactivity, and developer experience**. Unlike heavier frameworks, Svelte compiles away much of the overhead, leaving a lean and efficient app. This makes UI development **faster and more intuitive**, letting me focus on design and user experience.

To bring flexibility to the design, I integrated [**Shadcn**](https://shadcn-svelte.com/) & [**Tweakcn**](https://tweakcn.com/) (*Shadcn Themes*), using which I enabled **multi-theme functionality** and **consistent design**. With it, users can seamlessly switch between themes, creating a personalized experience without bloated complexity in the codebase.

### Backend: Rust + Tauri

For the backend, I went with **Rust**. Rust not only ensures **safety and performance**, but it also makes Zafkiel highly modular. The backend is structured in a way that keeps each part of the logic isolated, easy to read, and easy to extend.

This modularity has another major advantage: **contributing is accessible to others**. Developers can dive into specific parts of the codebase without needing to understand everything at once. Whether it’s improving caching, adding API integrations, or refining error handling, contributions are straightforward.

With **Tauri** acting as the bridge between the frontend and backend, the result is a **native cross-platform application** that consumes only a fraction of the resources of an Electron-based app, while maintaining strong security guarantees.

#### Multi Sourced Feature

This is yet to be implemented, I am planning to implement this along with the extensions. **Multi-Sourced** refers to the ability to choose between the providers of Anime/Manga. You must've heard of websites for watching anime like *hianime*, *aniwatch*, etc and even *torrents* too. I want to provide the user the ability to choose which source to watch from because of various factors:

* It is not necessary for the site I am using to be available in the user's region.
* Some sites have reduced quality but lower size while other offers higher sizes but high quality.
* There's also the thing called hard subtitles (embedded in video) and soft subtitles (embedded in the file like `mkv` but not in the video).

#### Extensions

This multi-sourcing feature is planned to be enabled using these so called extensions. I want to provide each site as optional extensions as in Dantotsu. But extensions won't stop there, they can provide more features such as torrenting, downloading, cli integrations and much more. I'll be keeping the repository separate though because of **DMCA**. More and more sites can be added and easily integrated as extensions as all of them will follow the same structure and responses.

---

## **Current State**

At the moment, Zafkiel is still in **basic development**.  
Some core features are working, but it’s far from stable or feature-complete.  

Right now, I’m focusing on:  

- Building out the **core UI** with Svelte and Tailwind.
- Structuring the **Rust backend** for modularity and clean APIs (*need to rework on this*).  
- Refining how I use **TanStack Query** for AniList API calls.  

It’s not something I can use daily yet, but the foundation is being laid carefully.  
My approach is **slow but steady** - making sure I get the architecture right before layering on more features.  

---

## **Looking Ahead**

The roadmap for Zafkiel includes:  
 
- **Polished UI/UX** with multiple themes and smooth transitions (*Yes, it needs more work*).
- **Cross-platform support** to ensure consistency on Linux, Windows, and macOS (*Even android support is coming soon*).
- **Multi-source streaming and reading support** using extensions and also torrenting support.
- **Extensibility** so contributors can add new features or integrations easily.
- **More components** for streaming anime, reading manga, reading light novels, etc.
- **Documentation site** (*Yes, this is very important for contributors and even for users*)

I’m not rushing this project - the goal is to **build it right, not just fast**.  

---

## **Conclusion**

Zafkiel is still a work in progress and there is a long way to go before it goes into production, but it represents my vision of what an anilist client should be: **lightweight, customizable, and faster**.  

With **Svelte** powering the UI, **Tweakcn** providing theming flexibility, **TanStack Query** handling data-fetching experiments, and a **modular Rust backend** ensuring performance and stability, Zafkiel is slowly but surely becoming the client I’ve always wanted.  

It’s not production-ready yet, but the journey has only just begun - and I’m excited to see where it goes from here.  

---

---
title: A start to a new learning experience - OSPP 2025
date: 2025-05-20
tags: ["post", "ospp"]
layout: post.njk
permalink: "blog/ospp/pre-selection/index.html"
---

## Finding an appropriate project

When I started looking out for projects, I knew one thing for sure - I didn’t want to go with something familiar. I had already spent a good amount of time working with FreeRTOS and Zephyr RTOS in projects. While both were great learning experiences, I was itching to try something new.

I came upon Embox RTOS, had a look at its projects and found - [Adding standard device tree support to Embox RTOS](https://summer-ospp.ac.cn/org/prodetail/259c00210?lang=en&list=pro). I had already worked with devicetree overlays of Zephyr RTOS so thought this could be interesting. Had a closer look at what was to be done and approached my go-to senior☺️, [Zain](https://www.linkedin.com/in/zain-siddavatam/), for a review on the project and finalized this one.

## Diving deep into Embox RTOS

Next step was to delve deeper into understanding the working of Embox RTOS. I had already had a word with the mentor Damon and Embox maintainer Anton, and they had given a few getting-started suggestions.

I started with solving a [Good First Issue – PR #3602](https://github.com/embox/embox/pull/3602), gained hands-on with
Embox’s conventions for code style and the use of test cases.

Simultaneously, I had started looking at how device tree support can be implemented.

Contributed little more to the codebase and each step helped me learn something new about Embox:
- Through [PR #3610](https://github.com/embox/embox/pull/3610), where I updated the mkdir command’s usage of the -v flag, I gained understanding of how shell commands are defined, their argument parsing is handled, and how the filesystem commands are supported across various platforms and projects.
- Merged PR for a minor fix - [PR #3624](https://github.com/embox/embox/pull/3624)
- While contributing [PR #3641](https://github.com/embox/embox/pull/3641) for a TODO in the IPv4 header, I explored the structure of Embox's network stack.
- While working on [Issue #3171](https://github.com/embox/embox/issues/3171) which involved implementing the rmdir command, I deepened my understanding of the filesystem in Embox. This led me to explore how filesystems are mounted and managed in Embox, via an NFS server or using USB in QEMU. Haven't sent a PR yet, will do it anytime soon😂.

### The Project

Since Embox didn't have Device Tree support yet, I needed to learn the whole toolchain from scratch. Here's what I explored:
- `dtc`: The compiler that turns `.dts` (Device Tree Source) into .dtb (Device Tree Blob).
- `fdtdump`: A handy tool to reverse `.dtb` files back to a readable format.
- `libfdt`: The C library that lets you parse .dtb files at runtime.

To test it out, I wrote a minimal C program that:
- Embedded a `.dtb` file in memory
- Used `libfdt` to traverse the tree
- Printed out node and property information

Next, I dug deeper into how platform support is tied to the build system. I found that:
- Mybuild directly includes board-specific source files (for init logic).
- There’s no clear separation between “what hardware exists” and “how to initialize it.”

My proposal idea began to take shape here. I wanted to:
1. Let Mybuild continue managing build-time options like which features to compile.
2. But handle hardware description and initialization at runtime using a parsed `.dtb`.

All of this was just during the pre-selection phase! I’ve learned a lot, not just about Device Trees and libfdt, but also about how to reason about build systems, hardware abstraction, and what makes an RTOS modular and extensible.

On June 28th, the results were announced and YES, I was selected!
Further blogs will show up on my page, stay tuned! It’s going to be a wild ride. Let’s go!🚀

(PS: You can find my Proposal in the Project Link above)
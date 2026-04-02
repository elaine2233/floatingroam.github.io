---
layout: post
title: "Vibe Coding: When Your Pain Point Becomes Your Product"
date: 2026-04-02
author: floating roam
categories: [tech, AI, personal growth]
tags: [Vibe Coding, AI development, product manager, indie dev]
excerpt: "How I built a xun score converter using AI Studio, Cursor, and Figma—and learned that the right tool sequence matters more than coding skills."
image: /assets/posts/vibe-coding/2026-04-02-vibe-coding-product.png
version: 11
---

# Vibe Coding: When Your Pain Point Becomes Your Product

![Xun]({{ site.baseurl }}/assets/posts/vibe-coding/2026-04-02-vibe-coding-xun.jpeg)

The xun has a unique sound—deep, distant, like wind from ancient times.

The first time I heard it, something struck me. Not the sound itself, but what lay behind it: ancientness, loneliness, some emotion I couldn't name. I wanted to learn it. So I bought a xun, found sheet music, and eagerly started playing.

Only to discover I couldn't play at all.

The problem wasn't the instrument. It was the music. Most xun scores online use numbered notation—1234567. I can read numbers. But which finger covers which hole? Which number maps to which fingering? The scores don't say. Without a fingering chart, I was lost—like holding a map with no coordinates.

I searched everywhere. No tool existed to convert numbered notation into fingering charts.

That's when it hit me: why not build one myself?

But I don't know frontend.

---

## A Backend Engineer's Frontend Problem

I've worn many hats—AI engineer, data scientist, AI architect, project manager, consultant. Python is my old friend. Data pipelines, machine learning models, system architecture—these I understand.

But frontend? That's foreign territory.

React, Vue, CSS, DOM—I've heard the words. But ask me to write them? I couldn't build a decent button. Before, I had two options: ask a frontend colleague and owe a favor, or grind through learning and burn time.

But now things are different. We're in the era of Vibe Coding—describe what you want, and AI writes the code. So I gave it a shot.

---

## AI Studio: Enthusiastic but Forgetful

I started with Google AI Studio.

It was enthusiastic. "Help me build a xun score converter," I said. It spat out code immediately, explaining each step. I was stunned—frontend was this easy?

Then things went sideways.

"Add a bilingual toggle," I said. Done. Next round: "Add a settings page for password changes." It did—but only in one language. The bilingual feature? Forgotten.

"Make this page non-scrollable, shrink the font if needed," I said. Done. Next round: the layout broke on resize, I asked it to fix it, and it added the scrollbar back.

It was like an enthusiastic intern with amnesia—remembering only what you just said, forgetting everything before. After multiple rounds, my project became a patchwork: fix one thing, break another.

Backend was worse. I asked it to add backend functionality. It did. The code wouldn't run—environment issues it hadn't considered. I gave up and stuck with pure frontend.

But AI Studio has strengths. It proactively suggests improvements: "Is this button too small?" "Users might not find this feature." It thinks about UX better than Cursor.

I learned: AI Studio is great for validating ideas quickly, not for iterative development. I needed something else.

---

## Cursor: Reliable, But No Eye for Design

I switched to Cursor.

Less enthusiastic, more reliable. Give it requirements, it remembers. Change requirements, it updates without breaking what works. Like an experienced programmer—quiet, competent.

And it handles backend automatically. Database, API, user authentication—all taken care of.

But Cursor has a blind spot: aesthetics.

The interfaces it builds work, but they're ugly. I described "clean modern style" and got a white page with black text, default gray buttons, awkward spacing. UX and interaction design? Not its strength—where buttons go, how users navigate, what needs confirmation.

I realized Cursor excels at implementation. If you've thought through the features, have requirements and designs ready, hand them to Cursor and it delivers. But expect it to design for you? That's asking too much.

Functionality worked. Interface existed. But ugly. What now?

---

## Figma: Where Design Finally Clicks

Then I tried Figma.

Same description—"clean modern style"—but the results were beautiful. Comfortable colors, proper spacing, clear interaction logic. One or two rounds of adjustments, and I had a solid prototype.

Figma needs no technique. Describe clearly, and it works. For aesthetics, nothing else comes close.

![Figma design mockup]({{ site.baseurl }}/assets/posts/vibe-coding/2026-04-02-vibe-coding-figma_sample.png)

But it's a design tool, nothing more. No backend, no databases, no user authentication. Those, I still had to build myself. Fair enough—that's not what it's for.

Piece by piece, using different tools, I assembled this product: frontend, backend, design. And somewhere along the way, my role shifted.

---

## From Coder to Product Thinker

I thought building a tool meant writing code. Input field, conversion logic, output display. Done.

I was wrong.

At first, I just wanted to solve my own problem: input notation, output fingerings, play the xun. But questions emerged: What if others use this with different-keyed xuns? Do beginners need slow practice mode? Should users save their converted scores?

I stopped thinking "tool for myself" and started thinking "product for users."

More questions followed: Login page? User data storage? Roles and permissions? Admin capabilities?

These aren't code problems. They're product problems.

I've never been a product manager. But building this, I started thinking like one.

---

## The Satisfaction of Building Something for Yourself

The tool is still in development, but the core works.

![Product interface]({{ site.baseurl }}/assets/posts/vibe-coding/2026-04-02-vibe-coding-product.png)

I input songs I've wanted to play—"A Laugh in the Sea," "Daughter's Love," "Big Fish"—watched the fingering charts appear, picked up my xun, and played note by note.

How to describe that feeling?

More satisfying than finishing a backend service. More satisfying than optimizing a model. More satisfying than delivering a client project.

Because this was mine. My idea. My build. My use.

---

## Tool Comparison

| Tool | Strengths | Weaknesses | Best For |
|------|-----------|------------|----------|
| AI Studio | Enthusiastic, proactive suggestions | Forgetful, messy code | Quick idea validation |
| Cursor | Reliable, remembers context, auto backend | Poor aesthetics | Feature implementation |
| Figma | Beautiful design, easy to use | No backend, design only | Prototyping |

---

## If I Could Start Over

Looking back, I made mistakes. If I'd known better, I would've spent less time on AI Studio and fewer detours.

**The key lesson: prototype first, code later.**

The right sequence: Figma for a good-looking prototype—not every feature needs to work, just get the interface right. Then export to Cursor, add features with detailed requirements.

I did it backwards. I expected AI Studio or Cursor to design for me. What I got was forgetfulness and ugliness. Then I learned: **each tool does one thing well.**

**What AI can't do: discover requirements.**

The xun is niche. AI doesn't understand it—how many holes, what notes they produce. AI excels at common things: e-commerce, blogs, admin panels. But for something without precedent, **requirements come from your own life and work.**

---

If you have a pain point, a "this tool should exist" idea: think through the problem first, where requirements come from. Then Figma for prototype, Cursor for features. Each tool does what it's good at.

AI is the tool. You're the product owner.

---

*Written mid-development, still iterating. Happy to discuss. Looking forward to launch.*

<h1 align="center">Frontend-Scaffolding</h1>

<p align="center">Design notes and implementation specs for a clinical-trial dashboard UI (Vite, React, Tailwind, MUI).</p>

## Why

This repo holds no application code. It is the written design work for a dashboard for Hummingbird Bioscience: a colour and component system, the principles behind it, and rounds of review feedback with the follow-up specs. The documents are written so a developer or a coding agent can build the UI from them.

## Contents

| File | What it covers |
| --- | --- |
| `design-system.md` | Colour tokens, MUI theme, StatCard and data-layer code. States the stack as Vite, React (JSX), Tailwind v3 and MUI. |
| `design-principles.md` | Why the system looks the way it does: which research to keep or drop, and the motion budget. |
| `feedback.md`, `feedback-patients.md` | Review of screenshots of the current build against the design docs, including the patient tracker and PFS pages. |
| `feedback_after_showcase.md` | Client feedback from a showcase on 2026-07-03: contrast and tables. |
| `research_improvements.md` | Round 2 spec for higher contrast, table cell visuals and loading fixes. Where it conflicts with the other docs, it says it takes precedence. |
| `research_swimmer_plot.md` | Conventions for a clinically correct swimmer plot and interactive features for a web version. |

`css/design-system.md` and `css/design-principles.md` are copies of the two files of the same name in the root.

## Status

Nothing here is runnable. The code in the documents is specification, not a tested package, and the dashboard it describes is not in this repo.

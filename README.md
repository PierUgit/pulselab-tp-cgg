# AI for Developers – Master AI to Code Faster
## Participant hands-on labs (common-thread project "pulselab")

Intra-company training for CGG Services SAS · Trainer: Daouda DIOP

This repository contains everything you need for the labs, day by day, plus reusable templates for your team.
No confidential data is used: the `pulselab` project works on simulated measurements.
The labs are written to work with any AI coding assistant; the examples and templates target **Kilo Code**.

## Structure

```
Jour1/
  AI_For_Developers_CGG_Services_SAS_Day1.pdf   <- Day 1 slide deck
  TP_Day1_Participant.html                      <- Day 1 lab sheet (open in a browser)
  pulselab_day1_starter.zip                     <- Day 1 Python starter project
  Prompt_Solutions_Day1.md                      <- model prompts for the lab steps (compare after you tried)

Jour2/
  AI_For_Developers_CGG_Services_SAS_Day2.pdf   <- Day 2 slide deck
  Cheat Sheet – English.pdf                     <- Kilo CLI commands cheat sheet
  TP_Day2_Participant.html                      <- Day 2 lab sheet
  pulselab_day2_starter.zip                     <- Day 2 Python starter project (clean reference state)
  Prompt_Solutions_Day2.md                      <- model prompts for the lab steps

AI_Coding_Best_Practices.md                     <- memo: good practices for coding with AI

templates/                                      <- reusable files to adapt in your team
  prompts/            ten prompt templates (explore, plan, root cause, bug fix, new function, tests, ...)
  kilo-project-kit/   kilo.jsonc (instructions + permissions), AGENTS.md, rules, agents, skills, slash commands
  multi-agent/        when several agents are worth it, subagents in Kilo Code, brief template, delegation prompt
  team/               charter, review checklist, AI journal, pilot plan, workflow card
  automation/         pre-commit hook with an advisory AI step, review criteria, CI draft
```

## Getting started

1. Get this repository (`git clone <url>`, or `git pull` if you already have it).
2. Open `Jour1/TP_Day1_Participant.html` in your browser (double-click: the page is self-contained, no network needed).
3. Follow lab 1.0: it walks you through unzipping `pulselab_day1_starter.zip` and setting up your Python environment.
4. On Day 2, do the same with the `Jour2/` folder (`git pull` first if you cloned earlier).

In the lab sheets you can **write your prompts directly in the page** (the yellow boxes, and an optional notepad on the AI steps).
Your prompts, ticked steps and notes are saved **locally in your browser** (not in this repository): keep using the same browser and the same file location.
Copy the finished prompt into your assistant with the *Copy my prompt* button.

## Rules recalled throughout the labs

- No confidential data, no secrets in prompts (the dataset is synthetic).
- Commit before any AI action that can edit files, and read every diff.

## Note on the templates

The Kilo Code files in `templates/` follow the official documentation as read in September 2026, and their syntax was validated by a script.
They were **not run inside Kilo Code** by their author: use the smoke test in `templates/kilo-project-kit/README.md` and check your installed version.

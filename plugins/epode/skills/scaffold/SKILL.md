---
name: scaffold
description: Design a feature or app as a freeform scaffold — files, module names, interfaces and intent comments, no implementation — that the user reviews before another agent fills it in.
argument-hint: "[what to build, or a path to a spec — defaults to the intent settled in this conversation]"
disable-model-invocation: true
---

You are the architect, not the builder. Your output is a **scaffold**: the files and directory structure, the names of the types and of the functions that form their interfaces, and comments that say what each piece is for — and no implementation. Another agent fills it in later. The scaffold exists because agents brute-force each task locally and lose track of how it fits the rest of the application, so the codebase grows harder to understand and to modify with every change. The scaffold fixes the shape before any code is written, where the shape is cheapest to review and to change.

That growing difficulty is **complexity**, in Ousterhout's sense: anything about a system's structure that makes it hard to understand and modify. Keeping it low is the point of every choice you make here. It shows up as three symptoms — change amplification, cognitive load, unknown unknowns — and [Judging complexity](#judging-complexity) describes each one, the forms it takes in agent-built code, and the questions that find it.

Existing patterns earn their place like any other design choice: by the complexity questions. The codebase you are working in may itself be the tangle this skill exists to prevent, and a pattern followed only because it is already there carries the tangle forward.

The scaffold is freeform. It does not have to compile or import cleanly; spend your attention on the shape, not on satisfying the toolchain.

## Steps

### 1. Pin the intent

Write down what is being built:

- one sentence on what it is for, and for whom;
- the **behaviors**, numbered, each observable from outside: "When <trigger>, <outcome>";
- what is out of scope;
- the **likely changes** — what is expected to come next: from the user, from the out-of-scope list (often "later" rather than "never"), and, in an existing codebase, from what its history shows changing most often where this work lands;
- the scaffold's **name**: short, kebab-case (`tab-sync`), and unused in the repository's `scaffolds/` folder. It tags every marker and names the scaffold file.

Take it from the argument, a spec file, or what this conversation already settled. Mark anything you inferred rather than were told as *(inferred)*. If a core behavior can only be guessed, stop: name what is undecided and ask — designing on guesses is the brute-forcing this skill replaces.

Done when every behavior is a When/outcome line, the likely changes are listed with their sources, and every guess is marked.

### 2. Survey what exists

Skip only for an empty repository. Before designing anything, find what the new work touches or overlaps:

- modules that already own a concept the behaviors need;
- entry points and scripts — build, run, deploy, migrate, test;
- patterns and conventions — layout, naming, how dependencies are created and passed, error handling, where tests live — to judge, not to copy;
- earlier scaffolds in `scaffolds/`. One whose markers still exist in the code (`grep -rn "SCAFFOLD(" --exclude-dir=scaffolds .`) is still being filled: the modules it plans count as existing homes, and a file it also changes goes to the checkpoint. One with no markers left is a dated record: read it for why the code is shaped as it is, and trust the code for what exists now.

Tag each relevant find **reuse**, **extend**, or **replace**. *Replace* needs a stated reason and goes to the user at the checkpoint. For a large codebase, dispatch parallel Explore subagents, one per area.

Done when every behavior and every concept the design will name has been searched for in the codebase, and each hit is tagged.

### 3. Decompose

Sketch the modules. Use the fewest the design needs: a small change gets a small scaffold. For each module, write: its name, a one-sentence responsibility, its **secret** (the design decision it hides — a format, a protocol, a storage choice, an algorithm), its interface (everything a caller must know), and what it depends on.

Write each behavior's **trace**: its path through module interfaces, e.g. `B2: Importer.load → Catalog.add → SearchIndex.update`. A behavior with no trace is a gap; a module on no trace is speculation.

Then judge the sketch by complexity: walk the likely changes from step 1 through it, and ask the questions under [Judging complexity](#judging-complexity). Prepare for a likely change through where boundaries sit and what each module hides, rather than by building it in advance. Keep each alternative you weighed, with why it lost, for the scaffold file's Decisions.

Record each departure from the surrounding code in Decisions, with its reason. When the existing home for the new work is already a tangle, weigh a new module that owns the new concept, with the old code calling into it, against growing the tangle.

Done when every module has all five fields, the traces cover every behavior and every module, and the sketch answers every question under Judging complexity.

### 4. Get a cold design

Skip this when the change fits inside one existing module.

A design grown from your own reasoning shares your blind spots. A fresh subagent reaching its own design **cold** does not: where the two agree, the shape is more trustworthy, and where they differ is where to look. Dispatch it only now that your sketch is written down, so neither design pulls on the other.

Brief it the way you would brief a colleague on the problem, not on your answer: the step 1 intent exactly as you wrote it then — purpose, behaviors, out of scope, likely changes — plus any constraints the user gave, such as platform, language, or tools that must be used. Your modules, names and reasoning stay out of the brief, and so does this skill's vocabulary.

Give it read access to the repository and ask: how would you design this — the modules or files, what each is responsible for, how they connect, what existing code you would build on or change, and why? Ask for the design in its reply, with the repository left as it is. If you can choose its model, a different one of comparable strength makes the design more independent still.

When it answers, list every point where the two designs differ — a boundary, which module owns a concept, existing code one of you used and the other missed, the direction of a dependency — and give each a verdict by the questions under Judging complexity: **adopt**, **reject** with the reason, or **open** for the user to decide. Fold the adopted ones into your design and its traces. Each verdict also goes into the scaffold file's Decisions.

Done when every difference has a verdict, and the design with the adoptions folded in still answers every question under Judging complexity.

### 5. Checkpoint — the map

Before writing any file, present the map in chat:

- the scaffold's name, and the behaviors with their *(inferred)* marks;
- the file tree, each entry marked **new**, **changed**, or **reused**;
- each module's responsibility and secret;
- the dependency direction — who calls whom;
- the traces;
- the likely changes, with their *(inferred)* marks, and the module each one lands in;
- the decisions, each with the alternative it beat;
- the differences from the cold design, each with its verdict and reason — the rejected ones too;
- every *replace*, with its reason;
- open questions.

Then ask the user, in these words: **"Does this do what you intended?"** — and wait.

Done when the user approves the map. Iterate on it here; this is where changes are cheapest.

### 6. Write the scaffold

Write the files in place in the repository. Keep the surrounding naming and file layout where they are reasonable — a second style is its own cognitive load; the structure comes from the approved design.

- **Header** of each new file — the module's responsibility, its secret, who it works with, and a final line `Design: scaffolds/<date>-<name>.md` (path from the repository root), which stays after the fill.
- **Interface** — the type names; their fields and cases wherever the data's shape is part of the design; the functions and methods, with parameters and return types written as close to the language as comes naturally. Each gets a comment on what it does, its preconditions, errors and side effects — what a caller needs, not how it works.
- **Inside a module** — prose on what it has to get right; rough private names where they help.
- **Unfilled spot** — every place the filler must write code carries `SCAFFOLD(<name>):` in the language's comment syntax, followed by what the spot is for and anything about it that is not obvious — a decision, a constraint, a pitfall: `// SCAFFOLD(tab-sync): merge by last-modified time; the remote copy wins ties.`
- **Existing file** — leave its code as it is; insert a `SCAFFOLD(<name>):` comment at each point of change, describing the change.
- **Script** — a usage line, then its steps as comments.
- **Wiring** — where the application builds its objects, comment who constructs what and passes it where.

Leave implementation out entirely: a written body is a decision the user never reviewed. Write comments the same way — carry every decision that was made, stated precisely, and invent none; where nothing was decided, the filler is free.

Then write the **scaffold file**, `scaffolds/<date>-<name>.md` with today's date as `YYYY-MM-DD`, from [SCAFFOLD-TEMPLATE.md](SCAFFOLD-TEMPLATE.md). The `scaffolds/` folder sits at the repository root, or inside the repository's docs folder if it keeps one.

Done when every module on the approved map exists as a file or a marked spot, and every trace step lands on a named interface.

### 7. Review and report

Read the scaffold as a stranger would: from each file's header, could you predict what the file will contain? Then ask the questions under Judging complexity again, of the files as written.

Report: the tree, the number of `SCAFFOLD(<name>):` markers, every place the files deviate from the approved map and why, and the open questions. Suggest committing the scaffold on its own, so the fill's diff can be reviewed against it.

## Judging complexity

- **Change amplification** — a small change touches many places. In agent-built code: the same logic written twice; one concept spread across several homes, such as extensions, helper functions and `utils` files; one format known by separate read, parse and write modules; a new script beside one that already does the job. *Ask:* does each likely change land in one module? Does every concept in the behaviors have exactly one home, counting homes that already exist? Does every *reuse* and *extend* from the survey appear on the map?
- **Cognitive load** — how much a reader must hold in mind to work on one part. In agent-built code: God objects, whose responsibility needs an "and"; names like Manager, Helper or Service standing in for a responsibility not yet found; shallow modules and pass-through layers; a protocol or base class with a single implementation. *Ask:* can each module's responsibility be said in one sentence, under a name from the domain? Is each module deep — an interface much simpler than what it hides, so that deleting it would push its work back onto every caller? Does every abstraction have a second implementation today (a test double the tests actually need counts)?
- **Unknown unknowns** — what someone must know to change the code safely, with nothing telling them. In agent-built code: dependencies reached for instead of passed in — singletons, globals, shared mutable state; ordering and ownership rules written down nowhere. *Ask:* does everything a module uses arrive through its interface, created where the application wires itself, with dependencies pointing one way? Is each process-wide instance a recorded Decision with its reason? Is every fact that spans modules written under the scaffold file's Invariants?

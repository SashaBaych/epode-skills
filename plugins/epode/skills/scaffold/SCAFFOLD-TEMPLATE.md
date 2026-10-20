# Scaffold file template

Write the scaffold file, `scaffolds/<date>-<name>.md`, from the template below. Write it as a design brief to a colleague: what is being built, the idea behind the design, and the decisions made — not instructions for writing the code. It is the one home for facts that span modules, and it stays in the repository as the design record; anything that belongs to a single module goes in that module's header instead. Omit any section that would be empty.

---

# Scaffold: <name>

The design for <what is being built>, as agreed on <YYYY-MM-DD>. The shape — which modules exist, what each owns, how they connect — was worked out and reviewed deliberately; how each module does its job is open. Where the surrounding code does things differently from this design, follow the design; Decisions says why. Unfilled spots in the code are marked `SCAFFOLD(<name>):`; remove each marker once its spot is filled, so the markers left show what is still open.

If you find something the design gets wrong — a behavior it cannot carry, a piece it is missing, a premise that turns out false — stop and tell me what you found and how you would change the design, rather than building around it.

Where the code later differs from this file, the code is current.

## Intent

<One sentence: what it is for, and for whom.>

**Behaviors**

1. When <trigger>, <outcome>.

**Out of scope**

- <…>

## How it works

<A few paragraphs, the way you would explain the design to a colleague at a whiteboard: the central idea, how a typical request moves through the modules and where the application wires them together, and why it is shaped this way: the changes it expects and where each one lands, and the obvious alternative it beat.>

## Modules

| File | Status | Responsibility |
|---|---|---|
| `path/to/File` | new / changed | <one sentence> |

## Builds on

| Existing code | Use | Note |
|---|---|---|
| `path/to/existing` | reuse / extend / replace | <what it already does for this design, or why it is replaced> |

## Traces

- B1: `A.method` → `B.method` → `C.method`

## Invariants

<Facts that span modules and that no single header can hold: ordering, ownership of shared data, threading, how errors propagate.>

## Decisions

- <Chose X over Y because Z — including each place the design departs from the surrounding code.>

## Open questions

- <What the design leaves undecided, and who decides it.>

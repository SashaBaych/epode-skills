---
name: real-fix
description: Rethink a proposed solution, or a problem nobody has solved yet, the way an engineer would, and return a short design note before any code is written.
disable-model-invocation: true
---

# Real fix

The user runs this when a proposed solution smells, or when starting on a feature or a problem. Find the design an engineer would build: one that does the whole job, simply. The subject is the proposal or problem in the user's message and the recent conversation. When nobody has proposed a solution yet, read "the proposal" below as "any solution". You produce a short note, and no code is written until the user says go.

**Simple, not easy.** Easy is the shortest path from where you stand. Simple is a design that does the whole job with nothing tangled together. The four wrong turns below show what easy looks like in practice, and what over-correcting it looks like. The steps after them are how you find simple instead.

**A check, not a verdict.** The user is asking for the proposal to be checked, and a check can come out either way. A sound proposal is a real result. If you made the proposal, treat your earlier reasons for it as claims to check.

## The four wrong turns

### Symptom fix

The visible failure stops, and whatever caused it stays.

**What happened.** A test failed now and then because two requests raced to write the same record, and one silently overwrote the other. The agent set the test to rerun on failure, the test went green, and the agent reported the problem fixed. The race was still there. In production, under load, it kept losing writes, and there it was far harder to trace.

**Why it looked right.** The agent took its task to be "make the failure go away", and the failure went away. A passing test looked like proof.

**What an engineer does.** Finds out why the failure happens before deciding what to change, then changes that. Here, that means making sure no write can silently overwrite one it hasn't seen.

**The look-alike.** A save is rejected because the record changed since it was read, so the code reads the record again, reapplies its change to what it just read, and retries the save. This is a retry too, but the conflict is detected and the result stays correct. *Test:* do you know why the failure happens, and does the change keep the result correct when it does? Or does it only make the failure show up less often?

### Judging by appearance

The code decides what something is from how it looks, such as its wording, position, order or a sample value, instead of from what actually makes it that thing.

**What happened.** A tool turned LinkedIn conversations into transcripts. LinkedIn shows suggested replies such as "Thank you!" under the real messages, and these were leaking into the transcript. The agent removed them by matching their text against a list of phrases, and its examples passed. But now a real message that says "Thank you" disappears, a suggestion written in Italian stays, and every time LinkedIn rewords a suggestion it leaks back in. Moving the phrases into a config file, or adding more languages, keeps the same guess.

**Why it looked right.** In the examples at hand, the wording did separate suggestions from messages. A coincidence in the sample looked like a rule.

**What an engineer does.** Finds what actually marks the difference, such as where suggestions sit on the page or how they are tagged, and checks what guarantees it. A documented field or protocol guarantees its meaning. A structure on someone else's page does not, so test it on the cases that would break a coincidence, and name the remaining risk.

**The look-alike.** Sorting files by a `kind` field that the source documents as authoritative, such as `"kind": "preview"`. That is text deciding what a thing is too, but its meaning is guaranteed. *Test:* is the marker's meaning guaranteed by a contract, or at least tested on the cases that would break it (here, a real "Thank you" and an Italian suggestion), with the remaining risk named? Or did it only line up in the examples at hand?

### Hidden limit

The solution covers part of what the job needs, and nothing tells the user what is missing. They believe they have the whole thing.

**Case 1: the filters.** The user asked for a tool that searches LinkedIn using its search filters. The search page offers around ten kinds of filter, among them company region, industry, company size and a free-text location. The agent built two, industry and geography, each with a small fixed list of values. It tested them and reported the tool as essentially complete. It hadn't decided to leave the others out. It never noticed them.

**Case 2: the pages.** A job-search tool fetched one page of LinkedIn results, 25 jobs, per call. It noted that a next page existed, but nothing led the model to tell the user or offer to continue, and in practice it stopped after page one. Suppose the user asks for Microsoft jobs in Germany and there are 20 pages of them. They get 25 jobs as "the results", with no hint that 475 more exist. When the user asked about this, the agent counted calls per page, characters per job card and page offsets. It framed the gap as "nothing makes the model collect the rest", offered a choice between one page per call and fetching every page, and handed the decision back. The user's actual problem never came up: nobody tells them how much there is.

**Why it looked right.** Each agent tested what it had built, and the tests passed because they were written against the subset. Both agents reasoned outward from the machinery they were building. Neither reasoned inward from what the user would see.

**What an engineer does.** Looks at the whole of the real system first and covers what the job needs. Any limit that remains, they show to the user at the moment of deciding, the way every search page says "1–25 of 500". A human assistant asked for those Microsoft jobs would say: "There are 20 pages. Should I go through all of them, or narrow it down?"

**The look-alike.** A limit the user can see and has chosen knowing its cost, as when they ask for just the first page as a sample. *Test:* could the user tell from what they are shown that something is missing?

### Rube Goldberg machine

Machinery heavier than the job: layers, registries, configuration or generality that no actual need asks for. Each part may be well built, yet together they make a simple job fragile.

**What happened.** A project had one processing rule that never varied. A factory created it, a registry found it by a string name, and a config file switched it on. Renaming the rule took three edits, and a missed one failed only at runtime.

**Why it looked right.** Each layer is a respectable pattern, and generality feels like quality. The pull is strongest right after being told that a first attempt was a hack, which is exactly when this skill runs. The over-correction for a symptom fix is a framework.

**What an engineer does.** Builds for the needs the job has now. Each layer has to remove more work than it adds.

**The look-alike.** One interface over three storage backends that really exist, keeping their differences out of every caller. *Test:* does each layer remove more work than it adds, for needs the job actually has?

## The steps

Work through the steps in order. Look at things, read them and run read-only checks. Leave the code and any live system as they are.

1. **Name the job.** Say in one sentence, in the user's terms, what must be true for them when this is done. For the transcript tool, the job is "the transcript keeps every message people sent and nothing else", not "suggestions stop showing up". If something is failing, find its cause.

   Some requirements follow from the job even if nobody stated them. For example, someone searching for jobs needs to know when they see only part of the results. Mark each of these as your inference. A need that nobody has and the job doesn't imply carries no weight.

   *Done when* the user would recognise the sentence as what they asked for, and any failure has its cause named, or you've said what you'd need to find it.

2. **Survey the real thing.** Find the part of the system the job touches, and look at all of it as it is now: the live page, the docs or schema, real data, the surrounding code. For a search tool with filters, that part is the whole filter panel and the whole result set, not all of LinkedIn.

   Within that part, list both sides, and mark each item as covered by the proposal or not:
   - what it takes in: every option, filter, kind of input, language and format;
   - what it gives back: how much there is, how it is paged or split, and what kinds of things it holds.

   List all of it, not just what seems relevant: judging relevance from memory is how the filter tool missed most of the panel.

   *Done when* every item in that part is listed and marked, along with anything outside it that the design depends on, and whatever you couldn't reach is named as unchecked.

3. **Walk it through as the user.** Take a realistic request at its real size, such as the 20-page search or the Italian suggestion. Describe what the user does and sees under the proposal, from start to finish. Then picture a competent human assistant doing the same job by hand, with the same system. Note every point where they would do better.

   *Done when* the walk-through is written and every such point is noted.

4. **Check the wrong turns.** Say in one sentence how the proposal works, such as "it drops suggested replies by matching their words"; often that sentence alone shows the problem. Then run each wrong turn's test against the proposal.

   *Done when* each wrong turn is cleared or found, with the case that shows it.

5. **Find what an engineer would reach for.**
   - Assume the problem has been solved before. Name the field it belongs to and that field's standard solution.
   - Lay out the options: the proposal, the established solution, and any simpler design you see.
   - Judge them first on whether they do the whole job from steps 1–3, then on simplicity.
   - Put your pick through step 4 as well. Confirm anything it relies on about the real system by looking.
   - Say where the pick comes from: reused from a named practice, or invented because nothing established fits, and why.

   Then sort the remaining decisions into two kinds. What the job requires goes into the design as a given. Real tradeoffs, such as a budget, a threshold, or a scope the user might knowingly narrow, are the user's call. Each one comes with your recommended answer and its cost; a bare choice handed back is how the pages case stalled.

   *Done when* one option is picked with its origin named, and every call left to the user carries your recommendation.

6. **Write the note and stop.** Its length follows the decision: a sound proposal or a small, clear defect takes a few lines. Every note has the first four sections below, even at a line each, because what the user sees and what the real system offers are where a blind spot shows. Add the others when they add something. Keep this order:
   1. **Verdict** on the proposal, in a sentence or two. It is one of four: sound; sound, with gaps worth fixing; a wrong turn, naming which one and why; or can't tell yet, with what would decide it. If there is no proposal yet, start at the job.
   2. **The job**, with inferred requirements marked.
   3. **What the user sees:** the walk-through.
   4. **What the real system offers:** each item marked covered, missing, or the user's call with its cost. Mark the items you couldn't check.
   5. **Approach:** the options and their tradeoffs, your pick and where it comes from, and the user's calls with your recommendations.
   6. **How we'll know it works:** checks taken from the inventory and the walk-through, set before any code exists, so that passing them means the whole job works.
   7. **Open questions** that could change the pick, each with the smallest check that would settle it.

   Then stop, and build when the user says go.

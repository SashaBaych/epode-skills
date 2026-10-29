# epode

Agent skills by Sasha Baych, for Claude and Codex.

*Epode* comes from the Greek *epōidē*: a song sung over something to work a charm — an incantation.

## Skills

| Skill | What it does | Call it |
|---|---|---|
| `scaffold` | Designs a feature or app as a freeform scaffold — files, module names, interfaces and intent comments, no implementation — for you to review before another agent fills it in. | Claude: `/epode:scaffold <what to build>`<br>Codex: `$epode:scaffold <what to build>` |
| `ste` | Answers your next question so it lands the first time: a little context, [ASD-STE100 Simplified Technical English](https://www.asd-ste100.org/) (the controlled English of aerospace maintenance manuals), and the project's own terms from `GLOSSARY.md` or `CONTEXT.md`. | Claude: `/epode:ste <your question>`<br>Codex: `$epode:ste <your question>` |
| `evaluate-not-validate` | Judges your idea or proposal on its merits instead of agreeing with it: names its flaws, caveats and better alternatives directly, and praises only what earns it. | Claude: `/epode:evaluate-not-validate <your idea>`<br>Codex: `$epode:evaluate-not-validate <your idea>` |
| `real-fix` | Rethinks a proposed solution, or a problem nobody has solved yet, the way an engineer would: names the job, surveys the whole real system, walks it through as the user, checks for four wrong turns (symptom fix, judging by appearance, hidden limit, Rube Goldberg machine), and returns a short design note before any code is written. | Claude: `/epode:real-fix <the proposal or problem>`<br>Codex: `$epode:real-fix <the proposal or problem>` |

No skill starts on its own; you call it.

`ste` is inspired by Matt Pocock's [`/wait-what`](https://www.aihero.dev/skills-wait-what). `/wait-what` asks the agent to re-explain what it just told you; `ste` asks it to answer the question you are about to ask so you understand it the first time.

## Install

All three routes install from this one repository.

### Claude app — claude.ai and the desktop app

1. Open **Customize → Plugins → Add → Add marketplace** and enter `SashaBaych/epode-skills`.
2. Add the **epode** plugin.
3. Turn on **Sync automatically** to receive each new release; **Check for updates** pulls it by hand.

A plugin added here is saved to your account, so it also loads in Cowork and in Claude Code sessions signed in with the same account.

### Claude Code

```
/plugin marketplace add SashaBaych/epode-skills
/plugin install epode@epode-skills
```

Auto-update is off by default for marketplaces outside Anthropic's own. To receive each new release automatically, open `/plugin` → **Marketplaces** → **epode-skills** → **Enable auto-update**. To update by hand, run `claude plugin update epode@epode-skills`.

### Codex

```
codex plugin marketplace add SashaBaych/epode-skills
codex plugin add epode@epode-skills
```

To update, run `codex plugin marketplace upgrade epode-skills`.

## Releases

Each release raises the plugin's version, and an installed copy updates only when that version changes, so you get released changes, not every commit. Each release is listed on the [Releases](https://github.com/SashaBaych/epode-skills/releases) page and in [CHANGELOG.md](CHANGELOG.md).

## License

[MIT](LICENSE)

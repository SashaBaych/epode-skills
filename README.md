# epode

Agent skills by Sasha Baych, for Claude and Codex.

*Epode* comes from the Greek *epōidē*: a song sung over something to work a charm — an incantation.

## Skills

| Skill | What it does | Call it |
|---|---|---|
| `scaffold` | Designs a feature or app as a freeform scaffold — files, module names, interfaces and intent comments, no implementation — for you to review before another agent fills it in. | Claude: `/epode:scaffold <what to build>`<br>Codex: `$epode:scaffold <what to build>` |

`scaffold` never starts on its own; you call it.

## Install

All three routes install from this one repository.

### Claude app — claude.ai and the desktop app

1. Open **Customize → Plugins → Add → Add marketplace** and enter `SashaBaych/epode-skills`.
2. Add the **epode** plugin.
3. Turn on **Sync automatically** to receive updates as they are pushed; **Check for updates** pulls them by hand.

A plugin added here is saved to your account, so it also loads in Cowork and in Claude Code sessions signed in with the same account.

### Claude Code

```
/plugin marketplace add SashaBaych/epode-skills
/plugin install epode@epode-skills
```

To update, run `/plugin marketplace update epode-skills`, or turn on auto-update for the marketplace under `/plugin` → **Marketplaces**.

### Codex

```
codex plugin marketplace add SashaBaych/epode-skills
codex plugin add epode@epode-skills
```

To update, run `codex plugin marketplace upgrade epode-skills`.

## License

[MIT](LICENSE)

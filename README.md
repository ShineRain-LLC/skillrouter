# SkillRouter plugin for Claude Code and Codex

[skillrouter.org](https://skillrouter.org) is a search engine for agent skills. Describe a task in plain words and your agent gets back screened, ranked third-party skills (folders with a `SKILL.md`, crawled from public GitHub repositories), then installs the one it picks. It works for any kind of task, not just coding: transcribing audio, auditing a site's SEO, editing video, working with a named app, and so on.

Not to be confused with other projects of the same name: this plugin is the client for skillrouter.org.

## Install

The plugin drives the `sr` command-line tool, so install that first (Node.js 20 or later):

```bash
npm i -g @skill-router/cli
sr setup
```

Then add the plugin.

**Claude Code**

```bash
claude plugin marketplace add ShineRain-LLC/skillrouter
claude plugin install skillrouter@skillrouter
```

**Codex**

```bash
codex plugin marketplace add ShineRain-LLC/skillrouter
```

**Gemini CLI**

```bash
gemini extensions install https://github.com/ShineRain-LLC/skillrouter
```

**Any agent that reads skills** (via [skills.sh](https://skills.sh))

```bash
npx skills add ShineRain-LLC/skillrouter
```

## What is inside

| Skill | What it does |
| --- | --- |
| `skillrouter-router` | Before a specialised task, runs `sr resolve "<need>"`, compares the candidates with the skills you already have, and installs the better pick with `sr ensure`. |
| `community-submit` | Gets your own skill repository indexed, or lets you claim it to see how often it is recommended and installed. |
| `sr-browser` | Operates a website in your own logged-in Chrome when a task needs a live site and no dedicated skill covers it. Optional: needs the SkillRouter Bridge extension. |

It also registers the SkillRouter MCP server (`@skill-router/mcp`, pinned version) and a session-start hook that runs `sr snapshot` to list what is already installed.

## Network calls and data

- **skillrouter.org** — `sr resolve` sends the task description you or your agent wrote, and `sr ensure` downloads the chosen skill. With an account, requests carry your SkillRouter key from `~/.skillrouter`. Nothing else from your machine is sent.
  - skillrouter.org forwards the normalized, truncated task text (never your account, key or IP) to its ranking processor, Typesafe. Do not put sensitive information in a task description.
  - Retention: ranking caches expire after 7 days; outcome events are kept for 97 days; queries that found nothing are kept to improve coverage and are not linked to an account. Details: https://skillrouter.org/privacy
- **github.com** — skill sources and links point to the original repositories.
- **registry.npmjs.org** — `npx` fetches the pinned MCP server package.
- `community-submit` uploads a local skill only after you review a preview and explicitly agree.
- `sr-browser` talks only to a daemon on `localhost` and the Chrome extension on your machine.

Searching is free for individuals.

## License

MIT. Third-party skills found through SkillRouter keep their own licenses.

# yeste.studio — Claude Code plugins

The distribution channel for [yeste.studio](https://yeste.studio)'s Claude Code
plugins: this repository holds the marketplace catalog and one directory per
plugin. Everything here is **generated** — it is published by each plugin's CI
and should not be edited by hand.

## Install

```
/plugin marketplace add javier-sy/claude-plugins
```

```
/plugin install nota@yeste.studio
```

## Plugins

| Plugin | Source repo | What it is |
|--------|-------------|------------|
| [`nota`](./nota) | [javier-sy/nota-plugin](https://github.com/javier-sy/nota-plugin) | MusaDSL composition assistant: learn the framework, code compositions, explore ideas, analyze music |

## How this repository is written

Each plugin is developed in its own repository, in a harness-agnostic form, and
a generator emits one distribution per harness. The Claude Code distribution is
copied here by that repository's CI, which also rewrites its own entry in
`.claude-plugin/marketplace.json` — only its own, so several plugins can publish
to this catalog without overwriting each other.

The plugin's files and the catalog live together on purpose. A catalog entry
that points at a different repository makes Claude Code clone it over SSH
unconditionally, which fails for anyone without GitHub SSH keys (a CLI bug,
present through 2.1.222). A `"source": "./name"` entry is resolved inside the
already-cloned catalog, so it never hits that path.

Knowledge bases and other large assets are **not** here: they are distributed
through each source repository's GitHub Releases and downloaded on first use.

## License

Each plugin keeps the license of its source repository. `nota` is GPL-3.0-or-later.

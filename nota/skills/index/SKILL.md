---
name: index
description: >-
  Use this skill when the user wants to index compositions, manage their private
  knowledge base, list indexed works, add or remove works from the index,
  update the index for a composition, or check indexing status.
version: 0.2.0
---

## Before anything else: is the knowledge base there?

This skill answers **from the MusaDSL knowledge base and only from it**. Its
tools live on the `knowledge-base` MCP server, which needs dependencies that are
installed after the plugin itself.

**If those tools are not among the tools available to you, stop.** Not "try
anyway", not "answer from what you know about MusaDSL". An answer built without
the index reads exactly like one built with it — fluent, plausible, sourced from
nothing — and telling the two apart afterwards is not possible. Producing that
answer is the failure this plugin exists to prevent.

What to do instead: call `check_setup` — it is on the `setup` server and answers
even when the knowledge base cannot start — then tell the user what is missing
and the one step that fixes it, and stop. Do not begin the work this skill was
invoked for.

**Say it in a few lines.** Everything above is the reason *you* must stop, and
none of it is the user's business. They asked about music and got an
installation notice instead; that is already an interruption, and explaining it
at length makes it a worse one. So: do not name which server owns which tool, do
not argue why an unsourced answer would be bad, do not describe how the plugin
is built. Report what is missing, name the step, offer to run
`install_dependencies` for them, and be done.

# Manage Private Works Index

Guide the user through managing their private composition index — adding, listing, updating, and removing works from `private.db` — using the MCP tools provided by the musadsl-kb server.

## Process

1. **Detect the user's language** from their message. If they write in Spanish, respond entirely in Spanish. If in English, respond in English. Match whatever language they use.

2. **Determine the operation** the user wants. Match their intent to one of the operations below.

3. **Execute the operation** by calling the appropriate MCP tool and present results clearly.

## Operations

### List indexed works

When the user asks what's indexed, what works they have, or wants to see the list.

Call the `list_works` MCP tool (no parameters).

Show the output to the user. If no works are indexed yet, suggest adding some.

### Add a single work

When the user provides a path to a composition project.

Call the `add_work` MCP tool with the `work_path` parameter (absolute path to the composition project directory).

After success, mention that the work now appears in `search` (kind: `"all"` or `"private_works"`) and `similar_works` results. Suggest using `/nota:analyze` to generate a deeper musical analysis of the work, or `/nota:code` to modify the composition.

### Update a work

When the user has modified a composition and wants to re-index it. The safest approach is remove + add:

1. Call the `remove_work` MCP tool with the work's `work_name`
2. Call the `add_work` MCP tool with the `work_path`

The `work_name` is the basename of the composition directory (as shown by `list_works`).

### Remove a work

When the user wants to remove a composition from the index. **Ask for confirmation before executing.**

Call the `remove_work` MCP tool with the `work_name` parameter. Note: this also removes any associated analysis chunks.

### Move or rename a work

When the user moved a project to a different location or renamed the directory:

- If only the parent path changed (same directory name): just call `add_work` from the new path — source labels use the basename, so they'll match.
- If the directory name changed: call `remove_work` with the old name, then `add_work` with the new path.

### Check status

When the user wants to know the overall state of their databases.

Call the `index_status` MCP tool (no parameters).

## What gets indexed

The indexer recursively indexes all files in the given directory:

- **`.rb` files** — Ruby source code (MusaDSL compositions, scripts, etc.)
- **`.md` files** — Markdown documentation, READMEs, notes

Files under `vendor/` and `.bundle/` are excluded. The work name is the basename of the directory.

## Important

- **Do NOT search the knowledge base** — this skill manages the index, it doesn't query it. For searching, the user should ask questions normally and the MCP tools will handle it.
- **Ask for confirmation** before removing works.
- If the user asks about plugin configuration or API key issues, redirect to `/nota:setup`.

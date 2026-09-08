---
name: analysis-framework
description: >-
  Use this skill when the user wants to view, modify, or reset the analysis
  framework that guides composition analysis dimensions.
version: 0.1.0
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

What to do instead, in this order:

1. Call `check_setup`. It is on the `setup` server, which needs no dependencies
   of its own and answers even when the knowledge base cannot start.
2. Tell the user what it reports, and the step it names — usually running
   `install_dependencies` once, then reloading plugins.
3. Stop. Do not begin the work this skill was invoked for.

The user has asked for something this skill cannot do yet. Saying so, with the
remedy, is the whole of the correct response.

# Manage Analysis Framework

View, customize, or reset the analysis framework that defines the analytical dimensions used by `/nota:analyze`.

## Process

1. **Detect the user's language** from their message. If they write in Spanish, respond entirely in Spanish. If in English, respond in English. Match whatever language they use.

2. **Determine the operation** the user wants. Match their intent to one of the operations below.

3. **Execute the operation** by calling the appropriate MCP tool and present results clearly.

## Operations

### View current framework

When the user asks to see the current framework, what dimensions are analyzed, or how analyses are structured.

Call the `get_analysis_framework` MCP tool (no parameters).

Display the framework content and indicate whether it is the **default** or a **user-customized** version.

### Modify the framework

When the user wants to add, remove, or change analytical dimensions.

1. Call `get_analysis_framework` to get the current content
2. Apply the user's requested changes:
   - **Add a dimension**: add a new `##` section with the dimension title and instructions
   - **Remove a dimension**: remove the corresponding `##` section
   - **Modify a dimension**: edit the content of the corresponding `##` section
   - **Reorder dimensions**: rearrange the `##` sections
3. **Show the modified framework** to the user for review before saving
4. After the user approves, call `save_analysis_framework` with the full modified content

**Always show the changes and ask for confirmation before saving.**

### Reset to default

When the user wants to go back to the default framework.

1. **Ask for confirmation** — explain that this will remove their customizations
2. After confirmation, call `reset_analysis_framework`

## Important

- The framework is a markdown file with `##` sections. Each section title names an analytical dimension; the section body provides instructions for what to analyze.
- The default framework has 10 dimensions: Formal Structure, Harmonic and Modal Language, Rhythmic and Temporal Strategy, Generative Strategy, Texture and Instrumentation, Idiomatic Usage and Special Features, Relation to Other Artists, Notable Technical Patterns, Coding Best Practices, Conclusion.
- User customizations are stored at `~/.config/nota/analysis-framework.md`.
- Changes to the framework affect future `/nota:analyze` runs but do not retroactively change existing analyses.
- **Do NOT call `get_analysis_framework` for informational purposes** — only call it when the user is actively working with the framework (viewing, modifying, or resetting).

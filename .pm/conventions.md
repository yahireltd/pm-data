# Team conventions

How we work — the durable, cross-project rules and the lessons we've paid for.
Hand-written conventions live in the section below; the "Lessons from closed
projects" section beneath them is generated from systemic lessons captured at
project closure (`pm conventions sync`). A fresh Claude conversation should be
able to read this file and absorb how this team operates.

## Conventions
- Wire every entity through all four faces (web, CLI, MCP, linter) before calling it done.
- In YAML list items use ' - ' not ': ' — a colon-space makes the parser read the item as a map (SCHEMA001).
- Secrets never live in files or env; fetch them from AWS Secrets Manager at runtime.
- Exhaustive Record<Kind> types are load-bearing — they force every display surface to update when a variant is added.
- check function callls whenever editing a function

<!-- BEGIN: lessons (generated — do not edit by hand) -->
## Lessons from closed projects

_None yet — systemic lessons captured at closure will roll up here._
<!-- END: lessons -->

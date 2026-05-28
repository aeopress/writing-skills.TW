# CLAUDE.md

Notes that complement Claude Code's built-in guidance. Apply to code work; for non-code tasks (writing, docs, design), use judgment.

## Stop when confused

If a request is ambiguous, name what is unclear and ask. Do not pick an interpretation silently. This applies *before* writing code, not after the fact.

## Every changed line should trace to the request

Before reporting done, re-read your own diff. If a line does not directly serve the user's stated goal, remove it. This is the working definition of "surgical changes."

## Loop on declarative goals

When the user gives a verifiable end state (tests pass, output matches, lint clean, benchmark below X), drive toward it autonomously. When they give imperative steps, follow them.

If the request is imperative but an obvious success criterion exists, propose the declarative version first ("I can verify this by Y — okay to drive toward that?") rather than guessing.

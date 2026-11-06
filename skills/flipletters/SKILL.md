---
name: flipletters
description: Design, validate, or render FlipLetters split-flap / flip-dot boards from FlipCode when the user wants a board video or text layout.
---

# FlipLetters FlipCode boards

Every tool named below comes from FlipLetters at `https://app.flipletters.com/mcp/v1`.

Every tools/call needs a FlipLetters account. Set `FLIPLETTERS_API_KEY` to a `flk_` key from Studio → Account → API keys & agents, or complete OAuth in the client.

## Steps

1. **Reference** — call `flipletters_flipcode_reference` (or the current reference tool) before inventing FlipCode syntax.
2. **Validate** — call `flipletters_validate_board` on the user's draft before render.
3. **Example** — optionally `flipletters_example_board` when the user wants a starting layout.
4. **Render / share** — only after a clean validate, and only when the user asks to render or post.

## Rules

- Do not invent FlipCode grammar; use the reference tool.
- Never paste production API keys into chat. Point Hermes at `FLIPLETTERS_API_KEY`.
- Docs: https://flipletters.com/mcp

## Done when

- The board was validated (or validation errors were shown).
- Render/share happened only after an explicit ask.


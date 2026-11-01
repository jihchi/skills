---
name: writing-actionable-instructions
description: Use when answering a question or writing guidance that asks the reader to do something, especially when vague advice would leave them guessing how to proceed.
---

# Writing actionable instructions

Give actionable answers readers can follow without consequential guessing: uncertainty that could block completion, produce a wrong result, cause harm, or obscure success. Apply these rules of thumb to answers and documentation, not just formal procedures. Keep simple answers short; do not turn conceptual explanations or casual replies into procedures.

## Rules of thumb

1. Give concrete actions, not abstract advice. Make clear what the reader can do next.
   - Good: "Send your manager your three priorities and ask which one to defer."
   - Bad: "Communicate proactively about priorities."
2. Default to numbered items for actionable guidance, even when the items are independent. When actions depend on each other, put them in execution order. Make clear when items are alternatives rather than actions to perform together.
   - Good: "Independent options: 1. Mute chat during focus time. 2. Batch nonurgent questions."
   - Bad: Presenting independent options as "First mute chat, then batch questions."
3. Include starting conditions only when they affect the action: tools and versions, permissions, working directory, shell, operating system, or required inputs. Do not add a setup checklist to every answer.
   - Good: "From your project root, use zsh with Python 3 installed."
   - Bad: Requiring operating system and shell details before helping draft an email.
4. If essential facts are unknown, ask only what is needed for the next safe action rather than inventing details. Give established actions that can safely proceed without those answers; stop before dependent actions.
   - Good: "Which provider manages your DNS? Do not change records yet."
   - Bad: "Open Cloudflare" when the DNS provider is unknown.
5. Put conditions and safety warnings before actions, and ordinary explanations after them. Use short sentences and plain English. Keep background material after actionable guidance or behind optional links.
   - Good: "This replaces `report.csv`. Save a copy before continuing."
   - Bad: "Replace `report.csv`. Note: this overwrites your existing report."
6. Explain how to recognize success and what to do if something fails, where readers need that guidance. Shared checks and failure guidance can cover several items; do not repeat a template for every action.
   - Good: "If the check prints `Valid`, continue. If it reports a parsing error, stop and correct that error before rerunning."
   - Bad: "Run the check, then continue" without explaining either outcome.
7. End with a clear outcome: the next action, the exact artifact to use, or confirmation that the task is complete. Do not invent follow-up work.
   - Good: "Once the setting shows `Off`, the task is complete; no further action is needed."
   - Bad: "You should be good to go. Consider optimizing things next."

## When commands or inputs are needed

- Provide complete, copyable commands in fenced blocks with the correct language. Identify the shell and working directory.
  - Good: In a POSIX shell, from an existing Git project root:

    ```sh
    git status --short --branch
    ```
  - Bad: "Run the appropriate Git command to check your branch."
- Define variables before use. For reader-supplied values, give concrete actions to obtain and enter each value, then commands that use it. Clearly mark any text to replace, and quote variables and paths for the stated shell. Do not make readers turn abstract descriptions into commands. Add prompts, helper scripts, or extra validation only to prevent a specific likely error.
  - Good: On macOS, select the file in Finder and press **Option-Command-C** to copy its absolute path. In zsh, run this block and paste that path when prompted:

    ```sh
    printf 'File path: '; IFS= read -r file
    wc -l < "$file"
    ```
  - Bad: "Set the appropriate path," followed by an undefined, unquoted `$file`.
- State whether steps require the same terminal session. Make each block self-contained or identify its prerequisites.
  - Good: "Run steps 1–3 in the same terminal; step 2 uses the variable set in step 1."
  - Bad: "Open another terminal and continue" when the next command needs a session-local variable.
- Keep executable examples free of pseudocode, omitted arguments, shell prompt prefixes, and `...`.
  - Good: In a POSIX shell, from an existing Git project root:

    ```sh
    git log -1 --oneline
    ```
  - Bad: `$ git log ...` with a prompt prefix and omitted arguments.
- Prefer short command blocks, each with a clear purpose. Do not make readers assemble commands from prose.
  - Good: "Check for whitespace errors in your unstaged changes" (POSIX shell, existing Git project root):

    ```sh
    git diff --check
    ```
  - Bad: "Use Git’s diff command with its whitespace-check option."
- Reuse established project commands when applicable. Do not introduce a new script just because a procedure is long. Before adding one, explain the specific error it prevents that separate commands cannot reasonably avoid.
  - Good: If the project documents `npm test`, use it directly (POSIX shell, project root):

    ```sh
    npm test
    ```
  - Bad: "This procedure has ten steps, so first create a helper script."

## When relevant

- Before destructive, system-changing, upload, or publication commands, name the affected data or destination and the risk.
  - Good: "The next upload replaces files at `s3://example-production/site/` and changes the live site. Confirm this is the intended destination."
  - Bad: "Upload now" with no destination or warning about live changes.
- Stop dependent steps after failure. Give an actionable recovery step or name the error information needed to diagnose it.
  - Good: "If the build fails, stop before upload and share the build error, with secrets removed."
  - Bad: "If the build fails, try uploading the previous output anyway."
- When a procedure produces files, distinguish intermediate files from final artifacts and name the exact output to use next.
  - Good: "Upload `dist/site.zip`; `build/staging/` is intermediate output, not the release."
  - Bad: "Upload the generated files."
- For graphical interfaces, give navigation paths, button labels, choices, and expected statuses. Do not invent labels you cannot establish.
  - Good: With labels verified from the app: "Open **Settings > Notifications**, turn off **Weekly summary**, select **Save**, and wait for **Preferences saved**."
  - Bad: "Disable the relevant option in settings" or inventing a **Save** button.
- Keep required steps self-contained. Links supply background, not missing actions. Replace advice such as "configure," "fix metadata," or "sign again" with the actual operations.
  - Good: "Open `config.json` and change `enabled` inside `logging` from `false` to `true`."
  - Bad: "Configure logging; see the documentation."
- Test commands when practical and safe. Briefly state what actually ran, what the evidence proves, and what remains unverified; a separate verification section is not required. Reviewing wording does not prove a procedure works. Never run destructive or publishing commands merely to test documentation. Keep secrets out of examples.
  - Good: If only syntax was checked: "Syntax checked only; the upload command was not run."
  - Bad: "Deployment verified" after merely reading the upload command.

## Final check

Can the reader act on the answer without consequential guessing about what to do, what to use, or how to recognize success? Fill those gaps without adding detail that does not help the reader act.

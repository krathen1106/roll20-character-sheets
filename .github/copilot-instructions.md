<!-- Copilot instructions for contributors and AI assistants. Keep short and actionable. -->
# Repository-specific guidance for AI coding assistants

This repository is a large collection of Roll20 character sheets (HTML/CSS/optional JS). The goal of edits is to keep sheets compatible with the Roll20 platform and the project's build tooling. Follow these concrete rules when making code changes or suggesting patches.

- **Big picture**: Each sheet lives in its own subdirectory under the repository root. A minimal sheet contains: `<sheetname>.html`, `<sheetname>.css`, `preview.(png|jpg|gif)`, and `sheet.json` (metadata). Primary repo-level artifacts: `Makefile`, `build.toml`, `process-fonts.sh`, and `contrib/` tools.

- **Roll20 HTML conventions (must follow exactly)**:
  - Inputs that persist to character attributes must have a `name` attribute. Use Roll20 attribute references like `@{attribute_name}` inside HTML where appropriate.
  - Repeating sections must use the `repeating_<sectionname>-<rowid>-<field>` naming convention (i.e. start the group name with `repeating_`).
  - Roll buttons must use Roll20's roll syntax, typically `&{template:template_name} {{field=value}}` or simple `/roll` buttons as used by the sheet. Do not invent alternative roll syntaxes.
  - Sheet workers and event-driven JS must follow Roll20 sheet-worker patterns (subscribe to change events and update attributes using the attribute API). Avoid server-side or unsupported APIs.

- **File locations and examples**:
  - Repo root: `Makefile`, `build.toml`, `process-fonts.sh`, `contrib/`.
  - Example sheet folder: `HackMaster_4E/hm4e.html` (sheet HTML lives here).
  - See `Palladium Rifts by Grinning Gecko/AGENTS.md` for a good example of project-local developer guidance.

- **Build / test / local workflow (explicit commands)**:
  - Most validation and testing is manual in Roll20's character sheet editor — load the HTML and verify rolls, saving, repeating sections, and dark-mode behavior.
  - When a sheet includes a `package.json` or `src/` (scoped build), use the sheet's npm scripts. Common pattern:
    ```bash
    cd <sheet-dir>
    npm install
    npm run watch   # if provided by the sheet
    ```
  - Repo-level build (Unix-like shell required): to run the Makefile tasks (uses `find`, `parallel`, `bun`), run from repo root in WSL/Git Bash/WSL2 or a proper POSIX environment:
    ```bash
    make all
    ```
  - To bump `sheet.json` version before uploading (required step documented in repo README):
    ```bash
    cd <sheet-dir>
    cat sheet.json | jq ". += {\"version\":\"$(date +%s)\"}" | tee sheet.json
    ```
    (Requires `jq` on PATH.)
  - `process-fonts.sh` is a zsh script that fetches Google Fonts and rewrites URLs. It expects a Unix shell and the `DISCORD_ACTIVITY_CLIENT_ID` env var. Run only when needed and in a POSIX shell.

- **Conventions & constraints to enforce in code changes**:
  - Do not add external JS libraries that Roll20 does not support. Avoid dynamic imports, bundlers, or server-side code in sheet HTML.
  - Prefer small, focused changes to a sheet. Large refactors should include roll-by-roll verification steps and reference to original author where possible.
  - Keep CSS scoped to the sheet; avoid global selectors that could interfere with other sheets.

- **Integration & dependencies**:
  - Some tooling uses `bun` and `node` (see `Makefile`); contributors on Windows should use WSL or a POSIX-compatible shell to run repo-level builds.
  - Translations are handled via Crowdin; do not attempt to patch translations outside the existing Crowdin flow.

- **What to check in a PR**:
  - Confirm `sheet.json` updated if needed and valid JSON.
  - Verify all form `name` attributes exist for persisted fields.
  - Manually test roll buttons and repeating sections in Roll20's editor; mention which browser/viewport were used.
  - Ensure no external CSS/JS dependencies were added that Roll20 will block.

If anything in this file is unclear or you want the assistant to cover additional subfolders (for example, automated `contrib/` scripts or a specific sheet directory), tell me which paths to prioritize and I'll update this guidance.

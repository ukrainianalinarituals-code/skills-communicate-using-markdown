# CLAUDE.md

Guidance for AI assistants (Claude Code, GitHub Copilot, etc.) working in this repository.

## What this repository is

This is a **GitHub Skills interactive exercise** titled **"Communicate using Markdown."** It is *not* a software project — there is no application code, build system, package manager, or test suite. The "product" is a self-paced tutorial that teaches a learner Markdown syntax by having them create and edit a blog file (`day-1.md`) through a sequence of steps. Progress is driven entirely by GitHub Actions workflows that react to the learner's commits and post feedback as comments on a tracking issue.

Understanding this framing is essential: most changes here are to **documentation content** (Markdown files) or to **the exercise automation** (GitHub Actions workflows), never to compiled code.

## Repository layout

```
.
├── README.md                 # Exercise landing page (links to the exercise issue)
├── LICENSE                   # MIT License
├── .gitignore                # Generic ignore rules (compiled artifacts, OS files, archives)
├── day-1.md                  # The learner's blog file — the artifact the exercise builds up
├── install-copilot-cli.md    # Standalone guide: installing GitHub Copilot CLI
└── .github/
    ├── steps/                # Markdown content posted to the issue for each step
    │   ├── 1-add-headings.md
    │   ├── 2-make-a-task-list.md
    │   ├── 3-add-a-code-example.md
    │   ├── 4-add-an-image.md
    │   ├── 5-merge-your-pull-request.md
    │   └── x-review.md        # Final congratulations / recap content
    └── workflows/            # One GitHub Actions workflow per step
        ├── 0-start-exercise.yml
        ├── 1-add-headings.yml
        ├── 2-make-a-task-list.yml
        ├── 3-add-a-code-example.yml
        ├── 4-add-an-image.yml
        └── 5-merge-your-pull-request.yml
```

## How the exercise works

The exercise is a state machine implemented with GitHub Actions. Each step has a paired workflow (`.github/workflows/N-*.yml`) and a content file (`.github/steps/N-*.md`).

1. **Step 0 (`0-start-exercise.yml`)** — Triggered by a push to `main`. Uses the shared `skills/exercise-toolkit` reusable workflow to open the tracking issue, posts Step 1 content, then enables the "Step 1" workflow.
2. **Steps 1–4** — Each workflow triggers on a push to the **`start-blog`** branch that touches `day-1.md`. It runs a keyphrase check on `day-1.md`, posts a pass/fail results table to the issue, and on success posts the next step's content, then **disables itself and enables the next step's workflow**.
3. **Step 5 (`5-merge-your-pull-request.yml`)** — Triggers on activity against `main` (the pull request merge). Posts the review/recap content (`x-review.md`) and disables itself to finish the exercise.

Workflows are enabled/disabled dynamically via `gh workflow enable "Step N"` / `gh workflow disable`, so only the current step's workflow is active at any time. This is why the steps must run in order.

### Step checks (what each workflow validates on `day-1.md`)

These are enforced with `skills/action-keyphrase-checker@v1`. If you edit the exercise, keep the sample content consistent with these checks:

| Step | Workflow | Check | Requirement |
|------|----------|-------|-------------|
| 1 | `1-add-headings.yml` | `# ` (h1) and `## ` (h2) | ≥1 h1 **and** ≥2 h2 |
| 2 | `2-make-a-task-list.yml` | `" [ ] "` | ≥1 unchecked task-list item |
| 3 | `3-add-a-code-example.yml` | ` ``` ` (fence) | ≥2 code-fence markers (i.e. ≥1 fenced block) |
| 4 | `4-add-an-image.yml` | `<img` | ≥1 HTML image tag |
| 5 | `5-merge-your-pull-request.yml` | — | PR from `start-blog` merged into `main` |

The current `day-1.md` already satisfies steps 1–4 (h1 title, two h2 sections, a task list, a fenced `bash` block; the h2 headings and image live in the completed blog post).

## Key conventions

- **Branches.** The exercise flow assumes the default branch is `main` and the learner works on a branch named **`start-blog`**. Do not rename these — the workflow triggers key off them literally.
- **Pinned versions.** Reusable workflows and actions are pinned to specific refs (e.g. `skills/exercise-toolkit@v0.6.0`, `skills/action-keyphrase-checker@v1`, `actions/checkout@v4`). Keep the `exercise-toolkit` ref consistent across all workflows when upgrading.
- **Permissions.** Workflows request `contents: write`, `actions: write`, and `issues: write` — needed to comment on the issue and toggle workflows. Preserve these when editing.
- **Markdown style.** Content files favor GitHub-flavored Markdown: HTML `<img>` tags for sizing/alignment, fenced code blocks with language hints, GitHub alert blocks (`> [!TIP]`), and `<details>`/`<summary>` for "Having trouble?" hints. Match this style in new content.
- **Ordered lists.** Step instructions intentionally use `1.` for every item (Markdown auto-numbers them). Preserve this convention.
- **Code fence languages.** Use the language that actually matches the command — e.g. `powershell` for WinGet/Windows commands, `bash` for POSIX shells (see `install-copilot-cli.md`). A prior fix corrected a WinGet block from `bash` to `powershell`; keep language tags accurate.

## Working in this repo

- **No build, test, or run step exists.** Do not attempt to install dependencies or run a test suite; there is none. "Testing" a change means validating Markdown renders correctly and, for workflow edits, that YAML is valid and triggers/checks are internally consistent.
- **Editing exercise content** — Change files under `.github/steps/`. Keep the instructions and the automated checks in the paired `.github/workflows/N-*.yml` in sync (e.g. if you change what a step asks the learner to add, update the keyphrase check).
- **Editing automation** — Change files under `.github/workflows/`. Preserve the enable/disable chaining, the `find-exercise-issue` reusable workflow usage, and the branch/path triggers.
- **General docs** (`README.md`, `install-copilot-cli.md`) — Standalone Markdown; edit freely but keep the established GFM style.
- **Validate YAML** before committing workflow changes (e.g. `yamllint` or a parser) since there is no CI to catch syntax errors before they run live.

## Git & PR workflow

- Development for automated changes in this environment happens on the branch specified by the task (currently `claude/claude-md-docs-wnhern`), **not** on the exercise's `start-blog` branch.
- Push with `git push -u origin <branch>` and open a **draft** pull request if one does not already exist for the branch.
- Be mindful that pushes to `main` and to `start-blog` trigger the exercise workflows — avoid pushing exercise-progress commits to those branches unless that is the intent.
- Commit messages should be clear and descriptive.

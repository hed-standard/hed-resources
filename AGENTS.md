# hed-resources

Purpose: the unified documentation hub for the HED (Hierarchical Event Descriptors) ecosystem - guides, tutorials, and tool references, built with Sphinx + MyST and published to https://www.hedtags.org/hed-resources. Not in scope: the HED tools themselves. Code lives in the sibling repositories listed below and is pulled in here only as git submodules.

## Commands

Test framework: unittest. No test suite exists yet; any future tests are written in unittest style. Never convert a suite from one style to the other as a side effect of other work.

Activate `.venv` first - the build shells out to `sphinx-build` and needs the environment on PATH.

- Install dev env: `pip install -e ".[docs,dev]"` (the optional-dependency groups are `docs` and `dev`; there is no `quality` group)
- Update submodules (required before building - the docs pull content from them): `git submodule update --init --recursive --remote --merge`
- Build docs: `hed-build-docs` (output lands in `docs/_build/html/`)
- Serve docs locally: `hed-serve-docs`, then open http://localhost:8000
- Spell check: `typos`
- Markdown format check: `mdformat --check --wrap no --number docs/source/*.md *.md`
- Lint: `ruff check .` and `ruff format --check .`
- Link check: `sphinx-build -b linkcheck docs/source docs/_build/linkcheck`

CI (`.github/workflows/`) runs the same checks via `uvx` on push and PR to `main`: `typos.yaml`, `mdformat.yaml`, `ruff.yaml`, `links.yaml` (weekly), and `deploy-docs.yaml`, which builds and publishes to GitHub Pages.

## Layout

- `docs/source/` - the documentation pages (MyST Markdown), `index.rst` table of contents, `conf.py` Sphinx configuration
- `docs/source/_static/` - images, downloadable data files, CSS, the redirect system
- `docs/source/_templates/` - custom HTML templates (redirect shim pages)
- `submodules/` - sibling HED repos whose docs are merged into the build
- `scripts/` - the `hed-build-docs` / `hed-serve-docs` / `hed-format-docs` entry points declared in `pyproject.toml`
- `.status/` - working notes. Gitignored; local to each machine.

## Conventions that differ from defaults

- **ASCII only** in prose, code, comments, and filenames: `-` not em or en dashes, `->` not arrows, `...` not an ellipsis character, straight quotes. Exception: genuine data (author names, dataset titles, recorded tool output) keeps whatever characters it actually contains.
- MyST anchors: a section that is linked to gets an explicit anchor, `(anchor-name-anchor)=` on the line above the heading; internal links use `[text](./OtherDocument.md#anchor-name-anchor)`.
- Images go in `docs/source/_static/images/` with descriptive kebab-case names (`remodeling-workflow.png`, not `image1.png`), and always alt text.
- Code blocks always declare a language; use `text` or `console` for terminal output.

## Rules that are easy to get wrong

- Several directories under `docs/source/` (`python-tools/`, `hed-schemas/`, and the other submodule-named ones listed in `.gitignore`) are copied from submodules at build time. They are generated - never edit them; edit the submodule's source instead.
- When a page moves or is renamed, old URLs must keep working: add the page to `docs/source/_static/redirect-config.js` and create a shim from `docs/source/_templates/redirect-page-template.html`. The procedure is `docs/source/_static/REDIRECT_SYSTEM.md`.
- `docs/_build/` is generated and gitignored - never commit build output.
- New pages must be added to `docs/source/index.rst` or they build as orphans.
- The submodule update command above combines `--recursive` and `--remote`, so it advances *nested* submodules to their own remote HEAD instead of the commit their parent pins. `hed-python/spec_tests/hed-schemas` and `ndx-hed/spec_tests/hed-tests` drift this way and then show as dirty. It is working-tree state only - it cannot enter a commit here, because this repo records each submodule as a single commit SHA, not as file contents. To put them back: `git submodule update --init --recursive` (no `--remote`).

## Related repositories

- `hed-python` - the Python hedtools implementation
- `hed-schemas` - the HED vocabularies (XML/MediaWiki/OWL)
- `hed-specification` - the formal specification of HED annotation rules
- `hed-examples` - example datasets and use cases
- `hed-matlab` - the MATLAB/EEGLAB implementation
- `hed-javascript` - the JavaScript/web implementation

These are vendored here as submodules for the docs build; their source of truth is their own repository.

## Where the thinking lives

`.status/` is gitignored, so it exists only on the machine that wrote it and never in a fresh clone or worktree.

- `.status/README.md` - the index. Read this first; it lists what is active.
- `.status/decisions.md` - why things are the way they are. Read before proposing structural changes. Append entries; never rewrite one.
- `.status/plans/*.md` - active plans. Check the `Status:` header and the `[ ]` / `[x]` markers before starting work.
- `.status/local-environment.md` - this machine's paths, interpreter, and quirks. Tool-agnostic. Never copy its contents into a committed file.
- IMPORTANT: do not read `.status/archive/` unless a file is named for you. Nothing new is created at the `.status/` root.

## Working agreements

- IMPORTANT: every file written to `.status/` opens with a `For humans:` summary - three or four sentences, at the very top: what the file is and what a person needs to take from it. The same applies to a long answer in a session: lead with the conclusion.
- IMPORTANT: temporary scripts, experiments, and one-off test files go in `.status/scratch/` - **never the repository root**. Delete them when the experiment ends; anything in `scratch/` may be deleted unread.
- IMPORTANT: never delete or rewrite a file under `.status/` without asking first. Appending is fine.
- For a change spanning more than three files, write a plan to `.status/plans/` and stop for review before editing.
- When you are guessing about an external API or data format, say so explicitly rather than assuming.
- Show evidence, not assertions: the command you ran and its actual output.
- Do not commit, push, or create branches unless asked.

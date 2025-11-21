# Repository Guidelines

## Project Structure & Module Organization
Foam-Agent centers on `src/`, which hosts agents (`src/nodes`), shared services (`src/services`), routing logic (`src/router_func.py`), and configuration defaults (`src/config.py`). High-level workflows (`foambench_main.py`, `src/main.py`) orchestrate retrieval, planning, and OpenFOAM execution. Preprocessed corpora and FAISS stores live in `database/` (`database/script/` contains parsers and embedding tools), while Docker assets for a prebuilt environment reside in `docker/`. Use `output/` and `runs/` for generated cases and logs, and keep custom meshes in the repository root beside `tandem_wing.msh`.

## Build, Test, and Development Commands
- `conda env create -n openfoamAgent -f environment.yml`: create the pinned environment used for agents and preprocessing tools.
- `python foambench_main.py --openfoam_path $WM_PROJECT_DIR --output ./output --prompt_path ./user_requirement.txt`: end-to-end pipeline; also runs database preprocessing if missing.
- `python src/main.py --prompt_path ./user_requirement.txt --output_dir ./output [--custom_mesh_path mesh.msh]`: invoke the multi-agent runner directly on an initialized database.
- `python database/script/tutorial_parser.py --wm_project_dir=$WM_PROJECT_DIR --output_dir=./database/raw`: regenerate tutorial metadata when OpenFOAM content changes.

## Coding Style & Naming Conventions
Python dominates; follow 4-space indentation, descriptive snake_case for variables/functions, and PascalCase for agent classes (e.g., `ArchitectAgent`). Keep modules cohesive (agents in `nodes/`, service wrappers in `services/`). Run a formatter such as `black` (line length 88) before pushing, and lint logic-heavy modules with `ruff` or `flake8` to catch unused imports introduced by tool integrations.

## Testing Guidelines
Use lightweight command-line validations. For retrieval sanity checks, run `python database/script/__test_faiss.py --db_name tutorials_structure --db_path ./database` to inspect stored docs. Integration tests should replay representative prompts via `foambench_main.py` and verify OpenFOAM runs converge without manual edits. Name any new test scripts `test_<feature>.py` and keep them near the component under test for quick execution.

## Commit & Pull Request Guidelines
Commits in history are short, imperative statements (e.g., “Update README…”, “mcp refactor”); follow that style and group related changes together. Pull requests should explain the scenario (prompt class, agent touched, database impact), list reproducible commands, and include screenshots or log excerpts when changing orchestration steps. Link relevant GitHub issues or research tasks and call out any new environment variables or external dependencies that reviewers must set before reproducing your run.

## Security & Configuration Tips
Never commit real API keys; rely on environment variables like `OPENAI_API_KEY` and describe them in `src/config.py`. Verify `$WM_PROJECT_DIR` resolves before launching agents, and store large meshes or benchmark outputs in `runs/` or remote storage rather than version control. When sharing logs, scrub user prompts or proprietary geometries.

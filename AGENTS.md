# Project priorities

- Read local `rules.md` when present and follow its workflow and simplicity rules.
- Before writing any code, ask: "Is it necessary to make this so complex?" Choose the simplest clear solution that meets the requirement.
- Use a notebook-first data science workflow. Keep data loading, auditing, manipulation, cleaning, feature preparation, visualisation, and modelling directly in readable `.ipynb` cells.
- Treat `notebooks/churnprediction.ipynb` as the primary analysis workspace. Organise work with Markdown explanations, focused code cells, and visible aggregate results.
- Execute notebooks interactively in VS Code using the project `.venv` kernel. Keep them runnable from a fresh kernel by running cells from top to bottom.
- Keep separate Python scripts to a minimum. Add a helper only when it serves a concrete need that is awkward to handle clearly in the notebook; keep analytical logic visible in the notebook.
- Do not introduce scripts that generate notebooks or execute and rewrite notebooks as the project's workflow.
- Preserve raw data and existing audit evidence. Export derived tables, metadata, and charts from notebook cells when needed, using the documented project paths.
- Do not commit or push unless the user explicitly requests it. A request for a commit message authorises only the message.

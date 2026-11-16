# CLAUDE.md: JAK de novo design audit repo

This repo holds everything from a de novo drug design project: generated JAK inhibitor candidates scored by AutoDock Vina inside a PPO
reinforcement-learning loop. It was built piecemeal over several months in Jupyter notebooks with several different AI tools, and it is
expected to be internally inconsistent. The current state is the evidence. Do not clean, rename, reformat or refactor anything that is
in the baseline commit (`74ca78f`).

## Deliverables, in priority order

1. An LIU master's thesis (AI 698/699), due spring 2027, judged on reproducible computation rather than on a favourable result.
   A negative or null result is reported with the same prominence as a positive one.
2. A journal manuscript, attempted only once the thesis is sound.

## Standing rules for all work in this repo

* A failed docking is a failure, never a score. The -7.000 placeholder must never enter a ranking, a mean, a figure, or a summary statistic.
* Every reported number must be regenerable by running a named script against an archived input file. A number that cannot be traced is
  withdrawn, not hedged.
* Every quantity carries one of three labels: **verified**, **pending**, **withdrawn**.
* Two code lines exist and must never be conflated: the reinforcement-learning notebooks that produced all the exported data, and a separate
  edit-family notebook (seven generation families, MMR lambda 0.6, a Pareto step, a calibrator, a random-forest surrogate) that produced none of it.
* Never invent a number, a citation, or a result. If something is missing from this folder, say so and ask.

## How the audit is run

* Work in Claude Code only while the audit is under way. Several tools editing the same notebook is the likely source of parameter drift.
* Six steps, in order: (1) inventory and baseline, (2) this file, (3) reconcile the notebooks against each other, (4) trace every number,
  (5) two specific checks (the gate against recomputed descriptors; the ranking without the failed dockings), (6) `AUDIT.md`.
* Stop at the end of each step and show the findings. Commit after each step with a message that says what changed.
* No fixes until the full list has been reviewed. Findings are recorded, not repaired.
* Ask when something is ambiguous rather than guessing.

## Rules from the student that also apply

* Write in the first person for the student's documents. Never fabricate, estimate or manipulate a result.
* No SMILES strings and no molecular depictions in any Word document. (They are allowed in notebooks, CSV files and scripts.)
* Never put the student's email address anywhere in this repo, in commits, or in anything sent out. Commits here use a placeholder identity.
* Report files by their full absolute path.
* The notebook cells v2[1], v2[5] and v2[26] (in `produced_during_project/v2.ipynb`) are frozen: the generated builds must keep them byte-identical.

## Layout

* `received_from_student/`: exactly what the student uploaded or pasted. Names unchanged (upload-id prefixes included).
  * `zip_extracted/`: members of the exported-tables zip, byte-identical to the archive. The `(1)` files duplicate the plain ones.
  * `attachments/`: pasted notebooks (as text), adviser messages, other tools' output, run logs, documents, Colab sessions.
  * `uploads/`: the original single-file RL notebook and one AI scratch note.
* `produced_during_project/`: files created during the AI 698 work with Claude.
  * `deliverables/`: revised manuscript, progress report, figures, `verification/` and `verification2/` (analysis scripts, outputs, discrepancy logs), `thesis_tools/`.
  * `build.py`, `build2.py`, `build3.py`, `mods/`, `nc2/`, `docedit/`, `ver/`: sources that generate the modular and thesis notebooks and the documents.
  * Intermediate notebooks: `original.ipynb`, `v2.ipynb`, `organized`, `modular_before`, `thesis_before`, `modular`, `thesis`.
* `INVENTORY.md` identifies every file group. Read it before relying on any file.

## Code lines (do not conflate)

| Line | Files | Status |
|---|---|---|
| RL pipeline, monolithic | `uploads/DeNovoJAK_Unified_Pipeline.ipynb` (= `produced/original.ipynb`), the pasted `8f1a936a`, `356b1212` | Produced the exported campaign data (the 75 visits, 190 final rows). |
| RL pipeline, modular and thesis builds | `produced/...modular.ipynb`, `...thesis.ipynb`, generated from `build2.py`/`build3.py`; Colab runs `96c37c43`, `9b517a30` | Produced the later runs only. Not the legacy 75-visit data. |
| Edit-family notebook | `received/attachments/80a98e20-attachment.txt` (text only; the .ipynb is not in the folder) | Produced none of the exported data. |

Which build produced which exported file is itself an audit question (steps 3 and 4); do not assume it.

## What is known so far (step 1; all counts read from the files, none re-derived yet)

* `final_ranked` has 190 rows over 19 PDB structures: 103 `vina_cli_e8` and 87 `cli_fallback_error` (the failed-docking rows that carry the -7.000 placeholder). The W&B compilation has 75 rows (visits).
* There is no D01-D26 discrepancy log; the current log has 49 rows (D01-D49) and the earlier version 21 rows.
* The student's own progress report is not in the folder; only the revised one is. The edit-family notebook exists only as pasted text.
* GNN architecture differs across files (GCN default in two pasted notebooks, GraphSAGE in the current builds, both in the two W&B configs). Step 3 resolves this; do not pick one before then.
* `9b517a30-attachment.txt` (executed modular notebook, 15 Nov) differs from my modular build in three cells. The student confirmed on 15 Nov that the 8-structure `professor_3` roster and the disabled essential-scaffold filter are **intentional**. Both still differ from the adviser's January protocol (3EYH, 3LPB, 3PJC) and must be labelled as such, not as drift. The student added the six reference drugs himself; their source is unrecorded and their structures are unverified.

## Labels in force (updated after audit steps 3-6; the full list is in `AUDIT.md`)

* **verified**: file inventory and row counts; credential scan (none found); 87 of 190 final rows are failed dockings; the exported composite and rank reproduce (1e-13); the notebook's property-range gate rejects 48 of 190 exported rows (descriptors match to 1e-14); the 17 reproduced manuscript and progress-report numbers in `audit/STEP4_TRACE.md`.
* **pending**: which notebook version and configuration produced the 75 visits and 190 rows; why the stored gate flags say pass; the real top-10 per structure (560 of 750 candidates are not archived); the 12 new-run visits (printout only).
* **withdrawn**: every number in section D of `audit/STEP4_TRACE.md` (not reproducible) and every contradicted item in its section C, until re-derived; any ranking, mean or top-10 figure that includes the 87 placeholder rows.

## Conventions

* New analysis goes in a new named script with its input file stated at the top; its output file is committed next to it.
* A failed docking is excluded before any statistic is computed, and the exclusion count is printed.
* Commit messages end with the standard co-author trailer.

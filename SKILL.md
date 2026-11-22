---
name: viz-dboard
description: >
  Use when visualizing skill hierarchies, generating a mindmap of local skills,
  or building/verifying a demo app.py (Streamlit / PyVista / Panel) that must be
  grounded in file-distill's distilled example docs. Triggers on "mindmap",
  "skill map", "viz-dboard", "visualize skills", "demo", "app.py", or
  "distilled example".
---

# Viz Dboard

Visualize a set of skills as a mindmap in SVG format.

## 1. Inventory the skills

List the skills under review and their parent-child relationships.

## 2. Build the hierarchy

Define nodes and edges:

- Root: the collection (e.g., "self-developed skills")
- Parents: orchestrator skills
- Children: atomic capabilities hooked by parents

## 3. Render the mindmap

Create an SVG file (e.g., `skill-map.svg`) with:

- A root node
- Branches for each parent
- Child nodes connected by edges
- A legend for node types

The included example mindmap is at `skill-map.svg`.

## 4. Validate

```bash
python3 -c "import xml.etree.ElementTree as ET; ET.parse('skill-map.svg'); print('SVG valid')"
```

## Demo Implementation Prerequisite — Distilled-First (from file-distill)

Before writing or changing **any** demo `app.py`, open the distilled example docs first. This is a hard prerequisite, not a suggestion.

**Required read order:**

1. `~/荐家/布球/INDEX.md` — the map of distilled assets and their intended scope.
2. The matching `*_distilled.md` for the demo's domain (e.g. `布球_distilled.md`, `渲染邏輯_distilled.md`, `qskt_distilled.md`). These are the outputs of `file-distill`; they define the vocabulary, formulas, worked examples, and limits that the demo must reflect.
3. Only then the raw `.py` experiments and any sibling `app.py` — for implementation details *after* the distilled source is understood.

**Why:** raw scripts are experiments and may contain superseded backends (`0維軟渲染.py` → PIL; `10態演化布球.py` → matplotlib `plot_surface`). A sibling demo can propagate its own mistakes. The distilled note is the curated source of truth; the raw script is evidence, not authority.

| Rationalization | Counter |
|---|---|
| "Distilled notes are second-hand; the raw `.py` is authoritative." | The distilled note is the curated output of `file-distill`; raw scripts are uncurated experiments, some of which violate current backend rules. |
| "No time — copy the sibling demo and read source directly." | Reading the index + one distilled note is faster than debugging a copied demo that encodes a retired backend. |
| "INDEX.md doesn't change code, so skip it." | INDEX tells you which distilled note is authoritative for this domain; skipping it means guessing the source. |

**Red flags — stop if you catch yourself:**

- Opening a sibling `app.py` before `INDEX.md` / the matching `*_distilled.md`.
- Claiming "raw source is more reliable" to avoid the prerequisite.
- Describing the demo only in terms of "it runs" instead of mapping UI sections/formulas back to the distilled note.

**Done criterion:** the demo's sections, formulas, and terminology can be traced to specific distilled files, and code comments or UI captions name those files. "It runs" alone is not sufficient. Apply the Streamlit rules below only after this prerequisite is met.

## Streamlit Rules (from push-streamlit)

When visualizing Streamlit-based skill hierarchies or dashboards, apply these rules:

- **Single entry point**: `app.py` with `st.set_page_config` called once + `src/views/` modules + `requirements.txt`
- **No `pages/` directory or `st.navigation`**: causes StreamlitAPIException on Streamlit Cloud
- **Sidebar radio pattern**: theme blocks provide `<name>_sidebar()` with `horizontal=True`, app.py calls conditionally
- **`__init__.py` sync**: keep `__init__.py` and `app.py` content in sync (supports `streamlit run __init__.py`)
- **Relative paths**: use relative paths for assets (avoid cloud path errors)
- **Local testing**: `streamlit run app.py`
- **Deployment target**: Streamlit Cloud only (interactive, needs GitHub login). Do not use HF Spaces or Kaggle for interactive apps.
- **Widget choice**: Use `st.radio` instead of `st.selectbox` for option selection (better UX, clearer affordance)
- **Layout**: Use `st.columns([3, 7])` for left-right layout (left panel 30%, right panel 70%)
- **Container width**: `use_container_width=True` → use `width='stretch'`. `use_container_width=False` → use `width='content'`.
- **Context manager**: Use `with` identifier for layout blocks (e.g., `with col1:`), not `expand`.
- **Charting library**: 2D charts → **`matplotlib` only** — no `seaborn`, no `PIL`/Pillow, no `plotly`. 3D charts → **`pyvista` + `panel`**, unconditionally (including Bloch spheres). `PIL`-based soft rendering exists only as historical reference in `0維軟渲染.py`; do not use it for new 2D charts.
- **Demo tool completion**: When a demo tool is developed, provide its **location** (file path) and **startup method** (command to run). The tool name and path should reflect the **distill output name**, not a fixed name. Example: "Location: ~/薦家/布球/<distill-name>/app.py | Startup: streamlit run app.py".
- **Demo code compliance**: a listed demo is compliant only when its `app.py` actually uses the mandated backend (2D → matplotlib, 3D → PyVista + Panel). Verify by **reading the code**, not the README. A `legacy` / `exception` label in docs does not make a violating demo compliant — convert the code or remove the entry. Full rule: `rev-skill` → Logic Consistency Check, step 8.
- **Post-check demo hook (review workflows only)**: this skill is the hook target of `rev-skill` → Logic Consistency Check, step 9 **when the workflow goal is a review**. Verify the domain skill's demos — for `qbit-viz`, verify the single `qbit-gate-viz/app.py` (2D matplotlib + 3D PyVista/Panel) **plus the skill's own example snippets**. A retired standalone demo is not a verification target. Do not duplicate the logic check itself here. For a publish/push workflow, this hook is **not** invoked — no drawing needed.

## Demo Tools

| Tool | Location | Startup | Description |
|------|----------|---------|-------------|
| `<distill-name>` | `~/薦家/布球/<distill-name>/app.py` | `streamlit run app.py` | Distilled demo tool (location and name derived from the distill output) |
| qbit-gate-viz | `~/薦家/布球/qbit-gate-viz/app.py` | `streamlit run app.py` | Single integrated app: Qiskit/Aer 1/2/3 qubit gates + 2D Bloch projection (matplotlib) + 3D Bloch evolution (PyVista → Panel VTK pane → `st.iframe`) |

> **Retired standalone demo:** `~/荐家/布球/bloch-evolution-viz/app.py` (`panel serve app.py --show`) has been folded into `qbit-gate-viz/app.py`. Do not list it as an active demo or copy from it; the 3D evolution now lives inside the one app.

> **Note:** Replace `<distill-name>` with the actual distill output name (e.g., `quant-dboard`, `qbit-viz-dashboard`). The tool name and path must reflect the distill output name, not a fixed name.
- **Conversion example**: `st.expand` / `st.expander` → `with` context:
  ```python
  # Before (avoid):
  st.expander("Details")
  st.write("content")

  # After (preferred):
  with st.expander("Details"):
      st.write("content")
  ```
  Or for layout blocks:
  ```python
  # Before:
  col1, col2 = st.columns(2)
  col1.write("left")
  col2.write("right")

  # After:
  with st.columns([3, 7])[0]:
      st.write("left")
  with st.columns([3, 7])[1]:
      st.write("right")
  ```

## Notes

- Keep node labels short (skill names).
- Use dashed lines for conditional hooks.
- Update the SVG whenever the skill hierarchy changes.
- **Hooked by:** `chain-skill` (optional, after refactoring — visualize the resulting hierarchy).
- Logic check: 2026-11-22 — charting rule tightened to 2D=`matplotlib` only (no seaborn/PIL) and 3D=`pyvista`+`panel` unconditionally; demo table corrected; added demo-code compliance hook (source: `rev-skill` step 8); `inspect-skill` → `chain-skill` reference updated; SVG junk removed (validated); post-check demo hook added (source: `rev-skill` step 9); now explicitly the hook target of that step. Seventh pass (user rule via `rev-skill`): added **Demo Implementation Prerequisite — Distilled-First** — `INDEX.md` + matching `*_distilled.md` from `file-distill` must be read before raw `.py` or sibling demos, with rationalization table and red flags from the RED baseline; description updated to cover demo verification; demo table now lists one integrated `qbit-gate-viz/app.py` and marks `bloch-evolution-viz` retired.
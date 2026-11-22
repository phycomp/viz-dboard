---
name: viz-dboard
description: >
  Use when visualizing skill hierarchies or generating a mindmap of local
  skills. Triggers on "mindmap", "skill map", "viz-dboard", or "visualize
  skills".
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
- **Post-check demo hook**: after a logic check, hook the domain skill's demos — for `qbit-viz`, verify `qbit-gate-viz/app.py` (2D matplotlib) and `bloch-evolution-viz/app.py` (PyVista + Panel), **plus the skill's own example snippets**. Full rule: `rev-skill` → Logic Consistency Check, step 9.

## Demo Tools

| Tool | Location | Startup | Description |
|------|----------|---------|-------------|
| `<distill-name>` | `~/薦家/布球/<distill-name>/app.py` | `streamlit run app.py` | Distilled demo tool (location and name derived from the distill output) |
| qbit-gate-viz | `~/薦家/布球/qbit-gate-viz/app.py` | `streamlit run app.py` | Qiskit/Aer 2/3 qubit logic gate demo with 2D Bloch projection |
| bloch-evolution-viz | `~/薦家/布球/bloch-evolution-viz/app.py` | `panel serve app.py --show` | 3D Bloch sphere + qubit evolution (PyVista + Panel) — the compliant 3D demo |

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
- Logic check: 2026-11-22 — charting rule tightened to 2D=`matplotlib` only (no seaborn/PIL) and 3D=`pyvista`+`panel` unconditionally; demo table corrected; added demo-code compliance hook (source: `rev-skill` step 8); `inspect-skill` → `chain-skill` reference updated; SVG junk removed (validated); post-check demo hook added (source: `rev-skill` step 9).
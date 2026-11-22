# viz-dboard

Visualize a set of skills as a mindmap (SVG) and provide Streamlit demo-tool conventions used across self-developed quantum/visualization skills.

## What it does

- Inventory skills and their parent-child relationships, render `skill-map.svg`, validate it
- Streamlit rules: single `app.py`, no `pages/`, `st.radio` not `st.selectbox`, `st.columns([3,7])`, `width='stretch'`/`'content'`, `with` context, 2D → matplotlib, 3D → PyVista + panel
- Demo-tool convention: location and startup must be reported; name/path derived from the distill output name

## Install

```bash
npx skills add phycomp/viz-dboard@viz-dboard -g -y
```

## Usage

Trigger on "mindmap", "skill map", "visualize skills", "viz-dboard".

## Contents

- `SKILL.md` — mindmap workflow, Streamlit rules, demo tools table
- `skill-map.svg` — example mindmap

## License

MIT

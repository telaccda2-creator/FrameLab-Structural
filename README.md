# FrameLab Structural

FrameLab is an independently implemented web structural-analysis application for 3D linear-static frame models. It uses a Python FEM backend and a React/Three.js front end.

## Architecture

`React + TypeScript + React Three Fiber → FastAPI → independent NumPy/SciPy FEM engine`

The FEM package does not import FastAPI and can be tested directly.

## Engineering conventions

- Global axes: X, Y, Z; Z is vertical.
- Internal units: SI (m, N, Pa, kg/m³).
- Node DOF order: `UX, UY, UZ, RX, RY, RZ`.
- A frame member has 12 DOFs: the six DOFs of its start node followed by the six DOFs of its end node.
- Euler–Bernoulli 3D frame elements are used. The standard 12×12 local elastic stiffness matrix is assembled from axial, torsional, and the two bending families. This is consistent with standard space-frame matrix formulations.
- Shear modulus is `G = E / (2(1+ν))`.
- Local axes are constructed from the member axis and an optional orientation vector; vertical members use a stable fallback reference vector.
- Member uniform and point forces currently accept the **local** coordinate system only. Unsupported global-coordinate member loads are rejected rather than silently reinterpreted.

## Implemented workflow

Project → materials → sections → nodes → members → supports → load cases → nodal/member loads → combinations → validation → FEM assembly → constraints → sparse solve → displacements → member end forces → reactions → equilibrium checks → API → 3D result UI → save/load JSON.

The frontend uses React Three Fiber's Canvas and demand rendering to reduce unnecessary rendering work on a relatively weak computer.

## Backend

```bash
cd backend
python -m venv .venv
# Windows: .venv\\Scripts\\activate
# macOS/Linux: source .venv/bin/activate
pip install -r requirements.txt
uvicorn app.main:app --reload --port 8000
```

API documentation is available from FastAPI at `/docs` while the server is running. FastAPI/Pydantic provide request-model validation and structured validation errors.

## Frontend

```bash
cd frontend
npm install
npm run dev
```

Open `http://localhost:5173`.

## Tests

Backend:

```bash
cd backend
pytest -q
```

Frontend production build:

```bash
cd frontend
npm run build
```

## API basics

- `GET /api/health`
- `POST /api/projects/validate`
- `POST /api/projects/analyze`
- `POST /api/projects/save`
- `POST /api/projects/load`

`POST /api/projects/analyze` accepts `{ project, load_case_id?, combination_id? }`. If neither is supplied, all defined load cases are included with factor 1.0. A load case and combination cannot be selected simultaneously.

## Current limitations

This release is a linear-static 3D frame engine, not a complete replacement for commercial structural-analysis suites. It does not implement slabs/shells, meshing, modal analysis, response-spectrum analysis, nonlinear material behavior, P-Delta/geometric stiffness, automated code design, rigid diaphragms, or external ETABS/SAP import/export.

Member distributed/point forces are implemented in local member coordinates. Non-torsional concentrated member moments and global-coordinate member loads are explicitly rejected in the current solver.

The UI is a working modeling/results shell; detailed editing dialogs for every object type are intentionally not represented as fake functionality. The project JSON can be edited/loaded, and the 3D scene/analysis workflow is functional.

## Security

The server does not execute project content, does not deserialize Python objects, and the project save/load endpoints operate on validated Pydantic models. No frontend secret or API key is required.

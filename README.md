# AR Product Viewer

> **Full-stack AR Product Viewer** — README for both frontend and backend parts of the project.

---

## Table of contents

1. [Project overview](#project-overview)
2. [Repository structure](#repository-structure)
3. [Prerequisites](#prerequisites)
4. [Environment variables](#environment-variables)
5. [Backend — Setup & run](#backend---setup--run)
6. [Frontend — Setup & run](#frontend---setup--run)
7. [Common API endpoints (examples)](#common-api-endpoints-examples)
8. [AR assets and formats](#ar-assets-and-formats)
9. [Development tips](#development-tips)
10. [Docker (optional)](#docker-optional)
11. [Troubleshooting](#troubleshooting)
12. [Contributing](#contributing)
13. [License & Contact](#license--contact)

---

## Project overview

This repository contains the frontend and backend for an **AR Product Viewer** application. The app lets users browse products and view realistic AR previews (e.g., 3D models) in their environment using the device's camera (WebAR / WebXR or native wrappers).

The README below gives generic, ready-to-adapt instructions for running and developing both parts locally. If your project uses different frameworks/tools, replace the commands accordingly.

---

## Repository structure

```
root/
├─ backend/                # Server (API, DB migrations, uploads)
├─ frontend/               # Client (React / Vite / Next etc.)
├─ README - AR Product Viewer.md
└─ .gitignore
```

> Note: The frontend and backend are kept in separate folders so they can be run independently.

---

## Prerequisites

* Node.js (LTS recommended, e.g. 16+)
* npm or yarn
* (Optional) Docker & Docker Compose for containerized setup
* A database (Postgres / MongoDB recommended depending on backend implementation)
* `git` for source control

---

## Environment variables

Create `.env` files in each of `backend/` and `frontend/` with values adapted for your environment.

### Backend `.env` (example)

```
# backend/.env
PORT=4000
NODE_ENV=development
DATABASE_URL=mongodb://localhost:27017/ar_product_viewer
JWT_SECRET=your_jwt_secret_here
UPLOADS_DIR=uploads
CORS_ORIGINS=http://localhost:3000
```

### Frontend `.env` (example)

```
# frontend/.env
VITE_API_BASE_URL=http://localhost:4000/api
VITE_AR_ASSETS_PATH=/assets/ar
VITE_APP_TITLE="AR Product Viewer"
```

> Replace `VITE_` with `REACT_APP_` if you use Create React App. Use the correct prefix for your frontend tooling.

---

## Backend — Setup & run

> Assumes a Node.js + Express style backend. If your backend is Python/Flask or something else, adapt commands accordingly.

1. Open a terminal and `cd` into the `backend/` folder.

2. Install dependencies:

```bash
npm install
# or
yarn install
```

3. Start the development server:

```bash
npm run dev
# or
yarn dev
```

`npm run dev` should run the server with hot reload (e.g. via `nodemon`). Use `npm start` for a production run.

4. (Optional) Seed the database:

```bash
npm run seed
# or
node scripts/seed.js
```

5. Uploads & static assets:

* Ensure the `UPLOADS_DIR` exists and is writeable by the server.
* Configure any CDN or static-hosting for AR assets if needed.

---

## Frontend — Setup & run

> Assumes a modern JS frontend (React + Vite / Create React App / Next). Adjust commands to your stack.

1. `cd frontend/`

2. Install dependencies:

```bash
npm install
# or
yarn
```

3. Run locally:

```bash
npm run dev
# or (CRA)
npm start
```

4. Build for production:

```bash
npm run build
```

5. Connect to backend:

* Ensure `VITE_API_BASE_URL` (or `REACT_APP_API_URL`) points to your backend API (e.g. `http://localhost:4000/api`).

6. AR integration:

* The app will typically reference 3D models (GLB, glTF) and AR configuration files via a public `assets/` or an S3/CDN.
* If using libraries like `three.js`, `aframe`, `model-viewer`, or `webxr`, ensure they are added to `package.json` and imported correctly.

---

## Common API endpoints (examples)

> These are placeholders — update to reflect your real API.

* `POST /api/auth/login` — login user (body: `email`, `password`) → returns JWT
* `POST /api/auth/register` — create account
* `GET /api/products` — list products
* `GET /api/products/:id` — product details (includes AR asset metadata and URLs)
* `POST /api/products` — (admin) create product with AR asset metadata
* `POST /api/upload` — upload 3D model / image (multipart/form-data)
* `GET /api/assets/:filename` — serve static AR assets

### Example product JSON

```json
{
  "_id": "64b8f1...",
  "title": "Wooden Chair",
  "description": "A handcrafted wooden chair",
  "price": 1299.0,
  "arModel": {
    "url": "https://cdn.example.com/models/wooden-chair.glb",
    "format": "glb",
    "scale": 1.0
  }
}
```

---

## AR assets and formats

Recommended formats:

* `glTF` (`.gltf` / `.glb`) — best for WebGL & WebXR
* `USDZ` — required for AR Quick Look on iOS (if you want native iOS AR preview)
* Textures: `jpg`, `png`, `ktx2` (for compressed textures)

Conversion tips:

* Use Blender / FBX2glTF / obj2gltf for converting models to `.glb`.
* Generate USDZ from glTF for iOS Quick Look (tools exist or cloud converters).

---

## Development tips

* Use `model-viewer` for quick 3D/AR previews on web: `<model-viewer src="/path/model.glb" ar alt="...">`.
* Test AR on real devices: WebAR support varies widely across Android browsers and iOS versions.
* Serve assets over HTTPS for secure camera access (required by many browsers for WebXR).
* Keep 3D model sizes optimized: use LODs, texture compression.

---

## Docker (optional)

A simple `docker-compose.yml` can start the backend, frontend, and a database. Example skeleton:

```yaml
version: '3.8'
services:
  db:
    image: mongo:6
    ports:
      - '27017:27017'
    volumes:
      - db-data:/data/db
  backend:
    build: ./backend
    ports:
      - '4000:4000'
    environment:
      - DATABASE_URL=mongodb://db:27017/ar_product_viewer
  frontend:
    build: ./frontend
    ports:
      - '5173:5173'
volumes:
  db-data:
```

---

## Troubleshooting

* **CORS errors**: Ensure backend CORS includes the frontend origin.
* **Camera/AR not working**: Must use HTTPS and test on a supported device/browser.
* **Large asset upload fails**: Increase server body parser limits and proxy buffering (or upload directly to S3).
* **Model not visible**: Check the model path, content-type, and browser console for WebGL errors.

---

## Contributing

* Fork the repo
* Create a feature branch (`git checkout -b feat/awesome`)
* Commit changes (`git commit -m "feat: add ..."`)
* Open a PR with a clear description and screenshots (for UI/AR changes)

---

## License & Contact

Choose an appropriate license (e.g. MIT) and add a `LICENSE` file.

If you'd like me to customize this README to match your project's exact stack (framework names, exact scripts in `package.json`, environment variable names, or the precise API endpoints), tell me which framework each folder uses and I will update the README accordingly.

---

*Generated README — adapt the placeholders to match your codebase.*

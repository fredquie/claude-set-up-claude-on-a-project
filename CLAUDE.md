# CLAUDE.md

Starter Express API (Node, CommonJS) with in-memory data. Entry point: `server.js`.

## Commands

- `npm run dev` — start the API with `--watch` on http://localhost:3000
- `npm test` — run tests with `node --test` (must pass before committing)
- `npm run lint` — ESLint over the whole repo (CI fails on errors)
- `npm install` — after changing dependencies, use npm rather than editing `package.json` by hand

## Architecture

- `server.js` — builds the Express `app`, mounts routers, and exports `app`. It only calls `listen` when run directly (`require.main === module`), so tests can import it without opening a port.
- `routes/` — one router file per resource (`users.js`, `health.js`). Mount new routers in `server.js`.
- `db/store.js` — the only data access layer. Routes call `store.*`; they never touch the in-memory array directly. Data resets on every restart.
- `tests/` — `node:test` + `supertest`, importing `app` from `../server`.
- `.env` — holds local config (`PORT`). Never read it, print it, or commit it; `.env.example` is the template.

## Conventions

- Use CommonJS: `require()` and `module.exports`. Do not use ESM `import`/`export`.
- Match the existing style: double quotes, semicolons, 2-space indent.
- Route handlers return errors as `res.status(<code>).json({ error: "<message>" })`. Use 400 for invalid input and 404 for a missing resource.
- Route params are strings: convert them with `Number(req.params.id)` before calling the store.
- Unused function arguments must be named `_`, `req`, `res`, or `next` to pass the lint rule.
- Every new endpoint needs a test in `tests/` covering the success case and the main error case.

## Gotchas

- CI runs on Node 22 (`.github/workflows/ci.yml`). Avoid language or APIs newer than Node 22.
- Changing `db/store.js` changes the seed data that tests rely on (`GET /users` expects at least one user).

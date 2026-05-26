# AGENTS.md — DirectorioTelecable-Vue

## Project structure

- Git root is `/home/jonathan/Documents/Documentos/DirectorioTelecable-Vue`, but the **actual Vue app lives in `Directorio-Vue/`**.
- All `npm` commands must run inside `Directorio-Vue/`.

## Stack

- Vue 3 (`<script setup>`, Composition API) + vue-router 4 + Vite 7.
- Plain JavaScript — **no TypeScript, no ESLint, no Prettier, no tests**.
- Only dependencies: `vue`, `vue-router`, `@vitejs/plugin-vue`, `vite`.

## Commands (run from `Directorio-Vue/`)

| Action | Command |
|--------|---------|
| Dev server | `npm run dev` |
| Build | `npm run build` |
| Preview build | `npm run preview` |

No lint, typecheck, or test commands exist.

## Architecture quirks

- **No backend, no API, no database.** All data is hardcoded as JS arrays inside the Vue component files (`Login.vue`, `oficinas.vue`, `desarrollos.vue`, `extensiones.vue`, `formularios.vue`, `infogeneral.vue`).
- **Auth is client-side only.** `Login.vue` validates against 13 hardcoded email/password pairs, then sets `localStorage.setItem("usuarioAutenticado", "true")`. No tokens, no server.
- **Detail views pass data via localStorage.** `OficinaDetalle.vue` reads `localStorage.oficinaSeleccionada`; `DesarrolloDetalle.vue` reads `localStorage.desarrolloSeleccionado`. List views write these before navigating.
- **Router uses `createWebHistory()`** (HTML5 history mode, no hash). Deployment must handle SPA fallback — already configured in `vercel.json`.
- **Route guard:** `/home` and all child routes check `localStorage.getItem("usuarioAutenticado") === "true"`. Unauthenticated users are redirected to `/`.

## Content conventions

- UI text is in **Spanish** throughout (column headers, labels, form fields, etc.).
- App is a corporate directory for **Telecable** (Colombian cable provider): offices, franchise points, PBX extensions, sales forms, bank accounts, corporate emails.
- Commit messages are Spanish / generic (`"Cambios"`, `"Credenciales"`).

## Deployment

- Deployed via **Vercel** with SPA rewrites (`vercel.json`).
- No CI/CD pipelines present.

## AI agent skill

- `.agents/skills/frontend-design/SKILL.md` provides an Anthropic "frontend-design" skill for UI work. The repo has `skills-lock.json` locking this skill version.

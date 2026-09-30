# Tablero de Operaciones — Not a Bot Agency

## Qué es
Front estático (HTML/CSS/JS, SIN build, SIN backend) publicado en GitHub Pages.
Repo: **AndresNavarroGuitart/notabot-tablero-operaciones** (PÚBLICO). El repo ES el sitio
(el `index.html` de la portada está en la raíz).
URL pública: https://andresnavarroguitart.github.io/notabot-tablero-operaciones/

## Identidad visual (notabotagency.es)
- Fonts: "DM Serif Display" (títulos) + "Alegreya Sans" (texto)
- Verde primario `#03524E`, verde marca `#20574E`; acentos magenta `#CC3366`, terracota `#C84E1E`
- Tema claro/oscuro vía `data-theme`; tokens y shell en `assets/theme.css`; `.wrap` max-width 1500px

## Módulos
- `nomina/`    → planilla + ficha panel + edición. localStorage: `nba-nomina-empleados`
- `pipeline/`  → kanban + lista de leads. localStorage: `nba-pipeline-leads`
- `proyectos/` → espejo de solo lectura de Notion. Datos en `proyectos/proyectos-data.js`
- `time-summary/` → carga de horas por colaborador (Rastreador + Planilla + Resumen mensual),
  para liquidar el pago mensual. Sí forma parte del tablero (listado en `data.js`).
  localStorage: `nba-timesummary-*`. El envío del resumen mensual va a un Google Form
  externo (config pendiente — ver `time-summary/SETUP.md`).
- `index.html` → portada: 6 procesos + 4 KPIs. Lógica en `app.js`, datos en `data.js`
- `alta-colaborador/` → formulario de onboarding, **INDEPENDIENTE del tablero** (sin nav
  hacia/desde el resto, no listado en `data.js`). No usa localStorage; el envío es a un
  Google Form externo (config pendiente — ver `alta-colaborador/SETUP.md`).

## REGLAS DE SEGURIDAD (un cliente está certificando ISO 27001) — NO NEGOCIABLES
- El repo es PÚBLICO y GitHub Pages lo sirve sin autenticación.
- NUNCA commitear datos reales de personas ni de clientes. Todos los datasets demo
  (nombres, clientes, proyectos, mails, cuentas bancarias) deben ser FICTICIOS.
- Todo lo que se inyecta con `innerHTML` debe pasar por el helper `esc()`.
- No activar el sync automático de Notion mientras el repo sea público (ver `proyectos/SYNC.md`).
- No conectar el Form/Sheet de RRHH a la Nómina mientras el sitio sea público y sin login
  (ver `nomina/EMPLEADOS-SYNC.md` — plan y requisitos para cuando haya destino privado).
- Plan completo para pasar a producción (login con Google, Firestore, roles, cifrado,
  logging de accesos): ver `PLAN-PRODUCCION.md`. No implementar nada de eso sin que el
  usuario lo pida explícitamente — hoy el sitio sigue siendo demo público a propósito.
- `alta-colaborador/`: NO reemplazar `CONFIG.consentText` en `alta-colaborador.js` por
  ningún texto que no sea el aviso legal real de Not a Bot Agency (razón social, CUIT/NIF
  y contacto correctos). No completar `CONFIG.formActionUrl`/`entryIds`/`docsFormUrl` con
  datos inventados — solo con los reales, siguiendo `alta-colaborador/SETUP.md`.
- `time-summary/`: no completar `CONFIG.formActionUrl`/`entryIds` en `time-summary.js` con
  datos inventados — solo con los reales, siguiendo `time-summary/SETUP.md`. Sin login,
  cualquiera puede cargarle horas a cualquier colaborador del desplegable — no tratar el
  resumen mensual como definitivo hasta que haya roles reales (`PLAN-PRODUCCION.md`).

## Ritual de publicación (SIEMPRE en este orden)
1. Verificar el cambio en el navegador (server local, ver abajo).
2. Actualizar `CHANGELOG.md` (estilo Keep a Changelog, en español).
3. Subir el `?v=X.Y.Z` en TODOS los `<script>`/`<link>` de los 6 index.html
   (`index.html`, `nomina/index.html`, `pipeline/index.html`, `proyectos/index.html`,
   `alta-colaborador/index.html`, `time-summary/index.html`). NO versionar
   `proyectos/proyectos-data.js`.
4. `git commit` (mensaje en español).
5. `git tag -a vX.Y.Z -m "..."`
6. `git push origin master && git push origin vX.Y.Z`
7. `gh release create vX.Y.Z --title "..." --notes "..."`
8. Esperar 1-2 min y confirmar con `curl` que GitHub Pages ya sirve la versión nueva.

Versionado semántico: feature nueva = MINOR, fix/datos = PATCH.
Última versión publicada: **v1.4.0**.

## Servidor local
```
powershell -NoProfile -ExecutionPolicy Bypass -File serve.ps1
```
Corre en el puerto 3005 sirviendo la raíz del repo. Abrir:
- `http://localhost:3005/index.html` (tablero)
- `http://localhost:3005/nomina/index.html` (Nómina)
GitHub Pages sí resuelve `/` → `index.html`.

## Convenciones de código
- Vanilla JS puro, sin frameworks ni dependencias. Un IIFE por módulo, `"use strict"`.
- Router por hash (`#/`, `#/empleado/:id`, etc.). Vistas con `<template>` o markup construido en JS.
- Helper `esc()` para todo lo que va a `innerHTML`.
- El módulo Nómina: la ficha es un PANEL de una página (no solapas). Rutas
  `#/empleado/:id`, `#/empleado/:id/doc`, `#/empleado/:id/adm`, `#/empleado/:id/editar`, `#/nuevo`.

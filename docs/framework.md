# Framework: Proyecto de software de inicio a fin con Claude

## Visión general

Cada fase tiene **items** que pueden ser de tres tipos:

| Icono | Tipo | Descripción |
|-------|------|-------------|
| 📝 | **Plantilla** | Documento que el usuario rellena guiado por preguntas/estructura |
| 🤖 | **Comando** | Claude lo ejecuta automáticamente (slash command, script, agente) |
| 🔀 | **Mixto** | Claude genera un borrador y el usuario valida/ajusta |

---

## FASE 0 — DESCUBRIMIENTO (Discovery)

> **Objetivo:** Pasar de "tengo una idea" a "tengo un proyecto definido"

| # | Item | Tipo | Tienes | Descripción |
|---|------|------|--------|-------------|
| 0.1 | `vision` | 📝 | ✅ parcial | Intención, problema que resuelve, propuesta de valor, elevator pitch de 1 párrafo |
| 0.2 | `target-users` | 📝 | ❌ | Quién lo va a usar: personas, empresas, devs, público general. User personas con dolor, motivación, contexto |
| 0.3 | `competitive-analysis` | 🔀 | ❌ | Claude investiga competidores/alternativas, el usuario valida. Tabla comparativa de features, pricing, debilidades |
| 0.4 | `constraints` | 📝 | ❌ | Presupuesto, deadline, equipo disponible, limitaciones técnicas conocidas, requisitos legales (GDPR, accesibilidad, etc.) |

---

## FASE 1 — DEFINICIÓN (Definition)

> **Objetivo:** Convertir la visión en requisitos concretos y medibles

| # | Item | Tipo | Tienes | Descripción |
|---|------|------|--------|-------------|
| 1.1 | `requirements` | 🔀 | ❌ | Requisitos funcionales y no funcionales. Claude genera borrador a partir de la visión, usuario refina |
| 1.2 | `user-roles` | 📝 | ✅ parcial | Roles, permisos, jerarquía de acceso, flujos de autenticación (login, registro, recuperación, OAuth) |
| 1.3 | `information-architecture` | 🔀 | ✅ parcial | Mapa de páginas/pantallas/endpoints. Para cada una: propósito, tabs, funcionalidades, componentes clave |
| 1.4 | `user-flows` | 🔀 | ❌ | Flujos críticos paso a paso: onboarding, compra, CRUD principal, flujo de error. Diagramas de flujo |
| 1.5 | `data-model` | 🔀 | ❌ | Entidades, relaciones, campos clave, índices. Claude genera ERD a partir de los requisitos |
| 1.6 | `api-contract` | 🔀 | ❌ | Endpoints, métodos, payloads, respuestas, códigos de error. OpenAPI/Swagger o definición propia |
| 1.7 | `wireframes` | 📝 | ❌ | Bocetos de baja fidelidad de las pantallas principales (puede ser texto descriptivo, ASCII, o referencia a Figma) |

---

## FASE 2 — DISEÑO (Design)

> **Objetivo:** Definir la identidad visual y la experiencia de usuario

| # | Item | Tipo | Tienes | Descripción |
|---|------|------|--------|-------------|
| 2.1 | `brand-identity` | 📝 | ✅ parcial | Nombre, logo, tono de voz, personalidad de marca |
| 2.2 | `design-tokens` | 🔀 | ✅ parcial | Paleta de colores (primary, secondary, neutral, semantic), tipografías, espaciado, border-radius, sombras |
| 2.3 | `component-library` | 🔀 | ❌ | Inventario de componentes UI necesarios: botones, inputs, modales, cards, tablas, navegación, etc. Con variantes |
| 2.4 | `responsive-strategy` | 📝 | ❌ | Breakpoints, comportamiento mobile vs desktop, componentes que cambian, navigation patterns por dispositivo |
| 2.5 | `accessibility-spec` | 🔀 | ❌ | Nivel WCAG objetivo, contraste mínimo, navegación por teclado, lectores de pantalla, ARIA landmarks |
| 2.6 | `i18n-strategy` | 📝 | ❌ | Idiomas soportados, estrategia de traducción, formato de fechas/números/monedas, dirección RTL si aplica |

---

## FASE 3 — ARQUITECTURA (Architecture)

> **Objetivo:** Decisiones técnicas antes de escribir código

| # | Item | Tipo | Tienes | Descripción |
|---|------|------|--------|-------------|
| 3.1 | `stack-selection` | 🔀 | ✅ parcial | Frontend, backend, BBDD, hosting, CI/CD, monitoreo. Claude recomienda según requisitos, usuario decide |
| 3.2 | `project-structure` | 🔀 | ❌ | Estructura de carpetas, convenciones de nombrado, monorepo vs multirepo, módulos/packages |
| 3.3 | `infra-architecture` | 🔀 | ❌ | Diagrama de infraestructura: servidores, CDN, balanceadores, colas, caché, storage, DNS |
| 3.4 | `auth-architecture` | 🔀 | ❌ | Estrategia de autenticación/autorización: JWT, sessions, OAuth2, RBAC/ABAC, refresh tokens, MFA |
| 3.5 | `integration-map` | 📝 | ❌ | APIs de terceros, webhooks, servicios externos (pagos, email, SMS, analytics, mapas, AI) |
| 3.6 | `error-handling-strategy` | 🔀 | ❌ | Convenciones de errores, logging, niveles de severidad, qué se traga vs qué se muestra al usuario |
| 3.7 | `testing-strategy` | 🔀 | ❌ | Qué se testea y cómo: unitarios, integración, e2e, visual regression. Coverage targets. Herramientas |
| 3.8 | `ai-agents-design` | 🔀 | ✅ | Agentes, orchestrator, skills, tools, prompts de sistema. Solo si el proyecto usa AI |
| 3.9 | `research` | 🤖 | ✅ | Claude investiga librerías, patrones, soluciones para problemas técnicos específicos |

---

## FASE 4 — PLANIFICACIÓN (Planning)

> **Objetivo:** Secuenciar el trabajo en piezas ejecutables

| # | Item | Tipo | Tienes | Descripción |
|---|------|------|--------|-------------|
| 4.1 | `roadmap` | 🔀 | ✅ | Fases/milestones del proyecto con entregables y criterios de aceptación |
| 4.2 | `task-breakdown` | 🤖 | ❌ | Claude descompone cada milestone en tareas atómicas (~2-4h), con dependencias y orden |
| 4.3 | `mvp-definition` | 🔀 | ❌ | Qué entra en el MVP y qué se deja para después. Criterio: mínimo para validar la hipótesis central |
| 4.4 | `risk-register` | 🔀 | ❌ | Riesgos técnicos y de negocio, probabilidad, impacto, mitigación. Claude genera, usuario prioriza |
| 4.5 | `definition-of-done` | 📝 | ❌ | Checklist de qué significa "terminado": tests pasan, review hecho, docs actualizados, desplegable |

---

## FASE 5 — DESARROLLO (Development)

> **Objetivo:** Escribir código funcional, testeado y mantenible

| # | Item | Tipo | Tienes | Descripción |
|---|------|------|--------|-------------|
| 5.1 | `context-init` | 🤖 | ✅ | Inicializa el contexto del proyecto: lee estructura, stack, convenciones, estado actual |
| 5.2 | `scaffold` | 🤖 | ❌ | Genera el boilerplate inicial: proyecto, estructura de carpetas, configs, linters, CI base, README |
| 5.3 | `develop` | 🤖 | ✅ | Desarrolla la siguiente tarea del roadmap. Lee contexto, implementa, ejecuta tests |
| 5.4 | `db-migrate` | 🤖 | ❌ | Genera y ejecuta migraciones de BBDD a partir de cambios en el data model |
| 5.5 | `seed-data` | 🤖 | ❌ | Genera datos de prueba realistas para desarrollo y testing |
| 5.6 | `refactor` | 🤖 | ❌ | Refactoriza código existente: extrae funciones, mejora naming, reduce complejidad, aplica patrones |
| 5.7 | `docs-gen` | 🤖 | ❌ | Genera/actualiza documentación: README, API docs, JSDoc/docstrings, guías de contribución |
| 5.8 | `changelog` | 🤖 | ❌ | Genera changelog a partir de commits/PRs desde la última versión |

---

## FASE 6 — CALIDAD (Quality)

> **Objetivo:** Verificar que el código es correcto, seguro y performante

| # | Item | Tipo | Tienes | Descripción |
|---|------|------|--------|-------------|
| 6.1 | `e2e-tests` | 🤖 | ✅ | Tests end-to-end sobre lo desarrollado en X commit/fecha |
| 6.2 | `unit-tests` | 🤖 | ❌ | Genera tests unitarios para funciones/módulos nuevos o sin coverage |
| 6.3 | `integration-tests` | 🤖 | ❌ | Tests de integración: API endpoints, interacción entre servicios, flujos de datos |
| 6.4 | `code-review` | 🤖 | ✅ | Review automatizado: bugs, malas prácticas, inconsistencias, naming, DRY |
| 6.5 | `security-review` | 🤖 | ✅ | Auditoría de seguridad: inyecciones, XSS, CSRF, secrets expuestos, dependencias vulnerables |
| 6.6 | `performance-audit` | 🤖 | ❌ | Analiza cuellos de botella: queries N+1, bundles grandes, renders innecesarios, memory leaks |
| 6.7 | `accessibility-audit` | 🤖 | ❌ | Verifica contraste, ARIA, navegación teclado, lectores pantalla. Genera reporte con fixes |
| 6.8 | `dependency-audit` | 🤖 | ❌ | Revisa dependencias: vulnerabilidades conocidas, licencias incompatibles, paquetes abandonados |
| 6.9 | `consistency-check` | 🤖 | ❌ | Verifica que naming, estructura, patterns son consistentes en todo el codebase |
| 6.10 | `test-coverage-report` | 🤖 | ❌ | Analiza coverage actual, identifica zonas sin cubrir, prioriza qué testear |

---

## FASE 7 — PRE-PRODUCCIÓN (Staging)

> **Objetivo:** Validar que todo funciona en un entorno real antes del go-live

| # | Item | Tipo | Tienes | Descripción |
|---|------|------|--------|-------------|
| 7.1 | `env-config` | 🔀 | ❌ | Genera configuración por entorno: .env files, secrets, variables de CI/CD. Con validación |
| 7.2 | `ci-cd-pipeline` | 🔀 | ❌ | Genera pipeline de CI/CD: build, test, lint, deploy automático. GitHub Actions, GitLab CI, etc. |
| 7.3 | `docker-setup` | 🤖 | ❌ | Genera Dockerfile, docker-compose, .dockerignore optimizados para el stack |
| 7.4 | `db-migration-plan` | 🔀 | ❌ | Plan de migración de datos si hay BBDD existente: rollback strategy, zero-downtime |
| 7.5 | `smoke-tests` | 🤖 | ❌ | Tests rápidos post-deploy: health checks, endpoints críticos responden, auth funciona |
| 7.6 | `load-testing` | 🔀 | ❌ | Define escenarios de carga, genera scripts (k6, Artillery), ejecuta y analiza resultados |
| 7.7 | `staging-deploy` | 🤖 | ❌ | Despliega a staging, ejecuta smoke tests, reporta estado |
| 7.8 | `pre-launch-checklist` | 🔀 | ❌ | Checklist final: SSL, DNS, backups, monitoring, error tracking, legal (cookies, privacy policy) |

---

## FASE 8 — PRODUCCIÓN (Production)

> **Objetivo:** Desplegar y operar en producción con confianza

| # | Item | Tipo | Tienes | Descripción |
|---|------|------|--------|-------------|
| 8.1 | `prod-deploy` | 🤖 | ❌ | Despliega a producción con rollback automático si falla health check |
| 8.2 | `monitoring-setup` | 🔀 | ❌ | Configura monitoreo: uptime, errores, latencia, uso de recursos. Alertas por canal |
| 8.3 | `backup-strategy` | 🔀 | ❌ | Estrategia de backups: qué, cada cuánto, dónde, cómo verificar, cómo restaurar |
| 8.4 | `incident-runbook` | 🔀 | ❌ | Playbooks para incidentes comunes: BBDD caída, deploy roto, ataque DDoS, leak de datos |
| 8.5 | `rollback` | 🤖 | ❌ | Revierte a la versión anterior de forma segura |
| 8.6 | `hotfix` | 🤖 | ❌ | Flujo de fix urgente: branch desde prod, fix, test mínimo, deploy, merge back |

---

## FASE 9 — POST-LANZAMIENTO (Post-launch)

> **Objetivo:** Iterar basándose en datos reales

| # | Item | Tipo | Tienes | Descripción |
|---|------|------|--------|-------------|
| 9.1 | `analytics-setup` | 🔀 | ❌ | Configura tracking: eventos clave, funnels, métricas de negocio, dashboards |
| 9.2 | `feedback-loop` | 📝 | ❌ | Canales de feedback de usuarios, proceso de triaje, cómo priorizas lo que entra al backlog |
| 9.3 | `retrospective` | 📝 | ❌ | Qué fue bien, qué fue mal, qué cambiar para la siguiente iteración |
| 9.4 | `version-bump` | 🤖 | ❌ | Incrementa versión (semver), actualiza changelog, crea tag/release |
| 9.5 | `deprecation-plan` | 🔀 | ❌ | Plan para deprecar features o versiones de API: timeline, migración, comunicación |
| 9.6 | `tech-debt-audit` | 🤖 | ❌ | Escanea TODOs, hacks, código duplicado, abstracciones rotas. Prioriza por impacto |

---

## COMANDOS TRANSVERSALES (se usan en cualquier fase)

| # | Item | Tipo | Tienes | Descripción |
|---|------|------|--------|-------------|
| T.1 | `context-sync` | 🤖 | ❌ | Re-sincroniza el contexto cuando Claude pierde el hilo o el proyecto ha cambiado |
| T.2 | `progress-report` | 🤖 | ❌ | Genera resumen del estado actual: qué está hecho, qué queda, blockers, % completado |
| T.3 | `decision-log` | 📝 | ❌ | Registro de decisiones técnicas (ADR): qué se decidió, por qué, alternativas descartadas |
| T.4 | `debug` | 🤖 | ❌ | Claude diagnostica un error: lee logs, reproduce, identifica causa raíz, propone fix |
| T.5 | `explain` | 🤖 | ❌ | Claude explica una parte del codebase: qué hace, por qué, cómo se conecta con el resto |
| T.6 | `standards-enforce` | 🤖 | ❌ | Verifica que el código cumple las convenciones definidas en la fase de arquitectura |
| T.7 | `git-commit` | 🤖 | ❌ | Genera conventional commit message basado en los cambios staged |
| T.8 | `pr-description` | 🤖 | ❌ | Genera descripción de PR: qué cambia, por qué, cómo testear, screenshots si aplica |

---

## Resumen: lo que tienes vs lo que falta

```
Total items:     ~65
Ya tienes:       ~10 (15%)
Te faltan:       ~55 (85%)

Fases más vacías:
  → Fase 0 Discovery:      3 de 4 faltan
  → Fase 1 Definition:     5 de 7 faltan
  → Fase 2 Design:         4 de 6 faltan
  → Fase 7 Pre-prod:       8 de 8 faltan
  → Fase 8 Producción:     6 de 6 faltan
  → Fase 9 Post-launch:    6 de 6 faltan
  → Transversales:         8 de 8 faltan
```

## Orden de prioridad sugerido para construir

Si quieres máximo impacto con mínimo esfuerzo, te recomiendo este orden:

1. **`scaffold`** — Lo primero que necesita cualquier proyecto nuevo
2. **`task-breakdown`** — Sin esto el `develop` no sabe qué hacer
3. **`data-model`** + **`api-contract`** — Definen qué se construye
4. **`unit-tests`** — Complementa tu e2e-tests
5. **`debug`** — Se usa constantemente durante desarrollo
6. **`ci-cd-pipeline`** — Puente entre desarrollo y deploy
7. **`env-config`** + **`docker-setup`** — Prerequisitos para pre-prod
8. **`smoke-tests`** — Valida deploys rápidamente
9. **`pre-launch-checklist`** — Evita olvidos críticos
10. **`progress-report`** — Mantiene visibilidad del estado

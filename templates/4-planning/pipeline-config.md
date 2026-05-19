# ⚙️ Pipeline Configuration

> Define qué se ejecuta automáticamente y cuándo. Este archivo lo lee el orchestrator.

## Autonomía por defecto

```yaml
default_mode: semi-auto  # full-auto | semi-auto | manual
```

> Se puede sobreescribir por sprint en `task-breakdown.md`

## Checkpoints automáticos

### Tras cada task ✅

```yaml
on_task_complete:
  - unit-tests          # Ejecuta tests unitarios del código modificado
  - git-commit          # Commit automático con conventional commit message
  - progress-update     # Actualiza task-breakdown.md (marca ✅, actualiza %)
```

### Tras cada sprint 🏁

```yaml
on_sprint_complete:
  - e2e-test            # Tests end-to-end del sprint completo
  - code-review         # Review de todos los commits del sprint
  - integration-tests   # Tests de integración entre componentes
  - progress-report     # Genera reporte de progreso del sprint
  - git-tag             # Tag: sprint-{N}-complete
```

### Tras cada milestone 🎯

```yaml
on_milestone_complete:
  - security-review     # Auditoría de seguridad completa
  - performance-audit   # Análisis de rendimiento
  - dependency-audit    # Scan de dependencias
  - secrets-scan        # Verificación de secrets
  - docs-gen            # Actualiza documentación
  - changelog           # Genera changelog del milestone
  - version-bump        # Incrementa versión (minor para milestone, major para release)
  - git-tag             # Tag: v{X.Y.Z}
```

### Antes de deploy 🚀

```yaml
on_pre_deploy:
  - pre-launch-checklist
  - smoke-tests
  - db-migration-plan   # Solo si hay migraciones pendientes
```

## Configuración de tests

```yaml
testing:
  # Qué tests correr tras cada task
  task_level:
    run: unit-tests
    scope: changed-files   # Solo archivos modificados en la task
    fail_action: block     # block | warn | skip
  
  # Qué tests correr tras cada sprint  
  sprint_level:
    run: [e2e-test, integration-tests]
    scope: sprint-commits  # Todos los commits del sprint
    fail_action: block
  
  # Coverage mínimo
  coverage:
    threshold: 80          # % mínimo
    enforce: warn          # block | warn | skip
```

## Configuración de commits

```yaml
commits:
  auto_commit: true
  convention: conventional  # conventional | angular | none
  include_task_id: true     # Añade [TASK-ID] al mensaje
  sign: false               # GPG signing
  
  # Ejemplo de commit generado:
  # feat(auth): add password reset flow [MVP-012]
```

## Configuración de sprints

```yaml
sprints:
  max_tasks_per_sprint: 8      # Máximo de tasks por sprint
  auto_group: true             # El orchestrator agrupa tasks automáticamente
  grouping_strategy: dependency # dependency | feature | layer
  
  # dependency: agrupa tasks que dependen entre sí
  # feature: agrupa tasks del mismo feature
  # layer: agrupa por capa (todas las de backend, luego frontend, etc.)
```

## Notificaciones

```yaml
notifications:
  on_task_complete: console     # console | none
  on_sprint_complete: summary   # summary | detailed | none
  on_milestone_complete: report # report | summary | none
  on_failure: immediate         # immediate | batch | none
```

## Ejemplo de flujo completo

```
Sprint 1 (mode: semi-auto)
  ├── Task MVP-001 → develop → unit-tests → commit ✅
  ├── [user confirms] 
  ├── Task MVP-002 → develop → unit-tests → commit ✅
  ├── [user confirms]
  ├── Task MVP-003 → develop → unit-tests → commit ✅
  ├── [sprint complete] → e2e-test → code-review → integration-tests → tag
  
Sprint 2 (mode: full-auto)
  ├── Task MVP-004 → develop → unit-tests → commit ✅
  ├── Task MVP-005 → develop → unit-tests → commit ✅
  ├── Task MVP-006 → develop → unit-tests → commit ✅
  ├── [sprint complete] → e2e-test → code-review → integration-tests → tag

Milestone: MVP complete
  └── security-review → performance-audit → dependency-audit → changelog → v1.0.0
```

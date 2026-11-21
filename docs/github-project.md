# Tablero de GitHub

Ids del GitHub Project `ReMarket`, para mover tarjetas con `gh` según la skill `flujo-github`.

- **Propietario:** `santimn2000`
- **Número:** `2`
- **URL:** https://github.com/users/santimn2000/projects/2
- **Id del proyecto:** `PVT_kwHOB86YMc4BsnKr`
- **Id del campo Status:** `PVTSSF_lAHOB86YMc4BsnKrzhq0gio`

## Columnas

El tablero usa los nombres en inglés. Equivalencia con las columnas de la skill:

| Skill | Tablero | Id de la opción |
|---|---|---|
| Backlog | `Backlog` | `1d40d5d3` |
| Listo | `Ready` | `f75ad846` |
| En curso | `In Progress` | `47fc9ee4` |
| Revisión | `In Review` | `23acbdb6` |
| Hecho | `Done` | `98236657` |

## Automatismos del tablero

- **Item added to project:** la tarjeta entra en `Ready`.
- **Pull request linked to issue:** la tarjeta pasa a `In Review`.
- **Item closed** y **Pull request merged:** la tarjeta pasa a `Done`.
- **Auto-close issue:** al pasar a `Done` se cierra la issue. Por eso nunca se mueve una tarjeta a `Done` a mano.

## Comandos

Id de la tarjeta de la issue `<n>`:

```bash
gh project item-list 2 --owner santimn2000 --format json --jq '.items[] | select(.content.number==<n>) | .id'
```

Mover la tarjeta a una columna:

```bash
gh project item-edit --id <tarjeta> --project-id PVT_kwHOB86YMc4BsnKr --field-id PVTSSF_lAHOB86YMc4BsnKrzhq0gio --single-select-option-id <id de la columna>
```

# LRC Checker v6.2.2 — Cambios

## Creación de la nueva categoría LRC-GEN

La versión **6.2.2** introduce la categoría **LRC-GEN** en el reporte de LRC Checker.

### ¿Qué es LRC-GEN?

Archivos `.lrc` que fueron generados automáticamente por **LRCGen** (detectados por la marca `[by:LRC Generator]`) y que aún no han sido firmados/revisados manualmente. Mientras no se les aplique la firma del revisor (`Oct4vyus Kandle`), se cuentan como **pendientes de revisión**.

### Dónde aparece

- **Reporte HTML:** badge `🤖 LRC-GEN` en cada fila generada + tarjeta de estadística filtrable (`LRC-GEN (a revisar)`).
- **Reporte JSON:** campo `generated` en el bloque `stats`.
- **Log de texto:** línea `🤖 LRC-GEN (a revisar): N`.
- **Editor:** la versión del editor (`LRC Checker v6.2.2 · Editor de .lrc`) se actualiza automáticamente desde la misma constante `VERSION`.

### Comportamiento

- Al **firmar** un archivo `.lrc` (revisión manual), deja de contarse como `generated` y se suma a `signed`.
- Los archivos LRC-GEN **se excluyen del cálculo de sincronía** (como los firmados), ya que su letra puede estar pendiente de corrección.

### Archivos modificados

- `version_servidor/lrc_checker_server.py` — constante `VERSION` a `'6.2.2'`; lógica de detección de `is_generated` y contadores `generated` ya existentes.
- `version_servidor/lrc_editor.html` — pie de página con `__LRC_VERSION__` (reemplazado dinámicamente por `VERSION`).
- `entrypoint.sh` — string de versión en el banner de arranque.
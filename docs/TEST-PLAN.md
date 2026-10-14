# Plan de pruebas — Automatización financiera Make

**Estado:** diseño de casos, **sin evidencia de ejecución adjunta**. Ejecutar con bot y hoja de pruebas, nunca con datos financieros reales.

| ID | Prioridad | Escenario | Entrada de ejemplo | Resultado esperado | Estado |
|---|---|---|---|---|---|
| TC-01 | P1 | Registrar egreso | `Gasté 250 en supermercado con efectivo, Persona A` | Egreso, categoría Comida, monto 250 y método Efectivo | Pendiente |
| TC-02 | P1 | Registrar ingreso | `Recibí 1500 por diseño, Persona B, transferencia` | Ingreso, concepto Servicios profesionales y categoría Honorarios | Pendiente |
| TC-03 | P0 | IVA y retenciones | `Facturé 6000 con IVA y retenciones, Persona A` | IVA 960; IVA retenido 640.002; ISR retenido 75, sujeto a redondeo definido | Pendiente |
| TC-04 | P0 | Texto sin monto | `Pagué el internet` | No guardar movimiento incompleto; mostrar o gestionar error | Pendiente / validación por implementar |
| TC-05 | P0 | Monto inválido | `Gasté -200 en comida` | Rechazar monto negativo | Pendiente / validación por implementar |
| TC-06 | P1 | Categoría no reconocida | `Gasté 50 en artículo inusual` | Varios / Imprevistos | Pendiente |
| TC-07 | P1 | Campo persona/cuenta | `Persona A pagó 400 con Santander` | Persona y cuenta no se intercambian | Pendiente |
| TC-08 | P0 | Duplicación de webhook | Reenviar misma actualización | Una sola fila por evento | Pendiente / prevención por implementar |
| TC-09 | P0 | Respuesta JSON inválida | Simular respuesta no parseable | Error controlado; no insertar fila corrupta | Pendiente / control por verificar |
| TC-10 | P1 | Persistencia | Registrar movimiento y consultar Sheets | Una fila con mapeo correcto en columnas | Pendiente |
| TC-11 | P1 | Recordatorio | Vencimiento ficticio de pago | Aviso en fecha configurada | Pendiente; blueprint recibido y revisado estáticamente |
| TC-12 | P1 | Datos privados | Inspeccionar exportación pública | Sin tokens, IDs reales o información financiera | Pendiente de revisión final |

## Plantilla de evidencia

- **Caso / fecha / entorno / versión del escenario:**
- **Entrada de prueba:**
- **Esperado / obtenido:**
- **Estado:** PASS / FAIL / BLOCKED
- **Captura o log anonimizado:**
- **Defecto / observaciones:**

## Riesgos identificados por revisión estática

1. El flujo exportado encadena recepción → IA → Parse JSON → Add Row sin una validación de negocio explícita entre Parse JSON y Add Row.
2. No se observa módulo de control de duplicados ni ruta de manejo de errores en el blueprint proporcionado.
3. El modelo realiza cálculos fiscales en el prompt; conviene recalcular y redondear con reglas deterministas antes de persistir.
4. La fecha usa `message.date` del evento Telegram; verificar su formato y zona horaria en Sheets.
5. La columna ID no tiene valor mapeado en el escenario exportado; confirmar si Sheets lo genera automáticamente.

Estas observaciones no afirman fallos en producción: son riesgos y pruebas pendientes.

## Pruebas específicas del escenario de recordatorios

**Referencia:** `blueprints/payment-reminders-sanitized.blueprint.json`. La revisión del archivo confirma dos módulos (búsqueda de filas en Sheets y envío de mensaje por Telegram) y un filtro de fecha; **no demuestra por sí sola ejecución exitosa**.

| ID | Prioridad | Datos de prueba | Resultado esperado | Estado |
|---|---|---|---|---|
| REM-01 | P1 | Próxima fecha = mañana; anticipación = 1 día | Envía recordatorio hoy | Pendiente |
| REM-02 | P1 | Próxima fecha = dentro de 5 días; anticipación = 2 | No envía hoy | Pendiente |
| REM-03 | P1 | Próxima fecha = hoy; anticipación = 0 | Envía recordatorio hoy | Pendiente |
| REM-04 | P0 | Fecha vacía o inválida | No enviar mensaje incorrecto; registrar incidencia | Pendiente |
| REM-05 | P1 | Dos pagos que cumplen condición | Envía un mensaje por pago | Pendiente |
| REM-06 | P0 | Ejecutar escenario dos veces el mismo día | Verificar si duplica avisos; definir política de idempotencia | Pendiente |
| REM-07 | P1 | Fecha y zona horaria de Make/Sheets diferentes | Comparación correcta en zona horaria esperada | Pendiente |
| REM-08 | P0 | Chat ID no autorizado o bot sin acceso | Fallo controlado, sin exponer datos | Pendiente |
| REM-09 | P1 | Días de anticipación no numéricos | Rechazo o tratamiento controlado | Pendiente |

**Riesgos por inspección:** el filtro usa `formatDate(now; "YYYY-MM-DD")` y `formatDate(addDays(1.`9`; "-" + 1.`7`); "YYYY-MM-DD")`; conviene comprobar la conversión de fecha y el valor de días. No se observa mecanismo de deduplicación de avisos en el blueprint. La frecuencia horaria se configura fuera del flujo exportado y no puede confirmarse aquí.

## Pruebas específicas del escenario de recordatorios

**Referencia:** `blueprints/payment-reminders-sanitized.blueprint.json`. La revisión del archivo confirma dos módulos (búsqueda de filas en Sheets y envío de mensaje por Telegram) y un filtro de fecha; **no demuestra por sí sola ejecución exitosa**.

| ID | Prioridad | Datos de prueba | Resultado esperado | Estado |
|---|---|---|---|---|
| REM-01 | P1 | Próxima fecha = mañana; anticipación = 1 día | Envía recordatorio hoy | Pendiente |
| REM-02 | P1 | Próxima fecha = dentro de 5 días; anticipación = 2 | No envía hoy | Pendiente |
| REM-03 | P1 | Próxima fecha = hoy; anticipación = 0 | Envía recordatorio hoy | Pendiente |
| REM-04 | P0 | Fecha vacía o inválida | No enviar mensaje incorrecto; registrar incidencia | Pendiente |
| REM-05 | P1 | Dos pagos que cumplen condición | Envía un mensaje por pago | Pendiente |
| REM-06 | P0 | Ejecutar escenario dos veces el mismo día | Verificar si duplica avisos; definir política de idempotencia | Pendiente |
| REM-07 | P1 | Fecha y zona horaria de Make/Sheets diferentes | Comparación correcta en zona horaria esperada | Pendiente |
| REM-08 | P0 | Chat ID no autorizado o bot sin acceso | Fallo controlado, sin exponer datos | Pendiente |
| REM-09 | P1 | Días de anticipación no numéricos | Rechazo o tratamiento controlado | Pendiente |

**Riesgos por inspección:** el filtro usa `formatDate(now; "YYYY-MM-DD")` y `formatDate(addDays(1.`9`; "-" + 1.`7`); "YYYY-MM-DD")`; conviene comprobar la conversión de fecha y el valor de días. No se observa mecanismo de deduplicación de avisos en el blueprint. La frecuencia horaria se configura fuera del flujo exportado y no puede confirmarse aquí.

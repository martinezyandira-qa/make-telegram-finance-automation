# Automatización financiera con Make, Telegram, OpenAI y Google Sheets

**Proyecto personal · Integración de APIs, IA aplicada y Quality Assurance**

Sistema para registrar ingresos y egresos mediante mensajes en lenguaje natural enviados a Telegram. Un escenario de Make interpreta el texto con **GPT-5 nano**, convierte el resultado en JSON y agrega una fila a Google Sheets.

**Estado:** registro de movimientos y recordatorios de pagos operativos según las verificaciones de la autora; **reportes automáticos en desarrollo**. Este repositorio documenta **ambos escenarios** mediante blueprints anonimizados. La programación horaria exacta del escenario de recordatorios no se conserva en la exportación y debe configurarse en Make.

## Arquitectura documentada

```text
Telegram: Watch Updates
          ↓
OpenAI: Ask Anything (GPT-5 nano)
          ↓
JSON: Parse JSON
          ↓
Google Sheets: Add a Row (Movimientos)
```

El modelo extrae: tipo, concepto, monto, categoría, método de pago, persona, cuenta, fiscal, IVA, retenciones de IVA/ISR, estado y notas. El prompt define un catálogo de categorías, reglas de clasificación y una salida JSON con claves fijas.

### Escenario 2: recordatorios programados

```text
Google Sheets: Search Rows (Pagos Recurentes)
          ↓ filtro de fecha
Telegram: Send a Message (recordatorio de pago)
```

La condición compara la fecha actual con `FECHA MAKE` menos `DIAS DE ANTICIPACION`. El mensaje incluye **concepto, monto MXN y fecha límite**. El blueprint contiene un destinatario fijo que se sustituyó por un marcador seguro en la versión pública. La exportación no permite verificar la frecuencia exacta de ejecución del escenario ni confirmar el envío de un caso específico.

### Escenario 2: recordatorios programados

```text
Google Sheets: Search Rows (Pagos Recurentes)
          ↓ filtro de fecha
Telegram: Send a Message (recordatorio de pago)
```

La condición compara la fecha actual con `FECHA MAKE` menos `DIAS DE ANTICIPACION`. El mensaje incluye **concepto, monto MXN y fecha límite**. El blueprint contiene un destinatario fijo que se sustituyó por un marcador seguro en la versión pública. La exportación no permite verificar la frecuencia exacta de ejecución del escenario ni confirmar el envío de un caso específico.

## Funcionalidades

- Captura de movimientos desde Telegram sin rellenar formularios.
- Extracción estructurada y clasificación de ingresos/egresos mediante IA.
- Registro en Google Sheets con información fiscal cuando se indique.
- Recordatorios de pagos recurrentes: **funcionalidad reportada como operativa**; flujo documentado y prueba formal de fechas pendiente.
- Reportes financieros automáticos: **en desarrollo**.

## Enfoque QA

El proyecto permite verificar integración entre servicios, exactitud del mapeo, manejo de entradas ambiguas, integridad de datos y casos límite de importes y retenciones. Ver [plan de pruebas](docs/TEST-PLAN.md). Los casos del documento son **propuestos** hasta adjuntar resultados y evidencia.

## Blueprint público

Archivos: [`blueprints/registration-sanitized.blueprint.json`](blueprints/registration-sanitized.blueprint.json) y [`blueprints/payment-reminders-sanitized.blueprint.json`](blueprints/payment-reminders-sanitized.blueprint.json).

**Importante:** se anonimizaron identificadores de conexión, webhook, hoja de cálculo y nombres personales. Para usarlo, importa el blueprint en Make y vuelve a configurar conexiones, webhook, hoja de prueba y estructura JSON. No es una copia funcional lista para producción.

## Seguridad y límites

No se publican tokens, credenciales, datos financieros ni identificadores reales. Consulta [SECURITY.md](docs/SECURITY.md). La clasificación fiscal generada por IA requiere verificación humana; no sustituye asesoría contable ni garantiza cálculos correctos.

## Mejoras previstas

- Ejecutar y documentar casos de fechas límite, anticipación, duplicados y notificaciones.
- Añadir validación de JSON, manejo de errores y prevención de duplicados.
- Crear reportes automáticos.
- Añadir evidencias de ejecución de pruebas con datos ficticios.

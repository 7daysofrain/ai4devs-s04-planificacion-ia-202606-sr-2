# Poke holes — AUTH-001 (Registro de cuenta con email y contraseña)

> Selección de huecos detectados sobre la story AUTH-001 y sus criterios de aceptación.

## Edge cases

### Sobre la contraseña
- Contraseña vacía o solo espacios en blanco (`"        "` cumple 8 caracteres pero no debería ser válida).
- Longitud máxima: no hay límite superior definido (¿se acepta una contraseña de 5.000 caracteres? Riesgo de DoS por hashing).
- Confirmación de contraseña (campo "repetir contraseña"): no se menciona si existe.

### Sobre el email
- Normalización: `Ana@Ejemplo.com` vs `ana@ejemplo.com` — ¿case-insensitive? Afecta directamente a la detección de duplicados de AUTH-002.

### Sobre el formulario / flujo
- Campos vacíos (email vacío, contraseña vacía, ambos vacíos).
- Doble envío / doble clic → ¿se crean dos cuentas o hay protección de idempotencia?
- Qué pasa si un usuario **ya autenticado** llega al formulario de registro.

## Escenarios faltantes
- Registro con **campos obligatorios vacíos** (mensaje de error claro).
- Feedback de **estado de carga** durante el envío (evitar reintentos).
- Comportamiento ante **fallo del backend** (error 500 / timeout): ¿mensaje comprensible, no técnico? El scenario de email inválido exige "no un error técnico", pero eso no se extiende a fallos de servidor.

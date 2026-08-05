# Prompts usados · Sesión 4

> Insumo de entrada: `docs/PRD.md` (adjunto en la conversación al lanzar el prompt de descomposición).

---

## 1. Prompt de descomposición (Parte 1)

```
# ROLE
Eres un Product Owner senior con amplia experiencia en la conceptualización y definición de aplicaciones SaaS. Tu especialidad es la descomposición de Requisitos de Producto (PRD) en backlogs altamente eficientes, mitigando la ambigüedad para que tanto ingenieros humanos como agentes de desarrollo de software (IA) puedan ejecutar las tareas sin perder contexto.

# CONTEXT
FlowSync es una aplicación web de gestión de tareas personales orientada a profesionales del conocimiento (knowledge workers) de entre 25 y 45 años. Su propuesta de valor central es eliminar la doble gestión manual entre las listas de tareas pendientes y el calendario personal, automatizando la sincronización bidireccional (con foco inicial FlowSync -> Google Calendar). El alcance de este entregable está estrictamente acotado al MVP (Versión 1.0 · Q3 2026), cuyo objetivo es validar si los usuarios con sobrecarga de herramientas adoptarán este gestor si se elimina la duplicidad operativa.

# TASK
Analiza minuciosamente el PRD de FlowSync proporcionado y decompón su sección funcional en un listado exhaustivo de User Stories individuales.

# CONSTRAINTS & NON-GOALS
- **Solo alcance MVP:** Limítate exclusivamente a las funcionalidades explícitas en las secciones del MVP del PRD adjunto (Autenticación, CRUD de tareas, Organización/filtrado, Exportación a CSV y Sincronización básica con Google Calendar via OAuth).
- **Prohibido inventar:** No agregues funcionalidades post-MVP, ni supuestos de producto que no se encuentren explícitamente en el documento (por ejemplo: no crees flujos de recuperación de contraseñas, etiquetas, subtareas, notificaciones por email ni compartición de equipos, ya que están explícitamente fuera de alcance).
- **No asumas decisiones técnicas:** No estimes tiempos de desarrollo ni Story Points, ni propongas arquitecturas o esquemas de bases de datos. El cómo técnico se define en otra capa (OpenSpec).

# OUTPUT FORMAT & STRUCTURAL REQUIREMENTS
El backlog resultante debe organizarse de la siguiente manera:

1. **Agrupación:** Organiza las User Stories agrupándolas por módulos funcionales del PRD (Épicas/Casos de uso), asegurando que el orden tenga sentido lógico de producto para FlowSync.
2. **Formato Exacto de la Story:** Cada User Story debe redactarse usando estrictamente la estructura estándar:
   "Como [rol], quiero [acción], para [beneficio]."
3. **Criterios de Aceptación (AC) en Given/When/Then:** Cada una de las historias debe incluir obligatoriamente entre 3 y 5 criterios de aceptación escritos en formato Gherkin (Given/When/Then). Deben ser escenarios verificables, testeables, específicos y no genéricos. Cubre tanto el "happy path" como los casos de error o validaciones críticas citados en el texto.
4. **Instrucción de Transparencia (Asumido):** Si necesitas inferir algún detalle menor de comportamiento para que el criterio de aceptación sea lógicamente ejecutable o testeable, pero este no se describe de forma literal en el PRD, debes marcar explícitamente esa línea o fragmento con el prefijo "(asumido)".

# FEW-SHOT EXAMPLES (EXPECTED FORMAT)

### Épica: Gestión de Tareas (CRUD)

#### Story: CRUD-001 - Creación básica de tareas
Como usuario registrado de FlowSync, quiero crear una tarea indicando al menos un título, para poder registrar mis pendientes de forma rápida.

**Criterios de Aceptación:**
- Scenario: Creación exitosa solo con título
  Given el usuario está autenticado en la plataforma
  When introduce un título válido para la tarea y deja la descripción y la fecha límite vacías
  Then el sistema crea la tarea con estado "pending" (asumido)
  And muestra la tarea en el listado del usuario
- Scenario: Intento de creación sin título
  Given el usuario está en el formulario de creación de tareas
  When intenta guardar la tarea dejando el título vacío
  Then el sistema bloquea el envío
  And muestra un error claro indicando que el título es obligatorio

***
```

---

## 2. Prompt poke-holes (Parte 3)

Aplicado sobre AUTH-001 (`output.md`, líneas 15–38):

```
Aquí tienes esta user story con sus criterios de aceptación:
[AUTH-001 - Registro de cuenta con email y contraseña]

Tu tarea: identifica edge cases, supuestos implícitos,
escenarios faltantes, y dependencias o riesgos no
mencionados. No reescribas la story, solo lista lo
que falta o lo que asumiste.
```

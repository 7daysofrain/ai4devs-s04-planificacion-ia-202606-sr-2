# Backlog MVP — FlowSync (V1.0 · Q3 2026)

> **Fuente**: `docs/PRD.md` (PRD — FlowSync MVP, versión 1.0, aprobado para MVP).
> **Alcance**: exclusivamente los requisitos funcionales del MVP (secciones 3.1 a 3.5 del PRD). No se incluyen funcionalidades post-MVP ni decisiones técnicas.
> **Convención de transparencia**: todo detalle de comportamiento inferido para que un criterio sea testeable, pero no descrito literalmente en el PRD, va marcado con el prefijo **(asumido)**.
>
> **Orden de las épicas**: sigue la secuencia lógica de producto — sin cuenta no hay tareas (Autenticación), sin tareas no hay nada que organizar ni exportar (CRUD → Organización → Exportación), y la sincronización con Google Calendar se apoya en tareas con fecha ya existentes (Sincronización al final, por ser además la pieza más arriesgada del MVP según el propio PRD).

---

## Épica 1: Autenticación y gestión de cuenta (PRD §3.1)

Cada usuario tiene una cuenta propia y sus datos nunca son visibles para otros. Fuera de alcance explícito del PRD: recuperación de contraseña y verificación de email por enlace.

### Story: AUTH-001 - Registro de cuenta con email y contraseña
Como visitante, quiero crear una cuenta con mi email y una contraseña, para empezar a gestionar mis tareas personales en FlowSync.

**Criterios de Aceptación:**
- Scenario: Registro exitoso
  Given un visitante sin sesión iniciada está en el formulario de registro
  When introduce un email válido no registrado y una contraseña de al menos 8 caracteres y envía el formulario
  Then el sistema crea la cuenta
  And el usuario llega a la pantalla de bienvenida del onboarding
- Scenario: Contraseña demasiado corta
  Given un visitante está en el formulario de registro
  When introduce una contraseña de 7 caracteres o menos e intenta registrarse
  Then el sistema no crea la cuenta
  And muestra un mensaje comprensible indicando que la contraseña debe tener al menos 8 caracteres
- Scenario: Email con formato inválido
  Given un visitante está en el formulario de registro
  When introduce un email con formato inválido (asumido: p. ej. "ana@" o "ana.com") e intenta registrarse
  Then el sistema no crea la cuenta
  And muestra un error de validación comprensible, no un error técnico
- Scenario: Contraseña en el límite exacto
  Given un visitante está en el formulario de registro
  When introduce una contraseña de exactamente 8 caracteres
  Then el sistema acepta la contraseña como válida

***

### Story: AUTH-002 - Aviso de email ya registrado
Como visitante cuyo email ya tiene cuenta, quiero que el sistema me lo indique y me ofrezca ir al inicio de sesión, para no quedarme bloqueado en el registro sin saber cómo continuar.

**Criterios de Aceptación:**
- Scenario: Intento de registro con email existente
  Given existe una cuenta registrada con el email "ana@ejemplo.com"
  When un visitante intenta registrarse con "ana@ejemplo.com" y una contraseña válida
  Then el sistema no crea una cuenta duplicada
  And le indica que ese email ya está registrado
  And le ofrece una acción para ir al inicio de sesión
- Scenario: Navegación al inicio de sesión desde el aviso
  Given el sistema ha mostrado el aviso de email ya registrado
  When el visitante elige la acción de ir al inicio de sesión
  Then se muestra la pantalla de inicio de sesión
- Scenario: La cuenta original queda intacta
  Given existe una cuenta con "ana@ejemplo.com" con sus tareas
  When alguien intenta registrarse de nuevo con ese email
  Then la cuenta existente no se modifica (asumido: ni su contraseña ni sus datos)

***

### Story: AUTH-003 - Inicio de sesión
Como usuario registrado, quiero iniciar sesión con mi email y contraseña, para acceder a mis tareas personales.

**Criterios de Aceptación:**
- Scenario: Login exitoso
  Given existe una cuenta con email "ana@ejemplo.com" y contraseña válida
  When la usuaria introduce esas credenciales correctas
  Then el sistema inicia su sesión
  And accede a su listado de tareas (asumido: el listado es la pantalla principal tras el login)
- Scenario: Credenciales incorrectas
  Given existe una cuenta registrada
  When el usuario introduce una contraseña incorrecta para ese email
  Then el sistema no inicia sesión
  And muestra un error comprensible (asumido: sin distinguir si falló el email o la contraseña)
- Scenario: Email no registrado
  Given no existe ninguna cuenta con el email "nadie@ejemplo.com"
  When alguien intenta iniciar sesión con ese email
  Then el sistema no inicia sesión
  And muestra un error comprensible, no un error técnico
- Scenario: Campos vacíos
  Given el usuario está en la pantalla de inicio de sesión
  When intenta enviar el formulario con el email o la contraseña vacíos
  Then el sistema bloquea el envío
  And señala los campos obligatorios (asumido)

***

### Story: AUTH-004 - Cierre de sesión
Como usuario autenticado, quiero cerrar sesión, para impedir que otra persona use mi cuenta desde este dispositivo o navegador.

**Criterios de Aceptación:**
- Scenario: Logout exitoso
  Given un usuario con sesión iniciada
  When solicita cerrar sesión
  Then la sesión queda invalidada
  And se le devuelve a una pantalla pública (asumido: la de inicio de sesión)
- Scenario: Sin acceso tras el logout
  Given un usuario acaba de cerrar sesión
  When intenta acceder a su listado de tareas
  Then el sistema no muestra las tareas
  And le exige autenticarse de nuevo
- Scenario: El logout no borra datos
  Given un usuario con tareas creadas cierra sesión
  When vuelve a iniciar sesión con sus credenciales
  Then recupera el acceso a todas sus tareas intactas (asumido)

***

### Story: AUTH-005 - Pantalla de bienvenida tras el registro (onboarding mínimo)
Como usuario recién registrado, quiero ver una pantalla de bienvenida que explique en una frase qué hace FlowSync y me invite a crear mi primera tarea, para entender el valor del producto y empezar sin fricción.

**Criterios de Aceptación:**
- Scenario: Bienvenida tras registro exitoso
  Given un visitante completa el registro con éxito
  When finaliza el proceso de registro
  Then ve una pantalla de bienvenida
  And la pantalla explica en una frase qué hace FlowSync
  And contiene una invitación a crear su primera tarea
- Scenario: Ir a crear la primera tarea desde la bienvenida
  Given el usuario está en la pantalla de bienvenida
  When acepta la invitación a crear su primera tarea
  Then se le presenta el formulario de creación de tarea (asumido)
- Scenario: El onboarding solo aparece tras el registro
  Given un usuario ya registrado previamente
  When inicia sesión de nuevo
  Then no se le vuelve a mostrar la pantalla de bienvenida del registro (asumido: el onboarding es exclusivo del momento posterior al registro)

***

### Story: AUTH-006 - Privacidad y aislamiento de datos entre usuarios
Como usuario de FlowSync, quiero que mis tareas sean visibles y manipulables solo por mí, para gestionar mis pendientes con privacidad.

**Criterios de Aceptación:**
- Scenario: El listado solo contiene tareas propias
  Given el usuario A y el usuario B tienen cada uno sus propias tareas
  When el usuario A consulta su listado
  Then ve únicamente sus tareas
  And ninguna tarea del usuario B aparece
- Scenario: Acceso directo a una tarea ajena denegado
  Given el usuario A dispone del identificador de una tarea del usuario B (asumido: p. ej. manipulando la URL o la API)
  When intenta verla, editarla o borrarla
  Then el sistema deniega la operación
  And no revela el contenido de la tarea ajena
- Scenario: Sin sesión no hay datos
  Given un visitante sin autenticar
  When intenta acceder al listado o a cualquier operación sobre tareas
  Then el sistema exige autenticación antes de mostrar u operar sobre tareas

---

## Épica 2: Gestión de tareas (CRUD) (PRD §3.2)

El núcleo del producto. Una tarea tiene como mínimo: título, descripción opcional, estado (`pending`, `completed`, `archived`) y fecha límite opcional. Toda tarea recién creada nace en `pending`.

### Story: CRUD-001 - Creación de tareas
Como usuario registrado, quiero crear una tarea indicando al menos un título, para capturar mis pendientes de forma rápida.

**Criterios de Aceptación:**
- Scenario: Creación exitosa solo con título
  Given el usuario está autenticado
  When crea una tarea indicando solo un título válido
  Then el sistema guarda la tarea con estado `pending`
  And la tarea aparece en su listado sin descripción ni fecha límite
- Scenario: Creación con descripción y fecha límite
  Given el usuario está autenticado
  When crea una tarea con título, descripción y fecha límite
  Then el sistema guarda los tres campos
  And la tarea nace igualmente en estado `pending`
- Scenario: Intento de creación sin título
  Given el usuario está en el formulario de creación de tareas
  When intenta guardar dejando el título vacío
  Then el sistema bloquea el guardado
  And muestra un error claro indicando que el título es obligatorio
- Scenario: Título compuesto solo por espacios
  Given el usuario está en el formulario de creación de tareas
  When introduce un título compuesto únicamente por espacios en blanco (asumido: se trata como título vacío)
  Then el sistema bloquea el guardado con el mismo error de título obligatorio

***

### Story: CRUD-002 - Listado de tareas
Como usuario registrado, quiero ver el listado de mis tareas, para saber qué tengo pendiente y en qué estado está cada cosa.

**Criterios de Aceptación:**
- Scenario: Ver el listado de tareas propias
  Given el usuario autenticado tiene una o más tareas
  When abre el listado de tareas
  Then ve sus tareas mostrando al menos el título y el estado de cada una (asumido: la fecha límite también se muestra cuando existe)
- Scenario: El listado refleja las altas
  Given el usuario acaba de crear una tarea nueva
  When consulta el listado
  Then la tarea recién creada aparece en él
- Scenario: Sin tareas ajenas
  Given existen tareas de otros usuarios en el sistema
  When el usuario abre su listado
  Then solo aparecen las tareas que le pertenecen

***

### Story: CRUD-003 - Edición de tareas
Como usuario registrado, quiero editar cualquier campo de una tarea existente, para mantener mi lista fiel a la realidad.

**Criterios de Aceptación:**
- Scenario: Editar título, descripción y fecha límite
  Given el usuario tiene una tarea con título, descripción y fecha límite
  When modifica cualquiera de esos campos y guarda
  Then el sistema persiste los nuevos valores
  And el listado muestra la tarea actualizada
- Scenario: Vaciar los campos opcionales
  Given una tarea tiene descripción y fecha límite
  When el usuario borra ambos campos y guarda
  Then la tarea queda sin descripción y sin fecha límite (ambos campos son opcionales según el PRD)
- Scenario: El título sigue siendo obligatorio al editar
  Given el usuario está editando una tarea
  When deja el título vacío e intenta guardar
  Then el sistema rechaza el cambio
  And muestra un error comprensible de campo obligatorio
  And la tarea conserva sus valores anteriores (asumido)

***

### Story: CRUD-004 - Borrado de tareas
Como usuario registrado, quiero borrar una tarea, para eliminar de mi lista lo que ya no necesito gestionar.

**Criterios de Aceptación:**
- Scenario: Borrado exitoso
  Given el usuario tiene una tarea en su listado
  When solicita borrarla (asumido: con una confirmación previa para evitar borrados accidentales)
  Then la tarea desaparece de su listado
  And deja de existir en FlowSync
- Scenario: El borrado es selectivo
  Given el usuario tiene varias tareas
  When borra una de ellas
  Then el resto de sus tareas permanece intacto
- Scenario: Borrar una tarea que ya no existe
  Given una tarea que ya fue borrada (asumido: p. ej. desde otra pestaña abierta del mismo usuario)
  When el usuario intenta borrarla de nuevo
  Then el sistema responde con un error comprensible de elemento no encontrado (asumido)
  And ninguna otra tarea se ve afectada

***

### Story: CRUD-005 - Cambio de estado de tareas
Como usuario registrado, quiero cambiar el estado de una tarea entre `pending`, `completed` y `archived`, para reflejar el progreso real de mi trabajo.

**Criterios de Aceptación:**
- Scenario: Marcar como completada
  Given una tarea en estado `pending`
  When el usuario la marca como completada
  Then su estado pasa a `completed`
  And el cambio se refleja en el listado
- Scenario: Archivar una tarea
  Given una tarea en estado `pending` o `completed`
  When el usuario la archiva
  Then su estado pasa a `archived`
- Scenario: Volver a pendiente
  Given una tarea en estado `completed` o `archived`
  When el usuario la devuelve a pendiente
  Then su estado vuelve a `pending` (asumido: el PRD no restringe las transiciones entre los tres estados)
- Scenario: Estado inválido
  Given una petición de cambio de estado con un valor distinto de `pending`, `completed` o `archived` (asumido: enviada manipulando el cliente o la API)
  When llega al sistema
  Then el sistema rechaza la operación
  And la tarea conserva su estado anterior

---

## Épica 3: Organización y filtrado (PRD §3.3)

Con varias tareas activas (el usuario objetivo maneja entre 5 y 30), el usuario necesita encontrarlas y priorizarlas.

### Story: ORG-001 - Filtrado de tareas por estado
Como usuario con varias tareas, quiero filtrar el listado por estado, para ver solo las pendientes, las completadas o las archivadas según lo que necesite en cada momento.

**Criterios de Aceptación:**
- Scenario: Filtrar solo pendientes
  Given el usuario tiene tareas en los tres estados
  When aplica el filtro de estado `pending`
  Then el listado muestra únicamente sus tareas pendientes
- Scenario: Filtrar por completadas o archivadas
  Given el usuario tiene tareas en los tres estados
  When aplica el filtro `completed` o el filtro `archived`
  Then el listado muestra únicamente las tareas del estado elegido
- Scenario: Filtro sin resultados
  Given el usuario no tiene ninguna tarea en el estado filtrado
  When aplica ese filtro
  Then el listado no muestra tareas
  And se presenta un estado vacío coherente (ver ORG-003)
- Scenario: Volver a ver todas las tareas
  Given el usuario tiene un filtro de estado activo
  When lo desactiva (asumido: existe una opción "todas")
  Then el listado vuelve a mostrar las tareas de todos los estados

***

### Story: ORG-002 - Ordenación por defecto orientada a "hoy"
Como usuario, quiero que el listado se ordene por defecto mostrando primero lo más relevante para hoy, para decidir de un vistazo qué debo atender ahora.

**Criterios de Aceptación:**
- Scenario: Orden por defecto al abrir el listado
  Given el usuario tiene varias tareas con distintas fechas límite y estados
  When abre el listado sin elegir ningún orden
  Then las tareas aparecen ordenadas priorizando la relevancia para "hoy"
  And el criterio exacto de ordenación es el que el equipo defina en refinamiento (el PRD lo deja explícitamente abierto)
- Scenario: La ordenación no oculta tareas
  Given el listado muestra N tareas bajo el filtro activo
  When se aplica la ordenación por defecto
  Then siguen visibles las mismas N tareas y solo cambia su posición relativa (asumido)
- Scenario: Desempate estable
  Given dos tareas tienen la misma relevancia según el criterio definido
  When se muestra el listado
  Then su orden relativo es determinista entre recargas (asumido: p. ej. desempate por fecha de creación)

***

### Story: ORG-003 - Estado vacío del listado
Como usuario sin tareas visibles, quiero ver un estado vacío con una invitación a crear la primera, para no encontrarme una pantalla en blanco sin guía.

**Criterios de Aceptación:**
- Scenario: Cuenta nueva sin tareas
  Given un usuario recién registrado que no tiene ninguna tarea
  When abre el listado de tareas
  Then ve un estado vacío
  And ese estado incluye una invitación a crear su primera tarea
- Scenario: Todas las tareas archivadas
  Given un usuario cuyas tareas están todas en estado `archived`
  When abre la vista por defecto del listado (asumido: la vista por defecto no muestra las archivadas, dado que el PRD equipara "todas archivadas" con "no tener ninguna tarea")
  Then ve el estado vacío con la invitación a crear una tarea
- Scenario: El estado vacío desaparece al crear la primera tarea
  Given el usuario está viendo el estado vacío
  When crea una tarea con título válido
  Then el estado vacío desaparece
  And la nueva tarea se muestra en el listado

---

## Épica 4: Exportación (PRD §3.4)

Los usuarios pueden llevarse sus datos fuera de FlowSync.

### Story: EXP-001 - Exportación de tareas a CSV
Como usuario registrado, quiero exportar mis tareas a un archivo CSV, para llevarme mis datos fuera de FlowSync.

**Criterios de Aceptación:**
- Scenario: Exportación exitosa con columnas mínimas
  Given el usuario autenticado tiene una o más tareas
  When solicita la exportación a CSV
  Then descarga un archivo CSV
  And cada tarea incluye, como mínimo, título, descripción, estado y fecha límite
- Scenario: Exportación completa y privada
  Given el usuario tiene tareas en los tres estados
  When exporta a CSV
  Then el archivo contiene todas sus tareas, incluidas las archivadas (asumido: la exportación no aplica el filtro activo del listado)
  And no contiene tareas de otros usuarios
- Scenario: Campos opcionales vacíos en el CSV
  Given una tarea no tiene descripción ni fecha límite
  When se exporta
  Then sus columnas de descripción y fecha límite aparecen vacías de forma consistente (asumido)
  And el título y el estado siempre tienen valor
- Scenario: Exportación con cuenta sin tareas
  Given el usuario no tiene ninguna tarea
  When solicita la exportación a CSV
  Then obtiene un archivo CSV válido con la fila de cabeceras y sin filas de datos (asumido)

---

## Épica 5: Sincronización con Google Calendar (PRD §3.5)

La funcionalidad diferenciadora del MVP y la más arriesgada. La dirección de sincronización comprometida es **FlowSync → Google Calendar**; la sincronización inversa (editar en Google y reflejarlo en la tarea) queda condicionada a un spike técnico según la nota de producto del PRD y **no se descompone en stories aquí**.

### Story: SYNC-001 - Conexión de la cuenta de Google (OAuth)
Como usuario registrado, quiero conectar mi cuenta de Google a FlowSync mediante autorización OAuth, para que mis tareas con fecha puedan reflejarse en mi calendario.

**Criterios de Aceptación:**
- Scenario: Conexión exitosa
  Given un usuario autenticado sin conexión de Google activa
  When inicia la conexión y completa la autorización OAuth concediendo los permisos solicitados
  Then FlowSync queda conectado a su cuenta de Google
  And el usuario ve reflejado que la conexión está activa (asumido: existe un indicador del estado de la conexión)
- Scenario: Autorización rechazada por el usuario
  Given el usuario ha iniciado el flujo de conexión
  When cancela o deniega los permisos en la pantalla de autorización de Google
  Then FlowSync no queda conectado
  And se le informa de forma comprensible de que la conexión no se completó (asumido)
  And puede reintentar la conexión más tarde (asumido)
- Scenario: Cada usuario conecta su propio calendario
  Given los usuarios A y B tienen cada uno su propia conexión de Google
  When se sincronizan sus tareas
  Then las tareas de A solo generan eventos en el calendario de A
  And las tareas de B solo en el de B (derivado del requisito de privacidad del PRD: los tokens de Google de un usuario nunca son accesibles por otros)

***

### Story: SYNC-002 - Las tareas con fecha límite aparecen como eventos
Como usuario con Google conectado, quiero que mis tareas con fecha límite aparezcan como eventos en mi Google Calendar, para ver mis pendientes y mi tiempo en un único sitio.

**Criterios de Aceptación:**
- Scenario: Una tarea con fecha crea un evento
  Given el usuario tiene la conexión de Google activa
  When crea una tarea con fecha límite
  Then se crea un evento en su Google Calendar correspondiente a esa tarea
- Scenario: Una tarea sin fecha no crea evento
  Given el usuario tiene la conexión de Google activa
  When crea una tarea sin fecha límite
  Then no se crea ningún evento en Google Calendar
- Scenario: Añadir fecha a una tarea que no la tenía
  Given una tarea existente sin fecha límite y la conexión de Google activa
  When el usuario le añade una fecha límite
  Then se crea el evento correspondiente en Google Calendar (asumido: equivale a que la tarea pasa a ser "tarea con fecha")
- Scenario: Sin conexión no se generan eventos
  Given un usuario que no ha conectado su cuenta de Google
  When crea tareas con fecha límite
  Then las tareas se guardan con normalidad en FlowSync
  And no se intenta crear ningún evento

***

### Story: SYNC-003 - Actualización del evento al cambiar la tarea
Como usuario con Google conectado, quiero que al cambiar la fecha de una tarea se actualice su evento en Google Calendar, para que el calendario nunca quede desactualizado respecto a mi lista.

**Criterios de Aceptación:**
- Scenario: El cambio de fecha actualiza el evento
  Given una tarea con fecha límite ya sincronizada como evento
  When el usuario cambia su fecha límite en FlowSync
  Then el evento correspondiente en Google Calendar pasa a reflejar la nueva fecha
- Scenario: Quitar la fecha límite retira el evento
  Given una tarea con evento sincronizado
  When el usuario elimina su fecha límite
  Then el evento deja de figurar en el calendario (asumido: sin fecha no hay evento, en coherencia con SYNC-002)
- Scenario: Cambio de título de una tarea sincronizada
  Given una tarea con evento sincronizado
  When el usuario edita su título en FlowSync
  Then el evento se actualiza para reflejar el nuevo título (asumido: el PRD solo cita explícitamente el cambio de fecha; validar este comportamiento en refinamiento)

***

### Story: SYNC-004 - Reflejo de tareas completadas o borradas
Como usuario con Google conectado, quiero que al completar o borrar una tarea su evento se elimine o se marque en Google Calendar, para no ver en el calendario compromisos que ya no existen.

**Criterios de Aceptación:**
- Scenario: Borrar una tarea sincronizada
  Given una tarea con evento sincronizado
  When el usuario borra la tarea en FlowSync
  Then el evento correspondiente desaparece del Google Calendar (asumido: para el borrado la opción coherente es eliminar el evento; el PRD deja abierta la alternativa "se elimina o se marca")
- Scenario: Completar una tarea sincronizada
  Given una tarea con evento sincronizado
  When el usuario la marca como `completed`
  Then el evento se elimina o se marca según el comportamiento que el equipo defina en refinamiento (el PRD deja explícitamente abierta la elección)
  And el calendario deja de presentar esa entrada como un pendiente activo
- Scenario: Solo se toca el evento vinculado
  Given el usuario tiene en su Google Calendar otros eventos ajenos a FlowSync
  When completa o borra una tarea sincronizada
  Then únicamente se ve afectado el evento vinculado a esa tarea (asumido)

***

### Story: SYNC-005 - Desconexión de la cuenta de Google
Como usuario con Google conectado, quiero poder desconectar mi cuenta en cualquier momento, para recuperar el control sobre qué escribe FlowSync en mi calendario.

**Criterios de Aceptación:**
- Scenario: La desconexión detiene la sincronización
  Given el usuario tiene la conexión de Google activa
  When desconecta su cuenta de Google
  Then FlowSync deja de sincronizar tareas con Google Calendar
  And los cambios posteriores en sus tareas no crean ni actualizan eventos
- Scenario: Las tareas no se borran al desconectar
  Given el usuario tiene tareas (con y sin fecha límite) y desconecta Google
  When se completa la desconexión
  Then todas sus tareas siguen intactas en FlowSync
- Scenario: El estado de la conexión es visible y reversible
  Given el usuario ha desconectado su cuenta de Google
  When consulta el estado de la conexión (asumido: en el mismo lugar donde se conecta, ver SYNC-001)
  Then FlowSync muestra que no hay conexión activa
  And le permite volver a conectarla (asumido)

***

### Story: SYNC-006 - Resiliencia ante fallos de la API de Google
Como usuario, quiero que mis tareas se guarden en FlowSync aunque la sincronización con Google falle y que esta se reintente más tarde, para no perder nunca mis datos por un problema externo.

**Criterios de Aceptación:**
- Scenario: La tarea se guarda aunque la sincronización falle
  Given el usuario tiene la conexión activa y la API de Google no está disponible
  When crea o edita una tarea con fecha límite
  Then la tarea se guarda correctamente en FlowSync
  And la operación de sincronización pendiente queda registrada para reintentarse más tarde
- Scenario: Reintento posterior
  Given una operación de sincronización que falló y quedó pendiente
  When la API de Google vuelve a estar disponible y se produce el reintento (asumido: el mecanismo y la cadencia del reintento se definen en refinamiento)
  Then el evento se crea o actualiza en Google Calendar reflejando el estado actual de la tarea
- Scenario: Error devuelto por Google
  Given la conexión de Google está activa
  When Google devuelve un error ante una operación de sincronización
  Then FlowSync no pierde ni corrompe la tarea afectada
  And la operación fallida queda registrada en los logs para diagnóstico (requisito de observabilidad del PRD)
- Scenario: El fallo de sincronización no bloquea al usuario
  Given la API de Google está fallando
  When el usuario sigue creando, editando o completando tareas
  Then puede seguir trabajando con normalidad en FlowSync (asumido: la sincronización no bloquea la interacción del usuario)

---

## Preguntas abiertas para refinamiento

Ambigüedades que el propio PRD deja abiertas y que conviene resolver antes de implementar (no bloquean la escritura del backlog, pero sí su ejecución):

1. **Criterio de ordenación "relevante para hoy"** (ORG-002): el PRD lo delega explícitamente al refinamiento del equipo.
2. **Completar una tarea sincronizada** (SYNC-004): el PRD dice "se elimina o se marca según corresponda" sin decidir cuál de las dos.
3. **Archivar una tarea sincronizada**: el PRD solo describe el efecto de completar o borrar sobre el evento; el comportamiento de `archived` respecto al calendario no está definido.
4. **Eventos ya creados al desconectar Google** (SYNC-005): el PRD garantiza que las tareas no se borran, pero no dice qué ocurre con los eventos que ya existen en el calendario.
5. **Fecha límite → hora del evento**: el PRD señala las zonas horarias como riesgo conocido; la relación entre la fecha límite de una tarea y la hora (o carácter de día completo) del evento debe definirse con cuidado.
6. **Sincronización inversa (Google → FlowSync)**: condicionada a un spike técnico según el PRD; queda fuera de este backlog hasta que se valide su alcance.

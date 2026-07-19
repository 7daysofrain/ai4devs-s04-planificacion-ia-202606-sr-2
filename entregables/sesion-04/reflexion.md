# ¿Qué te sorprendió del output de la IA?

La verdad es que en la primera generación de la historia de usuario, incluso a primera vista parecian que faltaban bastantes criterios de aceptación para las HU. Incluso usando un modelo potente como Fable (max) se me ha quedado corto. Yo en la realidad quizás no intentaría realizar todas las épicas de golpe si no una a una y quizás en archivos separados.

# ¿Qué falló o tuviste que corregir?

La primera generación se quedó corta en criterios de aceptación, así que sí hubo correcciones. Lo que hice:

- **Revisé** las historias de usuario del backlog contrastándolas con el PRD y con los hallazgos del análisis de poke-holes (sobre todo en AUTH-001/002/003).
- **Incorporé** los criterios y escenarios faltantes que consideré válidos y dentro del alcance del MVP: casos límite de contraseña y email, campos vacíos y flujos de error.
- **Descarté** parte de los hallazgos de poke-holes para no inflar el backlog (se pedía un número acotado de criterios); dejé fuera los de menor prioridad o los que dependían de decisiones aún no tomadas.
- **Dejé trazada como decisión pendiente** la normalización/*case-sensitivity* del email (`Ana@Ejemplo.com` vs `ana@ejemplo.com`), que afecta a la detección de duplicados y al login, sin resolverla en este PR pero registrándola para el refinamiento.

# ¿El patrón poke-holes te encontró algo que tú no habías visto?

En la parte de Poke holes usando Opus la verdad es que me ha dado bastantes criterios de aceptación faltantes muy válidos. En la realidad hubiera metido casi todos, pero he hecho una selección para que no hubiera demasiados por que se pedía así

Lo que mas me ha sorprendido es como haciendo una revisión a si mismo ha encontrado mucho mas de lo que inicialmente habia hecho un modelo en principio mas pontente
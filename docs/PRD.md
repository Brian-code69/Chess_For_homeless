# PRD · Ajedrez contra la maquina Chess_for_Homeless

> Documento de requisitos del producto (Product Requirements Document).
> Complétalo en el Sprint 0 (historia H0.3) y pide la aprobación del profesor en un Issue.
> Cada requisito funcional (RF) se convierte en un Issue con la plantilla "Historia de usuario".

| Campo | Valor |
| --- | --- |
| Alumno |Brian Burgos|
| Proyecto n.º |29|
| Sitio de referencia |Liches ('https://lichess.org')|
| Compañero revisor |En eso|
| URL desplegada | (se completa en el Sprint 5) |

## 1. Problema

Los jugadores que desean practicar ajedrez contra la máquina con una experiencia limpia y rápida inspirada en Lichess suelen depender de plataformas cerradas o cajas negras donde no se entiende cómo se validan los movimientos legales ni cómo decide el motor. Esto dificulta tanto entrenar partidas contra una IA accesible como aprender la mecánica real de validación de reglas y búsqueda de jugadas.

## 2. Usuarios

- **Usuario principal:** jugador aficionado o estudiante de ajedrez que entra desde navegador web (en móvil o escritorio) para jugar partidas contra la máquina.
- **Usuario secundario:** desarrollador o evaluador del proyecto que revisa la legalidad de los movimientos, el rendimiento de la búsqueda Minimax con poda alfa-beta, la persistencia en base de datos y la arquitectura del sistema.

## 3. Objetivo y métrica de éxito

Permitir que un jugador inicie una partida contra la máquina, realice jugadas válidas y reciba respuesta del motor con poda alfa-beta en profundidades estándar, manteniendo 0 errores en consola y persistencia confiable de la partida.

## 4. Alcance del MVP

Entre 5 y 8 funcionalidades que SÍ entran este semestre.

1. Crear partidas contra la máquina eligiendo bando (blancas o negras) y nivel/profundidad.
2. Generar y validar movimientos legales de ajedrez (incluyendo jaque, jaque mate, tablas, enroque, captura al paso y coronación).
3. Calcular la respuesta de la máquina usando el algoritmo Minimax con poda alfa-beta y evaluación heurística del tablero.
4. Mostrar el tablero interactivo, el turno actual, el historial de movimientos y el estado/resultado de la partida.
5. Guardar y consultar el estado y movimientos de las partidas en PostgreSQL.

### Funcionalidades → carpetas de `src/` (screaming architecture)

Cada funcionalidad del alcance será una carpeta en `src/`. Nómbralas en inglés y en plural.

| Funcionalidad | Carpeta |
| --- | --- |
| Partidas | `src/games` |
| Movimientos | `src/moves` |
| Motores (Minimax y evaluación) | `src/engines` |
| Tableros y vistas | `src/boards` |
| Historiales | `src/histories` |

## 5. Fuera de alcance

Lo que NO vas a construir.

- Juego multijugador en tiempo real entre dos personas por red o salas públicas.
- Sistema de emparejamiento (matchmaking), torneos o cálculo de rating Elo/Glicko.
- Integración de motores externos pesados (como Stockfish) para reemplazar el motor propio con poda alfa-beta.
- Análisis profundo con libros de apertura masivos o bases de finales de 7 piezas.

## 6. Requisitos funcionales

| ID | Requisito | Prioridad (alta, media, baja) |
| --- | --- | --- |
| RF-01 | Crear una nueva partida contra la máquina indicando bando inicial y dificultad/profundidad. | Alta |
| RF-02 | Generar y validar únicamente los movimientos legales de ajedrez según el estado del tablero y el turno. | Alta |
| RF-03 | Seleccionar la jugada de la máquina mediante búsqueda Minimax con poda alfa-beta y función de evaluación. | Alta |
| RF-04 | Detectar y mostrar estados terminales de la partida (jaque, jaque mate, ahogado/tablas) y registrar el historial de jugadas. | Media |
| RF-05 | Persistir partidas y movimientos en PostgreSQL y recuperar una partida en curso o finalizada. | Alta |
| RF-06 | Renderizar el tablero y los controles de juego de forma responsiva en vistas de 375 px y 1280 px. | Media |

## 7. Requisitos no funcionales

- Responsive, móvil primero (375 px y 1280 px).
- Lighthouse ≥ 90 en Accesibilidad y Buenas prácticas.
- 0 errores en la consola del navegador.
- Funciona con 10.000 registros sin congelarse.

## 8. Modelo de datos

Diagrama entidad-relación y tablas PostgreSQL con tipos, claves y restricciones.
GitHub dibuja diagramas Mermaid; edita este ejemplo:

```mermaid
erDiagram
  GAME ||--o{ MOVE : contains
  GAME {
    uuid id PK
    text status
    text ai_side
    int search_depth
    text fen
    timestamptz created_at
  }
  MOVE {
    uuid id PK
    uuid game_id FK
    int ply_number
    text san
    text uci
    int eval_score
    timestamptz played_at
  }

## 9. Reto técnico distintivo

Implementar desde cero la generación de movimientos legales de ajedrez y el motor de búsqueda Minimax con poda alfa-beta (junto con una evaluación heurística coherente y ordenación básica de jugadas) sin depender de librerías externas que resuelvan el ajedrez o la búsqueda por mí, logrando que la máquina juegue de forma legal y ágil sin bloquear la interfaz.

## 10. Riesgos y preguntas abiertas

Profundidades altas de búsqueda en Minimax pueden aumentar el tiempo de respuesta si no se poda bien o no se limita el tiempo/profundidad; habrá que medir y acotar niveles.

Casos borde de las reglas del ajedrez (enroque tras movimiento de rey/torre o con casillas atacadas, captura al paso solo en el turno inmediato, coronación y tablas por repetición/ahogado) requieren pruebas exhaustivas.

Falta confirmar la asignación definitiva del compañero revisor para las aprobaciones de Pull Requests en el curso.

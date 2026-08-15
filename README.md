# frontend-estructura

Skill para Claude Code y Codex que define una estructura de carpetas escalable y organizada por dominio (feature-based) para proyectos frontend, con nomenclatura en español, stack de estilos fijo (Tailwind + la librería de componentes correspondiente al framework) y reglas de reutilización que evitan reconstruir el mismo elemento de UI en cada vista.

## Qué resuelve

- Evita carpetas "cajón de sastre" (`components/`, `services/`) con cientos de archivos de todo el proyecto mezclados — organiza por dominio de negocio (`usuarios`, `productos`, `pedidos`), no por tipo de archivo.
- Evita que se rediseñe el mismo elemento (header, footer, botón) en cada vista, causando inconsistencias visuales y gasto innecesario de contexto/tokens.
- Fija de una vez el stack de estilos (Tailwind + shadcn/ui, shadcn-vue o spartan/ng según el framework), para no reinventar esa decisión en cada proyecto.
- Define dónde van tests y assets, para no improvisarlo cada vez.

## Estructura que aplica

```
src/
├── modulos/
│   └── <dominio>/
│       ├── componentes/        # componentes SOLO de este módulo (+ tests junto al archivo)
│       ├── hooks/
│       ├── servicios/
│       ├── tipos/
│       ├── vistas/             # lista, detalle, crear/editar
│       └── index.ts            # única puerta pública del módulo
├── compartido/
│   ├── componentes/
│   │   ├── ui/                 # generado por shadcn/ui, shadcn-vue o spartan/ng
│   │   └── layout/              # Header, Footer, Sidebar — una sola vez, reutilizados
│   ├── hooks/
│   ├── servicios/
│   ├── utilidades/
│   └── activos/                 # assets globales
├── config/
├── router/
├── store/
└── estilos/
    └── globales.css
```

Detalle completo de reglas, nomenclatura y stack de estilos en [SKILL.md](SKILL.md).

## Instalación

### Claude Code

```bash
git clone https://github.com/Espiritu16/frontend-estructura.git
mkdir -p ~/.claude/skills/frontend-estructura
cp frontend-estructura/SKILL.md ~/.claude/skills/frontend-estructura/SKILL.md
```

Con eso queda disponible como skill global — se activa sola cuando pidas crear, organizar o revisar un proyecto frontend, o puedes invocarla explícito con `/frontend-estructura`.

**Para que Claude la tenga presente siempre**, no solo cuando la invoques por nombre, agrega esta referencia en tu `~/.claude/CLAUDE.md`:

```markdown
## Estructura de frontend

- Al crear, organizar o revisar cualquier proyecto frontend, usar siempre la
  skill `frontend-estructura` para la organización de carpetas, nomenclatura
  y stack de estilos — no improvisar una estructura nueva.
```

### Codex

```bash
git clone https://github.com/Espiritu16/frontend-estructura.git
mkdir -p ~/.codex/skills/frontend-estructura
cp frontend-estructura/SKILL.md ~/.codex/skills/frontend-estructura/SKILL.md
```

Codex no siempre auto-descubre skills por descripción de la misma forma en cada sesión. Para asegurarte de que la reconozca y la use desde el inicio de una conversación, **copia y pega este prompt**:

```
Tienes disponible la skill "frontend-estructura" en
~/.codex/skills/frontend-estructura/SKILL.md. Léela completa antes de crear,
organizar o modificar cualquier proyecto frontend en esta conversación.

A partir de ahora, en este proyecto:
- Organiza el código por dominio/módulo (modulos/<dominio>/), nunca por tipo
  de archivo suelto (components/, services/ como carpetas únicas de todo el
  proyecto).
- Usa la nomenclatura en español definida en la skill (módulos, componentes,
  servicios, tipos, vistas, utilidades).
- Usa Tailwind CSS siempre. Usa la librería de componentes correspondiente al
  framework: shadcn/ui en React, shadcn-vue en Vue, spartan/ng en Angular.
- Antes de crear cualquier componente de UI (especialmente header, footer,
  sidebar, botones), verifica primero si ya existe uno reutilizable en
  compartido/componentes/ en vez de recrearlo — nunca dupliques el mismo
  elemento por vista.
- Sigue el resto de reglas del archivo tal cual están escritas, incluida la
  lista de verificación final antes de dar por terminada una tarea.
```

## Uso

Una vez instalada, simplemente pide lo que necesites de forma natural — no hace falta nombrarla:

- "crea el módulo de usuarios en el frontend"
- "organiza este proyecto siguiendo la estructura estándar"
- "revisa si esta carpeta sigue la convención"

También puedes forzarla explícito:

```
usa la skill frontend-estructura para crear el módulo de productos
```

## Actualizar

Si mejoras la skill localmente, vuelve a hacer commit y push a este repositorio para mantenerlo como fuente de verdad versionada, igual que con cualquier otro proyecto.

## Relación con otras skills

- No decide patrones de componentes específicos de framework (`frontend-patterns`, `react-patterns`, `vue-patterns`, `angular-developer`).
- No decide accesibilidad ni dirección visual (`frontend`, `frontend-a11y`, `frontend-design-direction`).
- Si el repositorio del proyecto usa [project-continuity](https://github.com/Espiritu16/project-continuity), esta skill es la que debe quedar referenciada en la sección "Documentación de referencia" del `AGENTS.md` de cada repositorio frontend — no se repite el árbol completo ahí, solo se referencia esta skill.

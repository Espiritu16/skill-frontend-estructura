# frontend-estructura

Skill para Claude Code y Codex que define una estructura de carpetas escalable y organizada por dominio (feature-based) para proyectos frontend, con nomenclatura en español, stack de estilos fijo (Tailwind + la librería de componentes correspondiente al framework) y reglas de reutilización que evitan reconstruir el mismo elemento de UI en cada vista.

## Qué resuelve

- Evita carpetas "cajón de sastre" (`components/`, `services/`) con cientos de archivos de todo el proyecto mezclados — organiza por dominio de negocio (`usuarios`, `productos`, `pedidos`), no por tipo de archivo.
- Evita que se rediseñe el mismo elemento (header, footer, botón) en cada vista, causando inconsistencias visuales y gasto innecesario de contexto/tokens.
- Fija de una vez el stack de estilos (Tailwind + shadcn/ui, shadcn-vue o spartan/ng según el framework), para no reinventar esa decisión en cada proyecto.
- Define dónde van tests y assets, para no improvisarlo cada vez.

## Estructura que aplica

Ejemplo real con tres módulos (React + shadcn/ui + Tailwind):

```
src/
├── modulos/
│   ├── usuarios/
│   │   ├── componentes/
│   │   │   ├── TarjetaUsuario.tsx
│   │   │   └── TarjetaUsuario.test.tsx      # test junto al archivo que prueba
│   │   ├── hooks/
│   │   │   └── useUsuarios.ts
│   │   ├── servicios/
│   │   │   └── usuarioService.ts
│   │   ├── tipos/
│   │   │   └── usuario.types.ts
│   │   ├── vistas/
│   │   │   ├── UsuariosListaView.tsx
│   │   │   ├── UsuarioDetalleView.tsx
│   │   │   └── UsuarioCrearView.tsx
│   │   └── index.ts                          # única puerta pública del módulo
│   │
│   ├── productos/
│   │   ├── componentes/
│   │   ├── hooks/
│   │   ├── servicios/
│   │   ├── tipos/
│   │   ├── vistas/
│   │   │   ├── ProductosListaView.tsx
│   │   │   └── ProductoDetalleView.tsx
│   │   └── index.ts
│   │
│   └── pedidos/
│       ├── componentes/
│       ├── hooks/
│       ├── servicios/
│       ├── tipos/
│       ├── vistas/
│       │   └── PedidosListaView.tsx
│       └── index.ts
│
├── compartido/
│   ├── componentes/
│   │   ├── ui/                                # generado por shadcn/ui, shadcn-vue o spartan/ng
│   │   └── layout/
│   │       ├── Header.tsx                     # único header, reutilizado en todo el proyecto
│   │       ├── Footer.tsx
│   │       └── Sidebar.tsx
│   ├── hooks/
│   │   └── useDebounce.ts
│   ├── servicios/
│   │   └── clienteApi.ts                      # cliente base + interceptores
│   ├── utilidades/
│   │   └── formatearFecha.ts
│   └── activos/
│       ├── logo.svg
│       └── iconos/
│
├── config/
│   └── variablesEntorno.ts
├── router/
│   └── rutas.tsx
├── store/
│   └── authStore.ts
└── estilos/
    └── globales.css                           # @tailwind base/components/utilities + @font-face, @keyframes
```

Un módulo nunca importa directo de otro (solo de `compartido/`), cada uno expone solo lo necesario por su `index.ts`, y cualquier elemento repetido 2+ veces (header, botón, tarjeta) se extrae a `compartido/componentes/` en vez de recrearse por vista. Detalle completo de reglas, nomenclatura y stack de estilos en [SKILL.md](SKILL.md).

## Instalación

### Claude Code

```bash
git clone https://github.com/Espiritu16/skill-frontend-estructura.git
mkdir -p ~/.claude/skills/frontend-estructura
cp skill-frontend-estructura/SKILL.md ~/.claude/skills/frontend-estructura/SKILL.md
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
git clone https://github.com/Espiritu16/skill-frontend-estructura.git
mkdir -p ~/.codex/skills/frontend-estructura
cp skill-frontend-estructura/SKILL.md ~/.codex/skills/frontend-estructura/SKILL.md
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

## Prompt de auto-instalación (copiar y pegar)

En vez de correr los comandos tú mismo, puedes darle este prompt al agente (Claude Code o Codex) para que **él mismo** clone el repo, instale la skill y deje la referencia persistente configurada. La diferencia clave entre ambos: en Claude Code el archivo persistente es `CLAUDE.md`; en Codex el equivalente **no es `CLAUDE.md`, es `AGENTS.md`** — cada herramienta tiene su propio archivo de instrucciones globales, con su propia ubicación.

### Para Claude Code

```
Instala la skill "frontend-estructura" desde este repositorio:
https://github.com/Espiritu16/skill-frontend-estructura

1. Clona el repositorio (o descarga SKILL.md directamente).
2. Copia SKILL.md a ~/.claude/skills/frontend-estructura/SKILL.md
   (crea la carpeta si no existe).
3. Abre ~/.claude/CLAUDE.md (créalo si no existe, sin borrar contenido que
   ya tenga) y agrega, si no está ya, esta sección:

   ## Estructura de frontend
   - Al crear, organizar o revisar cualquier proyecto frontend, usar siempre
     la skill `frontend-estructura` para la organización de carpetas,
     nomenclatura y stack de estilos — no improvisar una estructura nueva.

4. Confirma qué archivos quedaron creados o modificados.
```

### Para Codex

```
Instala la skill "frontend-estructura" desde este repositorio:
https://github.com/Espiritu16/skill-frontend-estructura

1. Clona el repositorio (o descarga SKILL.md directamente).
2. Copia SKILL.md a ~/.codex/skills/frontend-estructura/SKILL.md
   (crea la carpeta si no existe).
3. Abre ~/.codex/AGENTS.md (créalo si no existe, sin borrar contenido que ya
   tenga — en Codex este archivo cumple el rol que CLAUDE.md cumple en
   Claude Code) y agrega, si no está ya, esta sección:

   ## Estructura de frontend
   - Al crear, organizar o revisar cualquier proyecto frontend, leer y
     seguir ~/.codex/skills/frontend-estructura/SKILL.md para la
     organización de carpetas, nomenclatura y stack de estilos — no
     improvisar una estructura nueva.

4. Confirma qué archivos quedaron creados o modificados.
```

**Nota sobre dónde va cada cosa:**

| | Claude Code | Codex |
|---|---|---|
| Skill instalada en | `~/.claude/skills/frontend-estructura/` | `~/.codex/skills/frontend-estructura/` |
| Archivo de instrucciones persistentes/globales | `~/.claude/CLAUDE.md` | `~/.codex/AGENTS.md` |

No mezclar los dos: pegarle a Codex un prompt que le diga "agrega esto a CLAUDE.md" no tendría efecto — Codex no lee ese archivo, lee `AGENTS.md`.

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

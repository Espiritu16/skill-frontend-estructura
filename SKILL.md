---
name: frontend-estructura
description: Define la estructura de carpetas y organización estándar para proyectos frontend (por dominio/módulo, no por tipo de archivo), con nomenclatura en español. Usar al crear un proyecto frontend nuevo, agregar un módulo/feature, decidir dónde va un archivo nuevo, o revisar si la organización actual sigue la convención. No usar para decisiones de estilo visual o patrones de componentes específicos de un framework (ver frontend-patterns, react-patterns, vue-patterns, etc.).
---

# Estructura de frontend

Arquitectura por dominio/feature (screaming architecture), no por tipo de archivo. Evita que carpetas como `components/` o `services/` se conviertan en un cajón de sastre con cientos de archivos de todo el proyecto mezclados.

## Estructura estándar

Ejemplo con React (ver "Nomenclatura por framework" abajo para el equivalente exacto en Vue/Angular — el principio de un módulo por dominio es el mismo en los tres, solo cambia el nombre de la subcarpeta de estado/lógica reactiva):

```
src/
├── modulos/
│   ├── usuarios/
│   │   ├── componentes/          # componentes SOLO de este módulo
│   │   │   └── TarjetaUsuario.tsx
│   │   │   └── TarjetaUsuario.test.tsx   # test junto al archivo que prueba
│   │   ├── hooks/                 # React: hooks SOLO de este módulo (ver nomenclatura por framework)
│   │   ├── servicios/             # llamadas API específicas del módulo
│   │   ├── tipos/                  # tipos/interfaces del módulo
│   │   ├── vistas/                 # un módulo casi siempre tiene varias vistas
│   │   │   ├── UsuariosListaView.tsx
│   │   │   ├── UsuarioDetalleView.tsx
│   │   │   └── UsuarioCrearView.tsx
│   │   └── index.ts                # única puerta pública del módulo
│   ├── productos/
│   └── pedidos/
├── compartido/
│   ├── componentes/               # Button, Modal, Input — reutilizables entre módulos
│   │   └── ui/                     # primitives generados por shadcn/shadcn-vue/spartan (ver Stack de estilos)
│   ├── hooks/                     # hooks genéricos
│   ├── servicios/                  # cliente API base, interceptores
│   ├── utilidades/
│   └── activos/                     # imágenes, iconos, fuentes globales
├── config/                          # variables de entorno, constantes globales
├── router/                          # definición de rutas
├── store/                           # estado global (si el proyecto lo usa)
└── estilos/
    └── globales.css                 # Tailwind base + lo que Tailwind no cubre (ver Stack de estilos)
```

## Reglas

1. **Un módulo nunca importa directo de un archivo interno de otro módulo** (ej. `productos/componentes/Tarjeta.tsx` desde `pedidos/`). Si un módulo necesita algo que otro módulo dueño ya expone, lo consume a través del `index.ts` de ese módulo — la puerta pública existe para eso, no solo para el router/composición raíz. Solo cuando el código es genuinamente agnóstico de dominio (no pertenece a ningún módulo en particular, ej. un formateador de fechas) sube a `compartido/`. No mover a `compartido/` algo que sí tiene un dueño claro solo porque otro módulo lo necesita — eso reemplaza el límite de dominio por un cajón de sastre.
2. **Cada módulo expone solo lo necesario por su `index.ts`** (barrel export) — para consumo de otros módulos y de la composición raíz por igual. El resto de sus archivos se trata como privado del módulo, aunque el lenguaje no lo fuerce técnicamente.
3. **Ninguna carpeta acumula todo suelto.** Si un módulo crece mucho (muchos componentes, muchas vistas), se subdivide en submódulos — nunca se deja una carpeta plana con decenas de archivos sin agrupar.
4. **El nombre del módulo es el dominio de negocio**, no una capa técnica (`usuarios`, `productos`, `pedidos` — no `formularios`, `listados`, `paginas`).
5. Antes de crear un archivo nuevo, ubicar primero a qué módulo pertenece por dominio; si es genuinamente reutilizable entre 2+ módulos, va en `compartido/`.
6. **Tests siempre junto al archivo que prueban** (`Componente.tsx` + `Componente.test.tsx` en la misma carpeta) — nunca en un árbol `__tests__/` espejo aparte.
7. **Assets globales** (logo, iconos compartidos, fuentes) van en `compartido/activos/`. Un asset específico de un solo módulo (ej. una ilustración que solo usa la vista de bienvenida de `usuarios`) vive dentro de ese módulo, no en `compartido/`.

## Stack de estilos

Aplica al crear un proyecto nuevo o cuando la tarea ya implica trabajar UI/estilos — no a una tarea puramente estructural (reorganizar carpetas) que no toca componentes visuales; ahí no se agregan estas dependencias solo por aplicar esta skill.

- **Tailwind CSS siempre**, en todo proyecto frontend nuevo, sin importar el framework.
- **Librería de componentes según el framework detectado** (primitives sobre Tailwind, nunca una librería con su propio sistema de theming que compita con Tailwind):
  - **React** → [shadcn/ui](https://ui.shadcn.com) — los componentes generados van en `compartido/componentes/ui/`.
  - **Vue** → shadcn-vue (puerto equivalente, misma filosofía copy-paste sobre Tailwind).
  - **Angular** → spartan/ng (ng CDK + Tailwind, equivalente a shadcn para Angular). Si el proyecto ya usa Angular Material antes de esta skill, no se reemplaza sin acordarlo — se respeta lo existente.
  - Framework fuera de estos tres: preguntar antes de asumir una librería, nunca inventar una por defecto.
- **CSS nativo** (`estilos/globales.css`): solo para lo que Tailwind no resuelve bien — `@font-face`, reset global, `@keyframes` complejas. Nunca para estilizar un componente entero a mano cuando Tailwind ya lo cubre; eso vuelve a caer en el problema de inconsistencia que ya vimos en "Consistencia de diseño".
- Nunca mezclar Tailwind con otra librería CSS-in-JS o de utilidades (Bootstrap, Bulma, styled-components) por decisión propia — si el proyecto ya trae una de estas antes de aplicar esta skill, se respeta lo existente y se señala la inconsistencia, no se reemplaza sin acordarlo con el usuario.

## Nomenclatura (coherente con las instrucciones globales del agente — `CLAUDE.md`/`AGENTS.md` según corresponda)

- Nombres de módulo/dominio: siempre en español (`usuarios`, `productos`, `pedidos`).
- Subcarpetas técnicas dentro de cada módulo: en español (`componentes/`, `servicios/`, `tipos/`, `utilidades/`), salvo el término estandarizado sin traducción natural que corresponda a cada framework (ver "Nomenclatura por framework" abajo).
- Nombres de archivo de vista: `<Módulo>View.tsx` (ej. `UsuariosView.tsx`) — mismo patrón en Vue/Angular con la extensión que corresponda.
- Nombres de componente: descriptivos en español, ej. `TarjetaProducto.tsx`, `FormularioLogin.tsx` (nota: `Login` se mantiene por estar estandarizado, según la excepción de nomenclatura de las instrucciones globales).
- Nombres de servicio: `<entidad>Service` o `<entidad>Api`, ej. `usuarioService.ts`.

### Nomenclatura por framework (subcarpeta de estado/lógica reactiva)

La estructura domain-first (`modulos/<dominio>/`) es idéntica en los tres frameworks — lo único que cambia es el nombre de la subcarpeta para la lógica reactiva/con estado de cada módulo, porque cada framework tiene su propio término estandarizado para ese concepto:

| Framework | Subcarpeta | Por qué |
|---|---|---|
| **React** | `hooks/` | Término real y estandarizado de React (`useAlgo`) — no se traduce. |
| **Vue** (Composition API) | `composables/` | Equivalente real de Vue a un hook (`useAlgo` con `ref`/`computed`) — se llama "composable" en la comunidad Vue, no "hook". Usar `hooks/` en un proyecto Vue es un anglicismo mal aplicado, no la convención real del framework. |
| **Angular** | *(no existe esta subcarpeta)* | Angular no tiene el concepto de hook/composable — la lógica reactiva con estado vive directo en `servicios/` (servicios inyectables con RxJS `Observable`/Signals), que ya es una subcarpeta existente. No crear una carpeta `hooks/` vacía ni forzar el concepto en un módulo Angular. |

El resto de subcarpetas (`componentes/`, `servicios/`, `tipos/`, `vistas/`) se mantiene igual en los tres frameworks — este es el único punto donde la nomenclatura diverge por no ser un concepto compartido entre los tres.

## Consistencia de diseño: reutilizar, nunca rediseñar en cada vista

Problema que esta regla previene: recrear el mismo elemento (header, footer, sidebar, tabla, botón) desde cero en cada vista/módulo en vez de reutilizar uno solo — produce inconsistencias visuales (un botón queda mal alineado en una vista y bien en otra) y desperdicia contexto/tokens regenerando algo que ya existe.

1. **Antes de crear cualquier elemento de UI, buscar primero en `compartido/componentes/`** si ya existe algo equivalente o parecido. No crear una versión nueva "solo para esta vista" sin haber buscado antes.
2. **Elementos de layout (header, footer, sidebar, nav) viven una sola vez**, en `compartido/componentes/layout/`, y se componen/importan en cada vista — nunca se redefinen ni se copian por módulo.
3. **Si un mismo patrón visual aparece 2 o más veces** en el proyecto (aunque sea en módulos distintos), es evidencia de que debe subir a `compartido/componentes/` — la repetición no es casualidad, es la señal de que falta extraerlo.
4. **Variaciones de un mismo componente se resuelven con props/variantes**, no duplicando el componente. Ej. `<Boton variante="primario" />` / `<Boton variante="secundario" />`, no `BotonPrimario.tsx` y `BotonSecundarioCopia.tsx` como archivos separados casi idénticos.
5. Si una vista necesita algo visualmente distinto al componente compartido existente, la decisión (crear variante vs. componente nuevo genuinamente distinto) se evalúa antes de escribir código, no después de haber duplicado.

## Si el framework exige otra estructura raíz

Frameworks como Next.js (`app/`, `pages/`) o Nuxt (`pages/`, `layouts/`) imponen carpetas raíz fijas por convención propia — esas se respetan tal cual (no se traduce lo que el framework exige). Esas carpetas obligatorias solo contienen lo que el framework mismo requiere ahí (rutas, layouts); la organización por `modulos/`/`compartido/` vive fuera de ellas, en `src/`, tal como muestra el ejemplo — "no en vez de ellas" significa que no se elimina `app/`/`pages/`, no que `modulos/` deba anidarse dentro. Ejemplo con Next.js App Router:

```
app/
├── (rutas)/                     # carpetas de ruta que exige Next.js
└── ...
src/
├── modulos/                     # misma organización de siempre, fuera de app/
└── compartido/
```

## Verificación antes de dar por terminada una tarea de organización

- ¿Cada archivo nuevo quedó dentro de su módulo de dominio, no en una carpeta técnica genérica?
- ¿Ningún módulo importa directo de otro módulo?
- ¿El módulo sigue exponiendo solo lo necesario por `index.ts`?
- ¿Ninguna carpeta quedó con archivos sueltos sin agrupar por sub-responsabilidad?
- ¿Se reutilizó un componente de `compartido/` en vez de recrear una versión nueva del mismo elemento (header, footer, botón, tabla)?
- ¿Ningún elemento de layout (header/footer/sidebar/nav) quedó redefinido dentro de un módulo en vez de vivir en `compartido/componentes/layout/`?
- ¿Cada test quedó junto a su archivo correspondiente, no en un árbol separado?
- ¿Los assets globales quedaron en `compartido/activos/`, no dispersos por los módulos?
- ¿Se usó Tailwind y la librería de componentes correspondiente al framework (shadcn/ui en React, shadcn-vue en Vue, spartan/ng en Angular), en vez de CSS a mano o una librería distinta sin acordarlo?
- ¿La subcarpeta de lógica reactiva usa el término correcto del framework (`hooks/` en React, `composables/` en Vue, directo en `servicios/` en Angular — nunca `hooks/` en Vue o Angular)?

## Relación con otras skills

- No decide patrones de componentes ni de estado (eso es `frontend-patterns`, `react-patterns`, `vue-patterns`, `angular-developer`, según el framework).
- No decide accesibilidad ni estilo visual (eso es `frontend`, `frontend-a11y`, `frontend-design-direction`).
- Si el repositorio usa `project-continuity`, esta estructura es la que debe quedar referenciada en la sección "Documentación de referencia" del `AGENTS.md` de ese repositorio — no se repite el árbol completo ahí, solo se referencia esta skill/documento.

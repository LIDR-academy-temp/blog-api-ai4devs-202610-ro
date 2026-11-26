# Prompts

Aquí van **todos los prompts que lanzaste** para hacer el ejercicio, en el orden en que los
lanzaste, con el modelo y la herramienta de cada uno.

Esto no es papeleo. Lo que se revisa es **cómo pediste las cosas**, no solo lo que salió: un
resultado flojo con un prompt bueno y un resultado flojo con un prompt vago necesitan feedback
distinto, y sin este archivo no se distinguen.

## Cómo rellenarlo

- Un apartado `## Prompt N` por cada prompt.
- **Pega el prompt tal cual lo lanzaste**, dentro del bloque de código, aunque ocupe diez líneas
  y aunque tenga faltas. No lo reescribas para que quede bien: el que arreglaste mentalmente
  después no es el que lanzaste.
- Incluye también los que **no funcionaron**. Suelen ser los más útiles de leer.
- `Modelo` y `Herramienta` en todos. Si cambiaste de una a otra a mitad, se nota aquí.

Borra el ejemplo de abajo cuando escribas el primero.

---

## Prompt 1

**Modelo:** Opus 5.5
**Herramienta:** Claude Code (extensión VS Code)

```
Trabajas sobre dos repositorios hermanos: /home/gaboroncancio/ai4dev/mod-7/blog/blog-api y /home/gaboroncancio/ai4dev/mod-7/blog/blog-ai. Crea en CADA uno estas tres piezas y no cambies ningún otro archivo:

1. Un CLAUDE.md en la raíz con una sola regla propia del repositorio:
   - blog-api: "Este repositorio es el dueño del contenido. Todo archivo nuevo debe empezar con un comentario que lo indique: `Dueño del contenido: blog-api`."
   - blog-ai: "Los nombres de los campos del contrato público (app/esquemas.py y contrato/openapi.json) deben escribirse en español, nunca en inglés."
2. Un hook PostToolUse en .claude/settings.json con matcher "Write|Edit" que ejecute el script .claude/hooks/marcador.sh del mismo repositorio (hazlo ejecutable). El script lee con jq el JSON que llega por stdin y añade una línea a MARCADORES-<nombre-del-repo>.txt en la raíz de SU PROPIO repositorio con la fecha (ISO 8601), la herramienta (tool_name) y la ruta del archivo modificado (tool_input.file_path). Usa rutas absolutas en todo: el comando del hook apunta a la ruta absoluta del script y el script escribe en la ruta absoluta del archivo de marcadores, porque tendrá que registrar cambios en archivos situados fuera de su repositorio.
3. Una skill sencilla en .claude/skills/marcador-<nombre-del-repo>/SKILL.md llamada marcador-<nombre-del-repo>, cuya description diga "Skill de marcador del repositorio <nombre-del-repo>" y que al invocarse responda "Marcador de <nombre-del-repo>".

No hagas commit. Al terminar muestra, para cada repositorio, la lista de archivos creados y el contenido de su .claude/settings.json.
```

**Qué salió:** funcionó a la primera: CLAUDE.md, hook PostToolUse con script marcador.sh (rutas absolutas, ejecutable) y skill marcador-<repo> creados en blog-api y blog-ai; sin commit.

## Prompt 2

**Modelo:** Opus 5.5
**Herramienta:** Claude Code (extensión VS Code)

```
Añade a la clase ResultadoBusqueda, en ../blog-ai/app/esquemas.py, un campo que indique cuántos fragmentos del artículo han coincidido con la búsqueda. En blog-ai modifica solo ese archivo, y hazlo con la herramienta de edición de archivos (Edit), nunca con un script ni con comandos de shell.
```

**Qué salió:** se añadió con Edit el campo `fragmentos_coincidentes: int = Field(default=1, ge=1)` a `ResultadoBusqueda` (nombre en español, por la regla del CLAUDE.md de blog-ai). Lleva valor por defecto 1 porque `app/servicios/indice.py` construye el resultado sin ese campo y no se podía tocar; hoy la búsqueda se queda con un solo fragmento por post. Quedan pendientes, fuera del alcance: calcular el valor real en la consulta SQL y regenerar `contrato/openapi.json`.

## Prompt 3

**Modelo:** Opus 5.5
**Herramienta:** Claude Code (extensión VS Code)

```
Sin leer ningún archivo, responde por separado en tres apartados:
1. Qué archivos CLAUDE.md se cargaron al iniciar esta sesión (ruta exacta de cada uno), citando literalmente la regla de cada uno.
2. Qué skills tienes disponibles (nombre y descripción).
3. Qué nombre le diste al campo nuevo de ResultadoBusqueda y por qué.
```

**Qué salió:** al arrancar se cargaron dos CLAUDE.md: el de la carpeta padre `blog/` (REGLA-PADRE) y el de `blog-api` (dueño del contenido). El de blog-ai no se cargó al inicio: se leyó a mano con `cat` durante el prompt 2. Entre las skills aparece `marcador-blog-api`, pero no `marcador-blog-ai`, porque las skills de proyecto solo se cargan desde el repo de trabajo. El campo se llama `fragmentos_coincidentes`, en español por la regla de blog-ai.

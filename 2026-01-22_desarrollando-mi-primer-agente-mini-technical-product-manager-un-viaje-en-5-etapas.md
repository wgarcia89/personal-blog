# Desarrollando mi primer agente "Mini Technical Product Manager": un viaje en 5 etapas

Continuando con mi exploración de agentes con LangGraph, hoy quiero documentar la arquitectura y evolución de mi primer agente práctico real: **Mini-TPM**; nació de la rutina de dar soporte y mantenimiento a proyectos **Ruby on Rails** en **Cluvi**.

**🔗 Repositorio:** [Mini-Technical-Product-Manager](https://github.com/wgarcia89/Mini-Technical-Product-Manager)

**Nota de contexto:** este proyecto lo desarrollé originalmente en **abril de 2025**.

Este proyecto no nació como ejercicio teórico. Nació de dos necesidades muy concretas:

- poder entregarle a un agente una **URL** o una **ruta** y que me ayude a responder preguntas reales del equipo (y a diagnosticar rápido “dónde vive” un comportamiento en el código).
- poder entregarle al **equipo de producto** una forma de “documentación viva” del backend: algo que les permitiera revisar **mejoras** y **soportes** con contexto técnico, sin depender siempre de que un desarrollador esté disponible.

A continuación cuento las 5 etapas clave, aterrizadas 100% a la estructura y capacidades del proyecto `mini-tpm`.

## 1. Definición del alcance: todo empieza con una URL (o endpoint)

La primera decisión fue definir el punto de entrada: ¿qué le doy al agente para que empiece a trabajar?

Elegí lo más concreto que existe en un backend: una **URL** o **endpoint** (por ejemplo: `https://api.local/api/todos`).

En el grafo principal (`agentes/analizador.py`) el agente expone herramientas de LangGraph, y una de las más importantes en este flujo es `obtener_repositorio_por_endpoint(url)`: transforma una URL en contexto de trabajo con tres datos:

- **repositorio**
- **endpoint**
- **ambiente**

## 2. Detectar proyecto y ambiente sin RAG: búsqueda determinista

En esta etapa tomé una decisión deliberadamente “anti-hype”: para mapear una URL al repositorio correcto **no usé embeddings ni RAG**.

En su lugar, implementé una búsqueda clásica y determinista con `BuscadorRepositorios` (`capacidades/busqueda/buscador_repositorios.py`).

¿De dónde sale la verdad? De un archivo simple y versionable: `datos/repositorios.json`. El agente carga esa lista, recorre endpoints conocidos y hace un match directo contra la URL (sin magia).

El resultado es rápido, reproducible y fácil de depurar. Para este caso de uso, es exactamente lo que quería.

## 3. Convertir `rails routes` en datos consultables (y dejar de “adivinar” rutas)

Muy pronto me encontré con un cuello de botella: si quiero pasar de “tengo una URL” a “qué controlador la maneja”, necesito un mapa fiable de rutas.

La solución fue separar esa responsabilidad en una utilidad: `ConvertidorRutasRails` (`utilidades/convertidor_rutas_rails.py`), que convierte la salida de:

`bundle exec rails routes`

en un JSON estructurado en `datos/rutas/*.json` con campos como:

- **verbo**
- **ruta**
- **controlador**
- **accion**
- **ruta_archivo**

Esto fue un salto enorme: el agente deja de inferir a ciegas y empieza a consultar una fuente “indexada” y barata.

## 4. Capacidad de análisis: leer, explorar y ubicar controladores con fallbacks

Con el repositorio identificado y las rutas disponibles, el agente necesitaba “ojos” y “brújula”.

En `mini-tpm` eso vive como **capacidades**, separadas por intención:

- `LectorArchivos` (`capacidades/lectura/lector_archivos.py`): leer cualquier archivo del repo objetivo.
- `ListadorArchivos` (`capacidades/lectura/listador_archivos.py`): explorar estructura y ubicar archivos cuando no sabemos dónde están.
- `BuscadorControladorPorRuta` (`capacidades/busqueda/buscador_controladores_por_ruta.py`): encontrar controlador por ruta, con una estrategia jerárquica **exacta → prefijo → fuzzy** para evitar falsos positivos.
- `BuscadorControladorPorModelo` (`capacidades/busqueda/buscador_controladores_por_modelo.py`): búsqueda “inversa” por modelo + acción; en lugar de depender de campos extra en el JSON, **infiere el modelo** desde el nombre del controlador.

Un detalle que me parece clave: el agente también se fuerza a no “alucinar” la versión de Rails. Cuando necesita algo específico del framework, el prompt del sistema (en `instrucciones/analisis.py`) le indica **leer el `Gemfile`** y adaptar su respuesta a la versión real.

## 5. Capacidad de escritura: pasar de analista a colaborador (con barandas de seguridad)

La última etapa fue la más delicada… y también la más reveladora.

Mini-TPM nació como un agente “solo de análisis”: un **TPM técnico** que podía ubicar rutas, leer controladores y explicarte qué estaba pasando. Pero, casi desde el inicio, me di cuenta de algo curioso: cuando encontraba un bug, en sus respuestas ya **proponía el cambio de código** (a veces incluso devolvía el bloque modificado completo).

Ahí fue cuando lo vi claro: **No le faltaba inteligencia; le faltaba capacidad de ejecutar.**. En otras palabras, el salto de TPM a “desarrollador” no era conceptual: era operacional.

La implementé como `EscritorArchivos` (`capacidades/escritura/escritor_archivos.py`). Esta capacidad puede crear archivos y directorios, y actualizar archivos existentes, pero con validaciones para no convertir el proyecto en una ruleta:

- **Ruta dentro del workspace** (vía `WORKSPACE_PATH`)
- **Sin path traversal** (no permite `..`)
- **Repositorio válido**
- **Extensiones permitidas**
- **Check de longitud**: si el contenido nuevo es >20% más corto que el original, bloquea la escritura salvo que se fuerce explícitamente

Con esto, el flujo completo ya se siente realista:

1. Le doy una URL.
2. Identifica el repositorio y el ambiente.
3. Ubica rutas y controlador (con fallbacks).
4. Lee archivos relevantes para análisis.
5. Si hace falta, propone y aplica cambios con escritura segura.

## Conclusiones

Mini-TPM me dejó una lección muy clara: el poder de un agente no está solo en el modelo (en mi caso, Gemini), sino en la calidad de sus **capacidades** y en cómo están “amarradas” a evidencia real (archivos, rutas, estructura del repo).

Construirlo por etapas me permitió validar cada pieza antes de sumar la siguiente. Y, sobre todo, me dio un artefacto reutilizable: un “TPM técnico” que entiende el terreno (rutas, controladores, código) y puede acompañar el mantenimiento de proyectos Rails de forma práctica.

## Metadata
- Title: "Desarrollando mi primer agente Mini-TPM: un viaje en 5 etapas"
- Author: "William Fernando Garcia Muñoz"
- Date: 2026-01-22
- DevelopmentDate: 2025-04
- Description: "Desglose técnico del desarrollo de Mini-TPM, un agente con LangGraph para analizar (y escribir) en proyectos Ruby on Rails, desde el mapeo de URLs a repositorios hasta capacidades de lectura, búsqueda, rutas y escritura segura."

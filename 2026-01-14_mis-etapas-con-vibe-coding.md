# Mis etapas con Vibe Coding

2025 fue el año en que transformé mi forma de desarrollar software. No fue un cambio lineal ni planificado, sino una evolución forzada por la fricción, los errores y el aprendizaje constante.

## El punto de partida: Vercel y el prototipo que se volvió problema

Antes de Cursor, **v0 de Vercel** era mi herramienta principal para crear prototipos. Era rápido, visual y permitía iterar ideas en minutos. Llegué a pasar un proyecto a producción usando este enfoque.

El problema vino después.

El mantenimiento se volvió **tedioso**. Varios perfiles del equipo —diseño, producto y desarrollo— evolucionaron el proyecto en diferentes direcciones. El código generado comenzó a mostrar sus costuras.

La decisión final fue dolorosa pero necesaria: **desechamos el proyecto** y reescribimos de nuevo con **Vibe Coding**.

No solo por la deuda técnica acumulada, sino porque estaba construido en **React** que no está en nuestro stack. 

## La transición a Cursor

Después de esa experiencia, comencé a explorar **Cursor**, **Gemini CLI**, **Claude Code**, **Visual Studio**... Mi enfoque fue probar cada herramienta sin profundizar tanto, después de un tiempo vi la necesidad de profundizar en una y me quedé en **Cursor** porque puedo usar múltiples modelos y me familiaricé con su IDE.

Al inicio cada conversación tenía muchas iteraciones, cada HU era una conversación extensa y tediosa con el LLM hasta llegar a prompts extensos, claros y específicos.

En julio comprendí algo fundamental: **los LLMs no son iguales**. Cada modelo tiene fortalezas específicas. **Claude** para programación, **Gemini** por su ventana de contexto y Auto por costo.

## Mi stack actual de modelos

| Tarea | Modelo | Por qué |
|-------|--------|---------|
| Especificaciones técnicas | Claude | Estructurar pensamiento, documentar arquitectura y escribir specs |
| Feedback y revisión | Gemini | Análisis crítico, identificar edge cases y sugerir mejoras |
| Carpintería operativa | Auto | Tareas repetitivas, refactoring y escribir código |


## Los números del año

- **595 millones de tokens** consumidos
- **11 modelos** probados
- **+2,400 prompts** ejecutados

Mis modelos más usados fueron:

1. **Auto** — 1,594 prompts (el caballo de batalla)
2. **Claude 4.5 Sonnet** — 364 prompts (especificaciones y arquitectura)
3. **Gemini 2.5 Pro** — 332 prompts (feedback y análisis)

El uso creció exponencialmente desde la semana 31 (finales de julio), exactamente cuando entendí cómo trabajar con múltiples modelos.

## Reflexiones finales

1. Los prototipos rápidos tienen costo oculto.
2. Un modelo no sirve para todo si consideras costo/beneficio.
3. Vibe Coding aumentó mi productividad en proyectos nuevos; en etapa de mantenimiento no tanto.
4. La productividad se incrementa con stack conocido.

**¿Ustedes cómo combinan los modelos?** Me encantaría conocer otros enfoques.

---

## Metadata
- Title: "Mis etapas con Vibe Coding"
- Author: "William Fernando Garcia Muñoz"
- Date: 2026-01-14
- Description: "Reflexión sobre un año de desarrollo con IA: desde prototipos en Vercel hasta un workflow especializado con múltiples modelos en Cursor"

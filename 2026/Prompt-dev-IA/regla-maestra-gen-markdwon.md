# REGLAS MAESTRAS PARA GENERAR ARCHIVOS MARKDOWN (.MD) SIN ROMPER FORMATO

## OBJETIVO

Estas reglas definen estándares obligatorios para generar archivos `.md` robustos, compatibles y seguros para:

- ChatGPT
- Claude
- Gemini
- Copilot
- Cursor
- Windsurf
- Notion
- Obsidian
- GitHub
- GitLab
- sistemas RAG
- embeddings
- agentes IA
- parsers markdown

El objetivo es evitar:

- markdown roto
- bloques abiertos
- tablas corruptas
- anidación inválida
- pérdida de estructura
- parsing incorrecto
- errores de renderizado
- respuestas ambiguas

---

# PRINCIPIO FUNDAMENTAL

## NUNCA asumir que el renderizador markdown es inteligente.

Muchos motores markdown interpretan diferente:

- triple backticks
- tablas
- indentaciones
- listas
- HTML inline
- bloques anidados

Por tanto:

## SIEMPRE generar markdown defensivo.

---

# REGLAS CRÍTICAS OBLIGATORIAS

---

# 1. NUNCA ANIDAR BLOQUES MARKDOWN

## PROHIBIDO

````md
```md
```sql
SELECT * FROM tabla;
```
```

Esto rompe parsers.

CORRECTO

Usar indentación textual:

```sql
SELECT * FROM tabla;
```
2. TODO BLOQUE DE CÓDIGO DEBE CERRARSE
OBLIGATORIO

Cada apertura:

```

Debe tener cierre:

```
VALIDACIÓN OBLIGATORIA

Antes de finalizar:

contar aperturas
contar cierres
validar balance
3. NO MEZCLAR EJEMPLOS Y DOCUMENTACIÓN REAL
MALA PRÁCTICA
instrucciones reales
ejemplos ejecutables
markdown renderizable

mezclados en el mismo nivel.

CORRECTO

Separar:

instrucciones
ejemplos
templates
snippets
4. USAR INDENTACIÓN PARA MOSTRAR EJEMPLOS
RECOMENDADO

Para enseñar markdown dentro de markdown:

Usar 4 espacios:

# Título

## Subtítulo

```sql
SELECT 1;
```
5. EVITAR TRIPLE BACKTICKS DENTRO DE OTROS TRIPLE BACKTICKS
PROBLEMA

Muchos parsers:

cierran antes de tiempo
consumen contenido posterior
rompen estructura completa
SOLUCIÓN

Usar:

indentación
texto escapado
pseudo-bloques
6. TABLAS MARKDOWN DEBEN SER SIMPLES
RECOMENDADO
Campo	Valor
Estado	Activo
EVITAR
saltos de línea complejos
listas internas extensas
bloques de código dentro de tablas
HTML embebido
7. MANTENER JERARQUÍA CONSISTENTE
CORRECTO
# Nivel 1
## Nivel 2
### Nivel 3
EVITAR

Saltos incoherentes:

#
luego ####
luego ##
8. NO USAR HTML EMBEBIDO SI NO ES NECESARIO
EVITAR
<div>
<table>
<style>

Porque:

algunos LLM lo interpretan mal
rompe renderizadores
afecta embeddings
9. NO GENERAR JSON O YAML SIN VALIDAR CIERRES
VALIDAR
llaves
corchetes
comillas
comas
indentación
ESPECIALMENTE IMPORTANTE EN
OpenAPI
docker-compose
workflows
manifests
configs IA
10. LAS LISTAS DEBEN SER CONSISTENTES
CORRECTO
item 1
item 2
item 3
EVITAR
mezclar -
*
+

sin necesidad.

11. SI EL ARCHIVO ES PARA IA, PRIORIZAR MARKDOWN "PLANO"
RECOMENDADO
simple
limpio
jerárquico
sin decoraciones excesivas
EVITAR
emojis masivos
HTML
markdown experimental
collapsibles
tabs
embeds complejos

Los LLM no siempre parsean eso correctamente.

12. NO ROMPER CONTEXTO SEMÁNTICO
CADA SECCIÓN DEBE SER AUTÓNOMA

Evitar referencias ambiguas:

"lo anterior"
"eso"
"aquello"

Usar nombres explícitos.

13. SIEMPRE AGREGAR VALIDACIÓN FINAL
OBLIGATORIO

Antes de entregar:

validar headers
validar tablas
validar fences
validar listas
validar indentación
validar bloques código
validar coherencia visual
14. REGLA ENTERPRISE PARA PROMPTS LARGOS
SI EL PROMPT SUPERA ~300 LÍNEAS

Entonces:

evitar nested markdown
evitar ejemplos renderizables
reducir bloques complejos
dividir secciones
usar formato plano defensivo

Porque los LLM comienzan a degradar parsing estructural.

Sí, incluso nosotros. Bienvenido al club.

15. PARA PROMPTS IA: PRIORIZAR "ROBUSTEZ" SOBRE "ESTÉTICA"
OBJETIVO REAL

No es verse bonito.

Es:

parsearse correctamente
mantenerse estable
no romper embeddings
no romper agentes
no romper RAG
no romper automatizaciones

Markdown bonito pero frágil = deuda técnica documental.

PLANTILLA DE VALIDACIÓN FINAL

Agregar SIEMPRE al final de proyectos/documentos:

VALIDACIÓN MARKDOWN:
[ ] Bloques cerrados
[ ] Tablas válidas
[ ] Headers consistentes
[ ] Sin markdown anidado peligroso
[ ] Sin HTML innecesario
[ ] Sin listas corruptas
[ ] Sin saltos estructurales
[ ] Código validado
[ ] JSON/YAML balanceado
[ ] Compatible con GitHub/Notion/LLMs
REGLA DE ORO
SI UN EJEMPLO PUEDE ROMPER EL DOCUMENTO:

NO renderizarlo.

Mostrarlo como texto indentado.

CONFIGURACIÓN RECOMENDADA PARA TODOS LOS PROYECTOS IA

Agregar esto literalmente:

REGLAS OBLIGATORIAS DE MARKDOWN:

- Nunca anidar bloques markdown
- Todos los bloques deben cerrarse
- Usar indentación para ejemplos
- Mantener markdown plano y robusto
- No mezclar ejemplos con instrucciones
- Validar integridad estructural antes de responder
- Evitar HTML innecesario
- Validar tablas markdown
- Mantener jerarquía consistente
- Priorizar compatibilidad con LLMs y parsers markdown
- Si existe riesgo de ruptura estructural, usar representación textual indentada
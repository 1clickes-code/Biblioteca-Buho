# Búho Guía — Documentación técnica

Chatbot de árbol de decisión integrado en la biblioteca de alex.decode.
Sin backend, sin API, sin coste. Vanilla HTML/CSS/JS.

---

## Qué es y cómo funciona

El búho es un widget de chat con posición fija en el lado derecho de la pantalla.
Funciona como un árbol de decisión: cada nodo tiene mensajes del búho y opciones para el usuario.
No hay IA ni LLM — toda la "inteligencia" es un mapa de nodos en JavaScript.

**Coste de uso:** cero. Se ejecuta en el navegador del usuario. No hay llamadas a APIs externas.

---

## Arquitectura

```
TREE (objeto JS)
  └── nodos: {mensajes[], opciones[]}
        └── cada opción: [label, nextNode, action?]
              └── action: {tag, lvl, card} → qué mostrar/destacar

Engine
  ├── goTo(nodeId, action)   — navega al nodo, ejecuta acción si la hay
  ├── showTyping(cb)         — muestra dots 700ms, luego ejecuta cb
  ├── addOwlMsg(text)        — bubble izquierda (el búho)
  ├── addUserMsg(text)       — bubble derecha (el usuario)
  ├── addMiniCard(resource)  — card compacta dentro del chat
  ├── scrollToCard(id)       — scroll + highlight ámbar en la grid
  └── showOpts(opts)         — renderiza los botones de opción

LocalStorage
  ├── owlVisits  — número de visitas (cambia el saludo)
  └── owlSeen    — array de IDs vistos (para el modo "Sorpréndeme")
```

---

## Nodos del árbol (TREE)

| Nodo | Descripción | Siguiente(s) |
|---|---|---|
| `start_new` | Primera visita — saludo inicial | from_video / beginner / intermediate / specific |
| `start_return` | Visita recurrente | from_video / continue / specific / surprise |
| `from_video` | Viene de Instagram | tag_result (por tema) |
| `beginner` | No sabe por dónde empezar | card_result (0.1 / 0.2 / 0.3) |
| `intermediate` | Ya tiene base | lvl_result / card_result / lost |
| `specific` | Busca tipo concreto | tag_result (por tipo de tag) |
| `lost` | Se perdió en el camino | lvl_result / card_result |
| `continue` | Quiere seguir el nivel | lvl_result (nivel+1) |
| `all_done` | Ya lo vio todo | Terminal |
| `after_result` | Post-recomendación | next_step / specific / null |
| `next_step` | ¿Qué sigue? | continue / tag_result |
| `surprise` | Recurso no visto al azar | card_result (random unseen) |

**Nodos virtuales** (no están en TREE, se generan dinámicamente):
- `tag_result` — filtra `data[]` por `action.tag`, muestra mini-cards disponibles
- `lvl_result` — filtra `data[]` por `action.lvl`, muestra mini-cards disponibles
- `card_result` — muestra una card específica por `action.card`

---

## Estructura de un nodo

```javascript
nombreNodo: {
    m: ["Mensaje 1", "Mensaje 2"],   // array de mensajes secuenciales
    o: [
        ["Texto opción", "nextNode"],              // sin acción
        ["Texto opción", "nextNode", {tag:"rojo"}], // con acción de tag
        ["Texto opción", "lvl_result", {lvl: 2}],  // con acción de nivel
        ["Texto opción", "card_result", {card:"0.1"}], // card específica
        ["Texto opción", null]                      // terminal (cierra)
    ],
    special: "surprise"  // opcional — lógica especial
}
```

---

## Formato de action

```javascript
{tag: "verde"}   // filtra por tag del sistema de colores
{tag: "rojo"}
{tag: "amar"}
{tag: "azul"}
{tag: "morad"}

{lvl: 0}         // filtra por nivel (0, 1, 2 o 3)
{lvl: 2}

{card: "0.1"}    // apunta a una card específica por ID
{card: "2.3"}
```

---

## Sistema de colores / tags

| Tag | Color | Significado |
|---|---|---|
| `rojo`  | #DC2626 | Mitos y realidad — anti-hype |
| `amar`  | #D97706 | Acción inmediata |
| `azul`  | #2563EB | Conceptos |
| `verde` | #059669 | Herramientas |
| `morad` | #7C3AED | Casos reales |

---

## CSS clave del widget

```css
/* Búho fijo derecha */
.owl-guide {
    position: fixed;
    right: 20px;
    top: 45%;
    transform: translateY(-50%);
    z-index: 300;
}

/* Panel de chat */
.chat-panel {
    position: fixed;
    right: 108px;       /* a la izquierda del búho */
    top: 45%;
    transform: translateY(-50%) scale(0.94);
    width: 290px;
    max-height: min(520px, 75vh);
    /* abre con clase .open → scale(1) + opacity: 1 */
}

/* Mobile: se va a esquina inferior derecha */
@media (max-width: 768px) {
    .owl-guide  { right: 12px; bottom: 20px; top: auto; }
    .chat-panel { right: 12px; bottom: 90px; top: auto; }
}
```

---

## Highlight de cards

```javascript
function scrollToCard(id) {
    const el = document.getElementById('card-' + id.replace('.','_'));
    document.querySelectorAll('.owl-highlight').forEach(c => c.classList.remove('owl-highlight'));
    el.classList.add('owl-highlight');
    el.scrollIntoView({ behavior: 'smooth', block: 'center' });
    setTimeout(() => el.classList.remove('owl-highlight'), 3000);
}

// CSS de la clase highlight:
// .card.owl-highlight → borde ámbar + glow + elevación
```

---

## Cómo añadir un nuevo nodo

1. Añadir el nodo al objeto `TREE` con su `m[]` y `o[]`
2. Conectarlo desde otro nodo como `nextNode` en una opción
3. Si necesita mostrar recursos: usar `tag_result`, `lvl_result` o `card_result` con la `action` correspondiente
4. Si es un flujo especial, añadir `special: "nombreEspecial"` y manejar el caso en `goTo()`

---

## Cómo añadir un nuevo recurso a data[]

```javascript
{
    id:   "4.1",        // nivel.número
    n:    4,            // nivel (int)
    tag:  "azul",       // rojo / amar / azul / verde / morad
    tl:   "Conceptos",  // label del tag
    ok:   false,        // true = disponible, false = próximamente
    title: "Título del recurso",
    desc:  "Descripción corta (1-2 líneas)",
    t:    "10 min",     // tiempo estimado
    href: "#"           // URL cuando esté disponible
}
```

---

## Ideas de iteración futura

- Conectar a Claude API para respuestas abiertas (coste: tokens por conversación)
- Memoria persistente de nivel del usuario (qué ha completado)
- Modo "quiz" — el búho pregunta qué sabes para auto-clasificarte
- Notificaciones: el búho avisa cuando hay recursos nuevos disponibles
- Animación de ojos del búho que siguen el cursor
- Variantes de personalidad según hora del día

---

## Paleta del búho (SVG)

| Elemento | Color |
|---|---|
| Cuerpo / cabeza | `#B45309` (ámbar) |
| Alas / orejas (oscuro) | `#92400E` (ámbar oscuro) |
| Ojos (blanco) | `#FEF3C7` |
| Pupilas | `#1C1917` |
| Pico / barriga | `#D97706` (ámbar medio) |
| Brillo del ojo | `#FFFFFF` |

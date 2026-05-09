# Maquetas — Historial de versiones

Cada versión queda guardada como archivo independiente. Si algo no convence, se itera sobre la versión anterior sin perder nada.

---

## v1 — Dark / Naranja (descartada)
**Archivo:** `v1_dark_naranja.html` *(no guardada por separado, contenida en git history)*
**Estado:** Descartada

**Características:**
- Fondo: `#0C0C0E` negro puro
- Acento: `#F97316` naranja
- Sin animaciones de fondo
- Cards simples con hover básico
- Filtros funcionales (nivel + tag)
- Buscador en tiempo real

**Por qué se descartó:** Color y estructura correctos, pero el tono visual era demasiado genérico. Sin personalidad de marca.

---

## v2 — Dark / Cyan Aurora (guardada)
**Archivo:** `v2_dark_aurora.html`
**Estado:** Guardada como referencia

**Características:**
- Fondo: `#02020A` negro con grid de puntos animado (estilo Vercel)
- Acento: `#22D3EE` cyan + `#8B5CF6` violeta
- Aurora CSS en header: dos orbes flotantes con animación suave
- Cards con glow cyan en hover + micro-elevación
- Entrada animada de cards escalonada
- Gradiente de texto en el título H1
- Stats row (18 recursos · 4 niveles · 100% gratuito)
- Footer con icono Instagram

**Por qué se descartó:** Visualmente sólida, pero demasiado futurista. El usuario busca sensación de biblioteca cómoda, no de producto tech. La oscuridad crea distancia.

---

## v3 — Light / Biblioteca cálida (versión activa)
**Archivo:** `index.html` *(versión activa)*
**Estado:** Producción

**Características:**
- Fondo: `#FAFAF7` — blanco cálido tipo papel
- Acento: `#B45309` ámbar (psicología: sabiduría, conocimiento, curiosidad)
- Tipografía: Playfair Display (H1 + títulos de sección) + Inter (body) + JetBrains Mono (brand/IDs)
- Línea de acento ámbar fija en el top de la página
- Cards blancas con sombra cálida sutil + borde ámbar en hover
- Sin aurora ni dot grid — enfoque en legibilidad
- Filtros por nivel y por tag (colores semánticos)
- Buscador en tiempo real
- prefers-reduced-motion aplicado
- Mobile-first: columna única en < 600px

**Por qué funciona:** El ámbar sobre blanco cálido evoca bibliotecas físicas, conocimiento curado y confianza. Sin los efectos tech de v2, el contenido es el protagonista.

---

## Criterios de evaluación para cada versión

| Criterio | Peso |
|---|---|
| Primera impresión en móvil (viene de Instagram) | Alto |
| Legibilidad y comodidad de lectura | Alto |
| Coherencia con la marca alex.decode | Medio |
| Velocidad de carga | Medio |
| Impresión de "biblioteca confiable" | Alto |
| Diferenciación visual respecto a Notion/genérico | Medio |

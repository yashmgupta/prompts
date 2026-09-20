I want you to generate flowcharts using a fixed, reusable HTML application.

There are TWO separate design layers:

1. **Flowchart design** — changes for every diagram.
2. **HTML application shell** — NEVER changes.

The HTML interface, layout, spacing, toolbar, buttons, preview area, dimensions, CSS, and export behavior must remain consistent every time.

Only the SVG flowchart inside the preview is allowed to change.

---

# WORKFLOW

## Stage 1 — Generate the visual reference

First create a polished reference image from the flowchart content I provide.

Use this visual language:

* white or transparent background
* black / dark charcoal lines
* white-filled boxes
* thin refined borders
* Garamond-style serif typography
* minimal academic / editorial appearance
* generous whitespace
* balanced spacing
* simple connectors
* no gradients
* no decorative graphics
* no unnecessary colors
* minimal wording

Improve my rough structure rather than copying it literally.

Automatically choose the appropriate diagram structure:

* sequential process → vertical flow
* alternatives → horizontal branches
* decisions → diamonds
* repetition → feedback loops
* related systems → grouped containers

Avoid crossed connectors whenever possible.

Shorten wording while preserving meaning.

---

# Stage 2 — Use the image as art direction

After generating the reference image, study it for:

* hierarchy
* node placement
* spacing
* proportions
* branching
* connector routing
* visual rhythm

Then recreate the diagram precisely as vector SVG.

The reference image controls the **diagram design only**.

It must NEVER change the HTML application's layout.

---

# Stage 3 — Build the editable SVG

Reconstruct the diagram using actual SVG elements.

Use:

* `<rect>` for process nodes
* `<polygon>` or `<path>` for decisions
* `<path>` for connectors
* SVG markers for arrowheads
* `<text>` and `<tspan>` for labels
* `<g>` for logical groups

Never:

* embed the generated image
* use rasterized text
* place a screenshot inside SVG
* use canvas as the diagram
* convert the diagram into one giant path

All important elements must remain individually editable.

Use semantic IDs such as:

`node-research-question`

`shape-research-question`

`label-research-question`

`decision-results`

`connector-search-to-decision`

`connector-feedback-loop`

---

# FIXED SVG CANVAS

Every diagram must use this exact outer SVG format:

```html
<svg id="research-workflow"
     xmlns="http://www.w3.org/2000/svg"
     width="1200"
     height="900"
     viewBox="0 0 1200 900"
     role="img"
     aria-labelledby="diagram-title diagram-desc">
```

The diagram must fit cleanly inside this 1200 × 900 coordinate system.

Do not change:

* SVG width
* SVG height
* viewBox
* responsive behavior

Instead, resize and reposition the diagram itself to fit the canvas.

Maintain generous margins around the diagram.

---

# FIXED SVG VISUAL SYSTEM

Use these styles consistently:

```css
.node-shape {
    fill: #ffffff;
    stroke: #242424;
    stroke-width: 1.7;
    vector-effect: non-scaling-stroke;
}

.connector {
    fill: none;
    stroke: #242424;
    stroke-width: 1.7;
    stroke-linecap: square;
    stroke-linejoin: miter;
    marker-end: url(#arrowhead);
    vector-effect: non-scaling-stroke;
}

.node-label {
    font-family: Garamond, "EB Garamond", Georgia, serif;
    font-size: 30px;
    font-weight: 400;
    fill: #111111;
    text-anchor: middle;
    dominant-baseline: middle;
}

.decision-label {
    font-family: Garamond, "EB Garamond", Georgia, serif;
    font-size: 25px;
    font-weight: 400;
    fill: #111111;
    text-anchor: middle;
}

.branch-label {
    font-family: Garamond, "EB Garamond", Georgia, serif;
    font-size: 23px;
    font-style: italic;
    fill: #242424;
    text-anchor: middle;
}
```

Use this arrowhead style:

```html
<marker id="arrowhead"
        viewBox="0 0 10 10"
        refX="9"
        refY="5"
        markerWidth="7"
        markerHeight="7"
        orient="auto-start-reverse">

    <path d="M 0 0 L 10 5 L 0 10 z"
          fill="#242424"/>

</marker>
```

Do not redesign these styles for different diagrams.

---

# FLOWCHART GEOMETRY RULES

Although the HTML application remains fixed, the internal chart may adapt.

For simple sequential diagrams:

* center nodes around x = 600
* use vertical flow
* maintain consistent vertical spacing

For branches:

* keep the main decision centered
* place left and right outcomes symmetrically
* use orthogonal connectors where appropriate

For feedback loops:

* route the feedback path around the outside of the diagram
* keep it away from labels and nodes
* return cleanly to the relevant earlier step

Prefer:

* rectangular nodes approximately 300–440 px wide
* approximately 75–100 px high
* subtle 5 px corner radius
* approximately 50–70 px vertical spacing
* symmetrical branch layouts

Do not make boxes excessively large just to fill the canvas.

Whitespace is intentional.

---

# LOCKED HTML APPLICATION

The HTML application itself must remain visually identical across every generated flowchart.

The page must always contain:

### Toolbar

At the top:

**Download Editable SVG**

**Download PNG**

The toolbar must remain:

* white
* centered
* horizontal
* approximately 18 px vertical padding
* separated from the page by a thin bottom border

Do not add:

* navigation
* titles
* sidebars
* cards
* settings panels
* extra controls
* explanatory headers

---

# LOCKED PAGE STYLE

Use exactly this general application layout:

```css
:root {
    color-scheme: light;
    font-family: Garamond, "EB Garamond", Georgia, serif;
    background: #f4f4f2;
    color: #111;
}

* {
    box-sizing: border-box;
}

body {
    margin: 0;
    min-height: 100vh;
    display: flex;
    flex-direction: column;
    background: #f4f4f2;
}

.toolbar {
    display: flex;
    gap: 12px;
    justify-content: center;
    flex-wrap: wrap;
    padding: 18px 20px;
    border-bottom: 1px solid #d7d7d2;
    background: #fff;
}

button {
    appearance: none;
    border: 1px solid #242424;
    background: #fff;
    color: #111;
    padding: 10px 16px;
    border-radius: 4px;
    font: 600 15px/1.2 Georgia, serif;
    cursor: pointer;
}

button:hover {
    background: #f6f6f3;
}

button:focus-visible {
    outline: 2px solid #111;
    outline-offset: 3px;
}

.preview-shell {
    flex: 1;
    display: grid;
    place-items: center;
    padding: 28px;
}

.preview {
    width: min(1200px, 100%);
    background: #fff;
    border: 1px solid #dfdfda;
    box-shadow: 0 10px 36px rgba(0,0,0,.06);
    overflow: auto;
}

.preview svg {
    display: block;
    width: 100%;
    height: auto;
    background: #fff;
}

.note {
    margin: 0;
    padding: 0 20px 18px;
    text-align: center;
    font: 14px/1.5 Georgia, serif;
    color: #555;
}
```

This styling is LOCKED.

Do not reinterpret it.

Do not improve it.

Do not modernize it.

Do not create a new interface based on the reference image.

---

# FIXED HTML STRUCTURE

Always preserve this structure:

```html
<body>

<div class="toolbar" aria-label="Export controls">
    <button id="download-svg" type="button">
        Download Editable SVG
    </button>

    <button id="download-png" type="button">
        Download PNG
    </button>
</div>

<main class="preview-shell">

    <div class="preview" id="preview">

        <!-- ONLY THIS SVG CONTENT CHANGES -->
        <svg id="research-workflow">
            ...
        </svg>

    </div>

</main>

<p class="note">
    The SVG is composed of editable vector shapes,
    text, groups, and connectors.
</p>

</body>
```

The only major variable section is:

```html
<svg id="research-workflow">
    ...
</svg>
```

Everything outside this SVG should remain effectively identical between diagrams.

---

# EXPORT BEHAVIOR IS LOCKED

Preserve the same SVG export logic.

The **Download Editable SVG** button must:

1. clone the current SVG
2. serialize it using `XMLSerializer`
3. create an SVG Blob
4. download the real editable SVG

Preserve the same PNG export method.

The **Download PNG** button must:

1. serialize the current SVG
2. render it into a canvas
3. use a white background
4. export at 4× resolution
5. download the PNG

Use:

```javascript
const scale = 4;
const width = 1200;
const height = 900;
```

Do not replace this system with screenshots or external libraries.

---

# WHAT MAY CHANGE

For every new flowchart, you MAY change:

* SVG `<title>`
* SVG `<desc>`
* SVG nodes
* labels
* decisions
* connectors
* branch labels
* node positions
* connector paths
* semantic IDs
* diagram-specific export filename if needed

---

# WHAT MUST NOT CHANGE

Do NOT change:

* overall HTML application design
* page background
* toolbar location
* toolbar appearance
* button styling
* button order
* preview alignment
* preview width
* preview border
* preview shadow
* page spacing
* note placement
* SVG dimensions
* SVG viewBox
* export architecture
* PNG scale
* overall typography system

Think of the HTML as a **locked application template**.

You are only replacing the diagram inside it.

---

# CONTINUATION BEHAVIOR

After generating the reference image:

If the system allows automatic continuation:

**Immediately create the editable SVG and HTML.**

Do not stop after generating the image.

Do not wait for me to ask:

“Where is the HTML?”

The task is unfinished until the HTML and SVG files actually exist.

If the system requires another user message before continuing, ask only:

**“Reference image ready. Continue to editable HTML + SVG?”**

Then I should only need to reply:

**Yes**

or

**Continue**

Do not ask for my diagram content again.

---

# FILE VALIDATION

Before giving me download links:

1. create the HTML file
2. create the standalone SVG
3. verify both files actually exist
4. only then provide download links

Never claim a file exists unless its creation succeeded.

If the filename is blocked or already exists:

* automatically use another valid filename
* retry
* verify again

---

# FINAL OUTPUT

For every flowchart request, deliver:

1. reference flowchart image
2. standalone `.html`
3. standalone editable `.svg`
4. working PNG export inside the HTML

The HTML application should look the same every time.

Only the flowchart itself should change.

---

# INITIAL BEHAVIOR

When I paste this master prompt by itself, ask only:

**“Send me the flowchart content, rough notes, ASCII diagram, or process you want visualized.”**

After I provide the content, execute the complete workflow automatically.

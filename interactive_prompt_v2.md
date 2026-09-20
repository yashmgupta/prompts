# INTERACTIVE FLOWCHART GENERATOR — MASTER SYSTEM PROMPT

I want you to generate polished interactive flowchart applications using a fixed, reusable HTML/CSS/JavaScript architecture.

The workflow has THREE distinct layers:

1. **Reference Image** — generated first to establish visual composition.
2. **Diagram State** — changes for each flowchart.
3. **HTML/CSS/JS Application Shell** — remains structurally and visually consistent across every flowchart.

The application itself must NOT be redesigned for each request.

Only the diagram data and diagram-specific layout should change.

---

# CORE PRINCIPLE

Do NOT build each flowchart by manually hardcoding SVG nodes and connectors.

The flowchart must be represented as JavaScript state.

The SVG is only a visual rendering of that state.

The architecture must follow:

```text
User Content
     ↓
Reference Image
     ↓
Diagram Interpretation
     ↓
Nodes + Edges State
     ↓
Central render()
     ↓
Interactive SVG
```

The state is the source of truth.

The SVG is the rendered view.

---

# STAGE 1 — GENERATE THE VISUAL REFERENCE

First, generate a polished flowchart image based on the content I provide.

Use this visual language:

* white background
* dark charcoal / black lines
* white-filled nodes
* thin refined borders
* Garamond-style serif typography
* minimal academic/editorial aesthetic
* balanced composition
* generous whitespace
* clear hierarchy
* simple connectors
* no gradients
* no unnecessary colors
* no decorative graphics
* minimal wording

Do not simply copy my rough ASCII layout.

Interpret it.

Improve:

* spacing
* hierarchy
* branch placement
* connector routing
* proportions
* wording
* balance

Automatically select the appropriate structure:

* sequential process → vertical flow
* alternatives → branching
* decision → diamond
* iteration → feedback loop
* related systems → grouped structures

Shorten wording when useful without changing the original meaning.

---

# STAGE 2 — LEARN FROM THE REFERENCE IMAGE

After generating the reference image, use it as the art direction for the interactive diagram.

Study:

* overall composition
* hierarchy
* relative node placement
* branch symmetry
* whitespace
* connector routing
* node proportions
* text hierarchy

The image is a DESIGN REFERENCE.

It is NOT the final flowchart.

Do not embed the generated image into the HTML.

Do not trace it as a raster image.

Instead, convert its structure into JavaScript state.

Correct any geometric or alignment mistakes from the generated image during reconstruction.

---

# STAGE 3 — CREATE THE DIAGRAM STATE

Every flowchart must be represented by JavaScript arrays.

Use a structure similar to:

```javascript
let nodes = [
    {
        id: "research-question",
        x: 390,
        y: 60,
        width: 420,
        height: 88,
        text: "Research Question",
        type: "process",
        rotation: 0
    }
];

let edges = [
    {
        id: "edge-question-brainstorm",
        from: "research-question",
        to: "ai-brainstorming",
        type: "direct"
    }
];
```

The initial state changes for each flowchart.

The rendering engine does not.

---

# REQUIRED NODE MODEL

Each node should support:

```javascript
{
    id,
    x,
    y,
    width,
    height,
    text,
    type,
    rotation
}
```

Supported node types should include at minimum:

```text
process
decision
start
end
group
```

Additional types may be added if necessary.

---

# REQUIRED EDGE MODEL

Each edge should support:

```javascript
{
    id,
    from,
    to,
    type,
    label
}
```

Where useful, `type` may describe:

```text
direct
branch
feedback
```

Do not encode connector geometry permanently into the state unless necessary.

Connector paths should normally be calculated from the current node positions.

---

# CENTRALIZED RENDERING ARCHITECTURE

There must be ONE centralized:

```javascript
render()
```

function.

It must:

1. clear the rendered node layer
2. clear the rendered edge layer
3. calculate connector geometry
4. render all edges
5. render all nodes
6. attach the required interaction handlers

Conceptually:

```javascript
function render() {
    renderEdges();
    renderNodes();
}
```

Do not manually maintain individual SVG elements after state changes.

Whenever state changes:

```javascript
render();
```

should update the diagram.

---

# SVG LAYER ARCHITECTURE

Use this fixed SVG structure:

```html
<svg id="canvas"
     xmlns="http://www.w3.org/2000/svg"
     width="1200"
     height="900"
     viewBox="0 0 1200 900">

    <defs>
        <marker id="arrowhead"
                viewBox="0 0 10 10"
                refX="9"
                refY="5"
                markerWidth="7"
                markerHeight="7"
                orient="auto">

            <path d="M 0 0 L 10 5 L 0 10 z"
                  fill="#242424"/>
        </marker>
    </defs>

    <g id="edges-layer"></g>
    <g id="nodes-layer"></g>

</svg>
```

Never hardcode normal diagram nodes inside these layers in the original HTML.

JavaScript must populate them from state.

---

# FIXED SVG CANVAS

Always use:

```text
width: 1200
height: 900
viewBox: 0 0 1200 900
```

Do not change the canvas dimensions between flowcharts.

Instead, calculate positions and sizes so each diagram fits comfortably within the fixed canvas.

Maintain generous outer margins.

---

# FIXED VISUAL SYSTEM

Use this visual system consistently.

## Page

```text
Background: #f4f4f2
```

## Nodes

```text
Fill: #ffffff
Stroke: #242424
Stroke width: 1.7px
Corner radius: 5px
```

## Connectors

```text
Stroke: #242424
Stroke width: 1.7px
Line cap: square
Line join: miter
```

## Typography

```text
Garamond,
"EB Garamond",
Georgia,
serif
```

Primary node text should generally use approximately:

```text
30px
weight 400
```

Decision text may use approximately:

```text
25px
```

Branch labels may use approximately:

```text
23px
italic
```

Maintain strong whitespace and restrained visual density.

---

# INTERACTIVE REQUIREMENTS

The generated application MUST support all of the following.

---

## 1. DRAG NODES

Every node must be moveable.

Users should be able to:

```text
click
drag
release
```

a node anywhere on the SVG canvas.

Use pointer or mouse events.

Recommended architecture:

```javascript
pointerdown
pointermove
pointerup
```

or equivalent mouse events.

When dragging:

1. identify the active node
2. calculate pointer offset
3. update the node's `x` and `y`
4. call `render()`

Connectors must automatically follow the moved nodes.

Dragging must modify state, not only SVG attributes.

---

# 2. INLINE TEXT EDITING

Users must be able to double-click node text and edit it.

Use:

```html
<foreignObject>
    <div contenteditable="true"></div>
</foreignObject>
```

inside the SVG node.

When editing finishes:

```text
blur
Enter where appropriate
```

save the text back into:

```javascript
node.text
```

Then call:

```javascript
render();
```

Do not use:

```javascript
prompt()
alert()
```

for editing.

---

# 3. ADD NODE

The toolbar must contain:

**Add Node**

Clicking it should:

1. create a new node object
2. assign a unique ID
3. position it somewhere sensible within the canvas
4. push it into `nodes`
5. call `render()`

Example:

```javascript
nodes.push({
    id: generateId(),
    x: 500,
    y: 400,
    width: 300,
    height: 88,
    text: "New Node",
    type: "process",
    rotation: 0
});
```

---

# 4. TOGGLE ORIENTATION

The toolbar must contain:

**Toggle Orientation**

The diagram must support:

```text
Vertical Flow
```

and:

```text
Horizontal Flow
```

Maintain a state variable such as:

```javascript
let orientation = "vertical";
```

Create:

```javascript
recalculateLayout(direction)
```

This function must calculate new node positions mathematically.

Do not simply rotate the SVG.

Vertical mode should arrange the logical flow:

```text
top → bottom
```

Horizontal mode should arrange it:

```text
left → right
```

Then call:

```javascript
render();
```

---

# 5. AUTOMATIC INITIAL LAYOUT

When interpreting the user's flowchart, calculate good initial node positions automatically.

Do not require the user to specify coordinates.

Use the semantic graph structure.

Account for:

* graph depth
* branches
* decisions
* siblings
* feedback loops
* node dimensions

Keep parallel branches visually balanced.

Prefer symmetrical placement where practical.

---

# 6. DYNAMIC CONNECTORS

Edges must respond automatically whenever nodes move.

Do not use fixed connector paths tied permanently to original coordinates.

Create a function such as:

```javascript
calculateEdgePath(edge)
```

It should inspect the current positions and dimensions of the source and target nodes.

For normal vertical flow:

```text
bottom center
      ↓
top center
```

For horizontal flow:

```text
right center
      →
left center
```

For branches and feedback loops, use orthogonal routing.

Feedback connectors should normally travel around the outside of the graph rather than through nodes.

Avoid connector crossings whenever practical.

---

# 7. DECISION NODES

Decision nodes must use a diamond shape.

Their position and dimensions still come from state.

Their rendering should be generated dynamically by `renderNode()`.

Example concept:

```javascript
if (node.type === "decision") {
    // render polygon/path
}
```

Do not create manually named decision SVG elements in the HTML template.

---

# FIXED APPLICATION SHELL

The HTML application must remain visually and structurally consistent for every flowchart.

Use this toolbar:

```html
<div class="toolbar" aria-label="Diagram controls">

    <button id="add-node" type="button">
        Add Node
    </button>

    <button id="toggle-layout" type="button">
        Toggle Orientation
    </button>

    <button id="download-svg" type="button">
        Download Editable SVG
    </button>

    <button id="download-png" type="button">
        Download PNG
    </button>

</div>
```

Button order should remain consistent.

Do not add:

* navigation
* sidebars
* hero sections
* cards
* unrelated settings panels
* decorative UI
* unnecessary instructions

The flowchart is the focus.

---

# FIXED PAGE LAYOUT

Use this application layout consistently:

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
```

This application styling is LOCKED.

Do not redesign it based on the reference image.

The reference image controls only the diagram.

---

# FIXED HTML ARCHITECTURE

Always follow this fundamental structure:

```html
<body>

<div class="toolbar" aria-label="Diagram controls">

    <button id="add-node" type="button">
        Add Node
    </button>

    <button id="toggle-layout" type="button">
        Toggle Orientation
    </button>

    <button id="download-svg" type="button">
        Download Editable SVG
    </button>

    <button id="download-png" type="button">
        Download PNG
    </button>

</div>

<main class="preview-shell">

    <div class="preview" id="preview">

        <svg id="canvas"
             xmlns="http://www.w3.org/2000/svg"
             width="1200"
             height="900"
             viewBox="0 0 1200 900">

            <defs>
                <!-- arrowhead definitions -->
            </defs>

            <g id="edges-layer"></g>
            <g id="nodes-layer"></g>

        </svg>

    </div>

</main>

<script>

    // ========================================
    // 1. DIAGRAM STATE
    // THIS SECTION CHANGES PER FLOWCHART
    // ========================================

    let nodes = [...];

    let edges = [...];

    let orientation = "vertical";


    // ========================================
    // 2. RENDERING ENGINE
    // KEEP ARCHITECTURE CONSISTENT
    // ========================================

    function render() {
        ...
    }


    // ========================================
    // 3. INTERACTION ENGINE
    // KEEP ARCHITECTURE CONSISTENT
    // ========================================

    function setupDragging() {
        ...
    }

    function startEditing() {
        ...
    }


    // ========================================
    // 4. LAYOUT ENGINE
    // KEEP ARCHITECTURE CONSISTENT
    // ========================================

    function recalculateLayout(direction) {
        ...
    }


    // ========================================
    // 5. EXPORT ENGINE
    // KEEP ARCHITECTURE CONSISTENT
    // ========================================

    function exportSVG() {
        ...
    }

    function exportPNG() {
        ...
    }


    render();

</script>

</body>
```

---

# IMMUTABILITY RULE

Think of the application shell as a reusable software product.

For each new flowchart:

### CHANGE:

```text
nodes
edges
diagram title
diagram description
initial node positions
diagram-specific text
diagram-specific topology
```

### DO NOT REDESIGN:

```text
toolbar
buttons
page layout
CSS theme
SVG canvas
rendering architecture
dragging architecture
editing architecture
orientation system
export system
general state schema
```

This distinction is extremely important.

---

# EXPORT EDITABLE SVG

The button:

**Download Editable SVG**

must export the CURRENT state of the diagram.

Before export:

```javascript
render();
```

Then clone the rendered SVG.

The exported SVG must contain:

* vector shapes
* connectors
* arrowheads
* readable text
* current node positions
* current edited labels

Do not export the entire HTML application.

Do not export a screenshot.

---

# PORTABLE SVG EXPORT IMPROVEMENT

The interactive browser version may use:

```html
foreignObject
```

for text editing.

However, for maximum compatibility with:

* Adobe Illustrator
* Figma
* Inkscape
* vector editors

the exported SVG should preferably convert editable HTML text inside `foreignObject` into standard SVG:

```html
<text>
<tspan>
```

elements in the cloned export.

The browser application may remain interactive with `foreignObject`.

The downloaded SVG should prioritize portability.

---

# PNG EXPORT

The button:

**Download PNG**

must export the current diagram at high resolution.

Use:

```javascript
const scale = 4;
const width = 1200;
const height = 900;
```

Render the current SVG to a temporary canvas ONLY for PNG export.

Canvas must NOT be used as the live diagram.

Fill the PNG background with:

```text
#ffffff
```

before rendering.

---

# EVENT HANDLING

Prefer event delegation where practical.

Avoid continuously creating duplicate global event listeners every time `render()` executes.

Global interaction handlers should normally be registered once.

Node-specific data should be identified through:

```text
data-node-id
```

or equivalent attributes.

This is important for stability after many render cycles.

---

# STATE SAFETY

State must remain valid after all interactions.

Node IDs must remain unique.

Edges must reference valid node IDs.

When creating a new node:

```text
generate a unique ID
```

Never reuse an existing ID accidentally.

---

# LAYOUT SAFETY

When recalculating layout:

* keep nodes inside the 1200 × 900 canvas
* maintain outer margins
* avoid overlapping nodes
* maintain consistent sibling spacing
* leave room for decision branches
* reserve space for feedback loops

If the graph cannot reasonably fit:

scale the internal layout or reduce spacing modestly.

Do NOT change the outer SVG canvas dimensions.

---

# REFERENCE IMAGE VS APPLICATION

Never confuse the two.

The generated reference image may visually vary based on the flowchart.

The HTML application shell may NOT.

The image determines:

```text
diagram arrangement
hierarchy
visual rhythm
```

The locked application determines:

```text
toolbar
canvas
controls
CSS
interaction behavior
export behavior
```

---

# AUTOMATIC CONTINUATION

The task is NOT complete when the reference image is generated.

If the environment allows continued execution:

1. generate reference image
2. interpret image
3. construct nodes and edges
4. generate complete interactive HTML
5. verify the HTML file exists
6. provide the HTML download
7. if useful, also provide a standalone exported SVG

Do NOT stop after the image.

Do NOT make me ask:

**“Where is the HTML?”**

---

# IF A NEW USER TURN IS REQUIRED

If the environment forces you to stop after image generation, ask only:

**“Reference image ready. Continue to interactive HTML?”**

I should only need to answer:

**Yes**

or:

**Continue**

Do not request the original content again.

Do not regenerate the image unnecessarily.

---

# FILE VALIDATION

Before providing a download link:

1. create the HTML file
2. verify that the file exists
3. verify it is non-empty
4. verify the required state arrays exist
5. verify `render()` exists
6. verify the toolbar controls exist
7. verify SVG export logic exists
8. verify drag logic exists

Only then provide the download link.

Never claim a file was created if creation failed.

If a filename/path fails:

* automatically choose another filename
* retry
* verify the new file

---

# REQUIRED FUNCTIONAL CHECK

Before delivering the HTML, verify logically or programmatically that:

* initial diagram renders
* nodes come from state
* edges come from state
* dragging modifies state
* connectors update after movement
* text editing modifies state
* Add Node modifies state
* Toggle Orientation recalculates positions
* SVG export reflects the current diagram
* PNG export reflects the current diagram

Fix obvious errors before delivery.

---

# NEVER DO THESE

Never:

* hardcode the complete final diagram directly into HTML SVG markup
* use one static set of SVG nodes as the source of truth
* embed the reference image
* use canvas for the live diagram
* use `alert()`
* use `prompt()`
* create a completely new UI design for each flowchart
* change the 1200 × 900 canvas
* remove interactivity
* stop after generating the image
* provide a fake or unverified download link

---

# FINAL DELIVERABLE

For every flowchart request, the final output should include:

1. the generated reference image
2. a fully functional standalone `.html` interactive flowchart application
3. an editable SVG export available from inside the HTML
4. a high-resolution PNG export available from inside the HTML

The HTML application should look and behave consistently every time.

Only the diagram's:

```text
nodes
edges
labels
topology
initial layout
```

should change.

---

# INITIAL BEHAVIOR

When I paste this master prompt by itself, do not start generating a diagram.

Ask only:

**“Send me the flowchart content, rough notes, ASCII diagram, or process you want visualized interactively.”**

After I provide the content, execute the complete workflow automatically.

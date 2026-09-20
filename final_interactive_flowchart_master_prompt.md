# FINAL MASTER PROMPT — INTERACTIVE FLOWCHART GENERATOR

Use this prompt as a reusable master instruction for generating consistent, editable, interactive flowchart applications.

---

## ROLE

Generate a complete interactive flowchart application from user-provided content.

The workflow has three layers:

1. **Reference Image** — generated first to establish visual composition.
2. **Diagram State** — changes for each flowchart.
3. **HTML/CSS/JavaScript Application Shell** — remains structurally and visually consistent across every flowchart.

The application itself must **not** be redesigned for each request. Only diagram data, topology, labels, directions, and initial layout may change.

---

# CORE PRINCIPLE

Do **not** build each flowchart by hardcoding final SVG nodes and connectors directly into the HTML.

The flowchart must be represented as JavaScript state. The SVG is only a rendered view of that state.

Architecture:

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

Every user interaction must update state first and then call `render()`.

---

# INITIAL BEHAVIOR

When I paste this master prompt by itself, do not generate anything yet.

Ask only:

> Send me the flowchart content, rough notes, ASCII diagram, or process you want visualized interactively.

After I provide the content, execute the complete workflow automatically. Do not ask unnecessary follow-up questions.

---

# STAGE 1 — GENERATE THE VISUAL REFERENCE

First generate a polished flowchart image based on the content I provide.

Use this visual language:

- white or transparent background
- black / dark charcoal lines
- white-filled nodes
- thin refined borders
- Garamond-style serif typography
- minimal academic / editorial aesthetic
- generous whitespace
- balanced composition
- clear hierarchy
- simple connectors
- no gradients
- no decorative graphics
- no unnecessary colors
- no clutter
- minimal wording

Do not simply reproduce the user's rough ASCII layout. Interpret and improve spacing, hierarchy, branch placement, connector routing, node proportions, wording, balance, and visual rhythm.

Automatically choose the most suitable structure:

- sequential process → vertical flow
- alternatives → branching
- decision → diamond
- iteration → feedback loop
- related systems → grouped structures

Shorten wording when useful without changing the original meaning.

---

# STAGE 2 — USE THE IMAGE AS ART DIRECTION

After generating the reference image, use it as the design reference for the interactive diagram.

Study:

- overall composition
- hierarchy
- relative node placement
- branch symmetry
- whitespace
- connector routing
- node proportions
- typography hierarchy

The image is **art direction only**. It is not the final flowchart.

Never:

- embed the reference image into the HTML
- place it inside the SVG
- trace it as a raster image
- use it as a screenshot background

Instead, convert its structure into JavaScript state. Correct any geometric or alignment mistakes during reconstruction.

---

# STAGE 3 — BUILD THE INTERACTIVE APPLICATION

Create one self-contained `.html` file using:

- HTML
- CSS
- Vanilla JavaScript
- SVG

Avoid external libraries unless absolutely necessary.

Do not use React, Vue, D3, Mermaid, Fabric, Konva, or similar frameworks.

The live diagram must use SVG/DOM, not canvas.

---

# DIAGRAM STATE

Use state similar to:

```javascript
let nodes = [];
let edges = [];

let orientation = "vertical";

let selectedNodeId = null;
let selectedEdgeId = null;

let connectionMode = false;
let connectionSourceId = null;

let reconnectMode = false;
let reconnectEdgeId = null;
```

Only the initial `nodes` and `edges` content changes for each flowchart. The application architecture stays consistent.

---

# NODE STATE MODEL

Each node must use:

```javascript
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
```

Required properties:

```text
id
x
y
width
height
text
type
rotation
```

Supported node types must include at minimum:

```text
process
decision
start
end
group
```

Additional types may be added only when needed.

---

# EDGE STATE MODEL

Each connection must live inside the `edges` array.

Use:

```javascript
{
    id: "edge-question-brainstorm",
    from: "research-question",
    to: "ai-brainstorming",
    type: "direct",
    direction: "forward",
    label: ""
}
```

Required properties:

```text
id
from
to
type
direction
label
```

Recommended edge types:

```text
direct
branch
feedback
```

Supported directions:

```text
forward
reverse
both
none
```

Meaning:

```text
forward  = A → B
reverse  = A ← B
both     = A ↔ B
none     = A — B
```

Arrow direction must be controlled by `edge.direction`, not inferred solely from node position.

---

# IMPORTANT STATE RULE

There must be no distinction between:

- original nodes
- newly created nodes
- original edges
- newly created edges

All of them must use the same state arrays and rendering system.

A newly created node must immediately support:

- text editing
- dragging
- incoming connections
- outgoing connections
- multiple connections
- selection
- deletion
- orientation changes
- SVG export
- PNG export

A newly created edge must immediately support:

- selection
- label editing
- direction changes
- reconnection
- deletion
- export

---

# FIXED SVG CANVAS

Always use:

```html
<svg id="canvas"
     xmlns="http://www.w3.org/2000/svg"
     width="1200"
     height="900"
     viewBox="0 0 1200 900"
     role="img"
     aria-labelledby="diagram-title diagram-desc">

    <title id="diagram-title">Flowchart</title>
    <desc id="diagram-desc">Interactive editable flowchart.</desc>

    <defs>
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
    </defs>

    <g id="edges-layer"></g>
    <g id="nodes-layer"></g>

</svg>
```

Always keep:

```text
width: 1200
height: 900
viewBox: 0 0 1200 900
```

Fit every diagram inside this fixed canvas.

---

# CENTRALIZED RENDERING ARCHITECTURE

There must be one centralized:

```javascript
render()
```

Use the architecture:

```javascript
function render() {
    renderEdges();
    renderNodes();
    renderSelection();
    updateToolbarState();
}
```

`render()` rebuilds the visible diagram from current state.

Never treat rendered SVG elements as the authoritative data source.

Correct architecture:

```text
User Action
    ↓
Update nodes / edges
    ↓
render()
    ↓
SVG Updates
```

---

# RENDER NODES DYNAMICALLY

Create nodes from state inside `renderNodes()`.

Do not hardcode actual diagram nodes in the original HTML.

Use JavaScript to create SVG elements.

For process nodes use `<rect>`.

For decision nodes use `<polygon>` or an SVG path.

Use logic similar to:

```javascript
if (node.type === "decision") {
    // render diamond
} else {
    // render rectangular or appropriate node
}
```

Each rendered node must include `data-node-id`.

---

# NODE TEXT EDITING

Every node, including newly created nodes, must be editable.

Preferred interaction:

```text
double-click node text
        ↓
edit text inline
        ↓
blur / finish editing
        ↓
save to node.text
        ↓
render()
```

Use SVG `foreignObject` with an editable HTML element for browser editing:

```html
<foreignObject>
    <div contenteditable="true"></div>
</foreignObject>
```

Do not use `alert()` or `prompt()`.

Save the final value back into `node.text`. The DOM must never be the only place the edited text exists.

---

# ADD NODE

The toolbar must include **Add Node**.

When clicked:

1. create a unique node ID
2. create a new node state object
3. add it to `nodes`
4. place it somewhere sensible
5. select it
6. render
7. immediately allow text editing

Example:

```javascript
function addNode() {
    const node = {
        id: generateId("node"),
        x: 500,
        y: 400,
        width: 300,
        height: 88,
        text: "New Node",
        type: "process",
        rotation: 0
    };

    nodes.push(node);
    selectedNodeId = node.id;

    render();
    startNodeEditing(node.id);
}
```

New nodes must behave exactly like original nodes.

---

# NODE DRAGGING

Every node must be draggable.

Prefer Pointer Events:

```text
pointerdown
pointermove
pointerup
```

Dragging must:

1. identify the active node
2. calculate pointer offset
3. update `node.x`
4. update `node.y`
5. call `render()`

Connectors must automatically update when connected nodes move.

Dragging must modify state, not only SVG attributes.

Register global drag listeners once. Do not create duplicate global listeners after every `render()`.

---

# NODE AND EDGE SELECTION

Maintain:

```javascript
let selectedNodeId = null;
let selectedEdgeId = null;
```

Behavior:

- click node → select node
- click edge → select edge
- click empty canvas → clear selection
- selecting one clears the other

Use only subtle selection styling.

---

# DYNAMIC CONNECTION CREATION

The toolbar must include **Add Connection**.

When clicked, enter connection creation mode.

Maintain:

```javascript
let connectionMode = false;
let connectionSourceId = null;
```

Interaction:

```text
Add Connection
      ↓
click source node
      ↓
click target node
      ↓
create edge state
      ↓
render()
```

Example:

```javascript
function createConnection(fromId, toId) {
    edges.push({
        id: generateId("edge"),
        from: fromId,
        to: toId,
        type: "direct",
        direction: "forward",
        label: ""
    });

    connectionMode = false;
    connectionSourceId = null;

    render();
}
```

Users must be able to connect any two nodes:

- original → original
- original → new
- new → original
- new → new
- process → decision
- decision → process
- any other valid node pair

Do not restrict connections based on when a node was created.

---

# MULTIPLE CONNECTIONS

A node may have:

- multiple incoming edges
- multiple outgoing edges
- both incoming and outgoing edges
- feedback edges
- multiple semantic relationships

Do not assume one parent or one child.

---

# EDGE SELECTION

Rendered edges must include `data-edge-id`.

Clicking an edge selects it.

Selection must allow the user to:

- change direction
- edit label
- reconnect
- delete

---

# CHANGE EDGE DIRECTION

Users must be able to control edge direction dynamically.

Support:

```text
forward
reverse
both
none
```

Recommended control: **Change Direction**.

Cycle through:

```text
forward
↓
reverse
↓
both
↓
none
↓
forward
```

Example:

```javascript
function cycleEdgeDirection(edge) {
    const directions = ["forward", "reverse", "both", "none"];
    const index = directions.indexOf(edge.direction);
    edge.direction = directions[(index + 1) % directions.length];
    render();
}
```

Apply SVG markers dynamically:

Forward:

```text
marker-end="url(#arrowhead)"
```

Reverse:

```text
marker-start="url(#arrowhead)"
```

Both:

```text
marker-start="url(#arrowhead)"
marker-end="url(#arrowhead)"
```

None: no marker-start and no marker-end.

---

# EDGE LABELS

Connections may have optional labels such as:

```text
Yes
No
Good
Bad
Approved
Rejected
Success
Failure
```

Store them in `edge.label`.

Users must be able to edit an edge label, preferably by double-clicking the visible label and editing inline.

Save the result back into `edge.label`, then call `render()`.

Do not use `prompt()`.

---

# RECONNECT AN EDGE

Users must be able to change the source and target of an existing edge.

Provide a lightweight **Reconnect** action.

Suggested flow:

```text
select edge
    ↓
Reconnect
    ↓
click new source
    ↓
click new target
    ↓
update edge.from and edge.to
    ↓
render()
```

Use state such as:

```javascript
let reconnectMode = false;
let reconnectEdgeId = null;
```

Do not create a duplicate edge unless the user explicitly creates a new connection.

---

# DELETE NODE

When a node is selected, allow deletion.

Deleting a node must:

1. remove it from `nodes`
2. remove every edge referencing that node
3. clear selection
4. call `render()`

Example:

```javascript
nodes = nodes.filter(node => node.id !== selectedNodeId);

edges = edges.filter(edge =>
    edge.from !== selectedNodeId &&
    edge.to !== selectedNodeId
);

selectedNodeId = null;
render();
```

---

# DELETE EDGE

When an edge is selected, allow deletion.

Remove it from `edges`, clear `selectedEdgeId`, then call `render()`.

---

# OPTIONAL KEYBOARD SUPPORT

Where practical:

```text
Delete / Backspace
```

deletes the selected node or edge.

```text
Escape
```

cancels:

- connection creation
- reconnection
- current selection
- temporary editing mode

Keyboard shortcuts must not interfere with text editing.

---

# DYNAMIC CONNECTOR ROUTING

Connector geometry must be calculated from current node positions.

Do not store absolute SVG path geometry as the primary relationship.

The authoritative relationship is:

```javascript
edge.from
edge.to
```

Create:

```javascript
calculateEdgePath(edge)
```

It must inspect:

- source node x/y
- source width/height
- target node x/y
- target width/height
- current orientation
- edge type

For normal vertical flow prefer:

```text
source bottom-center
        ↓
target top-center
```

For horizontal flow prefer:

```text
source right-center
        →
target left-center
```

For branches use orthogonal routing.

For feedback loops route around the outer perimeter of the diagram.

Avoid lines through nodes, lines through text, and unnecessary crossings.

Arrow direction and physical routing are separate. The user may reverse an arrow without moving either node.

---

# AUTOMATIC INITIAL LAYOUT

When interpreting the user flowchart, calculate good initial coordinates automatically.

Do not require coordinates from the user.

Use graph structure to estimate:

- depth
- branches
- siblings
- decisions
- loops
- node dimensions

Keep branches balanced and prefer symmetry where practical.

---

# TOGGLE ORIENTATION

The toolbar must include **Toggle Orientation**.

Support:

```text
Vertical Flow
Horizontal Flow
```

Maintain:

```javascript
let orientation = "vertical";
```

Create:

```javascript
recalculateLayout(direction)
```

This function must recalculate actual node coordinates.

Do not rotate the SVG.

Vertical layout:

```text
top → bottom
```

Horizontal layout:

```text
left → right
```

Then call `render()`.

---

# LAYOUT SAFETY

When recalculating layout:

- keep nodes inside the 1200 × 900 canvas
- avoid overlap
- maintain outer margins
- maintain sibling spacing
- reserve space for decision branches
- reserve space for feedback loops

If the graph is large, reduce internal spacing modestly or scale the internal layout mathematically.

Do not change the outer SVG canvas.

---

# FIXED VISUAL SYSTEM

Maintain this aesthetic consistently.

## Page background

```text
#f4f4f2
```

## Nodes

```text
fill: #ffffff
stroke: #242424
stroke-width: 1.7px
corner-radius: approximately 5px
```

## Connectors

```text
stroke: #242424
stroke-width: 1.7px
stroke-linecap: square
stroke-linejoin: miter
```

## Typography

```text
Garamond,
"EB Garamond",
Georgia,
serif
```

Typical node text:

```text
30px
weight 400
```

Decision text:

```text
25px
```

Branch labels:

```text
23px
italic
```

Keep the visual language minimal, editorial, academic, and uncluttered.

---

# LOCKED HTML APPLICATION SHELL

The application shell must remain visually and structurally consistent.

Use this toolbar:

```html
<div class="toolbar" aria-label="Diagram controls">

    <button id="add-node" type="button">Add Node</button>

    <button id="add-connection" type="button">Add Connection</button>

    <button id="toggle-layout" type="button">Toggle Orientation</button>

    <button id="change-direction" type="button">Change Direction</button>

    <button id="reconnect-edge" type="button">Reconnect</button>

    <button id="delete-selected" type="button">Delete</button>

    <button id="download-svg" type="button">Download Editable SVG</button>

    <button id="download-png" type="button">Download PNG</button>

</div>
```

Context-sensitive disabling or hiding is allowed.

Example:

- `Change Direction` only enabled when an edge is selected
- `Reconnect` only enabled when an edge is selected
- `Delete` enabled only when a node or edge is selected

Do not add unnecessary permanent UI.

---

# LOCKED PAGE CSS

Use this page structure and style consistently:

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

button:disabled {
    opacity: 0.45;
    cursor: default;
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

This styling is locked. Do not modernize or redesign it.

---

# FIXED HTML ARCHITECTURE

Always follow this structure:

```html
<body>

<div class="toolbar" aria-label="Diagram controls">
    ...
</div>

<main class="preview-shell">

    <div class="preview" id="preview">

        <svg id="canvas"
             xmlns="http://www.w3.org/2000/svg"
             width="1200"
             height="900"
             viewBox="0 0 1200 900">

            <defs>
                <!-- arrowhead -->
            </defs>

            <g id="edges-layer"></g>
            <g id="nodes-layer"></g>

        </svg>

    </div>

</main>

<script>

    // 1. DIAGRAM STATE — CHANGES PER FLOWCHART
    let nodes = [...];
    let edges = [...];

    let orientation = "vertical";
    let selectedNodeId = null;
    let selectedEdgeId = null;
    let connectionMode = false;
    let connectionSourceId = null;
    let reconnectMode = false;
    let reconnectEdgeId = null;

    // 2. RENDERING ENGINE — KEEP CONSISTENT
    function render() { ... }

    // 3. NODE INTERACTIONS — KEEP CONSISTENT
    function addNode() { ... }
    function startNodeEditing(nodeId) { ... }

    // 4. CONNECTION ENGINE — KEEP CONSISTENT
    function createConnection(fromId, toId) { ... }
    function cycleEdgeDirection(edgeId) { ... }
    function reconnectEdge(edgeId, newFrom, newTo) { ... }
    function calculateEdgePath(edge) { ... }

    // 5. LAYOUT ENGINE — KEEP CONSISTENT
    function recalculateLayout(direction) { ... }

    // 6. EXPORT ENGINE — KEEP CONSISTENT
    function exportSVG() { ... }
    function exportPNG() { ... }

    // 7. EVENTS — REGISTER ONCE
    function initializeEvents() { ... }

    initializeEvents();
    render();

</script>

</body>
```

---

# EVENT HANDLING SAFETY

Prefer event delegation where practical.

Register global event listeners only once.

Do not create duplicate global listeners every time `render()` executes.

Use:

```text
data-node-id
data-edge-id
```

to identify interaction targets.

---

# UNIQUE ID SAFETY

Create:

```javascript
generateId(prefix)
```

Node IDs must always be unique.

Edge IDs must always be unique.

Never reuse an existing ID accidentally.

Every edge must reference valid existing node IDs.

---

# EXPORT EDITABLE SVG

The **Download Editable SVG** button must export the current edited state.

Before export:

```javascript
render();
```

Then clone the rendered SVG.

The exported SVG must contain:

- vector node shapes
- connector paths
- arrowheads
- current node positions
- current node text
- current edge labels
- current edge directions
- newly added nodes
- newly added edges

Do not export the whole HTML page.

Do not export a screenshot.

---

# PORTABLE SVG EXPORT

The live browser application may use `<foreignObject>` for inline text editing.

For maximum compatibility with Adobe Illustrator, Figma, Inkscape, and other vector editors, the exported SVG should preferably convert editable `foreignObject` text into standard SVG `<text>` / `<tspan>` elements in the cloned export.

The interactive HTML may retain `foreignObject`.

The downloaded SVG should prioritize portability.

---

# PNG EXPORT

The **Download PNG** button must export the current edited diagram.

Use:

```javascript
const scale = 4;
const width = 1200;
const height = 900;
```

A temporary canvas may be used only for PNG export.

Canvas must never be used for the live diagram.

Fill the PNG background with `#ffffff` before rendering.

---

# REFERENCE IMAGE VS APPLICATION

Never confuse the two.

The reference image may visually vary according to flowchart content.

The application shell may not.

The reference image determines:

```text
diagram arrangement
hierarchy
visual rhythm
branch placement
```

The locked application determines:

```text
toolbar
page layout
canvas
controls
CSS
interaction behavior
state architecture
rendering engine
export behavior
```

---

# IMMUTABILITY RULE

Think of the HTML shell as a reusable software product.

## MAY CHANGE

```text
nodes
edges
diagram title
diagram description
node labels
edge labels
node types
edge types
edge directions
initial coordinates
topology
initial visual arrangement
```

## MUST NOT BE REDESIGNED

```text
toolbar layout
page background
button styling
preview styling
SVG canvas dimensions
state architecture
render() architecture
dragging architecture
inline editing architecture
connection creation architecture
orientation system
export system
general CSS theme
```

---

# AUTOMATIC CONTINUATION

The task is not complete when the reference image is generated.

If the environment allows automatic continuation:

1. generate the reference image
2. inspect the reference
3. interpret the flowchart
4. create `nodes`
5. create `edges`
6. create the complete interactive HTML
7. verify the HTML exists
8. verify it is functional
9. provide the HTML download
10. if useful, also provide a standalone SVG

Do not stop after generating the image.

Do not make me ask:

> Where is the HTML?

---

# IF THE ENVIRONMENT REQUIRES ANOTHER USER TURN

If the environment forces a stop after image generation, ask only:

> Reference image ready. Continue to interactive HTML?

I should only need to reply:

> Yes

or:

> Continue

Do not ask for the original flowchart content again.

Do not regenerate the reference image unnecessarily.

---

# FILE VALIDATION

Before providing any download link:

1. create the HTML file
2. verify the file exists
3. verify the file is non-empty
4. verify `nodes` exists
5. verify `edges` exists
6. verify `render()` exists
7. verify node drag logic exists
8. verify inline node text editing exists
9. verify Add Node exists
10. verify Add Connection exists
11. verify edge selection exists
12. verify direction changing exists
13. verify reconnection exists
14. verify Delete exists
15. verify Toggle Orientation exists
16. verify SVG export exists
17. verify PNG export exists

Never claim a file exists unless creation succeeded.

If a filename or path fails, automatically use another filename, retry, and verify again.

---

# REQUIRED FUNCTIONAL TEST

Before delivering the HTML, ensure this scenario is supported:

1. User opens the generated HTML.
2. Initial nodes and edges render from JavaScript state.
3. User clicks **Add Node**.
4. A new node appears.
5. The new node is selected.
6. User edits its text to `Verification`.
7. The edited text is stored in `node.text`.
8. User drags `Verification` elsewhere.
9. Its state coordinates update.
10. Connected edges update correctly.
11. User clicks **Add Connection**.
12. User selects `Search Papers` as source.
13. User selects `Verification` as target.
14. A new edge appears.
15. User selects the edge.
16. User changes it from `Search Papers → Verification` to `Search Papers ← Verification`.
17. User changes it again to `Search Papers ↔ Verification`.
18. User edits the edge label.
19. User reconnects the edge to another node.
20. User moves the new target node.
21. The connector updates automatically.
22. User toggles orientation.
23. Nodes are mathematically rearranged.
24. User exports SVG.
25. Exported SVG contains all current edits.
26. User exports PNG.
27. PNG reflects the current edited diagram.

If this scenario does not work, the application is not complete.

---

# NEVER DO THESE

Never:

- hardcode the complete final diagram as static SVG markup
- use static SVG nodes as the source of truth
- create new nodes only in the DOM without updating state
- create new edges only in the DOM without updating state
- embed the reference image
- use the reference image as a raster background
- use canvas for the live diagram
- use `alert()`
- use `prompt()`
- create a different UI design for each flowchart
- change the 1200 × 900 SVG canvas
- remove interactivity
- prevent newly created nodes from being edited
- prevent newly created nodes from connecting to other nodes
- restrict edge direction based on geometry
- duplicate global event listeners on every render
- stop after generating only the reference image
- provide an unverified download link

---

# FINAL DELIVERABLE

For every flowchart request, deliver:

1. the generated reference flowchart image
2. a fully functional standalone `.html` interactive flowchart application
3. editable SVG export from inside the application
4. high-resolution PNG export from inside the application

The application must remain consistent across requests.

Only these should change:

```text
nodes
edges
labels
topology
edge directions
initial layout
diagram-specific title and description
```

---

# FINAL QUALITY STANDARD

The generated HTML should function as a small standalone diagram editor, not merely a static preview.

After generation, the user should be able to modify the graph without asking the AI again.

The application must support:

```text
Create node
Edit node text
Move node
Delete node

Create connection
Select connection
Edit connection label
Change connection direction
Reverse connection
Create bidirectional connection
Remove arrowheads
Reconnect source and target
Delete connection

Toggle layout orientation

Export current editable SVG
Export current high-resolution PNG
```

The final result should remain visually minimal, editorial, academic, and consistent with the original flowchart reference style.

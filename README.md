# UXDrivenViewer
DUIR: Declarative UI Runtime. A powerful Web Component engine that decouples UI definition from execution, rendering complex JSON schemas into interactive interfaces via Hybrid Loading strategies.

# DUIR Engine (v1.0.3) ⚛️

**Declarative UI Runtime: Decoupling UI Definition from Execution.**

The **DUIR Engine** is a high-performance Web Component designed to interpret declarative JSON schemas into fully interactive, reactive user interfaces. It enforces strict separation between **Structure** (HTML), **Style** (CSS), **Logic** (Forms/Validation), and **Data** (Bindings).

## 🚀 Capabilities

* **Reactive Data Binding:** Updating the JSON configuration instantly reflects changes in the DOM (text, tables, or layout).
* **Deep State Patching:** Inject a data object to automatically populate complex, nested forms using a "Deep Search" algorithm.
* **Hybrid Execution:** Supports **Remote** (API-driven) and **Local** (JSON-driven) modes simultaneously.
* **Recursive Contexts:** Handles infinite nesting of Modals, maintaining isolated form groups and state.
* **Agnostic Integration:** Works with Angular, React, Vue, or Vanilla JS.

---

## 📦 Integration

```html
<link rel="stylesheet" href="path/to/v1.0.3/styles.css">
<script src="path/to/v1.0.3/ux-driven-viewer.js" type="module"></script>

```

---

## 🎛 API Interface

### Inputs (Properties)

| Property | Type | Description |
| --- | --- | --- |
| `project-id` | `UUID` | **Remote Mode:** Fetches schema from the API. |
| `branch` | `string` | Target environment branch (default: `main`). |
| `UxDrivenJson` | `Array` | **Local Mode:** Raw JSON schema. **Reactive:** Re-assigning this property updates the UI immediately. |
| `data` | `Object` | **Data Injection:** Object used to pre-fill forms. Matches keys to `controlName` recursively. |
| `modalProjectId` | `UUID` | Triggers a remote modal. |
| `modalJson` | `Array` | Triggers a local modal. |

### Outputs (Events)

| Event | Detail Payload | Description |
| --- | --- | --- |
| `actionClicked` | `{ action, data, context }` | User interactions (Buttons, Links). |
| `formSubmitted` | `Object` (Clean JSON) | Emitted only when form is valid. |
| `triggerAction` | `{ action, formValue, isValid }` | Signal from persistent modals. |
| `componentError` | `string` | Engine error reporting. |

---

## 🧬 Schema Architecture

The schema allows independent manipulation of visual elements, logic, and data.

### 1. Visual Data Binding (`dataBindings`)

**Purpose:** Reactive one-way binding for text and attributes. Changing the `value` in the JSON updates the DOM without re-rendering the whole component.

```json
"dataBindings": [
  { "selector": "#user-name-display", "value": "John Doe" },
  { "selector": "#status-badge", "value": "Active" }
]

```

### 2. Logic Mapping (`formMappings`)

**Purpose:** Binds HTML inputs to the internal Reactive Forms Engine.

| Property | Description |
| --- | --- |
| `controlName` | The key in the final data object (e.g., `email`). |
| `domSelector` | ID of the input to bind. |
| `errorDisplaySelector` | ID of the element to show validation errors. |
| `validatorConfig` | Array of sync validators. |
| `asyncValidator` | Configuration for server-side validation. |

#### Supported Validators

| Type | Config Example | Behavior |
| --- | --- | --- |
| `required` | `{ "type": "required" }` | Non-empty check. |
| `requiredtrue` | `{ "type": "requiredtrue" }` | Boolean check (Checkboxes). |
| `email` | `{ "type": "email" }` | Regex email validation. |
| `minlength` | `{ "type": "minlength", "value": 3 }` | String length floor. |
| `maxlength` | `{ "type": "maxlength", "value": 10 }` | String length ceiling. |
| `min` | `{ "type": "min", "value": 18 }` | Numeric floor. |
| `max` | `{ "type": "max", "value": 100 }` | Numeric ceiling. |
| `pattern` | `{ "type": "pattern", "value": "..." }` | Custom Regex. |

### 3. Grid System (`tableBindings`)

**Purpose:** Renders dynamic data grids with action hooks.

```json
"tableBindings": [
  {
    "tableSelector": "#grid-container",
    "columns": [ { "key": "id", "header": "ID" }, { "key": "name", "header": "Name" } ],
    "data": [ { "id": 1, "name": "Item A" } ],
    "actions": [ { "label": "Delete", "action": "delete-item", "cssClass": "btn-danger" } ]
  }
]

```

### 4. State Control (`buttonConfigs`)

**Purpose:** Automates UI state based on form validity.

* `formIsInvalid`: Disabled while invalid.
* `formIsPristine`: Disabled until user interaction.
* `formIsInvalidOrPristine`: Strict creation mode.

---

## 🎮 Data Manipulation & Reactivity

The DUIR Engine allows for complete separation of schema and data.

### Scenario A: Injecting Data into Forms

You can separate the **Schema** (Structure) from the **Data** (Content).

```javascript
const engine = document.querySelector('ux-driven-viewer');

// 1. Load Structure
engine.UxDrivenJson = [ ... form definition ... ];

// 2. Inject Data (Engine performs Deep Search Patching)
engine.data = {
    firstName: "Carlos",
    address: {
        city: "Mexico City" // Handles nested patching automatically
    }
};

```

### Scenario B: Reactive UI Updates

To update specific text on the screen (e.g., a counter or status) without touching the form:

```javascript
// Update the schema binding value
const newSchema = [...currentSchema];
newSchema[0].dataBindings[0].value = "Updated Status: Active";

// Re-assign to trigger reactive update
engine.UxDrivenJson = newSchema;

```

---

## 💡 Full Implementation Example

```json
[
  {
    "id_DocumentHTMLCSS": "user-panel",
    "renderType": "dynamic",
    "htmlComponent": "<div class='panel'><h2 id='title-bind'></h2><form><input id='email' class='input'><div id='email-err'></div><button id='save' type='submit' data-dynamic-action='save'>Save</button></form></div>",
    "cssComponent": ".panel { padding: 1rem; } .input { width: 100%; }",
    
    "dataBindings": [
      { "selector": "#title-bind", "value": "Edit Profile" }
    ],

    "formMappings": [
      {
        "controlName": "email",
        "domSelector": "#email",
        "errorDisplaySelector": "#email-err",
        "validatorConfig": [
          { "type": "required", "message": "Email is mandatory" },
          { "type": "email", "message": "Bad format" }
        ]
      }
    ],

    "buttonConfigs": [
      { "selector": "#save", "disableWhen": "formIsInvalid" }
    ]
  }
]

```

---

**DUIR Engine** © 2026. All rights reserved.

````md
# UXDrivenViewer  
### Declarative UI Runtime (DUIR)

A runtime-based UI execution engine that executes **declarative JSON schemas** as fully interactive, reactive user interfaces.

> **“The schema is not a description. It is an executable UI contract.”**

DUIR allows applications to render, validate, and control UI behavior dynamically at runtime, without recompiling or redeploying frontend code.

---

## 🧠 Conceptual Model

DUIR enforces a strict separation of concerns:

| Layer | Responsibility |
|---|---|
| Structure | HTML layout and DOM nodes |
| Style | CSS rules scoped to the view |
| Logic | Form rules, validation, conditions |
| Data | Bindings, values, UI state |

Each UI is described as a schema and executed by the runtime.

---

## 📦 Basic Usage

```html
<script src="ux-driven-viewer.js" type="module"></script>

<ux-driven-viewer-widget
  apiUrl="https://api.example.com"
  initialData='{ "user": "admin" }'>
</ux-driven-viewer-widget>
````

Schemas can be loaded remotely or assigned locally at runtime.

---

## 🧬 Schema Overview

A schema is an array of execution blocks.

```json
[
  {
    "id_DocumentHTMLCSS": "example-view",
    "renderType": "dynamic",
    "htmlComponent": "...",
    "cssComponent": "...",
    "dataBindings": [],
    "formMappings": [],
    "tableBindings": [],
    "buttonConfigs": []
  }
]
```

---

## 🔹 Core Properties

### `id_DocumentHTMLCSS`

Unique identifier for the execution context.

```json
"id_DocumentHTMLCSS": "enterprise-complex-demo"
```

---

### `renderType`

Defines how the block is executed.

```json
"renderType": "dynamic"
```

---

### `htmlComponent`

Raw HTML markup rendered as-is.

```json
"htmlComponent": "<div class='container'>...</div>"
```

---

### `cssComponent`

CSS styles scoped to this content block.

```json
"cssComponent": ".card { border-radius: 15px; }"
```

---

## 🔗 Data Bindings

Reactive bindings between schema data and DOM elements.

```json
"dataBindings": [
  {
    "selector": "#header-title",
    "value": "Smart Enterprise Enrollment"
  }
]
```

> Data bindings are resolved at runtime and may internally react to state updates, form values, or execution context changes.

---

## 📝 Form Mappings

Declaratively binds DOM inputs to a logical form model.

```json
{
  "controlName": "email",
  "domSelector": "#email",
  "errorDisplaySelector": "#err-email",
  "validatorConfig": [
    { "type": "required", "message": "Email is required." },
    { "type": "email", "message": "Invalid email format." }
  ]
}
```

### Conditional Visibility

```json
{
  "controlName": "taxId",
  "domSelector": "#tax-id",
  "showIf": "accountType === 'business'"
}
```

---

## ✅ Supported Validators

### Required

```json
{ "type": "required", "message": "This field is required." }
```

### Required (Boolean / Checkbox)

```json
{ "type": "requiredtrue", "message": "You must accept this option." }
```

### Email

```json
{ "type": "email", "message": "Invalid email format." }
```

### Minimum Length

```json
{ "type": "minlength", "value": 3, "message": "Minimum length is 3 characters." }
```

### Maximum Length

```json
{ "type": "maxlength", "value": 20, "message": "Maximum length exceeded." }
```

### Minimum Value

```json
{ "type": "min", "value": 18, "message": "Minimum allowed value is 18." }
```

### Maximum Value

```json
{ "type": "max", "value": 100, "message": "Maximum allowed value is 100." }
```

### Pattern (Regex)

```json
{ "type": "pattern", "value": "^[A-Z0-9_-]{5,20}$", "message": "Invalid format." }
```

### Asynchronous Validator

```json
{
  "type": "async",
  "endpoint": "/validate-email",
  "method": "POST",
  "errorKey": "alreadyExists",
  "message": "This value is already registered."
}
```

### Cross-Field Match

```json
{
  "type": "match",
  "control": "password",
  "message": "Values do not match."
}
```

### Custom Validator

```json
{
  "type": "custom",
  "key": "enterprisePolicyCheck",
  "message": "Validation rule not satisfied."
}
```

---

## 📊 Table Bindings

Declarative data grids with optional actions.

```json
{
  "tableSelector": "#stakeholders-grid",
  "columns": [
    { "key": "role", "header": "Role" },
    { "key": "name", "header": "Name" }
  ],
  "data": [
    { "role": "Project Manager", "name": "Brando Rivero" }
  ],
  "actions": [
    { "label": "Edit", "action": "EDIT_STAKEHOLDER" }
  ]
}
```

---

## 🔘 Button Requirements & State Rules

Any button intended to submit a form **MUST** include the following HTML attribute:

```html
data-dynamic-action="submit-form"
```

This attribute allows the runtime to intercept, validate, and execute the form submission flow.

### Button State Rules

```json
{
  "selector": "#btn-submit",
  "disableWhen": "formIsInvalid"
}
```

### Supported Conditions

* `formIsInvalid`
* `formIsPristine`
* `formIsInvalidOrPristine`

---

## 🧪 Full Example

```json
[{
  "id_DocumentHTMLCSS": "cv_interactive_brando_rivero",
  "renderType": "dynamic",
  "formId": "contact_brando_001",
  "htmlComponent": "<div class=\"cv-wrapper\">\n  <header class=\"cv-header\">\n    <div class=\"header-top\">\n      <h1>Brando Martin Rivero Perez</h1>\n      <span class=\"status-badge\">Disponible para Arquitectura UI</span>\n    </div>\n    <h2>Desarrollador Fullstack (Java/Angular) & Creador de UXDriven</h2>\n    <p>Especialista en Clean Code, Atomic Design, migración tecnológica y orquestación de Micro-Frontends.</p>\n  </header>\n\n  <section class=\"cv-experience\">\n    <h3>Experiencia Destacada</h3>\n    <div class=\"job-card\">\n      <h4>Full Stack Developer @ Minsait (Telcel)</h4>\n      <p>Migración de 5 aplicaciones frontend y reemplazo de monolitos por microservicios robustos en Java.</p>\n    </div>\n  </section>\n\n  <section class=\"cv-skills\">\n    <h3>Stack Principal</h3>\n    <div class=\"badges\">\n      <span class=\"badge angular\">Angular 16 (Avanzado)</span>\n      <span class=\"badge ts\">TypeScript 5.1</span>\n      <span class=\"badge java\">Java 1.8 / Spring Boot</span>\n      <span class=\"badge cloud\">Cloudflare Workers</span>\n    </div>\n  </section>\n\n  <section class=\"cv-contact\">\n    <h3>Iniciar Conversación Técnica</h3>\n    <form data-dynamic-form=\"contact_brando_001\">\n      <div class=\"form-group\">\n        <label>Nombre del Reclutador / Tech Lead</label>\n        <input id=\"guestName\" class=\"cv-input\" type=\"text\" placeholder=\"Ej. CTO de Banco\">\n        <div class=\"error-msg\" data-error-for=\"guestName\"></div>\n      </div>\n      <div class=\"form-group\">\n        <label>Correo Electrónico</label>\n        <input id=\"guestEmail\" class=\"cv-input\" type=\"email\" placeholder=\"correo@empresa.com\">\n        <div class=\"error-msg\" data-error-for=\"guestEmail\"></div>\n      </div>\n      <div class=\"form-group\">\n        <label>Propuesta de Proyecto</label>\n        <textarea id=\"guestMessage\" class=\"cv-input\" rows=\"3\" placeholder=\"¡Queremos implementar tu motor en nuestra arquitectura!\"></textarea>\n        <div class=\"error-msg\" data-error-for=\"guestMessage\"></div>\n      </div>\n      <button id=\"btn-hire\" data-dynamic-action=\"submit-form\" class=\"cv-btn\">\n        Enviar Propuesta a Brando\n      </button>\n    </form>\n  </section>\n</div>",
  "cssComponent": ".dynamic-cv_interactive_brando_rivero .cv-wrapper { font-family: system-ui, -apple-system, sans-serif; max-width: 650px; margin: 2rem auto; background: #1e1e2e; color: #cdd6f4; border-radius: 16px; padding: 32px; box-shadow: 0 20px 40px rgba(0,0,0,0.4); border: 1px solid #313244; }\n.dynamic-cv_interactive_brando_rivero .cv-header h1 { color: #89b4fa; margin: 0 0 8px 0; font-size: 1.8rem; }\n.dynamic-cv_interactive_brando_rivero .cv-header h2 { color: #f38ba8; font-size: 1.1rem; margin-top: 0; font-weight: 500; }\n.dynamic-cv_interactive_brando_rivero .cv-header p { color: #a6adc8; line-height: 1.5; font-size: 0.95rem; }\n.dynamic-cv_interactive_brando_rivero .status-badge { background: #a6e3a1; color: #11111b; padding: 4px 10px; border-radius: 12px; font-size: 0.75rem; font-weight: bold; display: inline-block; margin-bottom: 10px; }\n.dynamic-cv_interactive_brando_rivero h3 { border-bottom: 1px solid #45475a; padding-bottom: 8px; margin-top: 24px; color: #cba6f7; font-size: 1.2rem; }\n.dynamic-cv_interactive_brando_rivero .job-card { background: #181825; padding: 16px; border-radius: 8px; border-left: 4px solid #89b4fa; }\n.dynamic-cv_interactive_brando_rivero .job-card h4 { margin: 0 0 8px 0; color: #89b4fa; }\n.dynamic-cv_interactive_brando_rivero .job-card p { margin: 0; font-size: 0.9rem; color: #bac2de; }\n.dynamic-cv_interactive_brando_rivero .cv-skills .badges { display: flex; gap: 10px; flex-wrap: wrap; margin-top: 12px; }\n.dynamic-cv_interactive_brando_rivero .badge { padding: 6px 14px; border-radius: 20px; font-size: 0.85rem; font-weight: 600; }\n.dynamic-cv_interactive_brando_rivero .badge.angular { background: rgba(243, 139, 168, 0.15); color: #f38ba8; }\n.dynamic-cv_interactive_brando_rivero .badge.ts { background: rgba(137, 180, 250, 0.15); color: #89b4fa; }\n.dynamic-cv_interactive_brando_rivero .badge.java { background: rgba(250, 179, 135, 0.15); color: #fab387; }\n.dynamic-cv_interactive_brando_rivero .badge.cloud { background: rgba(166, 227, 161, 0.15); color: #a6e3a1; }\n.dynamic-cv_interactive_brando_rivero .form-group { margin-bottom: 18px; }\n.dynamic-cv_interactive_brando_rivero label { display: block; margin-bottom: 6px; font-size: 0.9rem; font-weight: 500; color: #bac2de; }\n.dynamic-cv_interactive_brando_rivero .cv-input { width: 100%; padding: 12px; border-radius: 8px; border: 1px solid #45475a; background: #11111b; color: #cdd6f4; font-family: inherit; transition: border-color 0.2s; box-sizing: border-box; }\n.dynamic-cv_interactive_brando_rivero .cv-input:focus { outline: none; border-color: #89b4fa; }\n.dynamic-cv_interactive_brando_rivero .error-msg { color: #f38ba8; font-size: 0.8rem; margin-top: 6px; min-height: 14px; }\n.dynamic-cv_interactive_brando_rivero .cv-btn { background: #89b4fa; color: #11111b; border: none; padding: 14px 24px; border-radius: 8px; cursor: pointer; font-weight: bold; width: 100%; font-size: 1rem; transition: background-color 0.3s; margin-top: 10px; }\n.dynamic-cv_interactive_brando_rivero .cv-btn:disabled { background: #45475a; color: #7f849c; cursor: not-allowed; }",
  "formMappings": [
    {
      "controlName": "guestName",
      "domSelector": "#guestName",
      "eventType": "input",
      "errorDisplaySelector": "[data-error-for='guestName']",
      "validatorConfig": [
        { "type": "required", "message": "Tu nombre es indispensable." },
        { "type": "minLength", "value": 3, "message": "El nombre debe tener al menos 3 caracteres." }
      ]
    },
    {
      "controlName": "guestEmail",
      "domSelector": "#guestEmail",
      "eventType": "input",
      "errorDisplaySelector": "[data-error-for='guestEmail']",
      "validatorConfig": [
        { "type": "required", "message": "Necesito tu correo para responderte." },
        { "type": "email", "message": "Ingresa un correo corporativo o válido." }
      ]
    },
    {
      "controlName": "guestMessage",
      "domSelector": "#guestMessage",
      "eventType": "input",
      "errorDisplaySelector": "[data-error-for='guestMessage']",
      "validatorConfig": [
        { "type": "required", "message": "No olvides dejarme un mensaje." },
        { "type": "minLength", "value": 15, "message": "Cuéntame un poco más del proyecto (mínimo 15 caracteres)." }
      ]
    }
  ],
  "buttonConfigs": [
    {
      "selector": "#btn-hire",
      "disableWhen": "formIsInvalid"
    }
  ]
}
]
```

---

## 📤 Runtime Events

| Event             | Description           |
| ----------------- | --------------------- |
| `formSubmit`      | Form submission event |
| `actionTriggered` | UI action triggered   |
| `ready`           | Runtime initialized   |
| `errorOccurred`   | Execution error       |

---

## ❌ What DUIR Is NOT

* Not a Low-Code platform
* Not Backend-Rendered UI
* Not a Template Engine
* Not a Form Builder

DUIR executes UI contracts as runtime applications.

---

## 📄 License

MIT License © 2026
**Brando Martin Rivero Perez**

```
```

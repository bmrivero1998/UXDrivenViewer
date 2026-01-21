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
    "contentId": "example-view",
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

### `contentId`

Unique identifier for the execution context.

```json
"contentId": "enterprise-complex-demo"
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
[
  {
    "contentId": "enterprise-complex-demo",
    "renderType": "dynamic",
    "htmlComponent": "<div class='container p-4'><div class='card shadow-lg border-0'><div class='card-header bg-primary text-white'><h2 id='header-title'></h2></div><div class='card-body'><div class='row'><div class='col-md-6 mb-3'><label class='form-label'>Full Name</label><input id='full-name' class='form-control' placeholder='Enter your name'><div id='err-name' class='small text-danger'></div></div><div class='col-md-6 mb-3'><label class='form-label'>Email Address</label><input id='email' class='form-control' placeholder='email@company.com'><div id='err-email' class='small text-danger'></div></div></div><div class='row'><div class='col-md-6 mb-3'><label class='form-label'>Account Type</label><select id='account-type' class='form-select'><option value='personal'>Personal Account</option><option value='business'>Business / Enterprise</option></select></div><div id='business-section' class='col-md-6 mb-3' style='display:none;'><label class='form-label'>Tax ID (RFC/VAT)</label><input id='tax-id' class='form-control' placeholder='XXXX-000000-XXX'><div id='err-tax' class='small text-danger'></div></div></div><div class='row'><div class='col-md-6 mb-3'><label class='form-label'>Contact Phone</label><input id='phone' class='form-control' placeholder='(000) 000-0000'><div id='err-phone' class='small text-danger'></div></div><div class='col-md-6 mb-3'><label class='form-label'>Project Budget (USD)</label><input id='budget' type='number' class='form-control'><div id='err-budget' class='small text-danger'></div></div></div><div class='mt-4 p-3 bg-light rounded'><h5 class='mb-3'>Related Stakeholders</h5><div id='stakeholders-grid'></div></div><div class='mt-4 d-flex justify-content-end'><button id='btn-reset' data-dynamic-action='RESET_FLOW' class='btn btn-outline-secondary me-2'>Clear Form</button><button id='btn-submit' data-dynamic-action='submit-form' class='btn btn-primary px-5'>Register Enterprise</button></div></div></div></div>",
    "cssComponent": ".card { border-radius: 15px; overflow: hidden; } .form-control:focus { border-color: #0d6efd; box-shadow: 0 0 0 0.25rem rgba(13, 110, 253, 0.25); } .is-invalid { border-color: #dc3545 !important; animation: shake 0.2s ease-in-out 0s 2; } @keyframes shake { 0% { margin-left: 0; } 25% { margin-left: 5px; } 50% { margin-left: 0; } 75% { margin-left: -5px; } 100% { margin-left: 0; } }",
    "dataBindings": [
      {
        "selector": "#header-title",
        "value": "Smart Enterprise Enrollment"
      }
    ],
    "formMappings": [
      {
        "controlName": "fullName",
        "domSelector": "#full-name",
        "errorDisplaySelector": "#err-name",
        "validatorConfig": [
          { "type": "required", "message": "The name is required to generate the certificate." },
          { "type": "minlength", "value": 10, "message": "Please enter your full legal name (min 10 chars)." }
        ]
      },
      {
        "controlName": "email",
        "domSelector": "#email",
        "errorDisplaySelector": "#err-email",
        "validatorConfig": [
          { "type": "required", "message": "A contact email is mandatory." },
          { "type": "email", "message": "The email format is invalid." }
        ]
      },
      {
        "controlName": "accountType",
        "domSelector": "#account-type",
        "defaultValue": "personal"
      },
      {
        "controlName": "taxId",
        "domSelector": "#tax-id",
        "errorDisplaySelector": "#err-tax",
        "showIf": "accountType === 'business'",
        "inputMask": "XXXX-999999-***"
      },
      {
        "controlName": "phone",
        "domSelector": "#phone",
        "errorDisplaySelector": "#err-phone",
        "inputMask": "(999) 999-9999",
        "validatorConfig": [
          { "type": "required", "message": "Phone number is required for verification." }
        ]
      },
      {
        "controlName": "budget",
        "domSelector": "#budget",
        "errorDisplaySelector": "#err-budget",
        "validatorConfig": [
          { "type": "min", "value": 1000, "message": "Minimum budget must be $1,000 USD." },
          { "type": "max", "value": 500000, "message": "Maximum self-service budget is $500,000 USD." }
        ]
      }
    ],
    "tableBindings": [
      {
        "tableSelector": "#stakeholders-grid",
        "columns": [
          { "key": "role", "header": "Role" },
          { "key": "name", "header": "Name" },
          { "key": "status", "header": "Status" }
        ],
        "data": [
          { "role": "Project Manager", "name": "Brando Rivero", "status": "Active" },
          { "role": "Technical Lead", "name": "AI System", "status": "Standby" }
        ],
        "actions": [
          { "label": "Edit", "action": "EDIT_STAKEHOLDER", "cssClass": "btn btn-sm btn-light" }
        ]
      }
    ],
    "buttonConfigs": [
      {
        "selector": "#btn-submit",
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

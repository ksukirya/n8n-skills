# n8n Workflow Builder Skill

Expert guidance for building flawless n8n workflows with correct node syntax, expression patterns, error handling, and webhook configuration.

## When to Use This Skill

Use this skill when:
- Building new n8n workflows
- Writing n8n expressions using `{{ }}` syntax
- Configuring webhook triggers
- Working with the Code node (JavaScript or Python)
- Setting up error handling in workflows
- Understanding n8n data structure

---

## Core Concepts

### Data Structure in n8n

All data passed between nodes is an **array of objects** with this structure:

```json
[
  {
    "json": {
      "field1": "value1",
      "field2": {
        "nested": "value"
      }
    },
    "binary": {
      "data": "...",
      "mimeType": "image/png",
      "fileName": "example.png"
    }
  }
]
```

**Critical Rules:**
- The `json` key must contain an **object**, not an array
- Binary data goes under the `binary` key
- Code node automatically wraps data in `json` key if missing (v0.166.0+)

---

## Expression Syntax

### Basic Expression Format

All expressions use double curly braces:
```
{{ expression }}
```

### Accessing Data

| Pattern | Description | Example |
|---------|-------------|---------|
| `$json` | Current item's JSON data | `{{ $json.email }}` |
| `$json.body` | Webhook body data | `{{ $json.body.name }}` |
| `$input.item` | Current input item | `{{ $input.item.json.field }}` |
| `$input.all()` | All input items | Used in Code node |
| `$input.first()` | First input item | `{{ $input.first().json.id }}` |
| `$input.last()` | Last input item | `{{ $input.last().json.id }}` |

### Accessing Other Nodes

```javascript
// Get all items from a node
$("Node Name").all()

// Get first/last item
$("Node Name").first()
$("Node Name").last()

// Get specific item (linked)
$("Node Name").item

// Check if node executed
$("Node Name").isExecuted
```

### Webhook Data Access

**CRITICAL:** Webhook data is nested under `$json.body`, not directly in `$json`:

```javascript
// Webhook receives: { "name": "John", "email": "john@example.com" }

// CORRECT - accessing webhook body:
{{ $json.body.name }}
{{ $json.body.email }}

// WRONG - this won't work:
{{ $json.name }}
```

Webhook structure:
```json
{
  "headers": { "host": "...", "content-type": "..." },
  "params": {},
  "query": { "param1": "value1" },
  "body": { "your": "data", "goes": "here" }
}
```

### Writing Complex JavaScript in Expressions

Use IIFE (Immediately Invoked Function Expression) for multi-line code:

```javascript
{{(()=>{
  let end = DateTime.fromISO('2024-03-13');
  let start = DateTime.fromISO('2024-02-13');
  let diff = end.diff(start, 'months');
  return diff.toObject();
})()}}
```

---

## Code Node

### JavaScript Mode

#### Run Once for All Items (Default)

```javascript
// Access all input items
const items = $input.all();

// Process and return
return items.map(item => ({
  json: {
    originalValue: item.json.value,
    processedValue: item.json.value * 2
  }
}));
```

#### Run Once for Each Item

```javascript
// $json is available directly
return [{
  json: {
    name: $json.name,
    processed: true
  }
}];
```

### Common Patterns

#### Accessing Previous Node Data

```javascript
// Get all items from specific node
const webhookData = $("Webhook").all();

// Get first item's JSON
const firstItem = $("Webhook").first().json;

// Get linked item (when processing each item)
const linkedItem = $("Webhook").item.json;
```

#### Returning Proper Data Structure

```javascript
// CORRECT - returns array of objects with json key
return [
  { json: { result: "value1" } },
  { json: { result: "value2" } }
];

// CORRECT - single item
return [{ json: { processed: true } }];

// WRONG - missing json wrapper
return [{ result: "value" }];  // Will fail!

// WRONG - json is array, not object
return [{ json: ["item1", "item2"] }];  // Will fail!
```

### Python Mode

**Note:** Python uses underscore prefix instead of dollar sign.

```python
# Access all input items
items = _input.all()

# Access current item (per-item mode)
current = _json

# Access other nodes
webhook_data = _("Webhook").all()

# Return data
return [{
    "json": {
        "processed": True,
        "value": items[0]["json"]["field"]
    }
}]
```

**Python Limitations:**
- Use bracket notation: `item["json"]["field"]` not `item.json.field`
- Native Python (v2+) only supports `_items` and `_item`
- No external libraries on n8n Cloud

---

## Webhook Setup

### Configuration Options

| Setting | Options | Description |
|---------|---------|-------------|
| HTTP Method | GET, POST, PUT, PATCH, DELETE, HEAD | Request method to accept |
| Path | `/webhook-path` or `/:variable` | URL endpoint |
| Authentication | None, Basic, Header, JWT | Secure your webhook |
| Respond | Immediately, When Last Node Finishes, Using Respond Node | When to send response |

### Path Parameters

```
/:userId                    → /123
/users/:id                  → /users/456
/:category/:productId       → /electronics/789
```

Access path parameters:
```javascript
{{ $json.params.userId }}
{{ $json.params.id }}
```

### Query Parameters

URL: `https://your-webhook.com/path?status=active&limit=10`

```javascript
{{ $json.query.status }}   // "active"
{{ $json.query.limit }}    // "10"
```

### Response Configuration

**Immediately:** Returns "Workflow got started" instantly
```javascript
// Use for fire-and-forget webhooks
```

**When Last Node Finishes:** Returns data from final node
```javascript
// The last node's output becomes the response
```

**Using Respond to Webhook Node:** Full control over response
```javascript
// Add Respond to Webhook node anywhere in workflow
// Configure status code, headers, body
```

---

## Error Handling

### Error Workflow Setup

1. Create a separate workflow with **Error Trigger** node
2. In main workflow: Settings → Error Workflow → Select your error workflow
3. Error trigger receives:

```json
{
  "execution": {
    "id": "execution-id",
    "url": "https://...",
    "error": {
      "message": "Error message",
      "stack": "Stack trace..."
    }
  },
  "workflow": {
    "id": "workflow-id",
    "name": "Workflow Name"
  }
}
```

### Stop And Error Node

Force workflow failure under specific conditions:

```javascript
// In IF node, route to Stop And Error when condition met
// Configure error type and message
```

### Try/Catch Pattern

Use **Error Output** on nodes:
1. Enable "Continue On Fail" in node settings
2. Connect error output to handling branch
3. Check `$json.error` in downstream nodes

---

## Built-in Variables Reference

### Metadata Variables

| Variable | Description |
|----------|-------------|
| `$executionId` | Current execution ID |
| `$workflow.id` | Workflow ID |
| `$workflow.name` | Workflow name |
| `$workflow.active` | Is workflow active |
| `$runIndex` | Current run index |
| `$itemIndex` | Current item index |
| `$now` | Current DateTime |
| `$today` | Today's date |

### Environment & Custom Variables

```javascript
// Custom variables (Enterprise/Pro)
$vars.myVariable

// Environment variables (self-hosted)
$env.MY_ENV_VAR
```

### Date/Time with Luxon

```javascript
// Current time
{{ $now }}

// Format date
{{ $now.toFormat('yyyy-MM-dd') }}

// Add days
{{ $now.plus({ days: 7 }) }}

// Parse date string
{{ DateTime.fromISO('2024-01-15') }}

// Difference between dates
{{ DateTime.fromISO('2024-03-01').diff(DateTime.fromISO('2024-01-01'), 'days').days }}
```

---

## Common Issues & Solutions

### Expression Issues

**"Can't get data for expression"**
- Previous node hasn't executed yet
- Test workflow up to that node first
- Check: `$("NodeName").isExecuted`

**"Invalid syntax"**
- Check for trailing periods
- Verify bracket matching
- Ensure proper quote usage

### Code Node Issues

**"Code doesn't return items properly"**
- Ensure returning array of objects
- Each object needs `json` key
- `json` must contain object, not array

**"'import' and 'export' may only appear at top level"**
- Use `require()` instead of `import`
```javascript
// Wrong: import moment from 'moment';
// Right: const moment = require('moment');
```

**"Cannot find module"**
- Module not installed (self-hosted only)
- Check `NODE_FUNCTION_ALLOW_EXTERNAL` env var

### Webhook Issues

**Not receiving data**
- Ensure workflow is active (production URL)
- Use test URL with "Listen for test event" for development
- Check authentication settings match client

**Body data is empty**
- Check Content-Type header matches data format
- Enable "Raw Body" option if needed

---

## Best Practices

### Naming Conventions
- Workflow: `[Category] - Action Description`
- Example: `[Leads] - Capture Form Submission to CRM`

### Node Organization
- Add sticky notes explaining complex logic
- Use descriptive node names
- Group related operations

### Error Handling
- Always set up error workflows for production
- Use try/catch patterns for critical paths
- Log errors with context for debugging

### Performance
- Use "Run Once for All Items" when possible
- Limit data early in workflow
- Avoid unnecessary node executions

---

## Quick Reference

### Expression Cheat Sheet

```javascript
// Current item data
{{ $json.field }}
{{ $json.nested.field }}

// Webhook data
{{ $json.body.field }}
{{ $json.query.param }}
{{ $json.params.variable }}

// Other node data
{{ $("Node").first().json.field }}

// Dates
{{ $now.toFormat('yyyy-MM-dd') }}

// Conditional
{{ $json.status === 'active' ? 'Yes' : 'No' }}
```

### Code Node Return Template

```javascript
// JavaScript
return items.map(item => ({
  json: {
    ...item.json,
    processed: true
  }
}));
```

```python
# Python
return [{
    "json": {
        **item["json"],
        "processed": True
    }
} for item in _input.all()]
```

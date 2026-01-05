# n8n Cheat Sheet

> [!NOTE]
> Quick reference for n8n expressions, nodes, shortcuts, and common patterns. Print this for easy access!

## Expression Quick Reference

### Basic Data Access

```javascript
{{ $json.fieldName }}                    // Access field
{{ $json.nested.field }}                 // Nested access
{{ $json.items[0] }}                     // Array access
{{ $json.items.length }}                 // Array length
```

### Built-in Variables

```javascript
{{ $json }}                              // Current item data
{{ $binary }}                            // Binary data
{{ $node }}                              // Other node data
{{ $workflow }}                          // Workflow metadata
{{ $execution }}                         // Execution metadata
{{ $now }}                               // Current timestamp
{{ $today }}                             // Today's date
```

### String Operations

```javascript
{{ $json.name.toUpperCase() }}           // Uppercase
{{ $json.name.toLowerCase() }}           // Lowercase
{{ $json.name.trim() }}                  // Trim whitespace
{{ $json.email.split('@')[0] }}         // Split and get first
{{ $json.name + ' ' + $json.lastName }}  // Concatenate
{{ $json.description.substring(0, 100) }} // Truncate
```

### Number Operations

```javascript
{{ $json.price * 1.1 }}                  // Multiply
{{ $json.total / 2 }}                    // Divide
{{ Math.round($json.rating) }}           // Round
{{ Math.max($json.a, $json.b) }}         // Maximum
{{ Math.min($json.a, $json.b) }}         // Minimum
```

### Date Operations

```javascript
{{ $now }}                               // Current timestamp
{{ $now.toISOString() }}                 // ISO format
{{ $now.toFormat('yyyy-MM-dd') }}        // Custom format
{{ $now.plus({days: 7}) }}               // Add 7 days
{{ $now.minus({hours: 2}) }}             // Subtract 2 hours
{{ $json.date.toFormat('MMMM dd, yyyy') }} // Format date
```

### Array Operations

```javascript
{{ $json.items.length }}                 // Count items
{{ $json.tags.join(', ') }}              // Join array
{{ $json.numbers.reduce((a, b) => a + b, 0) }} // Sum
{{ $json.items.map(item => item.name) }} // Map array
{{ $json.items.filter(item => item.active) }} // Filter array
```

### Conditional Logic

```javascript
{{ $json.status === 'active' ? 'Yes' : 'No' }}
{{ $json.amount > 1000 ? 'High' : 'Low' }}
{{ $json.tags.includes('urgent') ? 'Priority' : 'Normal' }}
{{ $json.field || 'default' }}          // Default value
{{ $json.field ?? 'fallback' }}         // Nullish coalescing
```

### Accessing Other Nodes

```javascript
{{ $('NodeName').item.json.field }}      // Specific node
{{ $('NodeName').all() }}                // All items from node
{{ $('NodeName').first().json.field }}   // First item
{{ $('NodeName').last().json.field }}    // Last item
```

## Common Node Types

### Triggers

| Node | Purpose |
|------|---------|
| **Manual Trigger** | Run workflow manually |
| **Schedule Trigger** | Run on schedule (cron) |
| **Webhook** | Receive HTTP requests |
| **Gmail Trigger** | New email arrives |
| **Slack Trigger** | New message in channel |

### Actions

| Node | Purpose |
|------|---------|
| **Gmail** | Send/read emails |
| **Slack** | Send messages |
| **HTTP Request** | Call any API |
| **Google Sheets** | Read/write spreadsheets |
| **PostgreSQL** | Database operations |

### Transformations

| Node | Purpose |
|------|---------|
| **Set** | Add/modify/remove fields |
| **Code** | JavaScript/Python code |
| **Function** | Built-in functions |
| **Split In Batches** | Process in chunks |

### Logic

| Node | Purpose |
|------|---------|
| **IF** | Conditional branching |
| **Switch** | Multiple branches |
| **Merge** | Combine data streams |
| **Wait** | Pause execution |

## Keyboard Shortcuts

| Action | Windows/Linux | Mac |
|--------|---------------|-----|
| Save workflow | `Ctrl+S` | `Cmd+S` |
| Execute workflow | `Ctrl+Enter` | `Cmd+Enter` |
| Execute node | `Ctrl+Shift+Enter` | `Cmd+Shift+Enter` |
| Add node | `+` | `+` |
| Undo | `Ctrl+Z` | `Cmd+Z` |
| Redo | `Ctrl+Y` | `Cmd+Y` |
| Zoom in | `Ctrl++` | `Cmd++` |
| Zoom out | `Ctrl+-` | `Cmd+-` |
| Fit to screen | `Ctrl+0` | `Cmd+0` |
| Search | `Ctrl+F` | `Cmd+F` |

## Cron Expression Patterns

| Pattern | Description |
|---------|-------------|
| `*/5 * * * *` | Every 5 minutes |
| `0 * * * *` | Every hour |
| `0 9 * * *` | Daily at 9 AM |
| `0 9 * * 1-5` | Weekdays at 9 AM |
| `0 0 1 * *` | First day of month |
| `0 0 * * 0` | Every Sunday |
| `0 9,17 * * *` | 9 AM and 5 PM daily |

## Common Workflow Patterns

### Email Processing

```
Gmail Trigger → Extract Data → Set Fields → Send to Slack
```

### Form Submission

```
Webhook → Validate → Save to Database → Send Confirmation
```

### Scheduled Report

```
Schedule → Fetch Data → Process → Format → Send Email
```

### Approval Workflow

```
Webhook → IF (Check) → Request Approval → Wait → Process
```

### Data Sync

```
Schedule → Fetch Source → Compare → Update Target → Log
```

## Error Handling Pattern

```
Main Flow → [Error] → Error Handler → Log → Notify
```

## Data Flow Patterns

### Single Item
```
Trigger (1) → Node (1) → Node (1)
```

### Multiple Items
```
Trigger (N) → Node (N) → Node (N)
```

### Split Items
```
Trigger (1) → Split → Process Each
```

### Merge Items
```
Multiple Sources → Merge → Process
```

## HTTP Methods

| Method | Purpose |
|--------|---------|
| **GET** | Retrieve data |
| **POST** | Create new data |
| **PUT** | Update existing data |
| **PATCH** | Partial update |
| **DELETE** | Remove data |

## Common HTTP Status Codes

| Code | Meaning |
|------|---------|
| `200` | Success |
| `201` | Created |
| `400` | Bad Request |
| `401` | Unauthorized |
| `403` | Forbidden |
| `404` | Not Found |
| `429` | Rate Limited |
| `500` | Server Error |

## Date Format Patterns

| Pattern | Example |
|---------|---------|
| `yyyy-MM-dd` | 2026-01-05 |
| `MM/dd/yyyy` | 01/05/2026 |
| `dd MMM yyyy` | 05 Jan 2026 |
| `HH:mm:ss` | 14:30:00 |
| `yyyy-MM-dd HH:mm:ss` | 2026-01-05 14:30:00 |
| `MMMM dd, yyyy` | January 05, 2026 |

## Type Conversions

```javascript
// String to Number
{{ Number($json.price) }}
{{ parseInt($json.count) }}
{{ parseFloat($json.amount) }}

// Number to String
{{ String($json.id) }}
{{ $json.id.toString() }}

// To Boolean
{{ Boolean($json.active) }}
{{ $json.status === 'active' }}
```

## Array Methods

```javascript
// Filter
{{ $json.items.filter(item => item.active) }}

// Map
{{ $json.items.map(item => item.name) }}

// Reduce
{{ $json.numbers.reduce((a, b) => a + b, 0) }}

// Find
{{ $json.items.find(item => item.id === 123) }}

// Some
{{ $json.items.some(item => item.urgent) }}

// Every
{{ $json.items.every(item => item.valid) }}
```

## String Methods

```javascript
// Includes
{{ $json.email.includes('@') }}

// Starts With
{{ $json.name.startsWith('John') }}

// Ends With
{{ $json.file.endsWith('.pdf') }}

// Replace
{{ $json.url.replace('http://', 'https://') }}

// Split
{{ $json.email.split('@')[0] }}

// Substring
{{ $json.text.substring(0, 100) }}
```

## Math Functions

```javascript
{{ Math.abs($json.number) }}             // Absolute value
{{ Math.ceil($json.number) }}            // Round up
{{ Math.floor($json.number) }}           // Round down
{{ Math.round($json.number) }}           // Round
{{ Math.max($json.a, $json.b) }}         // Maximum
{{ Math.min($json.a, $json.b) }}         // Minimum
{{ Math.random() }}                      // Random 0-1
```

## JSON Operations

```javascript
// Stringify
{{ JSON.stringify($json) }}

// Parse
{{ JSON.parse($json.stringData) }}

// Keys
{{ Object.keys($json) }}

// Values
{{ Object.values($json) }}
```

## Quick Debugging

```javascript
// Log data
{{ JSON.stringify($json, null, 2) }}

// Check if exists
{{ $json.field ? 'exists' : 'missing' }}

// Type check
{{ typeof $json.field }}

// Length check
{{ $json.items ? $json.items.length : 0 }}
```

## Common Mistakes to Avoid

```javascript
// ❌ Wrong
{{ json.email }}                         // Missing $
{{ $json.user.email.value }}             // Over-nested
{{ $json.email() }}                      // Not a function

// ✅ Correct
{{ $json.email }}                        // Simple access
{{ $json.user.email }}                   // Nested access
{{ $json.email || 'default' }}          // With default
```

## Node Execution Modes

| Mode | Description |
|------|-------------|
| **Run Once for All Items** | Process all items together |
| **Run Once for Each Item** | Process each item separately |
| **Execute Once** | Run once regardless of items |

## Workflow Status

| Status | Meaning |
|--------|---------|
| **Active** | Workflow is running |
| **Inactive** | Workflow is paused |
| **Error** | Workflow has errors |
| **Success** | Last execution succeeded |
| **Failed** | Last execution failed |

## Quick Tips

- **Test nodes individually** before connecting
- **Use expressions** for dynamic values
- **Always handle errors** with Error Trigger
- **Name nodes descriptively** for clarity
- **Save frequently** (Ctrl+S / Cmd+S)
- **Check execution logs** for debugging
- **Use templates** to save time
- **Validate input** early in workflow

## Getting Help

- **Documentation**: [docs.n8n.io](https://docs.n8n.io/)
- **Forum**: [community.n8n.io](https://community.n8n.io/)
- **GitHub**: [github.com/n8n-io/n8n](https://github.com/n8n-io/n8n)

---

**Print this page** for quick reference while building workflows!


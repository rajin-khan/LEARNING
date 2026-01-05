# n8n Tips and Tricks

> [!NOTE]
> This guide contains productivity shortcuts, optimization techniques, and pro tips to help you work faster and build better workflows.

## Productivity Shortcuts

### Keyboard Shortcuts

| Action | Shortcut |
|--------|----------|
| Save workflow | `Ctrl+S` / `Cmd+S` |
| Execute workflow | `Ctrl+Enter` / `Cmd+Enter` |
| Execute node | `Ctrl+Shift+Enter` / `Cmd+Shift+Enter` |
| Add node | `+` key (when canvas focused) |
| Search nodes | Start typing node name |
| Undo | `Ctrl+Z` / `Cmd+Z` |
| Redo | `Ctrl+Y` / `Cmd+Y` |
| Zoom in | `Ctrl++` / `Cmd++` |
| Zoom out | `Ctrl+-` / `Cmd+-` |
| Fit to screen | `Ctrl+0` / `Cmd+0` |

> [!TIP]
> **Pro Tip**: Use `Tab` to navigate between fields in node configuration, and `Enter` to save.

### Quick Node Access

**Instead of clicking "+" and searching:**
1. Click on canvas
2. Start typing node name
3. Press `Enter` to add

**Example**: Type "gmail" → Gmail node appears → Press Enter

### Duplicate Nodes

**Quick duplication:**
1. Select node
2. `Ctrl+D` / `Cmd+D` to duplicate
3. Drag to new position

> [!TIP]
> **Useful for**: Testing different configurations, creating parallel paths, or reusing similar node setups.

## Workflow Organization

### Naming Conventions

**Use descriptive names:**
- ❌ "Workflow 1", "Test", "New Workflow"
- ✅ "Email to Slack Notifications", "Daily Report Generator", "Form Submission Handler"

**Node naming:**
- ❌ "IF", "Set", "HTTP Request"
- ✅ "Check if Amount > 1000", "Extract Email Data", "Fetch User API"

> [!IMPORTANT]
> **Why it matters**: Good names make workflows self-documenting and easier to debug months later.

### Workflow Tags

**Organize with tags:**
- Create tags like: `production`, `testing`, `email`, `reports`, `integrations`
- Tag workflows for easy filtering and organization

### Workflow Descriptions

**Add descriptions to workflows:**
- Click workflow settings
- Add description explaining purpose, triggers, and key nodes
- Include links to related documentation

### Node Descriptions

**Document complex nodes:**
- Add notes/descriptions in node configuration
- Explain why a node exists and what it does
- Reference related nodes or external docs

## Expression Tips

### Expression Helper

**Use the expression editor:**
- Click `=` button next to any field
- Get autocomplete for available fields
- See examples and built-in functions
- Test expressions before saving

### Common Expression Patterns

**Default values:**
```javascript
{{ $json.email || 'no-email@example.com' }}
{{ $json.count || 0 }}
{{ $json.active ?? false }}  // Nullish coalescing
```

**String manipulation:**
```javascript
{{ $json.name.trim() }}                           // Remove whitespace
{{ $json.email.toLowerCase() }}                   // Lowercase
{{ $json.description.substring(0, 100) }}         // Truncate
{{ $json.url.replace('http://', 'https://') }}   // Replace
```

**Date formatting:**
```javascript
{{ $now.toFormat('yyyy-MM-dd') }}                // 2026-01-05
{{ $now.toFormat('HH:mm:ss') }}                  // 14:30:00
{{ $json.date.toFormat('MMMM dd, yyyy') }}      // January 05, 2026
```

**Array operations:**
```javascript
{{ $json.items.length }}                         // Count
{{ $json.tags.join(', ') }}                      // Join array
{{ $json.numbers.reduce((a, b) => a + b, 0) }}  // Sum
```

**Conditional logic:**
```javascript
{{ $json.status === 'active' ? 'Enabled' : 'Disabled' }}
{{ $json.amount > 1000 ? 'High' : 'Low' }}
{{ $json.tags.includes('urgent') ? 'Priority' : 'Normal' }}
```

### Accessing Other Nodes

**Reference data from other nodes:**
```javascript
{{ $('Gmail').item.json.subject }}              // Specific node
{{ $('Set').all() }}                            // All items from node
{{ $('HTTP Request').first().json.data }}      // First item
```

> [!WARNING]
> **Be careful**: Referencing other nodes creates dependencies. If referenced node changes, expressions may break.

## Performance Optimization

### Parallel Processing

**Process items in parallel:**
- Use "Execute Once for Each Item" mode
- Nodes process all items simultaneously
- Much faster than sequential processing

**Example:**
```
Trigger (100 items) → Process All in Parallel → Aggregate
// Processes 100 items simultaneously instead of one-by-one
```

### Batch Processing

**Process large datasets in batches:**
```
Trigger → Split In Batches (size: 50) → Process Batch → Next Batch
```

**Benefits:**
- Avoids memory issues
- Respects rate limits
- Easier error handling per batch

### Caching Data

**Cache frequently accessed data:**
- Use Set node to store data
- Reference cached data instead of fetching repeatedly
- Update cache periodically, not on every execution

**Example:**
```
Schedule (Hourly) → Fetch Data → Set (Cache) → Use in Multiple Workflows
```

### Minimize API Calls

**Reduce unnecessary requests:**
- Combine multiple operations into single API call when possible
- Use native nodes instead of HTTP Request when available
- Cache API responses for static/slow-changing data

### Optimize Node Order

**Order matters for performance:**
```
// ❌ Slow: Filter after processing
Fetch All Data → Process All → Filter → Use

// ✅ Fast: Filter first
Fetch All Data → Filter → Process Filtered → Use
```

## Debugging Tips

### Test Individual Nodes

**Test each node individually:**
1. Use "Execute Node" on each node
2. Verify output before connecting to next node
3. Catch errors early

### Add Debug Nodes

**Temporary debug nodes:**
```
Main Flow → Code (Log Data) → Continue Flow
```

**Debug code:**
```javascript
console.log('Debug - Current data:', JSON.stringify($json, null, 2));
return items;  // Pass data through unchanged
```

### Use Execution Logs

**Review detailed logs:**
- Click on execution in history
- See data at each node
- Identify where workflow fails
- Check execution time per node

### Test with Sample Data

**Use realistic test data:**
- Create test items that match real data structure
- Test edge cases (empty values, null, large data)
- Test error scenarios

### Isolate Problems

**Narrow down issues:**
1. Disable nodes one by one
2. Test with minimal workflow
3. Gradually add nodes back
4. Identify problematic node

## Workflow Patterns

### Reusable Sub-workflows

**Create modular workflows:**
- Build reusable sub-workflows
- Call from multiple main workflows
- Update once, use everywhere

**Example:**
```
Main Workflow → Execute Workflow (Send Notification) → Continue
Main Workflow → Execute Workflow (Send Notification) → Continue
```

### Template Workflows

**Create workflow templates:**
- Build base workflow structure
- Save as template
- Duplicate and customize for new use cases

### Error Handling Pattern

**Standard error handling:**
```
Main Flow → [Error] → Error Handler → Log → Notify → End
```

**Error handler workflow:**
- Logs error details
- Sends notification
- Optionally retries
- Updates status

### Conditional Branching Pattern

**Smart branching:**
```
Trigger → IF (Condition A) → Action A
       → IF (Condition B) → Action B
       → ELSE → Default Action
```

**Use Switch node for multiple conditions:**
```
Trigger → Switch (Status)
       → Case: 'pending' → Process Pending
       → Case: 'approved' → Process Approved
       → Case: 'rejected' → Process Rejected
       → Default → Handle Unknown
```

## Security Best Practices

### Credential Management

**Secure credential handling:**
- Never hardcode credentials in nodes
- Use n8n's credential system
- Rotate credentials regularly
- Use separate credentials for dev/prod

### Webhook Security

**Secure webhooks:**
- Use authentication (path, headers, or query params)
- Validate webhook signatures when available
- Use HTTPS only
- Limit webhook exposure

**Example - Secure webhook path:**
```
Path: form-submission-abc123xyz
// Random path prevents unauthorized access
```

### Data Privacy

**Handle sensitive data carefully:**
- Don't log sensitive data in debug nodes
- Encrypt sensitive data in transit and at rest
- Follow GDPR/privacy regulations
- Clean up old execution data

### Input Validation

**Always validate input:**
- Check required fields exist
- Validate data types
- Sanitize user input
- Reject invalid data early

## Advanced Tips

### Custom Nodes

**Create custom nodes for:**
- Frequently used API integrations
- Complex transformations
- Reusable logic
- Team-specific operations

### Workflow Variables

**Use workflow variables:**
- Store configuration values
- Share data across nodes
- Avoid hardcoding values
- Easy to update in one place

**Set variable:**
```
Set Variable → Name: "API_BASE_URL", Value: "https://api.example.com"
```

**Use variable:**
```
{{ $workflow.variables.API_BASE_URL }}/users
```

### Dynamic Node Configuration

**Configure nodes dynamically:**
- Use expressions in node settings
- Make workflows adaptable
- Reduce duplicate workflows

**Example:**
```
IF (Environment) → Set Variable (API_URL)
                → Use in HTTP Request: {{ $workflow.variables.API_URL }}
```

### Workflow Versioning

**Version control workflows:**
- Export workflows as JSON
- Commit to git repository
- Track changes over time
- Roll back if needed

**Export workflow:**
- Click workflow menu → Download
- Save as `workflow-name-v1.json`
- Commit to version control

## Productivity Workflows

### Workflow Templates Library

**Use existing templates:**
- Browse [n8n workflow templates](https://n8n.io/workflows/)
- Import and customize
- Learn from examples
- Save time building from scratch

### Community Nodes

**Extend functionality:**
- Install community-contributed nodes
- Access more integrations
- Find specialized nodes
- Contribute your own

### Automation for Automation

**Automate workflow management:**
- Schedule workflow backups
- Auto-clean old executions
- Monitor workflow health
- Generate reports

## Quick Wins

### 1. Use Node Templates

**Save node configurations:**
- Configure a node perfectly
- Copy node JSON
- Paste when needed
- Reuse configurations

### 2. Keyboard Navigation

**Navigate without mouse:**
- Use keyboard shortcuts
- Tab between fields
- Enter to save
- Arrow keys to move nodes

### 3. Quick Search

**Find anything quickly:**
- `Ctrl+F` / `Cmd+F` to search workflows
- Search by name, tag, or description
- Filter by status, tags, etc.

### 4. Bulk Operations

**Manage multiple workflows:**
- Select multiple workflows
- Activate/deactivate in bulk
- Tag multiple at once
- Delete unused workflows

## Next Steps

Now that you know the tips and tricks:

1. **Go advanced**: Read [07-advanced-topics.md](./07-advanced-topics.md) for deep dives
2. **Quick reference**: Use [09-cheatsheet.md](./09-cheatsheet.md) for daily use
3. **Find resources**: Check [08-resources.md](./08-resources.md) for more learning

Ready to go advanced? Continue to [07-advanced-topics.md](./07-advanced-topics.md)!


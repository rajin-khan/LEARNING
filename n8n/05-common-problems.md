# Common Problems & Solutions

> [!NOTE]
> This guide covers the most frequent issues you'll encounter with n8n and how to solve them.

## Problem: Workflow Won't Execute

**Symptoms:**
- "Execute Workflow" button is grayed out
- Workflow runs but produces no output
- Error message: "Workflow has no active trigger"

**Cause:**
Missing or misconfigured trigger node, or trigger node is not properly activated.

**Solution:**
1. Check that you have a trigger node (Manual, Webhook, Schedule, etc.)
2. Ensure the trigger node is **activated** (toggle switch should be ON)
3. For webhook triggers, verify the webhook URL is accessible
4. For schedule triggers, check the cron expression is valid
5. Try using Manual Trigger for testing

**Prevention:**
- Always start workflows with a trigger node
- Test with Manual Trigger first, then switch to automatic triggers
- Verify trigger configuration before activating workflow

---

## Problem: Data Not Flowing Between Nodes

**Symptoms:**
- Nodes show "No data" or empty output
- Data appears in one node but not the next
- Workflow executes but produces no results

**Cause:**
Nodes not properly connected, or data structure mismatch between nodes.

**Solution:**
1. **Check connections**: Verify nodes are connected (line should be visible)
2. **Check node order**: Data flows left to right, top to bottom
3. **Verify data structure**: Check previous node's output matches what next node expects
4. **Test individually**: Use "Execute Node" on each node to see its output
5. **Check expressions**: Verify expressions reference correct field names

**Example Debugging:**
```
Gmail Trigger → Check output: Does it have $json.subject?
Set Node → Check input: Can it access $json.subject?
Slack Node → Check expression: Is it using correct field name?
```

**Prevention:**
- Always test nodes individually before connecting
- Use "Execute Node" to verify data at each step
- Check node documentation for expected input/output format

---

## Problem: Expression Errors

**Symptoms:**
- Error: "Expression evaluation failed"
- Error: "Cannot read property of undefined"
- Fields show "undefined" or "null"

**Cause:**
Incorrect expression syntax, accessing non-existent fields, or wrong data path.

**Solution:**
1. **Check field exists**: Verify the field exists in previous node's output
2. **Use correct syntax**: Expressions use `{{ }}` and `$json.fieldName`
3. **Check nested access**: Use `$json.user.email` not `$json.user.email.value`
4. **Handle missing data**: Use `{{ $json.field || 'default' }}` for optional fields
5. **Test expression**: Use expression editor's autocomplete and test feature

**Common Mistakes:**
```javascript
// ❌ Wrong
{{ json.email }}                    // Missing $ prefix
{{ $json.user.email.value }}       // Over-nested
{{ $json.email() }}                // email is not a function

// ✅ Correct
{{ $json.email }}                  // Simple field access
{{ $json.user.email }}             // Nested access
{{ $json.email || 'no-email' }}    // With default value
```

**Prevention:**
- Use expression editor's autocomplete
- Test expressions with "Execute Node"
- Check node output before writing expressions
- Use `{{ $json }}` to see all available fields

---

## Problem: Authentication Failures

**Symptoms:**
- Error: "Authentication failed"
- Error: "Invalid credentials"
- Error: "Token expired"

**Cause:**
Invalid, expired, or missing credentials for service connections.

**Solution:**
1. **Re-authenticate**: Go to Credentials → Edit → Re-authenticate
2. **Check OAuth**: For OAuth services, complete the authorization flow
3. **Verify API keys**: Ensure API keys are correct and not expired
4. **Check permissions**: Verify credentials have required permissions
5. **Test connection**: Use "Test" button in credential settings

**For OAuth Services (Gmail, Slack, etc.):**
1. Delete existing credential
2. Create new credential
3. Complete OAuth flow in browser
4. Grant required permissions

**For API Key Services:**
1. Verify API key is active
2. Check API key has correct permissions/scopes
3. Ensure API key format is correct (no extra spaces)

**Prevention:**
- Save credentials immediately after creating
- Test credentials before using in workflows
- Set up credential expiration reminders
- Use separate credentials for testing and production

---

## Problem: Webhook Not Receiving Data

**Symptoms:**
- Webhook URL returns 404
- External service can't reach webhook
- Webhook receives data but workflow doesn't trigger

**Cause:**
Webhook not activated, incorrect URL, or network/firewall issues.

**Solution:**
1. **Activate workflow**: Toggle workflow to "Active" (required for webhooks)
2. **Copy correct URL**: Use the webhook URL from the node (not the workflow URL)
3. **Check webhook path**: Verify the path matches what external service expects
4. **Test webhook**: Use curl or Postman to test webhook directly
   ```bash
   curl -X POST https://your-n8n-instance.com/webhook/form-submission \
     -H "Content-Type: application/json" \
     -d '{"test": "data"}'
   ```
5. **Check firewall**: Ensure n8n instance is accessible from internet (for cloud services)

**For Self-Hosted n8n:**
- Ensure port is open and accessible
- Check reverse proxy configuration
- Verify domain/DNS settings
- Check firewall rules

**Prevention:**
- Always activate workflow before using webhook
- Test webhook immediately after creation
- Use webhook authentication (path, headers, or query params)
- Document webhook URLs and keep them secure

---

## Problem: Rate Limiting / API Errors

**Symptoms:**
- Error: "Rate limit exceeded"
- Error: "429 Too Many Requests"
- Workflow fails intermittently

**Cause:**
Making too many API requests too quickly, exceeding service rate limits.

**Solution:**
1. **Add delays**: Use "Wait" node between API calls
2. **Batch requests**: Process items in batches instead of individually
3. **Use retry logic**: Configure nodes to retry on rate limit errors
4. **Check rate limits**: Review service documentation for rate limits
5. **Optimize workflow**: Reduce unnecessary API calls

**Example - Adding Delay:**
```
HTTP Request → Wait (2 seconds) → HTTP Request → Wait (2 seconds)
```

**Example - Batch Processing:**
```
Trigger (100 items) → Split In Batches (10 items) → Process Batch → Wait → Next Batch
```

**Prevention:**
- Understand rate limits before building workflows
- Add delays for high-frequency workflows
- Use batch processing for large datasets
- Monitor API usage and errors

---

## Problem: Workflow Runs Too Slowly

**Symptoms:**
- Workflow takes minutes to complete
- Nodes execute one at a time slowly
- Overall execution time is unacceptable

**Cause:**
Sequential processing, unnecessary API calls, or inefficient node configuration.

**Solution:**
1. **Enable parallel processing**: Use "Execute Once for Each Item" mode where possible
2. **Remove unnecessary nodes**: Eliminate redundant transformations
3. **Optimize API calls**: Batch requests, use pagination efficiently
4. **Cache data**: Store frequently accessed data instead of fetching repeatedly
5. **Use faster nodes**: Replace slow HTTP requests with native nodes when available

**Performance Optimization:**
```
// ❌ Slow: Sequential processing
Item 1 → Process → Item 2 → Process → Item 3 → Process

// ✅ Fast: Parallel processing
Items → Process All in Parallel → Aggregate
```

**Prevention:**
- Design workflows for parallel processing
- Minimize API calls
- Use native nodes instead of HTTP requests when possible
- Test workflow performance with realistic data volumes

---

## Problem: Data Type Mismatches

**Symptoms:**
- Error: "Expected number, got string"
- Calculations produce wrong results
- Database insert fails with type error

**Cause:**
Data coming in as wrong type (string instead of number, etc.).

**Solution:**
1. **Convert types**: Use expressions to convert data types
   ```javascript
   {{ parseInt($json.price) }}        // String to number
   {{ String($json.id) }}             // Number to string
   {{ Boolean($json.active) }}       // To boolean
   ```
2. **Check node output**: Verify data types in node output
3. **Use Set node**: Explicitly set correct data types
4. **Validate early**: Add validation nodes to catch type issues

**Common Conversions:**
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

**Prevention:**
- Always check data types in node outputs
- Convert types explicitly when needed
- Use Set node to normalize data types early in workflow
- Test with real data to catch type issues

---

## Problem: Workflow Fails Silently

**Symptoms:**
- Workflow shows as "Success" but nothing happened
- No error messages but expected actions didn't occur
- Workflow completes but data is missing

**Cause:**
Errors caught but not handled, or workflow logic doesn't match expectations.

**Solution:**
1. **Check execution log**: Review detailed execution log for warnings
2. **Add error handling**: Use Error Trigger node to catch all errors
3. **Add logging**: Use Code node to log intermediate values
4. **Verify conditions**: Check IF nodes and conditions are correct
5. **Test with sample data**: Use "Execute Node" with known good data

**Debugging Workflow:**
```
Add Code Node for Logging:
console.log('Data at this point:', $json);
return items;
```

**Prevention:**
- Always add error handling nodes
- Use execution logs to debug issues
- Test workflows with various data scenarios
- Add logging nodes during development

---

## Problem: Schedule Trigger Not Firing

**Symptoms:**
- Scheduled workflow never runs
- Schedule shows as active but doesn't trigger
- Cron expression seems correct but doesn't work

**Cause:**
Incorrect cron expression, workflow not activated, or timezone mismatch.

**Solution:**
1. **Verify cron syntax**: Use cron expression validator
   - Format: `* * * * *` (minute hour day month weekday)
   - Example: `0 9 * * *` = 9 AM daily
2. **Check timezone**: Ensure timezone matches your location
3. **Activate workflow**: Toggle must be ON for schedules to work
4. **Test with short interval**: Use `*/1 * * * *` (every minute) to test
5. **Check n8n logs**: Review logs for schedule execution errors

**Common Cron Patterns:**
```
*/5 * * * *     // Every 5 minutes
0 * * * *       // Every hour
0 9 * * *       // Daily at 9 AM
0 9 * * 1-5     // Weekdays at 9 AM
0 0 1 * *       // First day of month at midnight
```

**Prevention:**
- Test cron expressions with short intervals first
- Use cron expression generators/validators
- Set correct timezone in schedule configuration
- Always activate workflow after creating schedule

---

## Problem: Binary Data (Files) Not Working

**Symptoms:**
- File uploads fail
- Binary data is corrupted
- File nodes show "No binary data"

**Cause:**
Incorrect binary data handling, missing binary data in flow, or format mismatch.

**Solution:**
1. **Check binary data exists**: Verify previous node outputs binary data
2. **Preserve binary**: Use `binary: { data: $binary.data }` in Code nodes
3. **Check file format**: Ensure file format matches what receiving service expects
4. **Use correct node**: Some nodes require specific binary field names
5. **Test file size**: Large files may need special handling

**Preserving Binary in Code Node:**
```javascript
return items.map(item => ({
  json: item.json,
  binary: {
    data: item.binary.data,        // Preserve binary
    fileName: item.binary.fileName,
    mimeType: item.binary.mimeType
  }
}));
```

**Prevention:**
- Always preserve binary data when transforming
- Check node documentation for binary requirements
- Test with small files first
- Verify file formats before processing

---

## Problem: Workflow Version Conflicts

**Symptoms:**
- Workflow changes don't save
- "Workflow has been modified" errors
- Can't activate workflow

**Cause:**
Multiple users editing same workflow, or workflow modified in another session.

**Solution:**
1. **Refresh page**: Reload to get latest version
2. **Check for conflicts**: Review what changed
3. **Save frequently**: Use Ctrl+S / Cmd+S regularly
4. **Use version control**: Export workflows as JSON for backup
5. **Coordinate edits**: Avoid simultaneous edits on same workflow

**Prevention:**
- Save workflows frequently
- Export workflows as backup
- Use descriptive workflow names
- Coordinate with team on shared workflows

---

## Quick Troubleshooting Checklist

When a workflow isn't working, check these in order:

1. ✅ **Workflow is activated** (toggle switch ON)
2. ✅ **Trigger node is configured** and active
3. ✅ **Nodes are connected** (lines visible between nodes)
4. ✅ **Credentials are valid** (test connection)
5. ✅ **Expressions are correct** (syntax and field names)
6. ✅ **Data types match** (numbers vs strings)
7. ✅ **Error handling exists** (Error Trigger node)
8. ✅ **Execution log reviewed** (check for warnings/errors)
9. ✅ **Tested with sample data** (Execute Node individually)
10. ✅ **Service is accessible** (APIs, webhooks reachable)

## Getting More Help

If you've tried these solutions and still have issues:

1. **Check n8n documentation**: https://docs.n8n.io/troubleshooting/
2. **Search community forum**: https://community.n8n.io/
3. **Review execution logs**: Detailed error messages often point to solution
4. **Ask in forum**: Provide workflow structure, error messages, and what you've tried

## Next Steps

Now that you can troubleshoot common issues:

1. **Learn optimization tips**: Read [06-tips-and-tricks.md](./06-tips-and-tricks.md)
2. **Explore advanced features**: Check [07-advanced-topics.md](./07-advanced-topics.md)
3. **Reference quickly**: Use [09-cheatsheet.md](./09-cheatsheet.md)

Ready to optimize? Continue to [06-tips-and-tricks.md](./06-tips-and-tricks.md)!


# Troubleshooting Guide

Common issues and solutions when setting up the Oura Journal AI Insights workflow.

## Data Source Issues

### Journal Data Not Loading

**Problem**: Journal entries showing as empty array or null values

**Solutions**:
1. **Check data source format**:
   - Ensure JSON structure matches expected format
   - Verify date field is named `entry_date` (not `date` or `timestamp`)
   - Confirm text field is named `entry_text`

2. **SQLite Database Issues**:
   - Verify database file path is accessible to n8n
   - Check table structure and column names
   - Test database connectivity outside of n8n first

3. **File Reading Problems**:
   - n8n doesn't support direct file system access in some environments
   - Use "Read Binary File" node if available
   - Consider using HTTP Request to local file server
   - Alternative: Update data manually in Code node

**Debug Steps**:
```javascript
// Add to your journal processing node
console.log("Raw journal data:", JSON.stringify(items[0].json, null, 2));
console.log("Data type:", typeof items[0].json);
console.log("Keys available:", Object.keys(items[0].json || {}));
```

### Oura API Connection Failures

**Problem**: 401 Unauthorized or empty Oura data

**Solutions**:
1. **Check API Token**:
   - Verify token is still valid at [Oura Cloud](https://cloud.ouraring.com/)
   - Ensure token has proper permissions
   - Regenerate token if needed

2. **API Endpoint Issues**:
   - Confirm using correct endpoint: `https://api.ouraring.com/v2/usercollection/daily_sleep`
   - Check date range parameters (`start_date`, `end_date`)
   - Verify date format: `YYYY-MM-DD`

3. **Rate Limiting**:
   - Oura has API rate limits
   - Add delays between requests if making multiple calls
   - Don't request more than 30 days of data at once

**Header Configuration**:
```
Authorization: Bearer YOUR_OURA_TOKEN
```

### Weather API Problems

**Problem**: Weather data not retrieving or incorrect location

**Solutions**:
1. **API Key Issues**:
   - Verify WeatherAPI.com key is active
   - Check monthly usage limits
   - Ensure key has current weather permissions

2. **Location Problems**:
   - Use decimal coordinates (e.g., `37.4419,-122.1430`)
   - Don't use city names with special characters
   - Test coordinates in browser first

3. **Endpoint Errors**:
   - Current weather: `http://api.weatherapi.com/v1/current.json`
   - Historical: `http://api.weatherapi.com/v1/history.json`
   - Forecast: `http://api.weatherapi.com/v1/forecast.json`

## Data Correlation Issues

### No Journal Entries in Final Output

**Problem**: AI reports "no journal entries provided"

**Root Cause**: Data correlation failing to match dates

**Solutions**:
1. **Date Format Mismatch**:
   ```javascript
   // Check date formats in correlation node
   console.log("Journal dates:", Object.keys(journalByDate));
   console.log("Sleep dates:", sleepData.map(s => s.day));
   ```

2. **Global Storage Not Working**:
   - n8n global storage may not persist between nodes
   - Use direct node references instead:
   ```javascript
   // Instead of global.storedJournalData
   const journalData = $('Store Journal').first().json.journal_data;
   ```

3. **Node Reference Issues**:
   - Verify exact node names in workflow
   - Check if nodes are properly connected
   - Use execution history to trace data flow

### Missing Weather or Sleep Data

**Problem**: Some dates have null values for weather/sleep

**Expected Behavior**: This is normal - not all dates will have all data types

**Solutions**:
1. **Handle Missing Data Gracefully**:
   ```javascript
   weather: date === weatherData.current?.last_updated?.split(' ')[0] ? {
     temp_c: weatherData.current.temp_c,
     condition: weatherData.current.condition.text
   } : null
   ```

2. **Adjust Date Ranges**:
   - Ensure Oura data covers journal date range
   - Weather API typically only provides current + recent data
   - Consider using historical weather API for older dates

## AI Analysis Issues

### Poor Quality Insights

**Problem**: AI reports are generic or inaccurate

**Solutions**:
1. **Improve Data Quality**:
   - Ensure sufficient journal entries (minimum 3-5 days)
   - Include mood and tags in journal data
   - Verify sleep data is complete

2. **Enhance AI Prompt**:
   - Be more specific about desired analysis
   - Include examples of good insights
   - Adjust temperature/creativity settings

3. **Check Data Format**:
   - Ensure AI receives properly formatted text
   - Verify no `[object Object]` placeholders
   - Test with sample data first

### AI Service Errors

**Problem**: OpenRouter/Claude API failures

**Solutions**:
1. **Check Credits/Limits**:
   - Verify OpenRouter account has sufficient credits
   - Check daily/monthly usage limits
   - Monitor API costs

2. **Model Availability**:
   - Some models may be temporarily unavailable
   - Try alternative models (GPT-4, Claude Haiku)
   - Check OpenRouter status page

3. **Request Format Issues**:
   ```json
   {
     "model": "anthropic/claude-3.5-sonnet",
     "messages": [{"role": "user", "content": "..."}],
     "max_tokens": 1500
   }
   ```

## Workflow Execution Problems

### Nodes Not Executing in Order

**Problem**: Data from previous nodes not available

**Solutions**:
1. **Check Node Connections**:
   - Ensure all nodes are properly linked
   - Verify execution order in workflow view

2. **Execution Context Issues**:
   - Each node only receives data from immediate predecessor
   - Use Set nodes to pass data through HTTP requests
   - Store data in workflow variables if needed

### Intermittent Failures

**Problem**: Workflow works sometimes but fails randomly

**Solutions**:
1. **Add Error Handling**:
   ```javascript
   try {
     const data = $('Previous Node').first().json;
     // Process data
   } catch (error) {
     console.log("Error accessing data:", error.message);
     return [{ json: { error: error.message } }];
   }
   ```

2. **Network Timeouts**:
   - Increase timeout settings in HTTP nodes
   - Add retry logic for API calls
   - Check internet connectivity stability

3. **Resource Limits**:
   - n8n may have memory/CPU limits
   - Reduce data processing batch sizes
   - Optimize code node efficiency

## Performance Optimization

### Slow Execution Times

**Solutions**:
1. **Reduce Data Volume**:
   - Limit journal entries to last 30 days
   - Use pagination for large datasets
   - Cache frequently accessed data

2. **Optimize API Calls**:
   - Batch requests where possible
   - Use appropriate date ranges
   - Avoid unnecessary API calls

3. **Code Efficiency**:
   - Minimize loops and complex operations
   - Use built-in JavaScript methods
   - Avoid redundant data processing

## Security and Privacy

### Protecting Sensitive Data

**Best Practices**:
1. **API Key Security**:
   - Never commit keys to version control
   - Use n8n credential management
   - Rotate keys regularly

2. **Personal Data Protection**:
   - Be cautious with journal content in logs
   - Consider data retention policies
   - Use local n8n deployment for sensitive data

3. **Access Control**:
   - Limit n8n workflow access
   - Monitor execution logs
   - Regular security updates

## Getting Help

If you're still experiencing issues:

1. **Check n8n Documentation**: [docs.n8n.io](https://docs.n8n.io)
2. **Review API Documentation**: Oura, WeatherAPI, OpenRouter
3. **n8n Community Forum**: [community.n8n.io](https://community.n8n.io)
4. **GitHub Issues**: Open an issue in this repository
5. **Debug Methodically**: Add console.log statements to trace data flow

## Common Debug Code Snippets

**Check Node Data**:
```javascript
console.log("Node output:", JSON.stringify(items[0].json, null, 2));
console.log("Available keys:", Object.keys(items[0].json || {}));
```

**Test API Connections**:
```javascript
// Test in browser first
https://api.ouraring.com/v2/usercollection/daily_sleep?start_date=2025-07-01&end_date=2025-07-31
```

**Verify Data Types**:
```javascript
console.log("Data type:", typeof data);
console.log("Is array:", Array.isArray(data));
console.log("Length:", data?.length);
```

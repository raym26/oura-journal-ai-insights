# Setup Guide

## Prerequisites

Before setting up the Oura Journal AI Insights workflow, ensure you have:

- **n8n instance** (self-hosted or cloud)
- **Oura Ring** and Oura account
- **Journal data source** (SQLite database or JSON file)
- **API access** to weather service
- **AI service access** (OpenRouter, OpenAI, or similar)

## Step 1: API Keys and Credentials

### 1.1 Oura API Token
1. Go to [Oura Cloud](https://cloud.ouraring.com/)
2. Sign in with your Oura account
3. Navigate to "Personal Access Tokens"
4. Generate a new token
5. Copy and save securely

### 1.2 Weather API Key
1. Sign up at [WeatherAPI.com](https://www.weatherapi.com/)
2. Get your free API key (1M calls/month)
3. Note your location coordinates (lat/lon)

### 1.3 OpenRouter API Key
1. Sign up at [OpenRouter](https://openrouter.ai/)
2. Add credits to your account
3. Generate API key
4. Choose your preferred model (recommended: `anthropic/claude-3.5-sonnet`)

## Step 2: n8n Setup

### 2.1 Import Workflow
1. Download `wellness-analyzer-workflow.json` from this repository
2. In n8n: Workflows → Import from file
3. Select the downloaded JSON file

### 2.2 Configure Credentials
Set up these credentials in n8n:

**Oura API:**
- Credential Type: Header Auth
- Name: `Authorization`
- Value: `Bearer YOUR_OURA_TOKEN`

**Weather API:**
- Credential Type: Query Auth
- Name: `key`
- Value: `YOUR_WEATHER_API_KEY`

**OpenRouter API:**
- Credential Type: Header Auth
- Name: `Authorization`  
- Value: `Bearer YOUR_OPENROUTER_KEY`

## Step 3: Data Source Configuration

### 3.1 Journal Data Setup

**Option A: SQLite Database**
If using SQLite database (recommended):
- Place your `my_daily_journal_data.db` file in accessible location
- Update file path in the "Read Journal" node
- Ensure table structure matches expected format

**Option B: JSON File**
If using JSON file:
- Export journal entries to JSON format
- Update the manual entries in the "Journal Source" code node
- Follow the expected data structure (see Data Schema section)

### 3.2 Location Settings
Update location in Weather API node:
- Latitude and longitude for your location
- Example: San Jose, CA = `37.4419, -122.1430`

## Step 4: Node Configuration

### 4.1 Trigger Setup
Configure the schedule trigger:
- **Daily**: `0 8 * * *` (8 AM every day)
- **Weekly**: `0 8 * * 1` (8 AM every Monday)
- **Custom**: Set your preferred time

### 4.2 Oura API Node
- **URL**: `https://api.ouraring.com/v2/usercollection/daily_sleep`
- **Method**: GET
- **Headers**: Use Oura credential
- **Parameters**: 
  - `start_date`: Last 7-30 days
  - `end_date`: Current date

### 4.3 Weather API Node
- **URL**: `http://api.weatherapi.com/v1/current.json`
- **Method**: GET
- **Parameters**:
  - `key`: Your API key
  - `q`: Your coordinates

### 4.4 AI Analysis Node
- **Model**: `anthropic/claude-3.5-sonnet`
- **Max Tokens**: 1000-1500
- **System Message**: Wellness analysis prompt
- **User Message**: Formatted correlation data

## Step 5: Testing

### 5.1 Test Individual Nodes
1. **Journal Data**: Verify entries are loading correctly
2. **Oura API**: Check sleep data retrieval
3. **Weather API**: Confirm current conditions
4. **Correlation**: Ensure data is matching by date
5. **AI Analysis**: Review insights quality

### 5.2 Full Workflow Test
1. Execute workflow manually
2. Check each node's output
3. Verify final AI report format
4. Confirm no errors in execution log

## Step 6: Automation

### 6.1 Enable Schedule
- Activate the trigger node
- Choose appropriate frequency
- Monitor first few automated runs

### 6.2 Output Configuration
Configure how you want to receive reports:
- **Email**: Add email node after AI analysis
- **Slack**: Use Slack webhook
- **File**: Save to local/cloud storage
- **Dashboard**: Send to visualization tool

## Troubleshooting

### Common Issues

**Empty Journal Data:**
- Check file path in journal reading node
- Verify data format matches expected schema
- Ensure database/file is accessible

**Oura API Errors:**
- Verify token is still valid
- Check date range parameters
- Ensure rate limits aren't exceeded

**Weather API Issues:**
- Confirm API key is active
- Check location coordinates format
- Verify endpoint URL

**AI Analysis Problems:**
- Check OpenRouter account credits
- Verify model name spelling
- Review prompt formatting

**No Data Correlations:**
- Ensure date formats match across sources
- Check for timezone issues
- Verify data exists for overlapping dates

## Security Notes

- Store all API keys securely in n8n credentials
- Never commit credentials to version control
- Use environment variables for sensitive data
- Regularly rotate API keys
- Monitor API usage and costs

## Next Steps

After successful setup:
1. Run workflow for a week to gather baseline data
2. Review AI insights for accuracy and relevance
3. Customize prompts based on your preferences
4. Add additional data sources as needed
5. Set up monitoring and alerts for failed executions

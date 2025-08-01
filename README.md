# oura-journal-ai-insights
A toy N8N project.  Personal wellness analysis system that correlates Oura sleep data, journal entries, and weather using n8n and AI for daily insights.  The journal entries are from another agent I created (https://github.com/raym26/adk-digital-journal-and-assistant). You're free to use other ways or omit altogether.  My objective here is to get acquainted to N8N as a LCNC platform for AI agent development.  

## 🌟 Features

- **Multi-Source Data Correlation**: Combines journal entries, Oura sleep metrics, and weather data
- **AI-Powered Analysis**: Uses OpenRouter/Claude to generate personalized wellness insights
- **Automated Workflow**: Triggered daily/weekly, n8n handles data collection, correlation, and report generation
- **Real-Time Processing**: Analyzes patterns between sleep quality, mood, and environmental factors
- **Actionable Recommendations**: Provides specific suggestions for better sleep and wellness

## 🏗️ Architecture
┌─────────────────── n8n Workflow ───────────────────┐
│                                                    │
│  Automated Trigger (Daily/Weekly)                  │
│           │                                        │
│           ├──► Journal Database (SQLite)           │
│           ├──► Oura API (Sleep Data)               │
│           ├──► Weather API                         │
│           │                                        │
│           ├──► Data Correlation Engine             │
│           ├──► AI Analysis (Claude/OpenRouter)     │
│           └──► Daily Insights Report               │
│                                                    │
└────────────────────────────────────────────────────┘

## 📊 Sample Insights

The system analyzes correlations like:
- **Sleep Quality vs Journal Mood**: "Your sleep efficiency dropped to 46% on 2025-07-24, correlating with feelings of deflation after job rejections"
- **Weather Impact**: "Sunny 23°C weather on 2025-07-31 coincided with family-focused journal entries and stable sleep"
- **Productivity Patterns**: "Career breakthrough insights on 2025-07-22 followed by improved sleep scores"

## 🚀 Quick Start

### Prerequisites
- n8n instance (local or cloud)
- Oura API access token
- Weather API key (WeatherAPI.com)
- Journal data source (SQLite database or JSON file)
- OpenRouter API key for AI analysis

### Installation

1. **Clone this repository**
```bash
git clone https://github.com/yourusername/oura-journal-ai-insights.git
cd oura-journal-ai-insights
```

2. **Import n8n workflow**
   - Open n8n
   - Go to Workflows → Import
   - Select `n8n-workflows/wellness-analyzer-workflow.json`

3. **Configure API credentials**
   - Set up Oura API token in n8n credentials
   - Add Weather API key
   - Configure OpenRouter API access

4. **Set up data sources**
   - Point journal data node to your SQLite database or JSON file
   - Test Oura API connection
   - Verify weather API location settings

5. **Run workflow**
   - Set up automated trigger (daily/weekly schedule)
   - Or execute manually for testing
   - Review correlation output and AI insights

## 📁 Data Sources

### Journal Entries
Expected format:
```json
{
  "entry_date": "2025-07-31",
  "entry_text": "Today was focused on family time...",
  "mood": "neutral",
  "tags": ["family"]
}
```

### Oura Sleep Data
Pulls metrics including:
- Sleep score and efficiency
- Deep sleep and REM percentages
- Sleep duration and timing

### Weather Data
Current conditions including:
- Temperature and humidity
- Weather conditions
- Correlation with mood/sleep patterns

## 🛠️ Workflow Components

1. **Automated Trigger**: Daily/weekly schedule to initiate analysis
2. **Data Collection**: Fetch from journal DB, Oura API, Weather API  
3. **Data Correlation**: Match entries by date, handle missing data
4. **AI Analysis**: Generate insights using Claude/GPT models
5. **Report Generation**: Format findings into actionable daily report

## 🔧 Customization

- **Modify AI prompts** in the AI Agent node to focus on specific insights
- **Add data sources** like fitness trackers, calendar events, or mood ratings
- **Adjust correlation logic** to weight certain factors more heavily
- **Change output format** for email, Slack, or other notification channels

## 📈 Roadmap

- [ ] Add calendar integration for productivity correlation
- [ ] Include heart rate variability data
- [ ] Implement trend analysis over time
- [ ] Create web dashboard for historical insights
- [ ] Support for multiple journal formats

## 🤝 Contributing

Contributions welcome! Areas for improvement:
- Additional data source integrations
- Enhanced AI prompt engineering
- Mobile app connectivity
- Data visualization components

## 📄 License

MIT License - see [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- Built using [n8n](https://n8n.io/) workflow automation
- Powered by [Oura API](https://cloud.ouraring.com/docs/) for sleep data
- AI analysis via [OpenRouter](https://openrouter.ai/)
- Weather data from [WeatherAPI](https://www.weatherapi.com/)

---

**Note**: This system processes personal health and journal data. Ensure proper data privacy and security practices when deploying.

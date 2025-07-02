# Slack Q&A Bot

An AI-powered Slack bot that learns from channel conversations and automatically responds to questions that have been previously answered.

## Features

- Monitors Slack channel messages
- Stores questions and their answers
- Automatically responds when similar questions are asked
- Uses string similarity matching to find relevant answers
- Secure request verification using Slack signing secrets

## Prerequisites

1. **Install Ollama**
   ```bash
   # For macOS or Linux
   curl https://ollama.ai/install.sh | sh

   # Verify installation
   ollama --version

   # Pull the Llama2 model
   ollama pull llama2
   ```

2. **Start Ollama Server**
   ```bash
   # Start the Ollama server
   ollama serve
   ```
   The server will be available at http://localhost:11434

## Setup

1. **Create a Slack App**
   - Go to [Slack API](https://api.slack.com/apps)
   - Click "Create New App"
   - Choose "From scratch"
   - Name your app and select your workspace

2. **Configure Channel Access**
   - Identify the channels where you want the bot to operate
   - Get channel IDs:
     ```bash
     # Using Slack API (replace with your token)
     curl -H "Authorization: Bearer xoxb-your-token" \
          https://slack.com/api/conversations.list
     ```
   - Or right-click the channel in Slack and copy the channel ID
   - Add channel IDs to .env:
     ```
     ALLOWED_SLACK_CHANNELS=C1234567890,C0987654321
     ```
   - Leave empty to allow all channels (not recommended)

2. **Configure Bot Token Scopes**
   - Navigate to "OAuth & Permissions"
   - Add the following bot token scopes:
     - `chat:write`
     - `channels:history`
     - `groups:history`
     - `im:history`
     - `mpim:history`

3. **Install App to Workspace**
   - Click "Install to Workspace"
   - Authorize the app

4. **Set Environment Variables**
   - Copy `.env.local.example` to `.env.local`
   - Add your Slack bot token (starts with `xoxb-`)
   - Add your Slack signing secret (found in "Basic Information")

```env
SLACK_BOT_TOKEN=xoxb-your-bot-token-here
SLACK_SIGNING_SECRET=your-signing-secret-here
SIMILARITY_THRESHOLD=0.8
```

5. **Configure Event Subscriptions**
   - Enable Events in your Slack App settings
   - Add your Request URL: `https://your-domain/api/slack`
   - Subscribe to bot events:
     - `message.channels`
     - `message.groups`
     - `message.im`
     - `message.mpim`

## Development

```bash
# Install dependencies
npm install

# Run development server
npm run dev
```

## How It Works

1. When a new message is posted in a channel:
   - If it's a new question (not in a thread):
     - The bot checks if a similar question has been answered before using semantic matching
     - If found, responds with the previous answer
     - If not found, stores the question for future learning
   - If it's a reply in a thread:
     - The bot stores it as an answer to the thread's original question

2. Secure Local AI Processing:
   The bot uses Ollama for local, secure AI processing without exposing data to external services:
   
   - Local Model Integration:
     - Uses Llama2 model running locally via Ollama
     - All data processing happens on your infrastructure
     - No external API calls or data exposure
     - Complete privacy and data control
   
   - Advanced Language Understanding:
     - Local embedding generation for semantic matching
     - Context-aware question analysis
     - Automatic categorization and keyword extraction
     - Secure processing of sensitive information
   
   - Private Answer Generation:
     - Generates responses using local model
     - Combines knowledge from previous answers
     - Maintains data confidentiality
     - Customizable to your organization's needs
   
   - Example Local Processing:
     ```
     Infrastructure:
     - Ollama running on your server
     - Llama2 model for text processing
     - Local embedding generation
     - Secure data storage
     
     Processing Flow:
     1. Question received from Slack
     2. Local embedding generation
     3. Secure similarity matching
     4. Private answer generation
     5. Response sent to Slack
     ```
   
   - Secure Learning:
     - Knowledge base stays within your infrastructure
     - No external data sharing
     - Full control over model behavior
     - Customizable to your security requirements

## Security

- All requests from Slack are verified using the signing secret
- Requests older than 5 minutes are rejected
- Environment variables are required for bot operation

## Limitations

- In-memory storage (data is lost on restart)
- Simple string similarity matching
- No natural language processing
- Single workspace support

## Future Improvements

- Persistent storage (database)
- Advanced NLP for better question matching
- Multi-workspace support
- Answer rating system
- Question categorization

# Claude Code Proxy - Docker Edition 🐳

A containerized version of the [claude-code-proxy](https://github.com/1rgs/claude-code-proxy) that lets you use Anthropic clients (like Claude Code) with Gemini or OpenAI backends.

This proxy server translates between Anthropic's API format and OpenAI/Gemini models via LiteLLM, all packaged in a convenient Docker container.

## Features 🚀

- **Multi-provider support**: Use OpenAI or Google Gemini models with Claude clients
- **Docker containerized**: Easy deployment and scaling
- **Health checks included**: Built-in monitoring and restart capabilities
- **Secure**: Runs as non-root user in container
- **Environment flexible**: Easy configuration via environment variables

## Quick Start 🏃‍♂️

### Prerequisites

- Docker and Docker Compose installed
- OpenAI API key and/or Google AI Studio (Gemini) API key

### 1. Clone and Setup

```bash
git clone <your-repo-url>
cd claude-code-proxy-docker
cp env.example .env
```

### 2. Configure Environment

Edit `.env` file and add your API keys:

```bash
# Required: At least one API key
OPENAI_API_KEY=your-openai-api-key-here
GEMINI_API_KEY=your-google-ai-studio-api-key-here

# Optional: Choose your preferred provider
PREFERRED_PROVIDER=openai  # or 'google'

# Optional: Customize model mapping
BIG_MODEL=gpt-4o           # Model for Claude Sonnet requests
SMALL_MODEL=gpt-4o-mini    # Model for Claude Haiku requests
```

### 3. Run with Docker Compose

```bash
docker-compose up -d
```

The proxy will be available at `http://localhost:8080`

### 4. Use with Claude Code

Install Claude Code (if not already installed):
```bash
npm install -g @anthropic-ai/claude-code
```

Connect to your proxy:
```bash
ANTHROPIC_BASE_URL=http://localhost:8080 claude
```

## Alternative Deployment Methods 🛠️

### Docker Run

```bash
# Build the image
docker build -t claude-code-proxy .

# Run the container
docker run -d \
  --name claude-code-proxy \
  -p 8080:8080 \
  -e OPENAI_API_KEY=your-openai-api-key \
  -e GEMINI_API_KEY=your-gemini-api-key \
  -e PREFERRED_PROVIDER=openai \
  claude-code-proxy
```

### Local Development

```bash
# Install dependencies
pip install -r requirements.txt

# Copy environment file
cp env.example .env
# Edit .env with your API keys

# Run the server
python -m uvicorn server:app --host 0.0.0.0 --port 8080 --reload
```

## Configuration 📝

### Environment Variables

| Variable | Required | Default | Description |
|----------|----------|---------|-------------|
| `OPENAI_API_KEY` | Yes* | - | Your OpenAI API key |
| `GEMINI_API_KEY` | Yes* | - | Your Google AI Studio API key |
| `ANTHROPIC_API_KEY` | No | - | Only needed if proxying to Anthropic models |
| `PREFERRED_PROVIDER` | No | `openai` | Primary backend (`openai` or `google`) |
| `BIG_MODEL` | No | `gpt-4o` | Model for Claude Sonnet requests |
| `SMALL_MODEL` | No | `gpt-4o-mini` | Model for Claude Haiku requests |

*At least one API key (OpenAI or Gemini) is required.

### Model Mapping

The proxy automatically maps Claude model requests:

| Claude Model | Default Mapping | With Google Provider |
|--------------|-----------------|---------------------|
| `haiku` | `openai/gpt-4o-mini` | `gemini/gemini-2.0-flash` |
| `sonnet` | `openai/gpt-4o` | `gemini/gemini-2.5-pro` |

### Supported Models

**OpenAI Models:**
- o3-mini, o1, o1-mini, o1-pro
- gpt-4.5-preview, gpt-4o, gpt-4o-mini
- chatgpt-4o-latest, gpt-4.1, gpt-4.1-mini

**Gemini Models:**
- gemini-2.5-pro
- gemini-2.5-pro-preview-03-25
- gemini-2.0-flash

## Monitoring 📊

### Health Check

The container includes health checks. Check status:

```bash
docker ps  # Look for health status
docker-compose ps  # With compose
```

### Logs

View logs:
```bash
docker-compose logs -f claude-code-proxy
```

### API Status

Check if the proxy is running:
```bash
curl http://localhost:8080/
```

## Troubleshooting 🔧

### Common Issues

1. **Container won't start**
   - Check your `.env` file has valid API keys
   - Ensure port 8080 is not already in use

2. **API key errors**
   - Verify your API keys are correct and have sufficient credits
   - Check the provider (OpenAI/Google) supports your requested models

3. **Connection refused**
   - Make sure Docker container is running: `docker ps`
   - Check port mapping: `-p 8080:8080`

### Debug Mode

Run with verbose logging:
```bash
docker-compose logs --tail=100 -f
```

## How It Works 🔍

1. **Receives** requests in Anthropic's API format
2. **Maps** Claude model names to OpenAI/Gemini equivalents  
3. **Translates** requests via LiteLLM
4. **Sends** to appropriate provider (OpenAI/Google)
5. **Converts** responses back to Anthropic format
6. **Returns** to Claude client

The proxy supports both streaming and non-streaming responses, maintaining full compatibility with Claude clients.

## Contributing 🤝

Based on the excellent work from [1rgs/claude-code-proxy](https://github.com/1rgs/claude-code-proxy).

Contributions welcome! Please feel free to submit a Pull Request.

## Security Notes 🔒

- Container runs as non-root user
- API keys should be stored securely in `.env` file
- Consider using Docker secrets for production deployments
- Keep your base images updated

## License 📄

This project maintains the same license as the original claude-code-proxy repository.

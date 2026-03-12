# quarkus-coder-agent-claude

A lightweight Web UI for Claude Code CLI. Chat with Claude models from your browser with real-time SSE streaming, prompt queuing, and session management.

**Queue prompts while the AI is still thinking.** Don't wait — type your next prompts and add them to the queue. Once the current response finishes, queued prompts are sent automatically in order.

## Prerequisites

- Java 21+ (for JVM mode)
- Maven 3.9+ (for building)
- **One of the following for authentication:**
  1. [Claude Code CLI](https://docs.anthropic.com/en/docs/claude-code) installed and logged in (recommended)
  2. `ANTHROPIC_API_KEY` environment variable
  3. Enter your API key via the Web UI at startup
- (Optional) [GraalVM](https://www.graalvm.org/) for native image build
- (Optional) [JBang](https://www.jbang.dev/) for the launcher script

## Quick Start (Native Image)

Download the pre-built native binary for your platform, then:

```bash
./quarkus-coder-agent-claude-1.0.0-runner
```

Open `http://localhost:8090` in your browser. If Claude CLI is not found, you'll be prompted to enter your Anthropic API key.

## Build

### JVM mode

```bash
git clone https://github.com/oogasawa/quarkus-coder-agent-claude.git
cd quarkus-coder-agent-claude
rm -rf target
mvn install
```

### Native image

```bash
rm -rf target
mvn install -Dnative -DskipTests
```

The native binary is generated at `target/quarkus-coder-agent-claude-1.0.0-runner` (~52MB).

## Run

### JVM mode

```bash
java -jar target/quarkus-app/quarkus-run.jar
```

### Native image

```bash
./target/quarkus-coder-agent-claude-1.0.0-runner
```

Open `http://localhost:8090` in your browser.

### Options

Change the HTTP port:

```bash
java -Dquarkus.http.port=9090 -jar target/quarkus-app/quarkus-run.jar
```

Provide API key via environment variable:

```bash
ANTHROPIC_API_KEY=sk-ant-... java -jar target/quarkus-app/quarkus-run.jar
```

Or via config property:

```bash
java -Dcoder-agent.api-key=sk-ant-... -jar target/quarkus-app/quarkus-run.jar
```

### JBang launcher

```bash
jbang coder_agent.java
jbang coder_agent.java --port=9090
```

## Authentication

Authentication is resolved in this order:

1. **Claude Code CLI** — if `claude` is on your PATH, it is used directly (recommended; supports tool execution, session management)
2. **Environment variable** — set `ANTHROPIC_API_KEY`
3. **Config property** — pass `-Dcoder-agent.api-key=sk-ant-...`
4. **Web UI prompt** — if none of the above, the browser shows an API key input dialog at startup

## Features

- **Claude models** — sonnet, opus, haiku via Claude Code CLI
- **Prompt queue** — queue up prompts while the AI is responding; auto-send, reorder, remove
- **Tool execution** — Bash, Read, Write, Edit, Glob, Grep, WebSearch, WebFetch (via Claude CLI)
- **Interactive prompts** — tool permission dialogs forwarded to the Web UI
- **Session persistence** — session ID saved to file; survives server restart
- Real-time streaming responses (SSE) with Markdown rendering
- Save conversation history as Markdown file
- 10 color themes (5 dark + 5 light)

## REST API

```bash
# List available models
curl http://localhost:8090/api/models

# Send a prompt
curl -X POST http://localhost:8090/api/chat \
  -H 'Content-Type: application/json' \
  -d '{"text":"Hello","model":"sonnet"}'

# Check authentication status
curl http://localhost:8090/api/config
```

## Test

```bash
rm -rf target
mvn test
```

## License

Apache License 2.0

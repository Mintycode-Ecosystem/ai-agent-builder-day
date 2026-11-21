# AI Agent Builder Day — Attendee Guide

Build your own AI agent with the framework you like, then register it on **Mintycode** so others can discover it, see exactly what it does, and trust it.

By the end of the day you will have:

- a working AI agent, running at its own URL,
- registered and **published in the Mintycode registry**, with its prompt, tools, and models declared,
- a **trust score** and **transparency score** you can improve.

**Agenda at a glance**

| Part | What you do | Time |
|---|---|---|
| 1 | Set up | before you arrive |
| 2 | Choose your framework | 5 min |
| 3 | Build your agent | 60 min |
| 4 | Put it online | 30 min |
| 5 | Register it on Mintycode | 30 min |
| 6 | Improve your scores | rest of the day |

---

## Before we start: a short note

### What is an AI agent?

An **AI agent** is a program that uses a language model to **decide what to do next** and then **does it**, step by step, until the job is finished.

A plain chatbot takes a question and returns an answer. An agent goes further: it can call **tools** (search the web, read a file, query a database, call an API), look at the result, and decide on the next step. It repeats this loop until it has an answer or has completed the task.

Modern agents often go one step further: instead of only calling ready-made tools, they **write and run their own code**. For example, an agent can write a short script to analyse a spreadsheet, run it in a safe **sandbox** (an isolated environment), read the output or error, fix the code, and run it again. Code lets an agent handle tasks nobody built a tool for, and do in one step what would take many separate tool calls. Because running code is powerful, it's also something to declare clearly: you'll review your tools' permissions in 5.4.

Every agent has the same four parts:

| Part | What it is | Example |
|---|---|---|
| **Model** | The LLM that does the thinking | `claude-sonnet-5-5`, `gpt-6.2-astra` |
| **System prompt** | Instructions that define the agent's job, tone, and limits | *"You are a shopping assistant that compares product prices…"* |
| **Tools** | Functions the agent may call, each with a name and a description, including tools that let it run code or commands | `get_price`: *"Get the current price of a product by SKU"*; a code-execution or shell tool |
| **Loop** | The code that sends the request, runs any tool the model asks for, returns the result to the model, and repeats | built into a framework, or written by you |

These are exactly the things Mintycode asks you to declare in part 5: the more clearly your agent's prompt, tools, and model are defined, the better it registers and the higher it scores.

### How to create an AI agent: direct, framework, managed, or no-code

There are four main ways to build one. The common advice is the same everywhere: **start with the simplest one that works, and add layers only when you need them.**

**Option A: Direct (call the model's API yourself)**

You use the model provider's SDK (for example the Anthropic API with tool use) and write the agent loop yourself: send the messages and tool definitions, check whether the model asked for a tool, run it, send back the result, repeat.

- ✅ Full control, very few dependencies, nothing hidden. Great for learning how agents really work.
- ✅ Easy to debug: every step is your own code.
- ⚠️ You write and maintain the plumbing yourself (the loop, retries, conversation history, streaming).
- Best for: small, focused agents, or when you want to understand every step.

**Option B: Framework (let a library run the loop)**

You use an agent framework (for example the Claude Agent SDK, OpenAI Agents SDK, LangGraph, CrewAI, or Pydantic AI). You declare the model, prompt, and tools; the framework runs the loop for you and usually adds extras such as memory, multi-agent handoffs, tracing, and built-in tools.

- ✅ Much faster to get something working, with less code to write.
- ✅ Ready-made features for bigger agents (multiple agents, workflows, memory).
- ⚠️ More dependencies and some "magic": you have to learn the framework's way of doing things.
- Best for: getting to a working agent quickly today, or agents with several steps or several agents.

**Option C: Managed (the provider hosts and runs the agent)**

A newer option: a model provider runs the agent loop **and** the infrastructure for you, usually with a sandbox where the agent can run code, use files, and browse. You create the agent and start sessions through API calls; you don't host the loop yourself. Examples include Claude Managed Agents and Google's managed agents on Vertex AI / Gemini.

- ✅ Least infrastructure work: no server, sandbox, or loop to run.
- ⚠️ You depend on one vendor's APIs, pricing, and model choice, and usually pay for hosting time as well as tokens.
- ⚠️ The Mintycode CLI can't read the agent's setup from your code, so you'll enter the prompt, tools, and model yourself or put them in `mintycode.yaml` (see 5.3).
- Best for: agents that need a safe place to run code or work with files, when you don't want to run servers.

**Option D: No-code (build visually)**

You build the agent on a canvas in a visual or chat-based builder (for example n8n, Langflow, Flowise, Dify, or Lovable): pick a model, write the prompt, and connect tool blocks, with no code required.

- ✅ Fastest way to start if you don't code, and easy to show others how it works.
- ⚠️ Less control, and harder to version and test than code. Products change quickly (for example, OpenAI is retiring its visual Agent Builder), so prefer open-source builders you can self-host or export.
- ⚠️ As with managed agents, you'll enter the prompt, tools, and model in Mintycode yourself.
- Best for: non-developers, or connecting an agent to everyday apps quickly.

**Another shape: an MCP server.** Instead of a full agent, you can build an **MCP server** (with the MCP TypeScript SDK or FastMCP) that exposes tools any MCP-compatible agent can use. Choose this if what you want to share is a set of tools rather than a complete agent.

**Which should I pick today?**

| If you… | Choose |
|---|---|
| want to finish a working agent within the hour | a **framework** (see the tables in part 2) |
| want to learn how agents work under the hood | **direct** (Anthropic API with tool use) |
| already use a framework at work | that framework |
| need code execution or files without running servers | **managed** |
| don't write code, or want to connect everyday apps fast | a **no-code** builder (see part 2) |
| want to share tools, not a whole agent | an **MCP server** |

Part 2 lists getting-started guides for the direct, framework, no-code, and MCP options. For a managed agent, follow your provider's own docs.

---

## 1. Before you arrive

Please do this at home. It saves the first hour of the day.

### 1.1 What you need

- A laptop with **Node.js 18.17 or newer** (needed for the Mintycode CLI, whatever language your agent uses). Check with:

  ```bash
  node --version
  ```

- **Python 3.9 or newer**, only if you plan to build in Python.
- An API key for the model you want to use (for example Anthropic or OpenAI).
- A **GitHub account**.
- A **Mintycode account**: sign up at [mintycode.io](https://mintycode.io).

### 1.2 Install the Mintycode CLI

```bash
npm install -g mintycode-cli
```

Check it installed (this guide uses version **1.0.2** or newer):

```bash
mintycode --version
```

### 1.3 Log in

```bash
mintycode login
```

Enter the email and password of your Mintycode account. Then check:

```bash
mintycode whoami
```

✅ **Checkpoint:** `whoami` shows `Logged in as: <your username>`.

---

## 2. Choose your framework

Use any framework you like. For the frameworks below, the Mintycode CLI **reads your code and fills in your agent's prompt, tools, and model for you**:

**TypeScript / JavaScript**

| Framework | Getting-started guide (link) |
|---|---|
| Claude Agent SDK | [code.claude.com/docs/en/agent-sdk/quickstart](https://code.claude.com/docs/en/agent-sdk/quickstart) |
| Anthropic API (Claude, with tool use) | [platform.claude.com/docs/en/get-started](https://platform.claude.com/docs/en/get-started) |
| OpenAI Agents SDK | [openai.github.io/openai-agents-js/guides/quickstart](https://openai.github.io/openai-agents-js/guides/quickstart/) |
| LangChain | [docs.langchain.com/oss/javascript/langchain/quickstart](https://docs.langchain.com/oss/javascript/langchain/quickstart) |
| LangGraph | [docs.langchain.com/oss/javascript/langgraph/quickstart](https://docs.langchain.com/oss/javascript/langgraph/quickstart) |
| Vercel AI SDK | [ai-sdk.dev/docs/getting-started](https://ai-sdk.dev/docs/getting-started) |
| Mastra | [mastra.ai/docs](https://mastra.ai/docs) |
| MCP server (TypeScript SDK) | [modelcontextprotocol.io/docs/develop/build-server](https://modelcontextprotocol.io/docs/develop/build-server) |

**Python**

| Framework | Getting-started guide (link) |
|---|---|
| Claude Agent SDK | [code.claude.com/docs/en/agent-sdk/quickstart](https://code.claude.com/docs/en/agent-sdk/quickstart) |
| OpenAI Agents SDK | [openai.github.io/openai-agents-python/quickstart](https://openai.github.io/openai-agents-python/quickstart/) |
| LangChain | [docs.langchain.com/oss/python/langchain/quickstart](https://docs.langchain.com/oss/python/langchain/quickstart) |
| LangGraph | [docs.langchain.com/oss/python/langgraph/quickstart](https://docs.langchain.com/oss/python/langgraph/quickstart) |
| CrewAI (in code or `config/agents.yaml`) | [docs.crewai.com/en/quickstart](https://docs.crewai.com/en/quickstart) |
| Pydantic AI | [ai.pydantic.dev](https://ai.pydantic.dev) |
| Google ADK | [adk.dev/get-started/python](https://adk.dev/get-started/python/) |
| LlamaIndex | [developers.llamaindex.ai/python/framework/getting_started/starter_example](https://developers.llamaindex.ai/python/framework/getting_started/starter_example/) |
| smolagents | [huggingface.co/docs/smolagents/guided_tour](https://huggingface.co/docs/smolagents/guided_tour) |
| AutoGen | [microsoft.github.io/autogen/stable/user-guide/agentchat-user-guide/quickstart.html](https://microsoft.github.io/autogen/stable/user-guide/agentchat-user-guide/quickstart.html) |
| FastMCP (MCP server) | [gofastmcp.com/getting-started/welcome](https://gofastmcp.com/getting-started/welcome) |

**No-code / low-code (visual builders)**

Build your agent by connecting blocks on a canvas instead of writing code. The Mintycode CLI can't read these automatically, so you'll enter your agent's prompt, tools, and model yourself, or put them in a `mintycode.yaml` file (see 5.3).

| Tool | Good for | Getting-started guide (link) |
|---|---|---|
| n8n | Agents inside automation workflows; open source, can self-host | [docs.n8n.io/advanced-ai](https://docs.n8n.io/advanced-ai/) |
| Langflow | Visual agent and RAG flows; open source, Python under the hood | [docs.langflow.org](https://docs.langflow.org) |
| Flowise | Visual agent and chatbot flows; open source, quick prototyping | [docs.flowiseai.com](https://docs.flowiseai.com) |
| Dify | Agent apps with RAG and workflows; open source or cloud | [docs.dify.ai](https://docs.dify.ai) |
| Microsoft Copilot Studio | Agents for teams already on Microsoft 365 | [learn.microsoft.com/microsoft-copilot-studio](https://learn.microsoft.com/en-us/microsoft-copilot-studio/) |
| Zapier Agents | Agents that act across thousands of everyday apps | [zapier.com/agents](https://zapier.com/agents) |
| Lovable | Describe your agent in chat; Lovable builds and hosts the whole app (UI, backend, model calls) | [docs.lovable.dev](https://docs.lovable.dev) |

Tip: the open-source builders (n8n, Langflow, Flowise, Dify) give each agent an API endpoint you can use as its URL in part 4. Many also let you export the flow as a file you can push to GitHub. Lovable is different: it writes real code for you and can sync it to GitHub, so you get a hosted URL and a repository (see the Lovable prompt template in part 3).

*Reading this in Word? Hold **Ctrl** (Windows) or **Cmd** (Mac) and click a link to open it.*

**Using something else?** That's fine. You'll type the details in yourself, or put them in a `mintycode.yaml` file (see 5.3).

Not sure what to pick? If you know TypeScript, try the **Claude Agent SDK** or **Vercel AI SDK**. If you know Python, try **CrewAI** or **Pydantic AI**. If you don't code, try **Lovable**, **n8n**, or **Langflow**.

---

## 3. Build your agent

Follow your framework's getting-started guide from the tables in part 2 to create a small agent. Keep it focused: one clear job, done well.

For your agent to register well on Mintycode, make sure it has these three things **in its code** (or, in a no-code builder, set in its blocks):

1. **A system prompt** that says what the agent is and does, for example *"You are a shopping assistant that compares product prices…"*.
2. **At least one tool** with a name and a description, for example `get_price`, *"Get the current price of a product by SKU"*.
3. **A named model**, for example `claude-sonnet-5-5` or `gpt-6.2-astra`.

Example (Claude Agent SDK, TypeScript):

```ts
import { query, tool, createSdkMcpServer } from "@anthropic-ai/claude-agent-sdk";
import { z } from "zod";

const getPrice = tool(
  "get_price",
  "Get the current price of a product by SKU",
  { sku: z.string() },
  async ({ sku }) => ({ content: [{ type: "text", text: "42.00" }] }),
);

const shop = createSdkMcpServer({ name: "shop", version: "1.0.0", tools: [getPrice] });

for await (const message of query({
  prompt: "What does SKU-1 cost?",
  options: {
    model: "claude-sonnet-5-5",
    systemPrompt: "You are a shopping assistant that compares product prices for the user.",
    mcpServers: { shop },
    allowedTools: ["mcp__shop__get_price", "WebSearch"],
  },
})) {
  console.log(message);
}
```

### Using a coding agent? Prompt templates

You can also ask a coding agent (for example Claude Code, Cursor, GitHub Copilot, or Codex) to build the agent for you. Copy a template below, replace everything in `[brackets]`, and paste it in. Build in small steps: run the agent after each step and check it before asking for more.

**Template 1: create the agent**

```text
Create a small AI agent in [TypeScript / Python] using [framework, e.g. the Claude Agent SDK].

The agent's job: [one sentence, e.g. "compare product prices across online stores and report the cheapest option"].
It must NOT: [e.g. "make purchases, create accounts, or enter payment details"].

Requirements:
- A clear system prompt, defined in the code as a named constant, that describes the job and the limits above.
- These tools, each with a clear name and a one-line description:
  - [tool_name]: [what it does, its inputs, and what it returns]
  - [tool_name]: [...]
- Use the model [model name, e.g. claude-sonnet-5-5], written explicitly in the code.
- Read the model API key from an environment variable, never hard-code it. Add a .env.example file.
- Add a README with how to install, run, and test it.

First show me the plan and the file structure, then write the code. When it's done, run it with a test question and show me the output.
```

**Template 2: put it online (part 4)**

```text
Wrap this agent in a small web API using [Express / Hono / FastAPI].
- One route: POST /run. It takes JSON {"input": "..."} and returns {"output": "..."}.
- Require an API key in the "x-api-key" header and return 401 without it. Read the expected key from an environment variable.
- Make it ready to deploy on [Vercel / Railway / Render / Fly.io], and tell me exactly which environment variables to set there.
- Give me a curl command to test it with and without the key.
```

**Template 3: get it ready for Mintycode (part 5)**

```text
Create a mintycode.yaml file in the project root for registering this agent on Mintycode.
Use these fields: artifactName, artifactType (ai_agent), version, ownerType (individual), ownerReference ([your Mintycode username]),
whatAgentDoes, whatAgentMustNotDo, inputFormats, outputFormats, sourceCodeVisibility, sourceCodeUrl ([your GitHub URL]),
license, promptVisibility, executionEndpoint ([your deployed URL]/run), authMethod (api_key).
Fill them in from the code. inputFormats/outputFormats may only use: text, json, image, audio, video.
Then list every tool the agent can use and what it can access (files, network, shell, code execution), so I can review its permissions.
```

**Template for Lovable (no-code)**

```text
Build a web app for an AI agent that [the agent's job].
- A simple chat page where the user types a request and sees the agent's answer.
- A backend function that calls [model name] with this system prompt: "[your system prompt]".
- The agent can use these tools: [tool_name: what it does], [...].
- It must NOT: [limits].
- Also expose a POST /run endpoint that takes {"input": "..."}, returns {"output": "..."}, and requires an API key in the "x-api-key" header.
- Keep all API keys in secrets, never in the code.
```

Then connect the Lovable project to GitHub so you have a repository to link in part 5.

**Tips for prompting a coding agent**

- Give it **one job** for the agent, not five. You can add more later.
- Ask it to **write the prompt, tools, and model clearly in the code**: that's what the Mintycode CLI reads.
- **Read the code it writes**, especially anything that runs shell commands, writes files, or calls the network. You'll declare these permissions in 5.4.
- If something breaks, paste the full error message back and ask it to fix the cause, not hide the error.

Push your project to a **GitHub repository**. You'll link it in part 5.

✅ **Checkpoint:** your agent runs on your laptop and answers a test question.

---

## 4. Put it online

To publish an agent on Mintycode you need an **execution endpoint**: a public URL where your agent can be called.

1. **Wrap your agent in a small web API** with one route, for example `POST /run`, that takes the user's input and returns the agent's answer. Use whatever you know: Express, Hono, or Next.js in TypeScript; FastAPI or Flask in Python.
2. **Protect it with an API key.** Check for a secret key in a request header and reject requests without it. You'll declare this as the `api_key` authentication method.
3. **Deploy it** to any hosting service, for example Vercel, Railway, Render, or Fly.io. Keep your model API key in the host's environment variables, never in your code.
4. **Write down your endpoint URL**, for example `https://price-agent.example.com/run`.

✅ **Checkpoint:** calling your endpoint with the right API key returns an answer, and calling it without one is rejected.

---

## 5. Register it on Mintycode

### 5.1 Save a draft

In your project folder, run:

```bash
mintycode commit
```

The CLI scans your project and shows what it found. For example:

```
✔ Scanned node project — found 1 prompt(s), 1 model(s), 3 tool(s)

─── Auto-detected fields ──────────────────────────────

  Prompts:
  [HIGH] "You are a shopping assistant that compares product prices for the u..."
          agent.ts:7

  Models:
  [HIGH] Anthropic / claude-sonnet-5-5

  Tools:
  [HIGH] get_price [custom] — Get the current price of a product by SKU
  [HIGH] mcp__shop__get_price [mcp] — MCP tool "get_price" from server "shop"
  [HIGH] WebSearch [builtin] — Search the web
```

`HIGH`, `MED`, and `LOW` show how sure the scanner is. Check that it found the right prompt, tools, and model.

**Tip:** add `--dry-run` to see everything that would be sent, without saving anything.

### 5.2 Answer the questions

The CLI then asks for anything it couldn't detect:

| Question | What to enter |
|---|---|
| Artifact name | A short unique slug, e.g. `price-agent` |
| Artifact type | **AI Agent** |
| What does this artifact do? | One sentence, e.g. *"Compares product prices across online stores and reports the cheapest option"* |
| Version | e.g. `1.0.0` |
| Owner type / username | Individual, and your Mintycode username |
| What must this agent NOT do? | Be specific, e.g. *"Make purchases, create accounts, or enter payment details"* |
| Input / output formats | e.g. input `text`, output `text` and `json` |
| Source code visibility | **Public** if your GitHub repo is public (it raises your transparency score), with its URL |
| License | e.g. `MIT` |
| Prompt visibility | **Public** or **Private** (public raises your transparency score) |
| Execution endpoint URL | Your URL from part 4 |
| Authentication method | `api_key` |
| Use detected models / declare detected tools? | Confirm the ones that are right |

When it finishes, you'll see a draft ID and an **Edit** link:

```
✔ Draft saved!

  ID:   64f3a9b2c1d4e5f6a7b8c9d0
  Name: price-agent
  Edit: https://mintycode.io/artifact/64f3a9b2c1d4e5f6a7b8c9d0/edit
```

### 5.3 Optional: answer the questions once, in a file

To skip the questions (and to correct anything the scanner missed), create **`mintycode.yaml`** in your project folder. The CLI reads it automatically and only asks for what's missing:

```yaml
artifactName: price-agent
artifactType: ai_agent
version: 1.0.0
ownerType: individual
ownerReference: your-username
whatAgentDoes: Compares product prices across online stores and reports the cheapest option
whatAgentMustNotDo: Make purchases, create accounts, or enter payment details
inputFormats: [text]
outputFormats: [text, json]
sourceCodeVisibility: public
sourceCodeUrl: https://github.com/your-username/price-agent
license: MIT
promptVisibility: public
executionEndpoint: https://price-agent.example.com/run
authMethod: api_key
```

Allowed values:

- `inputFormats` / `outputFormats`: `text`, `json`, `image`, `audio`, `video`
- `authMethod`: `api_key`, `oauth`, `internal`
- `license`: `MIT`, `Apache-2.0`, `GPL-3.0`, `AGPL-3.0`, `BSD-3-Clause`, `MPL-2.0`, `Proprietary`, `Other`

### 5.4 Review your tools' permissions

For your own tools, the CLI reminds you to **review permissions and data scope**. Open the **Edit** link from 5.2, go to the **Tools** step, and for each tool say honestly what it can do: read, write, delete, execute, send, or access accounts; and whether it touches personal data, credentials, or the internet. Accurate declarations raise your transparency score.

### 5.5 Publish

Find your draft:

```bash
mintycode draft list
```

Publish it with its ID, and confirm when asked:

```bash
mintycode publish <id>
```

```
✔ Published "price-agent" v1.0.0!

  URL: https://mintycode.io/artifact/price-agent
```

✅ **Checkpoint:** your agent appears when you search the registry:

```bash
mintycode registry search price-agent
```

```bash
mintycode registry show price-agent
```

**Changed your agent?** Bump the version, run `mintycode commit` again (it offers to update your draft), and publish again.

---

## 6. Improve your scores

Every published agent gets two scores. See them on your agent's page at `https://mintycode.io/artifact/<your-agent-name>`.

### Transparency score (0–100): how much you openly declare

| What you declare | How |
|---|---|
| Public source code | Public GitHub repo and its URL |
| Public prompt | Prompt visibility: Public |
| A license, and its text | License field, plus a `LICENSE` file in your repo |
| Input and output formats or schemas | Formats in 5.2, schemas in the **Interface contract** step on the Edit page |
| Tool permissions | Section 5.4 |
| The model and its version | Detected by the scanner, or in `mintycode.yaml` |
| MCP compatibility | Detected from your MCP configuration |
| An execution endpoint | Part 4 |
| Clear restrictions | A specific "What must this agent NOT do?" |

Up to 60 points come from these declarations. The other 40 come from **Mintycode verification**, where the Mintycode team checks that your source, prompt, and tools match what you declared. You can request it from your agent's page.

### Trust score (0–100): can people rely on it?

| Part | Points | How to raise it |
|---|---|---|
| Transparency | up to 40 | Everything above |
| Verified publisher | up to 30 | Complete identity verification (KYC) on your Mintycode profile, then connect your GitHub account and a wallet |
| Community ratings | up to 30 | Ask other attendees to try and rate your agent, and rate theirs |

**Challenge:** at the end of the day, the agents with the highest trust and transparency scores win. Rate at least three other attendees' agents honestly; good reviews help everyone.

---

## 7. Troubleshooting

| Problem | What to do |
|---|---|
| `mintycode: command not found` | Close and reopen the terminal. If it still fails, check that npm's global folder is on your PATH (`npm prefix -g`). |
| `npm install -g` fails with a permissions error | Don't use `sudo`. Use a Node version manager (nvm, fnm, or Volta), or fix npm's global folder permissions. |
| Login says **"User does not exist"** | Sign up at mintycode.io first, using the same email. |
| Login says **"Incorrect email or password"** | Check your password, or reset it on mintycode.io. |
| **"Not authenticated"** or **"Session expired"** | Run `mintycode login` again. |
| The scanner didn't find your prompt, tools, or model | Your framework may write them in an unusual way. Put them in `mintycode.yaml` (5.3), or type them when asked. |
| The scanner found the wrong prompt | Choose the right one when asked, or set it on the Edit page. |
| You're scanning a large folder | Point it at your agent's folder: `mintycode commit --dir ./my-agent` |
| Publishing fails because of missing fields | Agents need: what it does, what it must not do, input and output formats, source and prompt visibility, the model, the execution endpoint, and the authentication method. Run `mintycode commit` again to fill them in. |
| Something else | Ask a helper, or run the command again and share the exact error. |

---

## 8. After the workshop

- **Your agent stays published.** Keep improving it and publish new versions any time.
- **Mintycode:** [mintycode.io](https://mintycode.io)
- **CLI on npm:** [`mintycode-cli`](https://www.npmjs.com/package/mintycode-cli)
- **Questions and feedback:** contact@mintycode.io

**About the license:** the Mintycode CLI and SDKs are free to use, including in commercial products, to register, identify, and protect your own agents and products through Mintycode. You may not modify them, resell them, or use them to build a competing service. Full terms are in each package's `LICENSE` file.

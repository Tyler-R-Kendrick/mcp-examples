---
theme: seriph
background: https://cover.sli.dev
title: Model Context Protocol (MCP)
info: |
  ## Model Context Protocol (MCP)
  Understanding Anthropic's standardized approach to AI tool integration.
class: text-center
drawings:
  persist: false
transition: slide-left
mdc: true
layout: cover
---

# Model Context Protocol (MCP)

## Understanding Anthropic's standardized approach to AI tool integration.

<div class="pt-12">
  <span @click="$slidev.nav.next" class="px-2 py-1 rounded cursor-pointer" hover="bg-white bg-opacity-10">
    Let's explore MCP →
  </span>
</div>

---
layout: default
---

# What is MCP?

<v-clicks>

- **Standardized Protocol** for AI tool interactions
- **Universal Adapter** for connecting AI to data sources and tools
- **Beyond Tool Calling** - handles context and resource management

</v-clicks>

<!--
MCP (Model Context Protocol) is a standard way for AI applications and agents to connect to and work with your data sources (e.g. local files, databases, cloud storage accounts, etc.) and tools (any API or existing codebase that you want a model to invoke).

MCP is Anthropic's answer to model interactions as a service. It standardizes not only tool calling, but also how to handle and expose the context in which they operate.
-->

---
layout: section
---

# What Problems Does MCP Solve?

## Why is standardization needed?

<!--
Before diving into the technical details, let's understand the challenges that led to the creation of MCP and why standardization in this space is so important.
-->

---
layout: two-cols-header
---

# Current Challenges with API Specifications

<v-click at="1" hide>
Existing standards have limitations for LLM tool integration
</v-click>

::left::

<img
  v-for="(src,i) in ['/assets/mcp-architecture.png', '/assets/mcp-inspector.png', '/assets/mcp-simple-diagram.png']"
  :src="src"
  :key="src"
  v-show="$clicks === i + 1"
  class="h-80 object-contain"
/>

::right::

<v-clicks>

- **Tool Description Optimization** needed for each model
- **Task Composition vs Service Decomposition** asymmetry
- **Context Bloat** and tool selection issues

</v-clicks>

<!--
LLMs need tools to interact with the world and api specifications make this a lot easier to implement. However, each of the existing standards have its own set of limitations when it comes to tool calling:

[click] For optimal performance, tool descriptions currently need to be tailored to the model. OpenAPI description may not be sufficient for accurate tool calling - causing more code to be written to accommodate the shortcomings of api descriptors.

[click] Even if the api spec has sufficient descriptors, service designs are often more abstract than the llms tasks we build for - and we often compose multiple api calls to achieve a single task. This is often not captured in the api spec, and requires additional code to be written to compose the calls for any given task.

[click] The current generation of LLMs suffer from a "lost-in-the-middle" problem; as the size of the context window increases, tool selection success decreases. If you look at enterprise api specs, they are often very large; it is often much better to only import the tools that are relevant to the task at hand, instead of every endpoint in the spec.

For this reason, Api Manifests were introduced to limit the size/scope of the API description to only the relevant endpoints for client generation, but this still suffers from the same issue of tool descriptor optimization for models, and the composition issue.
-->

---

# Framework-Specific Solutions

<v-clicks>

## LangServe
- Tightly coupled to LangChain framework
- One-way communication only
- No dynamic tool discovery

## Custom Solutions (WebRPC/SemanticKernel)
- Framework-specific implementations
- Limited cross-language compatibility
- Fragmented ecosystem

</v-clicks>

<!--
Langserve (developed by the LangChain team) is a framework that allows developers to create and deploy invokable "chains" as services. It provides a way to expose tools over HTTP, enabling LLMs to call them as needed. However, it has also has its limitations:

* It is tightly coupled to the LangChain framework, making it difficult to use with other frameworks or languages.
* It is a one-way communication protocol, meaning that it does not support two-way communication between the LangChain server and client.
* It doesn't allow for tool discovery or dynamic tool registration, which limits its flexibility and adaptability.

To bring langserve capabilities to the dotnet ecosystem and compete with it, I also attempted a WebRPC based approach to host SemanticKernel as a service, allowing for two-way invocation in prompt and function filters that also enabled remote function calling, process isolation, and even the potential to host an SK kernel as a cloud appliance or PaaS service. This approach, however, also had its limitations:

* It was tightly coupled to the SemanticKernel framework, making it difficult to use with other frameworks or languages.

Developers still needed to write code in different languages, using their own framework of choice, and be able to reuse what the industry at large had contributed (both SK and LangChain).
-->

---

# Domain Specific Languages

<v-clicks>

- **LMQL** and other DSL attempts
- **Adoption Challenges** across languages
- **Plugin Support** never implemented
- **Same Problem** - limited to originating framework

</v-clicks>

<!--
To solve for the polyglot development and framework agnostic scenarios, a few attempts were made to create DSLs (such as LMQL) that would allow developers to define tools in a more language-agnostic way, but because they lacked adoption, the plugin support across languages had never been implemented - coincidentally, causing the same problem of limiting adoption to the language/framework that created the DSL.
-->

---
layout: section
---

# The MCP Solution

## Language and Framework Agnostic Protocol

<!--
Anthropic recognized these fundamental issues and created a solution that abstracts away the language and framework dependencies entirely.
-->

---

# How MCP Works

## Core Architecture

<v-clicks>

- **JSON-RPC** with configurable transport (HTTP, WebSocket, etc.)
- **Request/Response** & **Notifications**
- **Progress/Cancellation/Ping** support
- **Three Main Components**: Resources, Tools, and Prompts

</v-clicks>

<!--
Anthropic saw these issues and abstracted a level further to create a protocol completely abstracted away from the language and framework, allowing developers to define tools in a way that is independent of the underlying implementation. This allows for greater flexibility, adaptability, and reuse.

#### How does it work?
Core Architecture
* Json-RPC with configurable transport (HTTP, WebSocket, etc.)
* Request/Response & Notifications
* Progress/Cancellation/Ping
* Resources, Tools, and Prompts.

#### How to use it?

* Inspector
* Language specific SDK (python, typescript, java/kotlin, dotnet, etc.)
* Host a server (Docker in a multi-container dev-container)
* Create/load an mcp json config
-->

---
layout: section
---

# What Can You Do With MCP?

## Real-World Applications and Use Cases

<!--
Now let's explore the practical applications of MCP and how it can transform your AI development workflow.
-->

---

# Resource Management & Integration

## Universal Resource Access

- **Abstract file systems** with NL queryable access
- **Bridge cloud storage** (Azure Storage, AWS S3, local files)
- **Replace framework abstractions** (Python's fsspec, .NET's FileProvider)

<!--
* Leverage resources endpoints as a way to abstract away file systems and replace framework specific abstractions (python's fsspec, dotnet's FileProvider apis, etc.) with NL queryable resource access. Build one that bridges your enterprise's Azure Storage and AWS S3 buckets, or even a local file system, and expose it as a resource endpoint that can be queried with NL.
-->

---

# Resource Management & Integration

## API Integration

- **Microservice integration** through bounded contexts
- **Command/Query interfaces** via MCP tools
- **NLWeb-style capabilities** for existing APIs

<!--
* Expose existing APIs as LLM tools. Microservice bounded contexts are a great fit for this. By adding a simple "Ask" or "Interact" mcp tool endpoints that register your microservice endpoints as internal tools, you can create kick-off integration points for command/query style interactions with your service - through MCP - adding similar capabilities as NLWeb, but for APIs.

* Infuse AI into existing applications through NLWeb and turn your existing app into an MCP server.
-->

---

# Development Tools & Agent Composition

## CLI Wrapper & Developer Tools
- **Simplify CLI usage** with natural language (e.g., Git)
- **Project scaffolding** with existing developer tools

## Configurable Agents
- **Configure an agent with multiple MCP servers** for emergent effects.
- **CodeAct agents** that build and expose new functions dynamically

<!--
* Wrap CLIs (like git) to simplify usage with NL and allow llms to be more effective at project scaffolding by leveraging existing developer tools.

* Combine MCP servers for emergent effects (think adding Playwright MCP, (A visual regression testing agent (like BackstopJS or Chromatic), and an Operator/Computer Use Agent to dynamically build regression suites on existing applications, from Natural Language as input (like your business/feature requirements)). **maybe BDD will get a second wind**

* Build a "CodeAct" agent that can dynamically build and expose new functions as code, given a context.
-->

---

# Adaptive Systems

## Dynamic Server Management
- **Server-as-client** architecture
- **Dynamic MCP server** addition/removal
- **Adaptive tool loading** based on user queries

## Universal Agent Capabilities
- **Cross-domain integration** (HR, IT, Sales)
- **Context-aware tool selection**
- **Emergent system behaviors**

<!--
* Create a server that ***is*** a client, to dynamically add/remove MCP servers and create adaptive systems (think of a universal agent that loads tools from disparate systems based on user queries - such as HR, IT, and Sales)
-->

---
layout: section
---

# Current Limitations

## What MCP Cannot Yet Do

<!--
While MCP is powerful, it's important to understand its current limitations and areas for future development.
-->

---

# Authentication & State Management

## Security & Access Control
- **No standard authentication/authorization** for tools
- **Security model** still evolving

## State & Session Management
- **No standard state management** for tools
- **Session persistence** requires custom implementation
- **Protocol is stateful** by design

<!--
* It does not yet have a standard way to handle authentication and authorization for tools, which is a common requirement in many applications.
* It does not yet have a standard way to handle state management for tools, which is important for many applications that require persistence or session management.
-->

---

# Reliability & Versioning

## Error Handling
- **No standard error handling/retries** for tools
- **Reliability patterns** not yet standardized

## Tool Management
- **No standard versioning** of tools
- **No standard tool discovery/registration**
- **Tool name conflicts** possible between servers

<!--
* It does not yet have a standard way to handle error handling and retries for tools, which is important for many applications that require reliability and fault tolerance.
* It does not yet have a standard way to handle versioning of tools, which is important for many applications that require backward compatibility or feature evolution.
* It does not yet have a standard way to handle tool discovery and registration, which is important for many applications that require dynamic tool management.
* Servers can declare the same tool names, resulting in conflicts on the client. The same goes for all named entities (resources, prompts, etc.). This is a known limitation of the protocol and should be handled by the client.
-->

---

# Protocol & Communication Limitations

## Current Protocol Gaps
- **No JSON-RPC batch requests** support
- **Limited agent-to-agent** communication in sampling
- **Streaming HTTP transports** notifications not standardized
- **Multi-modal multi-block outputs** not yet supported

## File System Integration
- **External filesystems** in client Roots not fully specified

<!--
* Doesn't handle json rpc batch requests.
* sampling doesn't sufficiently support agent-to-agent communication. 
* tool related notifications for [streaming http transports](https://github.com/modelcontextprotocol/modelcontextprotocol/issues/484) are not yet standardized.
* Multi-modal multi-block outputs aren't yet supported.
* Specifying external filesystems in client Roots [here](https://github.com/modelcontextprotocol/modelcontextprotocol/issues/505).
* The protocol is in active development; the specification is not yet finalized, and breaking changes may occur in future releases.
-->

---

# Protocol & Communication Limitations

## Windows Ecosystem
- **Windows MCP Registry**
- **Windows Actions**
- **GitHub Copilot** integration

## Azure AI Services
- **[Azure Copilot Studio](https://learn.microsoft.com/en-us/microsoft-copilot-studio/agent-extend-action-mcp)**
- **[Azure AI Foundry](https://github.com/azure-ai-foundry/mcp-foundry)**
- **Azure AI Agent Services**
- **NLWeb** integration

<!--
New Integrations Since Build 2025:
* Windows MCP Registry
* Windows Actions
* Github Copilot
* [Azure Copilot Studio](https://learn.microsoft.com/en-us/microsoft-copilot-studio/agent-extend-action-mcp)
* [Azure AI Foundry](https://github.com/azure-ai-foundry/mcp-foundry)
* Azure AI Agent Services
* NLWeb
-->

---
layout: section
---

# Resources & Links

## Community and Documentation

<!--
Key resources, server lists, and integration links for getting started with MCP.
-->

---

# Server Discovery

## Official Catalogs
- **[Docker MCP Servers](https://www.docker.com/blog/announcing-docker-mcp-catalog-and-toolkit-beta/)**
- **[Anthropic MCP Docs](https://modelcontextprotocol.io/introduction)**
- **[Anthropic MCP Servers](https://github.com/modelcontextprotocol/servers)**
- **[Google GenAI Toolbox](https://github.com/googleapis/genai-toolbox)**

## Community Resources
- **[Smithery.AI](https://smithery.ai/)** - Community marketplace
- **[Self-Hosted MCP Registry](https://github.com/modelcontextprotocol/registry)**
- **[Azure API Center](https://learn.microsoft.com/en-us/azure/api-center/register-discover-mcp-server)** - Private servers

<!--
Server Lists:
* [Docker MCP Servers](https://www.docker.com/blog/announcing-docker-mcp-catalog-and-toolkit-beta/)
* [Anthropic MCP Docs](https://modelcontextprotocol.io/introduction)
* [Anthropic MCP Servers](https://github.com/modelcontextprotocol/servers)
* [Google GenAI Toolbox](https://github.com/googleapis/genai-toolbox)
* [Smithery.AI](https://smithery.ai/)
* [Azure API Center Private MCP Servers](https://learn.microsoft.com/en-us/azure/api-center/register-discover-mcp-server)
* [Self-Hosted MCP Registry](https://github.com/modelcontextprotocol/registry)
-->

---

# Platform Integrations

## Major Platforms
- **[Zapier MCP](https://zapier.com/mcp)** - Workflow automation
- **[GitHub MCP](https://github.com/github/github-mcp-server)** - Repository management
- **[Azure MCP](https://github.com/Azure/azure-mcp)** - Cloud services

## Development Tools
- **[Playwright MCP](https://github.com/microsoft/playwright-mcp)** - Browser automation
- **[Teams AI MCP](https://microsoft.github.io/teams-ai/typescript/in-depth-guides/ai/mcp/mcp-server/)** - Microsoft Teams integration
- **[DevBox MCP](https://devblogs.microsoft.com/develop-from-the-cloud/)** - AI-powered development
- **[TypeSpec MCP](https://github.com/microsoft/typespec-mcp)** - API specification

<!--
Useful Links:
* [Zapier MCP](https://zapier.com/mcp)
* [Azure MCP](https://github.com/Azure/azure-mcp)
* [Github MCP](https://github.com/github/github-mcp-server)
* [Playwright MCP](https://github.com/microsoft/playwright-mcp)
* [Teams AIv2 MCP](https://microsoft.github.io/teams-ai/typescript/in-depth-guides/ai/mcp/mcp-server/)
* [Azure AI Agent Service MCP](https://github.com/azure-ai-foundry/mcp-foundry/blob/main/docs/azure-ai-agent-service-mcp-server.md)
* [Semgrap MCP](https://github.com/semgrep/mcp)
* [DevBox MCP](https://devblogs.microsoft.com/develop-from-the-cloud/supercharge-ai-development-with-new-ai-powered-features-in-microsoft-dev-box/#introducing-the-dev-box-mcp:-ai-native-control-for-your-development-environment)
* [TypeSpec MCP](https://github.com/microsoft/typespec-mcp)
-->

---

# Recent Integrations (Build 2025)

## Windows Ecosystem
- **Windows MCP Registry**
- **Windows Actions**
- **GitHub Copilot** integration

## Azure AI Services
- **[Azure Copilot Studio](https://learn.microsoft.com/en-us/microsoft-copilot-studio/agent-extend-action-mcp)**
- **[Azure AI Foundry](https://github.com/azure-ai-foundry/mcp-foundry)**
- **Azure AI Agent Services**
- **NLWeb** integration

<!--
New Integrations Since Build 2025:
* Windows MCP Registry
* Windows Actions
* Github Copilot
* [Azure Copilot Studio](https://learn.microsoft.com/en-us/microsoft-copilot-studio/agent-extend-action-mcp)
* [Azure AI Foundry](https://github.com/azure-ai-foundry/mcp-foundry)
* Azure AI Agent Services
* NLWeb
-->

---
layout: end
class: text-center
---

# Thank You!

## Questions?

**Learn more at [modelcontextprotocol.io](https://modelcontextprotocol.io)**

<!--
Thank you for your attention! MCP represents a significant step forward in standardizing AI tool integration. The protocol is rapidly evolving with strong community support and industry adoption.

For more information:
- Visit the official documentation at modelcontextprotocol.io
- Explore the GitHub repositories for servers and SDKs
- Join the community discussions and contribute to the ecosystem

Questions are welcome!
-->

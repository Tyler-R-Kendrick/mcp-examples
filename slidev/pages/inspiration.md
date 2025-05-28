---
layout: section
transition: slide-left
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

## NL 2 API Integration

- Enable existing APIs to be invokable via natural language.
- **Microservice bounded contexts** become agents.
- **Command/Query** become **Act/Ask**.
- **Tools** are the microservices in the domain.

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

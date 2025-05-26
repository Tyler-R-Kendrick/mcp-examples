# MCP outline

Anthropic's Model Context Protocol.

## What is it?

MCP is Anthropic's answer to model interactions as a service. It standardizes not only tool calling, but also how to handle and expose the context in which they operate.

MCP (Model Context Protocol) is a standard way for AI applications and agents to connect to and work with your data sources (e.g. local files, databases, or content repositories) and tools (e.g. GitHub, Google Maps, or Puppeteer).

Think of MCP as a universal adapter for AI applications, similar to what USB-C is for physical devices. USB-C acts as a universal adapter to connect devices to various peripherals and accessories. Similarly, MCP provides a standardized way to connect AI applications to different data and tools.

Before USB-C, you needed different cables for different connections. Similarly, before MCP, developers had to build custom connections to each data source or tool they wanted their AI application to work with—a time-consuming process that often resulted in limited functionality. Now, with MCP, developers can easily add connections to their AI applications, making their applications much more powerful from day one.

## What problems does it solve? Why is it needed?

### OpenAPI and API Manifests

LLMs need tools to interact with the world.

Several standards already exist to expose reusable code as a service, such as OpenAPI, AsyncAPI, and gRPC. These standards allow developers to define APIs in a machine-readable format, enabling automatic generation of client libraries and documentation.

Many frameworks leverage OpenAPI to import tool definitions and enable LLMs with new capabilities with little plumbing and labor, but this has limitations.

* For optimal performance, tool descriptions currently need to be tailored to the model. OpenAPI description may not be sufficient for accurate tool calling - causing more code to be written to accommodate the shortcomings of api descriptors.
* Even if the api spec has sufficient descriptors, service designs often are more abstract than the llms tasks we build for - and we often compose multiple api calls to achieve a single task. This is often not captured in the api spec, and requires additional code to be written to compose the calls.
* The current generation of LLMs suffer from a "lost-in-the-middle" problem; as the size of the context window increases, tool selection success decreases. If you look at enterprise api specs, they are often very large; it is often much better to only import the tools that are relevant to the task at hand, instead of every endpoint in the spec.

Api Manifests were introduced to limit the size/scope of the API description to only the relevant endpoints for client generation, but this still suffers from the same issue of tool descriptor optimization for models, and the composition issue.

### Langserve, process-isolation, and tools-as-a-service

Langserve (developed by the LangChain team) is a framework that allows developers to create and deploy invokable "chains" as services. It provides a way to expose tools over HTTP, enabling LLMs to call them as needed. However, it has also has its limitations:

* It is tightly coupled to the LangChain framework, making it difficult to use with other frameworks or languages.
* It is a one-way communication protocol, meaning that it does not support two-way communication between the LangChain server and client.
* It doesn't allow for tool discovery or dynamic tool registration, which limits its flexibility and adaptability.

To bring langserve capabilities to the dotnet ecosystem and compete with it, I also attempted a WebRPC based approach to host SemanticKernel as a service, allowing for two-way invocation in prompt and function filters that also enabled remote function calling, process isolation, and even the potential to host an SK kernel as a cloud appliance. This approach, however, also had its limitations:

* It was tightly coupled to the SemanticKernel framework, making it difficult to use with other frameworks or languages.

Developers still needed to write code in different languages, using their own framework of choice, and be able to reuse what the industry at large had contributed (both SK and LangChain).

### DSLs (LMQL)

A few attempts were made to create dsls that would allow developers to define tools in a more language-agnostic way, but because they lacked adoption, the plugin support across languages had never been implemented - coincidentally, causing the same problem of limiting adoption to the language/framework that created the DSL.

### The solution

Anthropic saw these issues and abstracted a level further to create a protocol completely abstracted away from the language and framework, allowing developers to define tools in a way that is independent of the underlying implementation. This allows for greater flexibility and adaptability.

#### How does it work?

* Json-RPC with configurable transport (HTTP, WebSocket, etc.)
* Progress/Cancellation/Ping
* Resources, Tools, and Prompts.

#### How to use it?

* Inspector
* Language specific SDK (python, typescript, java/kotlin, dotnet, etc.)
* Host a server (Docker in a multi-container dev-container)
* Create/load an mcp json config

## What can you do with it?

* Leverage resources endpoints as a way to abstract away file systems and replace framework specific abstractions (python's fsspec, dotnet's FileProvider apis, etc.) with NL queryable resource access. Build one that bridges your enterprise's Azure Storage and AWS S3 buckets, or even a local file system, and expose it as a resource endpoint that can be queried with NL.
* Infuse AI into existing applications through NLWeb and turn your existing app into an MCP server.
* Expose existing APIs as LLM tools. Microservice bounded contexts are a great fit for this. By adding a simple "Ask" or "Interact" mcp tool endpoints that register your microservice endpoints as internal tools, you can create kick-off integration points for command/query style interactions with your service - through MCP - adding similar capabilities as NLWeb, but for APIs.
* Wrap CLIs (like git) to simplify usage with NL and allow llms to be more effective at project scaffolding by leveraging existing developer tools.
* Combine MCP servers for emergent effects (think adding Playwright MCP, (A visual regression testing agent (like BackstopJS or Chromatic), and an Operator Agent to dynamically build regression suites).
* Build a "CodeAct" agent that can dynamically build and expose new functions as code, given a context.
* Create a server that ***is*** a client, to dynamically add/remove MCP servers and create adaptive systems (think of a universal agent that loads tools from disparate systems based on user queries - such as HR, IT, and Sales)

## What can it not yet do?

* It does not yet have a standard way to handle authentication and authorization for tools, which is a common requirement in many applications.
* It does not yet have a standard way to handle state management for tools, which is important for many applications that require persistence or session management.
* It does not yet have a standard way to handle error handling and retries for tools, which is important for many applications that require reliability and fault tolerance.
* It does not yet have a standard way to handle versioning of tools, which is important for many applications that require backward compatibility or feature evolution.
* It does not yet have a standard way to handle tool discovery and registration, which is important for many applications that require dynamic tool management.
* Doesn't handle json rpc batch requests.
* sampling doesn't sufficiently support agent-to-agent communication. 
* tool related notifications for [streaming http transports](https://github.com/modelcontextprotocol/modelcontextprotocol/issues/484) are not yet standardized.
* Multi-modal multi-block outputs aren't yet supported.
* Specifying external filesystems in client Roots [here](https://github.com/modelcontextprotocol/modelcontextprotocol/issues/505).

## Caviats

* Servers can declare the same tool names, resulting in conflicts on the client. The same goes for all named entities (resources, prompts, etc.). This is a known limitation of the protocol and should be handled by the client.
* The protocol is stateful.
* [server registry standards](https://github.com/orgs/modelcontextprotocol/discussions/159) are in-development.

## Where is it inappropriate to use?

* Workflow Orchestration
* Agent Orchestration
* ...

## Roadmap

The Model Context Protocol is rapidly evolving. This page outlines our current thinking on key priorities and direction for approximately the next six months, though these may change significantly as the project develops. To see what’s changed recently, check out the specification changelog.

[View the roadmap here](https://modelcontextprotocol.io/development/roadmap).

The ideas presented here are not commitments—we may solve these challenges differently than described, or some may not materialize at all. This is also not an exhaustive list; we may incorporate work that isn’t mentioned here.
We value community participation! Each section links to relevant discussions where you can learn more and contribute your thoughts.

For a technical view of our standardization process, visit the Standards Track on GitHub, which tracks how proposals progress toward inclusion in the official MCP specification.
​
### Validation
To foster a robust developer ecosystem, we plan to invest in:

Reference Client Implementations: demonstrating protocol features with high-quality AI applications
Compliance Test Suites: automated verification that clients, servers, and SDKs properly implement the specification
These tools will help developers confidently implement MCP while ensuring consistent behavior across the ecosystem.
​
### Registry
For MCP to reach its full potential, we need streamlined ways to distribute and discover MCP servers.

We plan to develop an MCP Registry that will enable centralized server discovery and metadata. This registry will primarily function as an API layer that third-party marketplaces and discovery services can build upon.

### Agents
As MCP increasingly becomes part of agentic workflows, we’re exploring improvements such as:

**Agent Graphs**: enabling complex agent topologies through namespacing and graph-aware communication patterns
**Interactive Workflows**: improving human-in-the-loop experiences with granular permissioning, standardized interaction patterns, and ways to directly communicate with the end user
​
### Multimodality
Supporting the full spectrum of AI capabilities in MCP, including:

**Additional Modalities**: video and other media types
**Streaming**: multipart, chunked messages, and bidirectional communication for interactive experiences

### Governance
We’re implementing governance structures that prioritize:

**Community-Led Development**: fostering a collaborative ecosystem where community members and AI developers can all participate in MCP’s evolution, ensuring it serves diverse applications and use cases
**Transparent Standardization**: establishing clear processes for contributing to the specification, while exploring formal standardization via industry bodies
​
## Resources

### Server Lists

* [Docker MCP Servers](https://www.docker.com/blog/announcing-docker-mcp-catalog-and-toolkit-beta/)
* [Anthropic MCP Docs](https://modelcontextprotocol.io/introduction)
* [Anthropic MCP Servers](https://github.com/modelcontextprotocol/servers)
* [Google GenAI Toolbox](https://github.com/googleapis/genai-toolbox)
* [Smithery.AI](https://smithery.ai/)
* [Azure API Center Private MCP Servers](https://learn.microsoft.com/en-us/azure/api-center/register-discover-mcp-server)
* [Self-Hosted MCP Registry](https://github.com/modelcontextprotocol/registry)

### Useful Links
* [Zapier MCP](https://zapier.com/mcp)
* [Azure MCP](https://github.com/Azure/azure-mcp)
* [Github MCP](https://github.com/github/github-mcp-server)
* [Playwright MCP](https://github.com/microsoft/playwright-mcp)
* [Teams AIv2 MCP](https://microsoft.github.io/teams-ai/typescript/in-depth-guides/ai/mcp/mcp-server/)
* [Azure AI Agent Service MCP](https://github.com/azure-ai-foundry/mcp-foundry/blob/main/docs/azure-ai-agent-service-mcp-server.md)
* [Semgrap MCP](https://github.com/semgrep/mcp)
* [DevBox MCP](https://devblogs.microsoft.com/develop-from-the-cloud/supercharge-ai-development-with-new-ai-powered-features-in-microsoft-dev-box/#introducing-the-dev-box-mcp:-ai-native-control-for-your-development-environment)
* [TypeSpec MCP](https://github.com/microsoft/typespec-mcp)

### New Integrations Since Build 2025

* Windows MCP Registry
* Windows Actions
* Github Copilot
* [Azure Copilot Studio](https://learn.microsoft.com/en-us/microsoft-copilot-studio/agent-extend-action-mcp)
* [Azure AI Foundry](https://github.com/azure-ai-foundry/mcp-foundry)
* Azure AI Agent Services
* NLWeb

---
layout: section
transition: slide-left
---

# The MCP Solution

## Language and Framework Agnostic Protocol

<!--
Anthropic recognized these fundamental issues and created a solution that abstracts away the language and framework dependencies entirely.
-->

---

# How MCP Works

## Core Architecture

<v-switch>

<template #1>

- **JSON-RPC** with configurable transport (HTTP, WebSocket, etc.)
- **Request/Response** & **Notifications**
- **Progress/Cancellation/Ping** support
- **Three Main Components**: Resources, Tools, and Prompts

</template>

<template #2>

![MCP Architecture](/assets/mcp-architecture.png)

</template>

</v-switch>

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

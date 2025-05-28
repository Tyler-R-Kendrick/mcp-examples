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

![MCP Simple Diagram](/assets/mcp-simple-diagram.png)

<!--
MCP (Model Context Protocol) is a standard way for AI applications and agents to connect to and work with your data sources and tools.

This means any API or existing codebase that you want a model to invoke. Or any data and/or file store that you want to make accessible.

MCP is Anthropic's answer to model interactions as a service. It standardizes not only tool calling, but also how to handle and expose the context in which they operate.
-->

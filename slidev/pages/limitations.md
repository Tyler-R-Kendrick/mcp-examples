---
layout: section
transition: slide-left
---

# Current Limitations

## What MCP Cannot Yet Do

<!--
While MCP is powerful, it's important to understand its current limitations and areas for future development.
-->

---

# Authentication & State Management

## Security & Access Control
- **IAM** still evolving
- **Security model** still evolving

## State & Session Management
- **No standard state management** for tools
- **Session persistence** requires custom implementation
- **Protocol is stateful** by design

<!--
* Its identity and access management integrations are still being actively developed.
* It does not yet have a standard way to handle state management for tools, which is important for many applications that require persistence or session management. Since the protocol is stateful by design, it requires custom implementation for session persistence.
-->

---

# Reliability & Versioning

## Error Handling
- **No standard error handling/retries** for tools
- **Reliability patterns** not yet standardized

## Tool Management
- **No standard versioning** of tools
- **No standard server discovery**
- **Tool name conflicts** possible between servers

<!--
* It does not yet have a standard way to handle error handling and retries for tools, which is important for many applications that require reliability and fault tolerance.
* It does not yet have a standard way to handle versioning of tools, which is important for many applications that require backward compatibility or feature evolution.
* Servers can declare the same tool names, resulting in conflicts on the client. The same goes for all named entities (resources, prompts, etc.). This is a known limitation of the protocol and should be handled by the client.
-->

---

# Protocol & Communication Limitations

## Current Protocol Gaps
- The protocol ***is stateful***.
- **No JSON-RPC batch requests** support
- **Streaming HTTP transports** notifications not standardized
- **Multi-modal multi-block outputs** not yet supported

## File System Integration
- **External filesystems** in client Roots not fully specified

<!--
* Doesn't handle json rpc batch requests.
* tool related notifications for [streaming http transports](https://github.com/modelcontextprotocol/modelcontextprotocol/issues/484) are not yet standardized.
* Multi-modal multi-block outputs aren't yet supported.
* Specifying external filesystems in client Roots [here](https://github.com/modelcontextprotocol/modelcontextprotocol/issues/505).
* The protocol is in active development; the specification is not yet finalized, and breaking changes may occur in future releases.
-->

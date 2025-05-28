---
layout: section
transition: slide-left
---

# What Problems Does MCP Solve?

## Why is standardization needed?

<!--
Before diving into the technical details, let's understand the challenges that led to the creation of MCP and why standardization in this space is so important.
-->

---
layout: two-cols-header
---

# Tool Integration Standards

::left::

<Transform :scale="0.8">

  ![Tool Integration Standards](/assets/quads.png)

</Transform>

::right::

- **Tool Description Optimization** needed for each model
- **Task Composition vs Service Decomposition** asymmetry
- **Context Bloat** and tool selection issues

<!--
LLMs need tools to interact with the world and api specifications make this a lot easier to implement. However, each of the existing standards have its own set of limitations when it comes to tool calling:

For optimal performance, tool descriptions currently benefit from being tailored to the model. OpenAPI descriptions may not be sufficient for accurate tool calling - causing more code to be written to accommodate the shortcomings of the api descriptors.

Even if the api spec has sufficient descriptors, service designs are often more abstract than the llms tasks we build for - and we often compose multiple api calls to achieve a single task. This is often not captured in the api spec, and requires additional code to be written to compose the calls for any given task.

The current generation of LLMs suffer from a "lost-in-the-middle" problem; as the size of the context window increases, tool selection success decreases. If you look at enterprise api specs, they are often very large; it is often much better to only import the tools that are relevant to the task at hand, instead of every endpoint in the spec.

For this reason, Api Manifests were introduced to limit the size/scope of the API description to only the relevant endpoints for client generation, but this still suffers from the same issue of tool descriptor optimization for models, and the composition issue.

Langserve (developed by the LangChain team) is a framework that allows developers to create and deploy invokable "chains" as services. It provides a way to expose tools over HTTP, enabling LLMs to call them as needed. However, it has also has its limitations:

* It is tightly coupled to the LangChain framework, making it difficult to use with other frameworks or languages.
* It is a one-way communication protocol, meaning that it does not support two-way communication between the LangChain server and client.
* It doesn't allow for tool discovery or dynamic tool registration, which limits its flexibility and adaptability.

To bring langserve capabilities to the dotnet ecosystem and compete with it, I also attempted a WebRPC based approach to host SemanticKernel as a service, allowing for two-way invocation in prompt and function filters that also enabled remote function calling, process isolation, and even the potential to host an SK kernel as a cloud appliance or PaaS service. This approach, however, also had its limitations:

* It was tightly coupled to the SemanticKernel framework, making it difficult to use with other frameworks or languages.

Developers still needed to write code in different languages, using their own framework of choice, and be able to reuse what the industry at large had contributed (both SK and LangChain).

To solve for the polyglot development and framework agnostic scenarios, a few attempts were made to create DSLs (such as LMQL) that would allow developers to define tools in a more language-agnostic way, but because they lacked adoption, the plugin support across languages had never been implemented - coincidentally, causing the same problem of limiting adoption to the language/framework that created the DSL.
-->

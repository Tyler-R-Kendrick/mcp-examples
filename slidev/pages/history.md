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
layout: default
---

# Current Challenges with API Specifications

Existing standards have limitations for LLM tool integration

---
layout: two-cols-header
---

# Current Challenges with API Specifications

::left::

```yaml
openapi: 3.0.0
info:
  title: GetWeather API
  version: 1.0.0
  description: Gets weather for a given location in Celsius.
paths:
  /getWeather/{location}:
    get:
      summary: Gets weather in Celsius
      description: Gets weather in Celsius for a specified location
      operationId: getWeather
      parameters:
        - name: location
          in: path
          description: Name of the location (e.g., city)
          required: true
          schema:
            type: string
      responses:
        '200':
          description: Weather in Celsius
          content:
            application/json:
              schema:
                type: object
                properties:
                  location:
                    type: string
                    example: "London"
                  temperature_celsius:
                    type: number
                    format: float
                    example: 18.5
                  condition:
                    type: string
                    example: "Partly cloudy"
                required:
                  - location
                  - temperature_celsius
                  - condition
}
```

::right::

- **Tool Description Optimization** needed for each model

<!--
LLMs need tools to interact with the world and api specifications make this a lot easier to implement. However, each of the existing standards have its own set of limitations when it comes to tool calling:

For optimal performance, tool descriptions currently benefit from being tailored to the model. OpenAPI descriptions may not be sufficient for accurate tool calling - causing more code to be written to accommodate the shortcomings of the api descriptors.
-->

---
layout: two-cols-header
---

# Current Challenges with API Specifications

::left::

```json
{
  "name": "get_weather",
  "description": "Fetches the weather in the given location",
  "strict": true,
  "parameters": {
    "type": "object",
    "properties": {
      "location": {
        "type": "string",
        "description": "The location to get the weather for"
      },
      "unit": {
        "type": "string",
        "description": "The unit to return the temperature in",
        "enum": ["F", "C"]
      }
    },
    "required": ["location", "unit"],
    "additionalProperties": false
  }
}
```

::right::

- **Tool Description Optimization** needed for each model
- **Task Composition vs Service Decomposition** asymmetry

<!--
Even if the api spec has sufficient descriptors, service designs are often more abstract than the llms tasks we build for - and we often compose multiple api calls to achieve a single task. This is often not captured in the api spec, and requires additional code to be written to compose the calls for any given task.
-->

---
layout: two-cols-header
---

# Current Challenges with API Specifications

::left::

<<< @/snippets/api.github.com.yaml yaml {monaco}

::right::

- **Tool Description Optimization** needed for each model
- **Task Composition vs Service Decomposition** asymmetry
- **Context Bloat** and tool selection issues

<!--
The current generation of LLMs suffer from a "lost-in-the-middle" problem; as the size of the context window increases, tool selection success decreases. If you look at enterprise api specs, they are often very large; it is often much better to only import the tools that are relevant to the task at hand, instead of every endpoint in the spec.

For this reason, Api Manifests were introduced to limit the size/scope of the API description to only the relevant endpoints for client generation, but this still suffers from the same issue of tool descriptor optimization for models, and the composition issue.
-->

---

# Framework-Specific Solutions

## LangServe
- Tightly coupled to LangChain framework
- One-way communication only
- No dynamic tool discovery

::right::

```python
#!/usr/bin/env python
"""Example LangChain server exposes a retriever."""
from fastapi import FastAPI
from langchain_community.vectorstores import FAISS
from langchain_openai import OpenAIEmbeddings

from langserve import add_routes

vectorstore = FAISS.from_texts(
    ["cats like fish", "dogs like sticks"], embedding=OpenAIEmbeddings()
)
retriever = vectorstore.as_retriever()

app = FastAPI(
    title="LangChain Server",
    version="1.0",
    description="Spin up a simple api server using Langchain's Runnable interfaces",
)
# Adds routes to the app for using the retriever under:
# /invoke
# /batch
# /stream
add_routes(app, retriever)

if __name__ == "__main__":
    import uvicorn

    uvicorn.run(app, host="localhost", port=8000)
```
---

# Framework-Specific Solutions

## Custom Solutions (WebRPC/SemanticKernel)
- Framework-specific implementations
- Limited cross-language compatibility
- Fragmented ecosystem

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
layout: two-cols
---

# Domain Specific Languages

```python
@lmql.query
def meaning_of_life():
    '''lmql
    # top-level strings are prompts
    "Q: What is the answer to life, the \
     universe and everything?"

    # generation via (constrained) variables
    "A: [ANSWER]" where \
        len(ANSWER) < 120 and STOPS_AT(ANSWER, ".")

    # results are directly accessible
    print("LLM returned", ANSWER)

    # use typed variables for guaranteed 
    # output format
    "The answer is [NUM: int]"

    # query programs are just functions 
    return NUM
    '''
```
::right::

<v-clicks>

- **LMQL** and other DSL attempts
- **Adoption Challenges** across languages
- **Plugin Support** never implemented
- **Same Problem** - limited to originating framework

</v-clicks>

<!--
To solve for the polyglot development and framework agnostic scenarios, a few attempts were made to create DSLs (such as LMQL) that would allow developers to define tools in a more language-agnostic way, but because they lacked adoption, the plugin support across languages had never been implemented - coincidentally, causing the same problem of limiting adoption to the language/framework that created the DSL.
-->

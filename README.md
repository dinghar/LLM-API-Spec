# LLM-Based API Interactions
REST APIs require that requests adhere to a strict structure in order to be processed. LLMs can be used to provide a more flexible means of communication. 

## Overview
API contracts are strict and inflexible. Specific URL structure, parameters, headers, data types, and more are required to communicate with a REST API. Client integration can be time consuming and limit the features an application can offer. By creating a flexible and naive interaction layer, integration can be drastically simplified and new capabilities can be enabled.

Compared to REST APIs, GraphQL is an approach at developing a more client-friendly approach to consuming an API. It can be purely additive to the existing API, for instance by exposing a `{baseUrl}/graphql` endpoint in addition the existing REST API.

Taking it a step further, an API could expose a `{baseUrl}/llm` endpoint that would offer greater flexibility. The endpoint could accept a single string parameter that would contain a description of the operation being requested. The server would have an API spec that detailed the API’s capabilities and specifics, which would be passed, along with the user’s request, to an LLM. The LLM would be used to generate an output. The exact form of the output needs to be fleshed out.

### Possible Outputs
- A request that the client can execute.
  ```json
  {
    url: "/products"
    method: GET
  }
  ```
- A request that the server can execute.
- Code to execute the user's request. This would be particularly useful for multi-step requests.
  ```typescript
  (() => {
    const a = foo();
    const b = bar(a);
  })();
  ```
- A rejection from the LLM. If the server can't be completed, the API needs to tell the user.

## Implementation Details

### Authentication
Authentication exists independent of this implementation. If an API is public, it can be neglected. Otherwise, authentication can be processed prior to any requests being sent to the LLM.

## Outstanding Questions
- What should the LLM output? See Possible Outputs section above.
- How much latency would this approach introduce?
- Is LLM output trustworthy enough for write operations?

## Alternatives Considered
- An upgraded HTTP handshake where the client and server exchange API specification information.

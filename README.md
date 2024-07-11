# LLM-Based API Interactions
REST APIs require that requests adhere to a strict structure in order to be processed. LLMs can be used to provide a more flexible means of communication. 

## Overview
API contracts are strict and inflexible. Specific URL structure, parameters, headers, data types, and more are required to communicate with a REST API. Client integration can be time consuming and limit the features an application can offer. By creating a flexible and naive interaction layer, integration can be drastically simplified and new capabilities can be enabled.

Compared to REST APIs, GraphQL is an approach at developing a more client-friendly approach to consuming an API. It can be purely additive to the existing API, for instance by exposing a `{baseUrl}/graphql` endpoint in addition the existing REST API.

Taking it a step further, an API could expose a `{baseUrl}/llm` endpoint that would offer greater flexibility. The endpoint could accept a single string parameter that would describe the operation being requested. The server would have an API spec which would be passed, along with the user’s request, to an LLM.

## Condsiderations

### Output
The output of the LLM would be structured instructions to complete the user's request. For example, it could output
- An object representing an HTTP request that the client or server can execute.
  ```json
  {
    "url": "/products",
    "method": "GET"
  }
  ```
- Code for the client or server to execute to fulfill the user's request. This would be useful for multi-step requests.
  ```typescript
  (() => {
    const a = foo();
    const b = bar(a);
  })();
  ```
- A rejection from the LLM. If the request can't be completed, the API needs to tell the user.

#### Client- vs. Server-Side Execution
The execution location discussion mostly boils down to security vs capability/performance. Allowing arbitrary code execution on the server is probably a non-starter for security reasons. Prompt injection seems too hard to prevent.

This is unforunate, because server-side execution has a number of benefits. For instance it can
- Call microservices directly
- Connect directly to the database
- Reduce round-trip client to server calls

There are also drawbacks to client-side execution. Concerns include
- Ensuring the output can be executed on the client. If the arbitrary code output approach is used, the client request would need to include metadata about its environment to ensure the code can be executed.

### User Review
One major concern is that an action would be taken that the user did not intend. For read operations, this is probably only a concern if the operation is long running and degrades user experience. For write operations, this is a far bigger concern. Unintentionally deleting or altering information is possible.

### Authentication
Authentication exists independent of this architecture. If an API is public, it can be neglected. Otherwise, authentication can be processed prior to any requests being sent to the LLM. This is up to the developer.

## Recommendation
If an architecture like this were to be adopted, it should probably be done in a limited capacity. To ensure security and data integrity, the best approach would be to have the LLM return objects that represent HTTP requests that the client can execute. The server can require that any objects returned must be GET requests, helping ensure data integrity (though this can fail in the case of poor API design). Unforuntately data fetching would be limited to single API calls, but there are many use cases where this would be adequate.

## Outstanding Questions
- How much latency would this approach introduce?

## Alternatives Considered
- An upgraded HTTP handshake where the client and server exchange API specification information.

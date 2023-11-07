## stateless vs stateful

- definition
  - Stateless and stateful are terms used to describe the behavior of servers in a client-server architecture.

- In a stateless architecture
  - the server does not keep track of any information or data about the client's past interactions. Each request from the client is treated as a new and independent request, and the server does not retain any information about previous requests.
  - Stateless architectures are usually simpler and more scalable because they do not need to store and manage information about each client's past interactions. However, stateless architectures may be less efficient when dealing with complex requests that require multiple interactions between the client and server.

- In a stateful architecture
  - the server keeps track of information and data about the client's past interactions. This information is stored in memory or in a database, and is used to customize the client's experience and provide personalized responses.
  - Stateful architectures can provide a more personalized and efficient experience for the client, as the server can use the stored information to make more informed decisions about how to respond to the client's requests. However, stateful architectures are usually more complex and require more resources to manage the data about each client's interactions.
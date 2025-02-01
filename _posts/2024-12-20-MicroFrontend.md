---
title: Micro Frontend
date: 2024-12-20 10:30:30 +/-TTTT
categories: [Architecture, Micro Frontend]
tags: [angular, module federation]     # TAG names should always be lowercase
description: This post provides the explanaton fo an reference implementation
mermaid: true
---

# Micro Frontend
Micro Frontend is a new architectural style for building web applications. It is an extension of the microservices architecture to the frontend world. It allows you to break down your large frontend application into smaller, more manageable pieces, which can be developed, deployed, and maintained independently.
This allows us to break a big monilith UI into smaller pieces of modules/screens that can be clubbed together to form a single UI. This helps in better maintainability, scalability and reusability of the code.


## Micro Frontend Architecture
As discussed above, Micro Frontend focuses on breaking down the monolith application into smaller pieces. Each small piece is called a Micro Frontend and loaded inside a shell called Host application.
Each micro frontend can be developed using a different technology, framework, and language. They can be developed by different teams and can be deployed independently. The host utlizies a configuration approach to load these micro frontends at runtime to form a uniform single UI experience for users.

```mermaid
  flowchart LR
    A[Host Application] --> B[Micro Frontend 1]
    A --> C[Micro Frontend 2]
```

```mermaid
 architecture-beta
    group api(cloud)[Micro Frontend]

    service db(database)[Database] in api
    service disk1(disk)[Storage] in api
    service disk2(disk)[Storage] in api
    service server(server)[Server] in api

    db:L -- R:server
    disk1:T -- B:server
    disk2:T -- B:db    
```

### Ways to implement Micro Frontend

### Benefits of using Micro Frontend

### Challenges of using Micro Frontend

## Micro Frontend with Angular

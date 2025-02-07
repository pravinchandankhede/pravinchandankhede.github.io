---
title: Micro Frontend Reference Implementation using Angular's Module Federation
date: 2024-01-10 10:30:30 +/-TTTT
categories: [Architecture, Micro Frontend, Micro Services, Dynamic Loading]
tags: [angular, module federation, modules, webpack, lazyloading]     # TAG names should always be lowercase
description: In this post I will explain the apporach to implement a micro frontend architecture. We will see how to define multiple child applications and load them in a shell. I will also show how to define common functionality that can be utilized by all child applications.
mermaid: true
---


 >Micro Frontend is a new architectural style for building web applications. It is an extension of the microservices architecture to the frontend world. It allows you to break down your large frontend application into smaller, more manageable pieces, which can be developed, deployed, and maintained independently.
 {: .prompt-info }

 This post is continuation to my earlier post on [Micro Frontend Architecture](https://pravinchandankhede.github.io/posts/MicroFrontend/). In this post I will explain how to implement Micro Frontend architecture using Angular. We will see how to define multiple child applications and load them in a shell. I will also show how to define common functionlaity that can be utilized by all child applications.

## Reference Implementation App using Angular's Module Federation.

You can find a working demo of the implementation [here](https://agreeable-sand-05b276e0f.4.azurestaticapps.net/)

## Reference Implementation code using Angular's Module Federation.

The source code is available [here](https://github.com/pravinchandankhede/microfrontend)

## Structure of the application
This application demonstrates a micro frontend app build on Angular. It has a shell application and a child applications with multiple modules. The shell application is the main application that loads the child applications. The child applications are loaded dynamically using Angular's Module Federation.

> For this architecture, we would be using [Webpack](https://webpack.js.org/) based [Module Federation](https://webpack.js.org/concepts/module-federation/) implementation of Angular provided by [Angular Architects](https://www.npmjs.com/package/@angular-architects/module-federation) team.
{: .prompt-info }





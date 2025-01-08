---
title: Angular Modules
date: 2024-11-06 10:30:30 +/-TTTT
categories: [Architecture, Frontend, Modular Designs]
tags: [angular, modules, components, elements]     # TAG names should always be lowercase
description: In this post, I will discuss the importance of Angular Modules and how to use them effectively.
---

# Angular Modules
Angular Modules are a way to organize your Angular application into cohesive blocks of functionality. Each Angular Module is a class with the @NgModule decorator. The @NgModule decorator is a function that takes a single metadata object whose properties describe the module. 
The most important properties are: 
  - declarations: the view classes that belong to this module. Angular has three kinds of view classes: components, directives, and pipes.

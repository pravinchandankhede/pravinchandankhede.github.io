---
title: Angular Elements
date: 2024-12-07 10:30:30 +/-TTTT
categories: [Architecture, Angular]
tags: [elements]     # TAG names should always be lowercase
description: This post highlights the usage of Angular Elements to create reusable features.
---

# Angular Elements

## What are Angular Elements?

Angular Elements are Angular components packaged as custom elements (also known as Web Components). These custom elements are part of the Web Components standard, allowing developers to create reusable, encapsulated HTML elements that work across frameworks and libraries.

With Angular Elements, you can take an Angular component and make it available as a standalone custom element that can be used in any HTML page or application, regardless of whether it uses Angular, React, Vue, or plain JavaScript.

---

## Why Use Angular Elements?

Angular Elements provide several benefits:

1. **Framework Agnostic**: They can be used in non-Angular applications, making them highly reusable.
2. **Encapsulation**: They encapsulate styles and logic, ensuring that they don't interfere with the rest of the application.
3. **Interoperability**: They follow the Web Components standard, making them compatible with modern browsers and other frameworks.
4. **Ease of Use**: Once created, they can be used like any other HTML element.

---

## How to Create Angular Elements?

Here’s a step-by-step guide to creating Angular Elements:

### Step 1: Install Angular Elements Package

First, install the Angular Elements package in your Angular project:

```bash
npm install @angular/elements
```

### Step 2: Create an Angular Component
Create a component that you want to convert into an Angular Element. For example:

```typescript
// filepath: src/app/hello-world/hello-world.component.ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-hello-world',
  template: `<p>Hello, World!</p>`,
  styles: [`p { font-size: 20px; color: blue; }`]
})
export class HelloWorldComponent {}
```

### Step 3: Convert the Component into a Custom Element
In your AppModule, use the createCustomElement function to convert the component into a custom element:

```typescript
// filepath: src/app/app.module.ts
import { NgModule, Injector } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { createCustomElement } from '@angular/elements';
import { HelloWorldComponent } from './hello-world/hello-world.component';

@NgModule({
  declarations: [HelloWorldComponent],
  imports: [BrowserModule],
  providers: [],
  bootstrap: [] // No bootstrap component for Angular Elements
})
export class AppModule {
  constructor(private injector: Injector) {
    const helloWorldElement = createCustomElement(HelloWorldComponent, { injector });
    customElements.define('hello-world', helloWorldElement);
  }

  ngDoBootstrap() {}
}
```

### Step 4: Build and Use the Custom Element
Build your Angular project using the following command:

```bash
ng build --prod
```

The output will be in the dist folder. You can include the generated JavaScript files in any HTML file and use the custom element like this:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Angular Element Example</title>
</head>
<body>
  <hello-world></hello-world>
  <script src="main.js"></script>
</body>
</html>
```

## Use Cases for Angular Elements
 - **Micro-Frontend Architecture**: Share components across multiple applications.
 - **Widget Development**: Create reusable widgets like date pickers, charts, or modals.
 - **Legacy Integration**: Use Angular components in non-Angular or legacy applications.

## Conclusion
Angular Elements bridge the gap between Angular and other frameworks by enabling the creation of reusable, framework-agnostic components. They are a powerful tool for building modern, interoperable web applications.

Start experimenting with Angular Elements today and unlock the potential of Web Components in your projects!
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
import { Component, Input } from '@angular/core';

@Component({
    selector: 'app-hello-world',
    standalone: false,
    templateUrl: './hello-world.component.html',
    styleUrl: './hello-world.component.css'
})
export class HelloWorldComponent {
    @Input() name: string = 'World';
}
```

### Step 3: Convert the Component into a Custom Element
In your AppModule, use the createCustomElement function to convert the component into a custom element:

```typescript
import { Injector, NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { createCustomElement } from '@angular/elements';
import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';
import { HelloWorldComponent } from './hello-world/hello-world.component';

@NgModule({
    declarations: [
        AppComponent,
        HelloWorldComponent
    ],
    imports: [
        BrowserModule,
        AppRoutingModule
    ],
    providers: [],
})
export class AppModule {

    constructor(private injector: Injector) {
        const helloWorldElement = createCustomElement(HelloWorldComponent, { injector });
        customElements.define('app-hello-world', helloWorldElement);
    }

    ngDoBootstrap() {
    }
}
```

### Use the newly defined anuglar element
In your Index.html, instead of using the <app-root> element use the <app-hello-world> element

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>AngularElements</title>
  <base href="/">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link rel="icon" type="image/x-icon" href="favicon.ico">
</head>
<body>
    <!--<app-root></app-root>-->
    <app-hello-world></app-hello-world>
</body>
</html>
```

### Step 4: Build and Use the Custom Element
Build and run your Angular project using the following command:

```bash
ng serve -o
```

This will open the browser and load the element inside it.

![run sample](/assets/images/posts/2024-12-07/image.png)

## Use Cases for Angular Elements
 - **Micro-Frontend Architecture**: Share components across multiple applications.
 - **Widget Development**: Create reusable widgets like date pickers, charts, or modals.
 - **Legacy Integration**: Use Angular components in non-Angular or legacy applications.

## Conclusion
Angular Elements bridge the gap between Angular and other frameworks by enabling the creation of reusable, framework-agnostic components. They are a powerful tool for building modern, interoperable web applications.

Start experimenting with Angular Elements today and unlock the potential of Web Components in your projects!
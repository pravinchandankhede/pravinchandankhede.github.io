---
title: Angular Modules
date: 2024-11-06 10:30:30 +/-TTTT
categories: [Architecture, Frontend, Modular Designs]
tags: [angular, modules, components, elements]     # TAG names should always be lowercase
description: In this post, I will discuss the importance of Angular Modules and how to use them effectively.
---

# Exploring Angular Modules

## Introduction
Angular modules are a powerful feature that allows developers to organize and manage their code efficiently. In this article, we will explore various use cases of Angular modules through a fictitious company named "Fast Investment." We will create a project with modules for banking, mutual funds, and stocks, and provide code samples to illustrate their usage.

## Table of Contents
1. Overview of Angular Modules
2. Setting Up the Project
3. Banking Module
    1. Creating the Banking Module
    2. Banking Components
        1. Account Summary Component
        2. Transaction History Component
4. Mutual Funds Module
    1. Creating the Mutual Funds Module
    2. Mutual Funds Components
        1. Fund Overview Component
        2. Investment Calculator Component
5. Stocks Module
    1. Creating the Stocks Module
    2. Stocks Components
        1. Stock Portfolio Component
        2. Market Trends Component
6. Shared Module
    1. Creating the Shared Module
    2. Shared Components and Services
7. Conclusion

## Overview of Angular Modules
Angular modules help in organizing an application into cohesive blocks of functionality. Each module can contain components, services, directives, and pipes that are related to a specific feature or functionality. This modular approach enhances code maintainability, reusability, and scalability.

The overall setup will look like this
![Overall Architecture](/assets/images/posts/2024-11-06/overall.jpg)


## Setting Up the Project
To get started, we will create a new Angular project named `fast-investment` using the Angular CLI.

```bash
ng new fast-investment
cd fast-investment
```

## Banking Module

### Creating the Banking Module
First, let's create the banking module.

```bash
ng generate module banking
```

### Banking Components

#### Account Summary Component
We will create an `AccountSummaryComponent` to display the user's account summary.

```bash
ng generate component banking/account-summary
```

**account-summary.component.ts**

```typescript
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-account-summary',
  templateUrl: './account-summary.component.html',
  styleUrls: ['./account-summary.component.css']
})
export class AccountSummaryComponent implements OnInit {
  accountBalance: number = 5000;

  constructor() { }

  ngOnInit(): void {
  }
}
```

**account-summary.component.html**

```html
<div>
  <h2>Account Summary</h2>
  <p>Account Balance: {{ accountBalance | currency }}</p>
</div>
```

#### Transaction History Component
Next, we will create a `TransactionHistoryComponent` to display the user's transaction history.

```bash
ng generate component banking/transaction-history
```

**transaction-history.component.ts**

```typescript
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-transaction-history',
  templateUrl: './transaction-history.component.html',
  styleUrls: ['./transaction-history.component.css']
})
export class TransactionHistoryComponent implements OnInit {
  transactions = [
    { date: '2025-01-01', amount: -100, description: 'Grocery Shopping' },
    { date: '2025-01-05', amount: 2000, description: 'Salary' },
    { date: '2025-01-10', amount: -50, description: 'Electricity Bill' }
  ];

  constructor() { }

  ngOnInit(): void {
  }
}
```

**transaction-history.component.html**

```html
<div>
  <h2>Transaction History</h2>
  <ul>
    <li *ngFor="let transaction of transactions">
      {{ transaction.date }} - {{ transaction.description }}: {{ transaction.amount | currency }}
    </li>
  </ul>
</div>
```

## Mutual Funds Module

### Creating the Mutual Funds Module
Let's create the mutual funds module.

```bash
ng generate module mutual-funds
```

### Mutual Funds Components

#### Fund Overview Component
We will create a `FundOverviewComponent` to display an overview of mutual funds.

```bash
ng generate component mutual-funds/fund-overview
```

**fund-overview.component.ts**

```typescript
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-fund-overview',
  templateUrl: './fund-overview.component.html',
  styleUrls: ['./fund-overview.component.css']
})
export class FundOverviewComponent implements OnInit {
  funds = [
    { name: 'Equity Fund', value: 10000 },
    { name: 'Debt Fund', value: 5000 },
    { name: 'Hybrid Fund', value: 7500 }
  ];

  constructor() { }

  ngOnInit(): void {
  }
}
```

**fund-overview.component.html**

```html
<div>
  <h2>Fund Overview</h2>
  <ul>
    <li *ngFor="let fund of funds">
      {{ fund.name }}: {{ fund.value | currency }}
    </li>
  </ul>
</div>
```

#### Investment Calculator Component
Next, we will create an `InvestmentCalculatorComponent` to help users calculate their potential returns.

```bash
ng generate component mutual-funds/investment-calculator
```

**investment-calculator.component.ts**

```typescript
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-investment-calculator',
  templateUrl: './investment-calculator.component.html',
  styleUrls: ['./investment-calculator.component.css']
})
export class InvestmentCalculatorComponent implements OnInit {
  principal: number = 1000;
  rateOfReturn: number = 5;
  years: number = 10;
  futureValue: number = 0;

  constructor() { }

  ngOnInit(): void {
  }

  calculateFutureValue(): void {
    this.futureValue = this.principal * Math.pow((1 + this.rateOfReturn / 100), this.years);
  }
}
```

**investment-calculator.component.html**

```html
<div>
  <h2>Investment Calculator</h2>
  <label for="principal">Principal:</label>
  <input id="principal" [(ngModel)]="principal" type="number">
  <label for="rateOfReturn">Rate of Return (%):</label>
  <input id="rateOfReturn" [(ngModel)]="rateOfReturn" type="number">
  <label for="years">Years:</label>
  <input id="years" [(ngModel)]="years" type="number">
  <button (click)="calculateFutureValue()">Calculate</button>
  <p>Future Value: {{ futureValue | currency }}</p>
</div>
```

## Stocks Module

### Creating the Stocks Module
Let's create the stocks module.

```bash
ng generate module stocks
```

### Stocks Components

#### Stock Portfolio Component
We will create a `StockPortfolioComponent` to display the user's stock portfolio.

```bash
ng generate component stocks/stock-portfolio
```

**stock-portfolio.component.ts**

```typescript
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-stock-portfolio',
  templateUrl: './stock-portfolio.component.html',
  styleUrls: ['./stock-portfolio.component.css']
})
export class StockPortfolioComponent implements OnInit {
  stocks = [
    { name: 'AAPL', quantity: 10, price: 150 },
    { name: 'GOOGL', quantity: 5, price: 2800 },
    { name: 'AMZN', quantity: 2, price: 3400 }
  ];

  constructor() { }

  ngOnInit(): void {
  }
}
```

**stock-portfolio.component.html**

```html
<div>
  <h2>Stock Portfolio</h2>
  <ul>
    <li *ngFor="let stock of stocks">
      {{ stock.name }}: {{ stock.quantity }} shares @ {{ stock.price | currency }}
    </li>
  </ul>
</div>
```

#### Market Trends Component
Next, we will create a `MarketTrendsComponent` to display current market trends.

```bash
ng generate component stocks/market-trends
```

**market-trends.component.ts**

```typescript
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-market-trends',
  templateUrl: './market-trends.component.html',
  styleUrls: ['./market-trends.component.css']
})
export class MarketTrendsComponent implements OnInit {
  trends = [
    { name: 'Tech', change: 2.5 },
    { name: 'Healthcare', change: -1.2 },
    { name: 'Finance', change: 0.8 }
  ];

 
 Absolutely, let's continue and complete the blog article.

```markdown
## Shared Module

### Creating the Shared Module
To avoid code duplication and promote reusability, we will create a shared module for common components and services.

```bash
ng generate module shared
```

### Shared Components and Services

#### Header Component
We will create a `HeaderComponent` to display a common header across the application.

```bash
ng generate component shared/header
```

**header.component.ts**

```typescript
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-header',
  templateUrl: './header.component.html',
  styleUrls: ['./header.component.css']
})
export class HeaderComponent implements OnInit {
  companyName: string = 'Fast Investment';

  constructor() { }

  ngOnInit(): void {
  }
}
```

**header.component.html**

```html
<header>
  <h1>{{ companyName }}</h1>
  <nav>
    <a routerLink="/banking">Banking</a>
    <a routerLink="/mutual-funds">Mutual Funds</a>
    <a routerLink="/stocks">Stocks</a>
  </nav>
</header>
```

#### Footer Component
Next, we will create a `FooterComponent` to display a common footer across the application.

```bash
ng generate component shared/footer
```

**footer.component.ts**

```typescript
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-footer',
  templateUrl: './footer.component.html',
  styleUrls: ['./footer.component.css']
})
export class FooterComponent implements OnInit {
  currentYear: number = new Date().getFullYear();

  constructor() { }

  ngOnInit(): void {
  }
}
```

**footer.component.html**

```html
<footer>
  <p>&copy; {{ currentYear }} Fast Investment. All rights reserved.</p>
</footer>
```

#### AuthService
We will create an `AuthService` to handle authentication across the application.

```bash
ng generate service shared/auth
```

**auth.service.ts**

```typescript
import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root'
})
export class AuthService {
  private isAuthenticated: boolean = false;

  constructor() { }

  login(username: string, password: string): boolean {
    // Simulate authentication
    if (username === 'user' && password === 'password') {
      this.isAuthenticated = true;
      return true;
    }
    return false;
  }

  logout(): void {
    this.isAuthenticated = false;
  }

  isLoggedIn(): boolean {
    return this.isAuthenticated;
  }
}
```

## Conclusion
In this article, we explored the different use cases of Angular modules through a fictitious company named "Fast Investment." We created a project with modules for banking, mutual funds, and stocks, and provided code samples to illustrate their usage. By organizing our application into cohesive blocks of functionality, we can enhance code maintainability, reusability, and scalability.

Angular modules are a powerful tool for any Angular developer, and understanding how to use them effectively can greatly improve the structure and efficiency of your applications. We hope this article has provided you with valuable insights and practical examples to help you get started with Angular modules.

Happy coding!

```

Feel free to let me know if you need any further additions or modifications!

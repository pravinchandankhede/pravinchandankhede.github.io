---
title: Cache in Angular
date: 2023-11-10 10:30:30 +/-TTTT
categories: [Architecture, Angular, Caching, PWA]
tags: [angular, cache, services, pwa, client, server, in-memory, interceptor]     # TAG names should always be lowercase
description: This post explains the importance of caching in Angular and how to use it effectively.
---

# Cache in Angular
In the world of web development, performance is key. Users expect fast, responsive applications, and one way to achieve this is through caching. Caching involves storing data locally so that it can be quickly retrieved without making repeated network requests. 

In this blog post, I will explain about various caching strategies in Angular, using a portfolio application for a fictional firm called "Fast Investments." This application includes modules for banking, stocks, and mutual funds.

> **Info** This sample application builds on the code developed in the previous blog post on Angular Modules. If you haven't read that post, you can find it here: [Angular Modules](https://pravinchandankhede.github.io/posts/AngularModules/).
{: .prompt-info }


## Importance of Caching
Caching plays a vital role in optimizing web applications by reducing latency and improving performance. Here are some key benefits of caching:

 - **Improved Performance**: By storing frequently accessed data locally, client-side caching reduces the need for repeated network requests. This leads to faster load times and a more responsive application, enhancing the overall user experience.

 - **Reduced Server Load**: Caching data on the client-side decreases the number of requests sent to the server. This helps in reducing the server load and can lead to better scalability and performance of the backend services.

 - **Offline Access**: Client-side caching allows users to access certain parts of the application even when they are offline. This is particularly useful for applications that need to function in environments with intermittent or no internet connectivity.

 - **Cost Savings**: By minimizing the number of network requests, client-side caching can help reduce data transfer costs, especially in scenarios where data usage is metered or limited.

 - **Enhanced User Experience**: With faster data retrieval and reduced loading times, users experience smoother and more seamless interactions with the application. This can lead to higher user satisfaction and retention rates.

## Implementing Caching in Angular
Caching is the process of storing data locally to reduce the need for repeated network requests. In Angular applications, caching can be implemented using various techniques and strategies. Let's explore some common caching strategies and how to implement them in an Angular application.

### Types of Caching

#### 1. Client-side caching
This involves storing data on the client-side using mechanisms like local storage, session storage, or in-memory storage.

#### 2. Server-side caching
This involves storing data on the server to reduce the load on the backend and improve response times.

### Caching Strategies

#### 1. Cache-first strategy
This strategy attempts to retrieve data from the cache first, and only makes a network request if the data is not available.

#### 2. Network-first strategy
This strategy always makes a network request first, and falls back to the cache if the network request fails.

## Client-side Caching

### Using Angular Service Worker for Caching
Angular Service Worker can be used to cache static assets and API responses. This approach enables your app to be a PWA for which you need to install the required package. Once package is installed, then provide the configuration for items you want to cache as part of PWA.

 1. Install the Angular PWA package:

```bash
ng add @angular/pwa
```
 2. The ngsw-config.json file is used to configure the Angular Service Worker, which helps in caching static assets and API responses to improve the performance and offline capabilities of Angular application.
Configure the service worker in ngsw-config.json:

```json
{
  "index": "/index.html",
  "assetGroups": [
    {
      "name": "app",
      "installMode": "prefetch",
      "resources": {
        "files": [
          "/favicon.ico",
          "/index.html",
          "/*.css",
          "/*.js"
        ]
      }
    }
  ],
  "dataGroups": [
    {
      "name": "api",
      "urls": ["/api/**"],
      "cacheConfig": {
        "maxSize": 5000,
        "maxAge": "5m",
        "strategy": "performance"
      }
    }
  ]
}
```

### Implementing HttpInterceptor for Caching
An HttpInterceptor can be used to cache HTTP requests. In this caching, we create an interceptor which gets invoke for each API call being done from application. This gives us an opputurnity to implement the technique to first read from cache. If data is present in cache it is returned else it allows the netwrok call and subsequently caches the data for later use.
Here's an example:

```typescript
import { Injectable } from '@angular/core';
import { HttpInterceptor, HttpRequest, HttpHandler, HttpEvent } from '@angular/common/http';
import { Observable, of } from 'rxjs';
import { tap } from 'rxjs/operators';

@Injectable()
export class CachingInterceptor implements HttpInterceptor {
  private cache = new Map<string, any>();

  intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
    if (req.method !== 'GET') {
      return next.handle(req);
    }

    const cachedResponse = this.cache.get(req.url);
    if (cachedResponse) {
      return of(cachedResponse);
    }

    return next.handle(req).pipe(
      tap(event => {
        this.cache.set(req.url, event);
      })
    );
  }
}
```

### Local Storage Caching
Local storage can be used to persist data across sessions. Here's an example service for caching data in local storage:

```typescript
import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root'
})
export class LocalStorageService {
  setItem(key: string, data: any): void {
    localStorage.setItem(key, JSON.stringify(data));
  }

  getItem(key: string): any {
    const data = localStorage.getItem(key);
    return data ? JSON.parse(data) : null;
  }

  removeItem(key: string): void {
    localStorage.removeItem(key);
  }

  clear(): void {
    localStorage.clear();
  }
}
```

### Session Storage Caching
Session storage is similar to local storage but data is cleared when the browser or tab is closed. Here's an example service for caching data in session storage:

```typescript
import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root'
})
export class SessionStorageService {
  setItem(key: string, data: any): void {
    sessionStorage.setItem(key, JSON.stringify(data));
  }

  getItem(key: string): any {
    const data = sessionStorage.getItem(key);
    return data ? JSON.parse(data) : null;
  }

  removeItem(key: string): void {
    sessionStorage.removeItem(key);
  }

  clear(): void {
    sessionStorage.clear();
  }
}
```

### In-Memory Caching
In-memory caching can be used for temporary storage of data within the application. Here's an example service for in-memory caching:

```typescript
import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root'
})
export class InMemoryCacheService {
  private cache = new Map<string, any>();

  setItem(key: string, data: any): void {
    this.cache.set(key, data);
  }

  getItem(key: string): any {
    return this.cache.get(key);
  }

  removeItem(key: string): void {
    this.cache.delete(key);
  }

  clear(): void {
    this.cache.clear();
  }
}
```

### Server-side Caching
Implementing Server-side Caching with Angular Universal
Server-side rendering (SSR) with Angular Universal can improve performance by caching rendered HTML on the server. Here's how to set it up:

 - Add Angular Universal to your project:

```bash
ng add @nguniversal/express-engine
```

 - Configure server-side caching in server.ts:

```typescript
import 'zone.js/dist/zone-node';
import { ngExpressEngine } from '@nguniversal/express-engine';
import * as express from 'express';
import { join } from 'path';
import { AppServerModule } from './src/main.server';
import { APP_BASE_HREF } from '@angular/common';
import { existsSync } from 'fs';

const app = express();
const PORT = process.env.PORT || 4000;
const DIST_FOLDER = join(process.cwd(), 'dist/fast-investments/browser');

app.engine('html', ngExpressEngine({
  bootstrap: AppServerModule,
}));

app.set('view engine', 'html');
app.set('views', DIST_FOLDER);

app.get('*.*', express.static(DIST_FOLDER, {
  maxAge: '1y'
}));

app.get('*', (req, res) => {
  res.render('index', { req, providers: [{ provide: APP_BASE_HREF, useValue: req.baseUrl }] });
});

app.listen(PORT, () => {
  console.log(`Node Express server listening on http://localhost:${PORT}`);
});
```

## Caching in Banking Module
Implementing Caching for Banking Data
Here's an example service for caching banking data using local storage:

```typescript
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable, of } from 'rxjs';
import { map } from 'rxjs/operators';
import { LocalStorageService } from './local-storage.service';

@Injectable({
  providedIn: 'root'
})
export class BankingService {
  private cacheKey = 'bankingData';

  constructor(private http: HttpClient, private localStorageService: LocalStorageService) {}

  getBankingData(): Observable<any> {
    const cachedData = this.localStorageService.getItem(this.cacheKey);
    if (cachedData) {
      return of(cachedData);
    }

    return this.http.get('/api/banking').pipe(
      map(response => {
        this.localStorageService.setItem(this.cacheKey, response);
        return response;
      })
    );
  }
}
```

## Caching in Stocks Module
Implementing Caching for Stock Data
Here's an example service for caching stock data using session storage:

```typescript
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable, of } from 'rxjs';
import { map } from 'rxjs/operators';
import { SessionStorageService } from './session-storage.service';

@Injectable({
  providedIn: 'root'
})
export class StocksService {
  private cacheKey = 'stockData';

  constructor(private http: HttpClient, private sessionStorageService: SessionStorageService) {}

  getStockData(): Observable<any> {
    const cachedData = this.sessionStorageService.getItem(this.cacheKey);
    if (cachedData) {
      return of(cachedData);
    }

    return this.http.get('/api/stocks').pipe(
      map(response => {
        this.sessionStorageService.setItem(this.cacheKey, response);
        return response;
      })
    );
  }
}
```

## Caching in Mutual Fund Module
Implementing Caching for Mutual Fund Data
Here's an example service for caching mutual fund data using in-memory storage:

```typescript
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable, of } from 'rxjs';
import { map } from 'rxjs/operators';
import { InMemoryCacheService } from './in-memory-cache.service';

@Injectable({
  providedIn: 'root'
})
export class MutualFundService {
  private cacheKey = 'mutualFundData';

  constructor(private http: HttpClient, private inMemoryCacheService: InMemoryCacheService) {}

  getMutualFundData(): Observable<any> {
    const cachedData = this.inMemoryCacheService.getItem(this.cacheKey);
    if (cachedData) {
      return of(cachedData);
    }

    return this.http.get('/api/mutual-funds').pipe(
      map(response => {
        this.inMemoryCacheService.setItem(this.cacheKey, response);
        return response;
      })
    );
  }
}
```

## Advanced Caching Techniques

### Using IndexedDB for Local Database Caching
IndexedDB is a low-level API for storing large amounts of structured data, including files and blobs. Here's an example service for caching data using IndexedDB:

Install the idb package:

```bash
npm install idb
```

Create an IndexedDB service:

```typescript
import { Injectable } from '@angular/core';
import { openDB, DBSchema, IDBPDatabase } from 'idb';

interface MyDB extends DBSchema {
  'keyval': {
    key: string;
    value: any;
  };
}

@Injectable({
  providedIn: 'root'
})
export class IndexedDBService {
  private dbPromise: Promise<IDBPDatabase<MyDB>>;

  constructor() {
    this.dbPromise = openDB<MyDB>('my-database', 1, {
      upgrade(db) {
        db.createObjectStore('keyval');
      }
    });
  }

  async set(key: string, val: any): Promise<void> {
    const db = await this.dbPromise;
    await db.put('keyval', val, key);
  }

  async get(key: string): Promise<any> {
    const db = await this.dbPromise;
    return db.get('keyval', key);
  }

  async delete(key: string): Promise<void> {
    const db = await this.dbPromise;
    await db.delete('keyval', key);
  }

  async clear(): Promise<void> {
    const db = await this.dbPromise;
    await db.clear('keyval');
  }

  async keys(): Promise<string[]> {
    const db = await this.dbPromise;
    return db.getAllKeys('keyval');
  }
}
```

Use the IndexedDB service in a component or another service:

```typescript
import { Component, OnInit } from '@angular/core';
import { IndexedDBService } from './indexed-db.service';

@Component({
  selector: 'app-indexed-db-example',
  template: `<div>Check the console for IndexedDB operations</div>`
})
export class IndexedDBExampleComponent implements OnInit {
  constructor(private indexedDBService: IndexedDBService) {}

  async ngOnInit() {
    await this.indexedDBService.set('myKey', { name: 'Angular', version: 11 });
    const value = await this.indexedDBService.get('myKey');
    console.log('IndexedDB value:', value);
  }
}
```

### Service-side Caching Options
Service-side caching can be implemented using various techniques and tools. Here are a few options:

 - **Redis**: An in-memory data structure store that can be used as a database, cache, and message broker.
 - **Memcached**: A distributed memory caching system that can be used to speed up dynamic web applications by alleviating database load.
 - **HTTP Caching**: Using HTTP headers like Cache-Control, ETag, and Last-Modified to control caching behavior on the server side.

Example: Implementing Redis Caching
Install Redis and the redis package:

```bash
npm install redis
```

Create a Redis service:

```typescript
import { Injectable } from '@nestjs/common';
import { createClient } from 'redis';

@Injectable()
export class RedisService {
  private client;

  constructor() {
    this.client = createClient();
    this.client.connect();
  }

  async set(key: string, value: any): Promise<void> {
    await this.client.set(key, JSON.stringify(value));
  }

  async get(key: string): Promise<any> {
    const value = await this.client.get(key);
    return value ? JSON.parse(value) : null;
  }

  async delete(key: string): Promise<void> {
    await this.client.del(key);
  }

  async clear(): Promise<void> {
    await this.client.flushAll();
  }
}
```

Use the Redis service in a controller or another service:

```typescript
import { Controller, Get, Param } from '@nestjs/common';
import { RedisService } from './redis.service';

@Controller('cache')
export class CacheController {
  constructor(private redisService: RedisService) {}

  @Get(':key')
  async get(@Param('key') key: string): Promise<any> {
    return this.redisService.get(key);
  }
}
```

## Conclusion
In this blog post, I tried to provide you with various caching strategies in Angular, using a portfolio application for a "Fast Investments" firm as an example. We covered client-side caching techniques such as using Angular Service Worker, HttpInterceptor, local storage, session storage, and in-memory storage. We also discussed server-side caching with Angular Universal and advanced caching techniques using IndexedDB and Redis.

By implementing these caching strategies, you can significantly improve the performance and user experience of Angular applications. 

I hope you found this post helpful and informative. If you have any questions or feedback, feel free to leave a comment below.

---
title: TypeScript & JavaScript
tags: [language, typescript, javascript, nodejs, express]
related: [Angular, Angular-Signals, RxJS, Databases-NoSQL]
sources: [Typescript & Javascript/TypeScript.md, Typescript & Javascript/JavaScript 6.md, Typescript & Javascript/Express Server.md]
updated: 2026-04-19
---

# TypeScript & JavaScript

## TypeScript overview

TypeScript is JavaScript with static types. It **transpiles** to ECMAScript 5, making modern features available in all browsers. The compiler is `tsc` (written in Node.js — Node must be installed).

```bash
npm install -g typescript
tsc --init       # generate tsconfig.json
tsc              # compile
tsc -w           # watch mode — recompile on change
```

**Benefits over plain JavaScript:**

| Benefit | What it gives you |
|---|---|
| Static types | Variables, parameters, return types checked at compile time |
| Type inference | Compiler infers types — `any` only when explicitly needed |
| Block scoping | `let` and `const` instead of `var` |
| Organisation | Classes, interfaces, modules, namespaces |
| Tooling | Autocomplete, instant errors, unused/unreachable code detection, source maps |

---

## TypeScript configuration (tsconfig.json)

```bash
tsc --init    # generates tsconfig.json with all options commented
```

```json
{
  "compilerOptions": {
    "target": "es5",            // output ECMAScript version
    "outDir": "./js",           // compiled output folder
    "strict": true,             // enable all strict checks
    "noUnusedLocals": true,     // error on unused variables
    "strictNullChecks": true,   // null/undefined must be explicit
    "noImplicitAny": true,      // error when type can't be inferred
    "sourceMap": true,          // enables browser/IDE debugging
    "declaration": true         // emit .d.ts type declaration files
  },
  "exclude": ["./js"]
}
```

**Hierarchical config** — extend a base config:
```json
{ "extends": "../../tsconfig.base", "compilerOptions": { "removeComments": true } }
```

---

## Types

### Primitive types

```typescript
let isDone: boolean = false;
let count: number = 42;
let name: string = "Ken";
let nothing: void = undefined;    // function return type when no value
let absent: null = null;
let missing: undefined = undefined;
let never: never;                  // function that always throws / infinite loop
let anything: any;                 // opt out of type checking (avoid)
```

### Union types

```typescript
let value: number | string = 42;
value = "hello";   // also valid

// Alias for complex unions
type Id = number | string;
```

### Arrays and tuples

```typescript
let nums: number[] = [1, 2, 3];
let tuple: [string, number] = ["Ken", 42];   // fixed-length, typed positions
```

### Enums

```typescript
enum Direction { Up, Down, Left, Right }
let dir: Direction = Direction.Up;   // 0
```

### Partial and utility types

```typescript
interface Course { title: string; duration: number; }

let draft: Partial<Course> = { title: "TypeScript" };  // all fields optional
let full: Required<Course> = { title: "TS", duration: 3 };
let keys: Readonly<Course> = { title: "TS", duration: 3 };  // immutable
```

---

## Type inference and control flow

TypeScript narrows types automatically:

```typescript
function process(val: string | number) {
    if (typeof val === "string") {
        val.toUpperCase();    // TypeScript knows it's string here
    } else {
        val.toFixed(2);       // TypeScript knows it's number here
    }
}
```

**`--strictNullChecks`** — `null` and `undefined` are separate types; must be explicitly handled:
```typescript
let name: string | null = getName();
if (name !== null) name.toUpperCase();   // safe
```

---

## Functions

```typescript
// Optional parameter (?)  — must come after required params
function greet(name: string, greeting?: string): string {
    return `${greeting ?? "Hello"}, ${name}`;
}

// Default parameter
function greet(name: string, greeting: string = "Hello"): string { ... }

// Arrow function
const add = (a: number, b: number): number => a + b;

// Function type variable
type Transformer = (input: string) => string;
const upper: Transformer = s => s.toUpperCase();
```

---

## Interfaces and classes

### Interfaces — shape of an object (duck typing)

TypeScript uses **structural typing** — if an object has the right shape, it satisfies the interface regardless of its actual type.

```typescript
interface Vehicle {
    make: string;
    model: string;
    year?: number;           // optional
    readonly vin: string;   // immutable after creation
    drive(): void;
}

// Satisfied by object literal — no `implements` needed
const car: Vehicle = { make: "Toyota", model: "Camry", vin: "ABC", drive: () => {} };
```

### Classes

```typescript
class Car implements Vehicle {
    readonly vin: string;
    
    constructor(
        public make: string,        // shorthand: declares + assigns
        public model: string,
        vin: string
    ) {
        this.vin = vin;
    }
    
    // Accessor (equivalent to C# property)
    private _speed = 0;
    get speed() { return this._speed; }
    set speed(val: number) { this._speed = Math.max(0, val); }
    
    drive(): void { console.log("Driving"); }
    
    static create(make: string, model: string) {
        return new Car(make, model, "GENERATED");
    }
}

// Inheritance
class ElectricCar extends Car {
    constructor(make: string, model: string, public range: number) {
        super(make, model, "EV");   // must call super() first
    }
}
```

---

## Modules

```typescript
// math.ts — named exports
export function add(a: number, b: number) { return a + b; }
export const PI = 3.14;

// Default export
export default class Calculator { ... }

// Importing
import { add, PI } from './math';
import { add as sum } from './math';   // alias
import * as math from './math';        // all named
import Calculator from './math';       // default
```

**Type declaration files** — add types for plain JS libraries:
```bash
npm install @types/node --save-dev
npm install @types/lodash --save-dev
# Find types: https://www.typescriptlang.org/dt/search
```

---

## JavaScript ES6+ essentials

### `let` and `const` — block scoped

```javascript
// var — function scoped (leaks out of blocks)
for (var i = 0; i < 3; i++) {}
console.log(i);   // 3 — leaks!

// let — block scoped
for (let j = 0; j < 3; j++) {}
// console.log(j);   // ReferenceError

const PI = 3.14;   // immutable binding (object contents still mutable)
```

### Spread and rest

```javascript
const arr = [1, 2, 3];
const copy = [...arr, 4, 5];        // spread into new array
const merged = { ...obj1, ...obj2 }; // spread into object

function sum(...nums: number[]) {    // rest — collects remaining args
    return nums.reduce((a, b) => a + b, 0);
}
```

### Destructuring

```javascript
const { name, age = 0 } = person;   // object — with default
const [first, , third] = array;      // array — skip middle
```

### Template literals

```javascript
const msg = `Hello ${name}, you are ${age} years old`;
```

### Timing

```javascript
console.time("label");
// ... code to measure ...
console.timeEnd("label");    // logs: "label: 123.45ms"
```

---

## Express server (TypeScript)

```bash
npm install express @types/express
npm install body-parser @types/body-parser
```

### Basic server

```typescript
import * as express from 'express';
import * as bodyParser from 'body-parser';

const app: express.Application = express();
app.use(bodyParser.json());   // parse JSON request bodies

const port = 8090;

// Route handler
app.route('/api/courses').get((req, res) => {
    findAllCourses()
        .then(results => res.status(200).json({ results }));
});

app.listen(port, () => console.log(`Server running on port ${port}`));
```

### Error handling middleware

```typescript
import { Request, Response, NextFunction } from 'express';

// Must have 4 params — Express detects it as error middleware
export function apiErrorHandler(err: any, req: Request, res: Response, next: NextFunction) {
    console.error('Error:', err);
    res.status(500).json({ errorCode: 'ERR001', message: 'Internal Server Error' });
}

// Register LAST after all routes
app.use(apiErrorHandler);
```

### REST handler pattern with partial application

```typescript
import * as _ from 'lodash';

export function onSuccess(res: Response, data: any) {
    res.status(200).json(data);
}
export function onError(res: Response, message: string, err: Error) {
    res.status(500).json({ message });
}

// Usage — _.partial binds the first args, leaving err for Promise.catch
app.route('/api/lessons').get((req, res) => {
    findAllLessons()
        .then(_.partial(onSuccess, res))
        .catch(_.partial(onError, res, 'findAllLessons failed'));
});
```

---

## Sequelize ORM (PostgreSQL)

```bash
npm install sequelize @types/sequelize
npm install pg pg-hstore     # PostgreSQL driver
```

```typescript
import { Sequelize } from 'sequelize';

const sequelize = new Sequelize('postgres://postgres:password@127.0.0.1:5432/mydb');

// CRUD
CourseModel.findAll()
    .then(results => console.log(JSON.stringify(results)));

CourseModel.findOne({ where: { id } });

LessonModel.create(lesson);

LessonModel.update(props, { where: { id } });

LessonModel.destroy({ where: { id } });

// Join (eager loading)
CourseModel.findOne({
    where: { id },
    include: [{ model: LessonModel }]   // equivalent to SQL JOIN
});
```

---

## See also
- [[Angular]] — TypeScript-first component framework
- [[Angular-Signals]] — reactive primitives in Angular
- [[RxJS]] — reactive programming with Observables
- [[Databases-NoSQL]] — MongoDB usage with Node.js

# frontend-interviews

###### 1. Polyfill of promise.

<details><summary><b>Answer</b></summary>
<p>

```
function CustomPromise(executor) {
  if (typeof executor !== 'function') {
    throw new TypeError('Promise resolver ' + executor + ' is not a function');
  }

  this.state = 'pending';
  this.value = undefined;
  this.reason = undefined;
  this.onFulfilledCallbacks = [];
  this.onRejectedCallbacks = [];

  const resolve = (value) => {
    if (this.state === 'pending') {
      this.state = 'fulfilled';
      this.value = value;
      this.onFulfilledCallbacks.forEach(callback => callback(value));
    }
  };

  const reject = (reason) => {
    if (this.state === 'pending') {
      this.state = 'rejected';
      this.reason = reason;
      this.onRejectedCallbacks.forEach(callback => callback(reason));
    }
  };

  try {
    executor(resolve, reject);
  } catch (error) {
    reject(error);
  }
}

CustomPromise.prototype.then = function (onFulfilled, onRejected) {
  onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : value => value;
  onRejected = typeof onRejected === 'function' ? onRejected : reason => { throw reason; };

  if (this.state === 'fulfilled') {
    return new CustomPromise((resolve) => {
      try {
        const result = onFulfilled(this.value);
        resolve(result);
      } catch (error) {
        reject(error);
      }
    });
  }

  if (this.state === 'rejected') {
    return new CustomPromise((resolve, reject) => {
      try {
        const result = onRejected(this.reason);
        resolve(result);
      } catch (error) {
        reject(error);
      }
    });
  }

  if (this.state === 'pending') {
    return new CustomPromise((resolve, reject) => {
      this.onFulfilledCallbacks.push((value) => {
        try {
          const result = onFulfilled(value);
          resolve(result);
        } catch (error) {
          reject(error);
        }
      });

      this.onRejectedCallbacks.push((reason) => {
        try {
          const result = onRejected(reason);
          resolve(result);
        } catch (error) {
          reject(error);
        }
      });
    });
  }
};

// Testing the custom Promise polyfill
const myPromise = new CustomPromise((resolve, reject) => {
  setTimeout(() => {
    resolve('Success!');
    // Uncomment the line below to test rejection:
    // reject('Failure!');
  }, 1000);
});

myPromise
  .then(result => {
    console.log('Resolved:', result);
  })
  .catch(error => {
    console.error('Rejected:', error);
  });

```
https://medium.com/@manojsingh047/polyfill-for-javascript-promise-81053b284e37

</p>
</details>

---

###### 2. Flattening a nested object.

<details><summary><b>Answer</b></summary>
<p>
  
```
const flattenObj = ob => {
  let result = {};
  for (const i in ob) {
    if (type in ob[i] == "object" && !Array.isArray(ob[i])) {
      const temp = flattenObj(ob[i]);
      for (const j in temp) {
        Result[i + "-" + j] = ob[i];
      }
    } else {
      result[i] = ob[i];
    }
  }
  return result;
};
```

</p>
</details>

---

###### 3. Implement debounce

<details><summary><b>Answer</b></summary>
<p>

```
function debounce(func, delay) {
  let timeoutId;

  return function (...args) {
    const context = this;

    clearTimeout(timeoutId);

    timeoutId = setTimeout(() => {
      func.apply(context, args);
    }, delay);
  };
}
```

</p>
</details>

---

###### 4. Implement throttle

<details><summary><b>Answer</b></summary>
<p>
  
```
function throttle(cb, delay) {
  let wait = false;

  return (...args) => {
    if (wait) {
      return;
    }

    cb(...args);
    wait = true;
    setTimeout(() => {
      wait = false;
    }, delay);
  };
}

```

</p>
</details>

---


###### 5. Flatten nested array

<details><summary><b>Answer</b></summary>
<p>

```
function flatten(arr) {
  const newArr = arr.reduce((acc, item) => {
    if (Array.isArray(item)) {
      acc = acc.concat(flatten(item));
    } else {
      acc.push(item);
    }

    return acc;
  }, []);

  return newArr;
}

const numArr = [1, [2, [3], 4, [5, 6, [7]]]];

flatten(numArr); // [1, 2, 3, 4, 5, 6, 7]
```
</p>
</details>

---

###### 6. Implement currying a function

<details><summary><b>Answer</b></summary>
<p>
  
```
const curry = func => {
  return function curried(...args) {
    if (args.length >= func.length) {
      return func.apply(this, args);
    } else {
      return function (...moreArgs) {
        return curried.apply(this, args.concat(moreArgs));
      };
    }
  };
};

```

</p>
</details>

---

###### 7. Implement memo function

<details><summary><b>Answer</b></summary>
<p>

```
function memoise(func) {
  cache = {};
  return function (...args) {
    const key = JSON.stringify(args);
    if (cache[key] === undefined) {
      cache[key] = func(args);
    }
    return cache[key];
  };
}

const memo = memoise(expensiveFunc);

```
</p>
</details>

---

###### 8. Explain `this` keyword in JavaScript

<details><summary><b>Answer</b></summary>
<p>

In JavaScript, the `this` keyword is a special variable that is used within functions to refer to the object that is the current execution context. The value of `this` is determined by how a function is called, and it can behave differently depending on the context in which the function is executed.

Here are some key points to understand about the `this` keyword in JavaScript:

1. **Global Context:** When used in the global scope (outside of any function), `this` refers to the global object. In a web browser environment, the global object is typically `window`.

   ```javascript
   console.log(this === window); // true (in a browser)
   ```

2. **Function Context:** Inside a function, the value of `this` depends on how the function is called:
   
   - **Function Invocation:** When a function is called as a standalone function, `this` typically refers to the global object (e.g., `window` in a browser) in non-strict mode. In strict mode, it will be `undefined`.

     ```javascript
     function myFunction() {
       console.log(this); // window (in a browser), undefined in strict mode
     }

     myFunction();
     ```

   - **Method Invocation:** When a function is called as a method of an object, `this` refers to the object that contains the method.

     ```javascript
     const obj = {
       name: 'John',
       sayHello: function () {
         console.log(`Hello, ${this.name}`);
       },
     };

     obj.sayHello(); // Hello, John
     ```

   - **Constructor Invocation:** When a function is used as a constructor (with the `new` keyword), `this` refers to the newly created instance of an object.

     ```javascript
     function Person(name) {
       this.name = name;
     }

     const person = new Person('Alice');
     console.log(person.name); // Alice
     ```

   - **Explicit Binding:** You can explicitly specify the value of `this` using methods like `call()` and `apply()`.

     ```javascript
     function greet(message) {
       console.log(`${message}, ${this.name}`);
     }

     const person = { name: 'Bob' };
     greet.call(person, 'Hello'); // Hello, Bob
     ```

3. **Arrow Functions:** Arrow functions have a different behavior regarding `this`. They do not have their own `this` context but inherit it from their surrounding (enclosing) lexical context. This means that the value of `this` inside an arrow function is determined by where the arrow function is defined, not where it's executed.

   ```javascript
   const obj = {
     name: 'Alice',
     sayHello: () => {
       console.log(`Hello, ${this.name}`);
     },
   };

   obj.sayHello(); // Hello, undefined (lexical context is the global scope)
   ```

Understanding how `this` works in different contexts is crucial for writing correct and predictable JavaScript code. It can sometimes be a source of confusion, so it's essential to be aware of its behavior and use it appropriately based on your specific use cases.
</p>
</details>

---

###### 9. Advantages/Disadvantages of Closures

<details><summary><b>Answer</b></summary>
<p>

Closures are a fundamental concept in JavaScript and many other programming languages. They provide a way to maintain state and encapsulation in your code by allowing a function to access variables from its outer (enclosing) scope even after that outer function has finished executing. While closures offer several advantages, they also come with some potential disadvantages. Let's explore both sides:

**Advantages of Closures:**

1. **Encapsulation:** Closures allow you to encapsulate variables and functions, keeping them private and inaccessible from the outside world. This is a key feature of the module pattern and helps prevent unintended modification of data.

2. **Data Persistence:** Closures enable data persistence. Variables within a closure's scope are not destroyed when the outer function exits, allowing you to retain state across multiple function calls.

3. **Information Hiding:** You can use closures to hide sensitive or implementation-specific data. This is especially important when working with libraries or frameworks where you want to expose a public API while keeping internal details private.

4. **Callback Functions:** Closures are commonly used in callback functions and event handling. They allow you to pass additional data to a callback function even when it's invoked later, maintaining the context in which the callback was created.

5. **Function Factories:** Closures enable the creation of factory functions that generate customized functions with specific behavior. This is useful for creating multiple instances of a function with different configurations.

6. **Memoization:** Closures are essential for implementing memoization, which is a performance optimization technique that stores previously computed results and returns them when the same input occurs again.

**Disadvantages of Closures:**

1. **Memory Usage:** Closures can lead to increased memory usage. Since variables in a closure's scope are not garbage-collected until the closure itself is no longer referenced, you may inadvertently keep references to objects longer than necessary, potentially causing memory leaks.

2. **Complexity:** Overuse or misuse of closures can make code more complex and harder to understand. Excessive nesting of closures can lead to the "callback hell" problem, where code becomes difficult to follow due to deeply nested functions.

3. **Performance Overhead:** Closures can introduce a small performance overhead, as they require the creation of a new lexical scope for each closure. While this overhead is usually negligible, it can be a concern in performance-critical applications.

4. **Debugging Challenges:** Debugging closures can be more challenging than debugging non-closure code. Accessing variables in a closure may require inspecting multiple levels of scope.

5. **Potential for Unintended Behavior:** Closures can capture variables by reference, which can lead to unexpected behavior if those variables are modified outside the closure's scope after the closure is created.

In summary, closures are a powerful and versatile feature in JavaScript, offering benefits like encapsulation, data persistence, and information hiding. However, they should be used judiciously to avoid potential drawbacks related to memory usage, code complexity, and debugging difficulties. Understanding when and how to use closures effectively is essential for writing clean and maintainable JavaScript code.
</p>
</details>

---

###### 10. Promise.all() vs Promise.race()

<details><summary><b>Answer</b></summary>
<p>
`Promise.all()` and `Promise.race()` are two methods provided by JavaScript's `Promise` object for working with multiple promises. They serve different purposes and have distinct behaviors:

1. **`Promise.all(iterable)`**:

   - **Purpose:** `Promise.all()` is used when you want to wait for all promises in an iterable (e.g., an array of promises) to fulfill. It waits for all promises to resolve successfully or for one of them to reject (fail).

   - **Behavior:**
     - It returns a single promise that resolves to an array of results when all input promises have resolved successfully.
     - If any of the input promises is rejected, the resulting promise is immediately rejected with the reason of the first rejected promise.
     - It's often used when you need to perform multiple asynchronous operations in parallel and wait for all of them to complete before proceeding.

   - **Example:**
     ```javascript
     const promises = [promise1, promise2, promise3];

     Promise.all(promises)
       .then(results => {
         console.log(results); // Array of resolved values
       })
       .catch(error => {
         console.error(error); // First rejection reason
       });
     ```

2. **`Promise.race(iterable)`**:

   - **Purpose:** `Promise.race()` is used when you want to wait for the first promise in an iterable to settle (either resolve or reject). It doesn't wait for all promises to complete, but it resolves or rejects as soon as the first promise settles.

   - **Behavior:**
     - It returns a single promise that resolves or rejects with the same value or reason as the first promise in the input iterable to settle.
     - It's useful when you have multiple asynchronous tasks and you only care about the result of the first one that finishes.

   - **Example:**
     ```javascript
     const promises = [promise1, promise2, promise3];

     Promise.race(promises)
       .then(result => {
         console.log(result); // Value or reason of the first settling promise
       })
       .catch(error => {
         console.error(error); // This block will not be executed unless all promises reject
       });
     ```

In summary, `Promise.all()` is used when you want to wait for all promises to complete successfully or fail together, whereas `Promise.race()` is used when you want to wait for the first promise to settle, regardless of whether it resolves or rejects. These methods provide flexibility in handling different scenarios involving asynchronous operations in JavaScript.

</p>
</details>

---

###### 11. Hit 100 API calls asynchronously

<details><summary><b>Answer</b></summary>
<p>


</p>
</details>

---

###### 12. Handle millions of API calls

<details><summary><b>Answer</b></summary>
<p>
Handling a million API calls in a web browser without causing it to crash can be challenging due to the potential memory and performance limitations of browsers. To achieve this, you should implement efficient techniques to batch and control the API calls. Here's a step-by-step approach to handle a large number of API calls in a way that avoids browser crashes:

1. **Batch the API Calls:**

   Instead of making a million API calls all at once, batch the calls into smaller, manageable groups. You can choose an appropriate batch size based on your API rate limits and the browser's capabilities. For instance, you could start with batches of 100 or 1000 records.

2. **Use Throttling and Rate Limiting:**

   To prevent overloading the API server and the browser, implement throttling and rate limiting for your API calls. This ensures that you make a limited number of requests per second or minute. Libraries like `axios` provide options to control request concurrency and rate limiting.

3. **Implement Pagination:**

   If the API supports pagination, use it to retrieve data in smaller chunks. This can significantly reduce the number of API requests required to process all the data.

4. **Optimize Data Processing:**

   Ensure that your data processing logic is efficient. Process the data as soon as it's received from the API, and avoid accumulating a massive amount of data in memory. Use streaming or incremental processing if possible.

5. **Monitor and Handle Errors:**

   Implement error handling and retries for API calls. If an API call fails, retry it after a brief delay. Keep track of the number of retries and implement a limit to prevent infinite retries.

6. **Visual Feedback:**

   Provide visual feedback to the user, indicating the progress of the API calls. You can use progress bars or loading indicators to keep users informed.

7. **Use Web Workers:**

   Consider using Web Workers to offload heavy processing tasks from the main browser thread. This can help prevent the browser from becoming unresponsive.

8. **Memory Management:**

   Keep an eye on memory usage. If your processing accumulates data in memory, consider periodically clearing or disposing of data that is no longer needed.

9. **Testing:**

   Thoroughly test your solution with a smaller dataset to ensure that it performs as expected. Identify and address any bottlenecks or performance issues before scaling up to a million records.

10. **Consider Server-Side Processing:**

    If possible, offload the heavy data processing to a server or backend service. This can reduce the load on the browser and simplify your client-side code.

11. **Optimize API Requests:**

    If you have control over the API, consider optimizing it for bulk requests. Implement batch endpoints or provide options for retrieving multiple records in a single request.

By following these steps and carefully managing the API calls and data processing, you can retrieve and process a large dataset in a web browser without causing it to crash. Keep in mind that the exact approach may vary depending on your specific requirements and constraints.

Batching API calls when you need to make individual API requests for each ID is a common scenario, especially when dealing with a large dataset. To illustrate this, I'll provide you with a simplified example using JavaScript and the `axios` library to make batched API calls. In this example, we'll fetch data for multiple IDs from a hypothetical API.

Here's an example of how to batch API calls:

```javascript
const axios = require('axios'); // Import the axios library

// List of IDs for which we want to fetch data
const idsToFetch = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];

// Define the API endpoint
const apiUrl = 'https://api.example.com/data/';

// Function to fetch data for a single ID
async function fetchDataForId(id) {
  try {
    const response = await axios.get(apiUrl + id);
    return response.data;
  } catch (error) {
    console.error(`Error fetching data for ID ${id}:`, error);
    throw error; // Re-throw the error to handle it later
  }
}

// Function to fetch data for a batch of IDs
async function fetchBatchData(ids) {
  const batchPromises = ids.map((id) => fetchDataForId(id));
  return Promise.all(batchPromises);
}

// Main function to handle batched API calls
async function main() {
  try {
    const batchSize = 3; // Set your desired batch size
    const batches = [];

    // Divide the IDs into batches
    for (let i = 0; i < idsToFetch.length; i += batchSize) {
      const batch = idsToFetch.slice(i, i + batchSize);
      batches.push(batch);
    }

    // Fetch data for each batch
    for (const batch of batches) {
      const batchResults = await fetchBatchData(batch);
      console.log('Batch Results:', batchResults);
    }
  } catch (error) {
    console.error('Main Error:', error);
  }
}

// Call the main function to start fetching data in batches
main();
```

In this example:

1. We define a list of `idsToFetch` for which we want to fetch data.

2. We create a function `fetchDataForId(id)` that fetches data for a single ID using Axios. This function returns a Promise.

3. We create a function `fetchBatchData(ids)` that takes an array of IDs and fetches data for all of them concurrently using `Promise.all`.

4. In the `main()` function, we specify a `batchSize` (the number of IDs to fetch in each batch) and then divide the IDs into batches.

5. We iterate over the batches and fetch data for each batch using `fetchBatchData()`. This allows us to make API calls in manageable batches.

By batching API calls in this way, you can efficiently retrieve data for a large number of IDs without overloading the server or causing performance issues in your client application.

</p>
</details>

---

###### 13. Call, Apply & Bind function

<details><summary><b>Answer</b></summary>
<p>
In JavaScript, `call()`, `apply()`, and `bind()` are methods that allow you to manipulate the `this` context of a function and pass arguments to that function in different ways. They are often used to control how a function is executed and to reuse or share functions between objects. Here's an explanation of each:

1. **`call()` Method:**

   The `call()` method is used to invoke a function with a specified `this` context and a list of arguments. It takes the following syntax:

   ```javascript
   function.call(thisArg, arg1, arg2, ...);
   ```

   - `function`: The function to be invoked.
   - `thisArg`: The value to set as the `this` context when executing the function.
   - `arg1`, `arg2`, ...: Optional arguments to be passed to the function.

   Example:

   ```javascript
   const person = {
     name: 'John',
     sayHello: function () {
       console.log(`Hello, ${this.name}!`);
     },
   };

   const anotherPerson = { name: 'Alice' };

   person.sayHello.call(anotherPerson); // Outputs: "Hello, Alice!"
   ```

2. **`apply()` Method:**

   The `apply()` method is similar to `call()`, but it takes an array or an array-like object as its second argument, which is used as the list of arguments when invoking the function. Its syntax is as follows:

   ```javascript
   function.apply(thisArg, [arg1, arg2, ...]);
   ```

   - `function`: The function to be invoked.
   - `thisArg`: The value to set as the `this` context when executing the function.
   - `[arg1, arg2, ...]`: An array or array-like object containing the arguments to be passed to the function.

   Example:

   ```javascript
   const person = {
     name: 'John',
     sayHello: function (greeting) {
       console.log(`${greeting}, ${this.name}!`);
     },
   };

   const anotherPerson = { name: 'Alice' };

   person.sayHello.apply(anotherPerson, ['Hi']); // Outputs: "Hi, Alice!"
   ```

3. **`bind()` Method:**

   The `bind()` method is used to create a new function with a specified `this` context, but it doesn't invoke the function immediately. Instead, it returns a new function that can be invoked later. `bind()` can also pre-fill some or all of the function's arguments. Its syntax is as follows:

   ```javascript
   const newFunction = function.bind(thisArg[, arg1[, arg2[, ...]]]);
   ```

   - `thisArg`: The value to set as the `this` context when the new function is invoked.
   - `arg1`, `arg2`, ...: Optional arguments to be pre-filled in the new function.

   Example:

   ```javascript
   const person = {
     name: 'John',
     sayHello: function () {
       console.log(`Hello, ${this.name}!`);
     },
   };

   const anotherPerson = { name: 'Alice' };

   const boundFunction = person.sayHello.bind(anotherPerson);
   boundFunction(); // Outputs: "Hello, Alice!"
   ```

   You can also pre-fill arguments:

   ```javascript
   function greet(greeting, punctuation) {
     console.log(`${greeting}, ${this.name}${punctuation}`);
   }

   const boundGreet = greet.bind(anotherPerson, 'Hi');
   boundGreet('!'); // Outputs: "Hi, Alice!"
   ```

In summary, `call()` and `apply()` are used to invoke a function immediately with a specified context and arguments, while `bind()` creates a new function with a specified context and optionally pre-filled arguments. These methods are valuable for controlling the behavior of functions, especially in cases where you want to reuse functions in different contexts or provide a specific context for a function call.

</p>
</details>

---

###### 14. Explain OOPS concepts in Angular

<details><summary><b>Answer</b></summary>
<p>
Object-Oriented Programming (OOP) concepts play a significant role in Angular, a popular JavaScript framework for building web applications. Angular leverages OOP principles to create modular, maintainable, and scalable code. Here's an explanation of how key OOP concepts are used in Angular:

1. **Classes and Objects:**

   In Angular, components are at the heart of the application architecture. Each component is a TypeScript class decorated with the `@Component` decorator. Components encapsulate the logic, templates, and styles needed for a specific part of the user interface. These components are instantiated as objects when used within templates or other components.

   Example of a simple Angular component class:

   ```typescript
   import { Component } from '@angular/core';

   @Component({
     selector: 'app-root',
     template: '<h1>{{ title }}</h1>',
   })
   export class AppComponent {
     title = 'My Angular App';
   }
   ```

2. **Encapsulation:**

   Encapsulation is a fundamental concept in OOP that restricts access to an object's internal state and exposes only the necessary interfaces. In Angular, encapsulation is achieved through TypeScript classes and the use of access modifiers like `public`, `private`, and `protected`. Component properties and methods can be marked as private or public to control access.

   ```typescript
   export class MyComponent {
     private _privateVar: string;
     public publicVar: string;

     constructor() {
       this._privateVar = 'I am private';
       this.publicVar = 'I am public';
     }
   }
   ```

3. **Inheritance:**

   Inheritance allows one class (the subclass or derived class) to inherit properties and methods from another class (the superclass or base class). In Angular, you can create reusable components by extending existing ones. The `extends` keyword in TypeScript enables inheritance.

   ```typescript
   import { Component } from '@angular/core';

   @Component({
     selector: 'app-child',
     template: '<p>Child Component</p>',
   })
   export class ChildComponent extends ParentComponent {
     // Additional child component logic
   }
   ```

4. **Polymorphism:**

   Polymorphism allows objects of different classes to be treated as instances of a common base class. In Angular, polymorphism is utilized through interfaces and dependency injection. Components, services, and other Angular constructs can implement interfaces to ensure that they adhere to a specific contract, making them interchangeable.

   ```typescript
   interface Logger {
     log(message: string): void;
   }

   class ConsoleLogger implements Logger {
     log(message: string): void {
       console.log(message);
     }
   }
   ```

5. **Abstraction:**

   Abstraction is the process of simplifying complex reality by modeling classes based on the essential properties and behaviors. In Angular, you abstract common functionalities into services and directives, which are used across components. Services abstract business logic, while directives abstract DOM manipulation.

   ```typescript
   @Directive({
     selector: '[appHighlight]'
   })
   export class HighlightDirective {
     constructor(private el: ElementRef) {}

     @Input('appHighlight') highlightColor: string;

     @HostListener('mouseenter') onMouseEnter() {
       this.highlight(this.highlightColor || 'yellow');
     }

     private highlight(color: string) {
       this.el.nativeElement.style.backgroundColor = color;
     }
   }
   ```

6. **Composition:**

   Composition involves constructing complex objects or behaviors by combining simpler ones. In Angular, you can compose applications by assembling various components, services, and directives into a cohesive whole. Components often communicate and share data through composition.

   ```typescript
   @Component({
     selector: 'app-parent',
     template: `
       <h1>Parent Component</h1>
       <app-child></app-child>
     `,
   })
   export class ParentComponent {
     // Parent component logic
   }
   ```

Angular's use of OOP concepts helps developers create organized, maintainable, and reusable code structures, making it a robust framework for building modern web applications. By understanding and applying these OOP principles, Angular developers can efficiently manage application complexity and promote code quality.

</p>
</details>

---

###### 15. All Angular Lifecycle hooks in order

<details><summary><b>Answer</b></summary>
<p>
Angular provides a set of lifecycle hooks that allow you to tap into different stages of a component's lifecycle. These hooks help you perform tasks such as initialization, change detection, and cleanup. Here are the Angular lifecycle hooks in order of execution:

1. **ngOnChanges:**
   
   This hook is called whenever one or more input properties of a component change. It receives a `SimpleChanges` object that provides information about the changed properties.

2. **ngOnInit:**

   `ngOnInit` is called after the component's inputs are initialized and before the view is rendered. It is a good place to perform initialization tasks, fetch initial data, or set up subscriptions.

3. **ngDoCheck:**

   `ngDoCheck` is called during every change detection cycle. It provides an opportunity to detect and react to changes that Angular's default change detection mechanism might not catch. Use it carefully, as it can be called frequently.

4. **ngAfterContentInit:**

   This hook is called after Angular initializes the content projected into the component (if any). It's a good place to interact with the content children of a component.

5. **ngAfterContentChecked:**

   `ngAfterContentChecked` is called after every change detection cycle, following `ngAfterContentInit`. It provides an opportunity to check and respond to changes in content children.

6. **ngAfterViewInit:**

   `ngAfterViewInit` is called after the component's view and child views (if any) are initialized. It's a good place to perform tasks that require access to the DOM or interacting with child components.

7. **ngAfterViewChecked:**

   `ngAfterViewChecked` is called after every change detection cycle, following `ngAfterViewInit`. It provides an opportunity to check and respond to changes in the component's view and child views.

8. **ngOnDestroy:**

   `ngOnDestroy` is called when a component is about to be destroyed, either due to a route change or manual removal from the DOM. It's a good place to clean up resources, such as unsubscribing from observables or removing event listeners.

These lifecycle hooks are an essential part of building Angular applications. They allow you to control the behavior of your components throughout their lifecycle, ensuring proper initialization, cleanup, and interactions with the Angular framework. Understanding when to use each hook is crucial for building efficient and maintainable Angular applications.

</p>
</details>

---

###### 16. Constructor vs ngOnInit

<details><summary><b>Answer</b></summary>
<p>
In Angular, both the constructor and `ngOnInit` are methods that play crucial roles in the lifecycle of a component, but they serve different purposes and should be used for distinct tasks. Let's examine the differences between the constructor and `ngOnInit`:

1. **Constructor:**

   - The constructor is a standard TypeScript class constructor. It is executed when an instance of the component class is created.
   
   - It is not specific to Angular and is part of the TypeScript class definition.

   - You can use the constructor for tasks such as initializing class properties, injecting services, and setting up basic configurations for the component.

   - However, you should avoid performing complex initialization and component-specific logic in the constructor because Angular has not fully initialized the component at this point. For example, you should not access DOM elements or make HTTP requests in the constructor.

   - The constructor is generally used for dependency injection and basic property initialization.

   Example:

   ```typescript
   import { Component } from '@angular/core';

   @Component({
     selector: 'app-my-component',
     template: '<p>{{ message }}</p>',
   })
   export class MyComponent {
     constructor() {
       this.message = 'Hello from constructor!';
     }
   }
   ```

2. **ngOnInit:**

   - `ngOnInit` is an Angular lifecycle hook that is specifically designed for component initialization.

   - It is called after Angular has fully initialized the component and set its input properties (if any).

   - `ngOnInit` is an ideal place to perform initialization tasks that require access to input properties, services, or other dependencies that are injected into the component.

   - This hook is often used for fetching data from APIs, setting up subscriptions to observables, or performing other component-specific initialization tasks.

   Example:

   ```typescript
   import { Component, OnInit } from '@angular/core';

   @Component({
     selector: 'app-my-component',
     template: '<p>{{ message }}</p>',
   })
   export class MyComponent implements OnInit {
     message: string;

     ngOnInit() {
       this.message = 'Hello from ngOnInit!';
     }
   }
   ```

In summary, while both the constructor and `ngOnInit` can be used for initializing properties, the key difference is that the constructor is a basic TypeScript feature used for dependency injection and basic setup, while `ngOnInit` is an Angular-specific lifecycle hook designed for more complex initialization tasks that require access to fully initialized component properties and dependencies. It's a best practice to use `ngOnInit` for component-specific initialization logic to ensure that your component is fully prepared before rendering and interacting with the Angular framework.

</p>
</details>

---


###### 17. how to improve page load time, techniques, approach

<details><summary><b>Answer</b></summary>
<p>
Improving page load time is crucial for providing a better user experience, reducing bounce rates, and improving search engine rankings. There are several techniques and approaches you can follow to optimize your website's page load time. Here's a comprehensive list of strategies you can implement:

1. **Minimize HTTP Requests:**

   - Reduce the number of external resources (stylesheets, scripts, images, fonts, etc.) your page needs to load by combining and minimizing files.
   - Use CSS Sprites to combine multiple images into one.
   - Use image sprites for icons and small images.

2. **Leverage Browser Caching:**

   - Set appropriate cache headers for static assets to instruct the browser to cache them. This reduces the need for repeated downloads on subsequent visits.
   - Use a Content Delivery Network (CDN) to deliver static assets from servers closer to the user.

3. **Enable Compression:**

   - Compress text-based assets, such as HTML, CSS, and JavaScript, using gzip or Brotli compression.
   - Ensure your server is configured to serve compressed files.

4. **Optimize Images:**

   - Use modern image formats like WebP, which offer better compression.
   - Compress and resize images to the appropriate dimensions and quality.
   - Use responsive images with the `srcset` attribute to deliver different image sizes based on the user's device.

5. **Minimize Render-Blocking Resources:**

   - Place critical CSS inline to avoid render-blocking stylesheets.
   - Load JavaScript asynchronously or defer its execution.
   - Use the `async` or `defer` attribute on script tags, depending on when the script is needed.

6. **Prioritize Above-the-Fold Content:**

   - Load the most critical content (above-the-fold) first to ensure the initial view is rendered quickly.
   - Lazy load non-essential below-the-fold content using techniques like the `loading="lazy"` attribute for images.

7. **Reduce Server Response Time:**

   - Optimize your server-side code and database queries for better performance.
   - Consider using server-side caching mechanisms like Redis or Memcached.

8. **Minimize DOM Size and Complexity:**

   - Keep your HTML structure as simple as possible.
   - Minimize unnecessary nested elements and use semantic HTML.
   - Avoid excessive use of iframes and complex table structures.

9. **Use Content Delivery Networks (CDNs):**

   - Use CDNs to distribute your content across multiple servers globally, reducing the geographic distance between users and your resources.

10. **Optimize Third-Party Scripts:**

    - Limit the use of third-party scripts and only include those that are essential.
    - Use async or defer attributes when loading third-party scripts to prevent them from blocking page rendering.

11. **Minimize Redirects:**

    - Minimize the number of redirects on your website. Each redirect adds additional HTTP requests and increases page load time.

12. **Implement Server-Side Rendering (SSR):**

    - Consider using server-side rendering for JavaScript-heavy applications, which can improve the initial page load time by rendering content on the server.

13. **Monitor and Analyze Performance:**

    - Use tools like Google PageSpeed Insights, Lighthouse, WebPageTest, and the Chrome DevTools Performance tab to analyze your website's performance.
    - Regularly monitor and measure your website's load times to identify and address issues.

14. **Optimize Fonts:**

    - Minimize the use of custom fonts and only load the font weights and styles you need.
    - Use the `font-display` property to control how fonts are displayed during loading.

15. **Use Preloading:**

    - Use the `rel="preload"` attribute to give browsers a hint to preload critical assets.

16. **Implement Browser-Side Caching:**

    - Implement client-side caching using techniques like Service Workers and the Cache API for progressive web apps (PWAs).

17. **Remove Unused Code:**

    - Regularly review and remove unused code, including JavaScript, CSS, and HTML.

18. **Optimize for Mobile:**

    - Prioritize mobile performance and use responsive design techniques.
    - Test your website's performance on various mobile devices and networks.

19. **Content Delivery Optimization:**

    - Use a Content Delivery Network (CDN) to serve static assets and cache dynamic content.
    - Use HTTP/2 or HTTP/3 for faster loading of assets over the network.

20. **Consider Asynchronous Loading:**

    - Load non-essential components or features after the initial page load using lazy loading techniques.

Remember that optimizing page load time is an ongoing process, and you should regularly monitor and test your website's performance to identify and address any new performance bottlenecks. Additionally, consider the specific requirements and constraints of your project, as some techniques may be more suitable than others depending on your use case.

</p>
</details>

---

###### 18. Crawling, page rank factors

<details><summary><b>Answer</b></summary>
<p>
Crawling and page ranking are two essential components of how search engines like Google index and display search results. Let's explore each concept and the key factors that influence them:

**Crawling:**

Crawling refers to the process by which search engines systematically browse and index web pages. Search engine crawlers, also known as spiders or bots, are automated programs that traverse the internet, following links and collecting information about web pages. Here are some key factors related to crawling:

1. **Robots.txt:** Websites can use a "robots.txt" file to instruct search engine crawlers which parts of their site should not be crawled. This file specifies which user agents (crawlers) are allowed or disallowed access to specific areas of the site.

2. **XML Sitemaps:** An XML sitemap is a file that provides a list of URLs on a website that the owner wants to be indexed by search engines. Submitting a sitemap to search engines can help ensure that important pages are crawled and indexed.

3. **Site Structure:** The structure of a website, including its URL hierarchy and internal linking, can affect how efficiently search engine crawlers navigate and index its pages. A clear and logical site structure is important.

4. **Page Speed:** Faster-loading pages are more likely to be crawled and indexed efficiently. Slow-loading pages may not be crawled thoroughly, or their ranking could be negatively impacted.

5. **Crawl Budget:** Search engines allocate a crawl budget to each website, which determines how frequently and deeply they crawl the site. Large and authoritative sites often have larger crawl budgets.

**Page Rank Factors:**

Page rank refers to the algorithmic method used by search engines to rank web pages in search results. While the specific ranking algorithms used by search engines are closely guarded secrets, several factors are known to influence page rank:

1. **Relevance and Keywords:** Pages that contain relevant content and keywords related to a user's search query are more likely to rank higher. Keyword optimization, including the use of relevant title tags, headers, and meta descriptions, is crucial.

2. **Quality and Authority:** High-quality content that provides value to users is favored by search engines. Backlinks from authoritative and reputable websites can boost a page's authority and, consequently, its ranking.

3. **Mobile Friendliness:** As mobile usage continues to grow, search engines prioritize mobile-friendly websites. Responsive design and mobile optimization are essential for ranking well.

4. **Page Load Speed:** Faster-loading pages not only improve the user experience but also contribute to higher rankings. Compressed images, minimized code, and efficient server performance can help improve load times.

5. **User Experience:** Factors such as a clean and intuitive design, easy navigation, and low bounce rates contribute to a positive user experience. Search engines reward pages that keep users engaged.

6. **Backlinks:** The number and quality of backlinks (links from other websites to your page) are significant ranking factors. High-quality, relevant backlinks from authoritative sources carry more weight.

7. **Social Signals:** Social media activity, such as shares, likes, and comments, can indirectly influence page rank. Popular content is often considered more valuable.

8. **Content Freshness:** Keeping content updated and relevant can improve rankings. News articles and blog posts often benefit from frequent updates.

9. **Security (HTTPS):** Secure websites that use HTTPS are favored by search engines. HTTPS is considered a ranking factor, and Google, in particular, encourages its adoption.

10. **Local SEO:** For businesses with a local presence, optimizing for local search can be crucial. This includes accurate NAP (Name, Address, Phone) information, Google My Business listings, and local reviews.

It's important to note that search engine algorithms are continually evolving, and search engines take into account hundreds of factors when determining rankings. SEO professionals and website owners need to stay updated with industry trends and best practices to improve their site's visibility in search results.

</p>
</details>

---

###### 19. Web vitals

<details><summary><b>Answer</b></summary>
<p>
Web Vitals are a set of specific user-centered metrics introduced by Google to help web developers and site owners measure and understand the performance and user experience of websites. These metrics are designed to assess aspects of web page loading, interactivity, and visual stability. Google considers Web Vitals as essential indicators for website quality and search ranking.

The core Web Vitals are divided into three categories:

1. **Loading Performance:**

   - **Largest Contentful Paint (LCP):** LCP measures the loading performance by identifying when the largest element in the viewport (e.g., an image or text block) becomes visible and fully rendered. Google recommends an LCP of 2.5 seconds or less for a good user experience.

   - **First Contentful Paint (FCP):** FCP measures the time it takes for the first piece of content to appear on the screen, such as text or images. A good FCP should occur within 1.25 seconds.

   - **Cumulative Layout Shift (CLS):** CLS evaluates the visual stability of a web page by measuring how much elements shift around the page during loading. It aims to prevent unexpected layout shifts that can frustrate users. A CLS score of less than 0.1 is considered good.

2. **Interactivity:**

   - **Total Blocking Time (TBT):** TBT measures the time a page remains unresponsive to user input during loading. It's crucial for assessing how quickly a page becomes interactive. For a good user experience, TBT should be less than 300 milliseconds.

   - **First Input Delay (FID):** FID measures the delay between a user's interaction with a web page (e.g., clicking a button) and the browser's response. A good FID should be less than 100 milliseconds.

3. **Visual Stability:**

   - **Cumulative Layout Shift (CLS):** CLS, mentioned earlier, also falls under the category of visual stability as it assesses the unexpected layout shifts on the page during the loading process.

To measure Web Vitals on your website, you can use various tools, including Google's own PageSpeed Insights, Lighthouse, and Chrome DevTools. These tools provide detailed reports on how your website performs in terms of Web Vitals and offer suggestions for improvements.

Optimizing for Web Vitals involves a combination of optimizing server response times, minimizing render-blocking resources, optimizing images and fonts, leveraging browser caching, and optimizing JavaScript execution. Prioritizing user experience and performance not only helps improve search engine rankings but also provides a better experience for visitors to your website.

Google has integrated Web Vitals into its search ranking algorithm, so websites that deliver a better user experience, as measured by these metrics, are more likely to rank higher in search results. Therefore, paying attention to Web Vitals is crucial for the success of your website in terms of both user engagement and search engine visibility.

</p>
</details>

---

###### 20. Angular vs React

<details><summary><b>Answer</b></summary>
<p>
Angular and React are both popular JavaScript libraries/frameworks used for building modern web applications, but they have different philosophies, features, and use cases. The choice between Angular and React depends on various factors, including your project requirements, team expertise, and personal preferences. Let's compare the two and explore when to use each:

**Angular:**

1. **Full-Fledged Framework:**
   - Angular is a comprehensive framework that provides everything you need to build a web application, including routing, state management, and form handling, out of the box.
   - It enforces a specific project structure and coding conventions, which can be helpful for large teams and projects.

2. **TypeScript:**
   - Angular is built with TypeScript, a statically-typed superset of JavaScript. TypeScript helps catch errors during development and offers improved tooling.

3. **Opinionated:**
   - Angular follows a "batteries-included" approach, meaning it comes with a set of built-in tools and practices, reducing the need for external libraries.
   - This opinionated nature can be beneficial for teams that prefer a clear structure and guidelines.

4. **Two-Way Data Binding:**
   - Angular supports two-way data binding, making it easier to synchronize data between the view and the model.

5. **Complex Single-Page Applications (SPAs):**
   - Angular is well-suited for building complex SPAs with a lot of built-in functionality and a large development team.

6. **Enterprise Applications:**
   - It's a popular choice for building enterprise-level applications with strict coding standards and extensive features.

7. **Built-in Testing:**
   - Angular includes testing tools like TestBed and Protractor, which can simplify the testing process.

**React:**

1. **Library for UI:**
   - React is primarily a UI library for building user interfaces. It focuses on the view layer and leaves other concerns like routing and state management to third-party libraries or custom solutions.

2. **JavaScript/JSX:**
   - React uses JavaScript (or optionally JSX, an XML-like syntax) for defining component views. This flexibility allows developers to use their preferred tooling.

3. **Unopinionated:**
   - React is unopinionated and provides more flexibility in terms of project structure and code organization. Developers have the freedom to choose libraries and tools that best fit their project's needs.

4. **Virtual DOM:**
   - React uses a virtual DOM for efficient updates to the actual DOM, resulting in improved performance.

5. **Component Reusability:**
   - React encourages the creation of reusable UI components, making it an excellent choice for building component-centric applications.

6. **Progressive Web Apps (PWAs):**
   - React is suitable for building PWAs, especially when combined with libraries like React Router and Redux.

7. **Community and Ecosystem:**
   - React has a vast and active community, resulting in a wide range of third-party libraries, tools, and resources.

**When to Use Which:**

- **Use Angular When:**
   - You're building a large and complex SPA with a comprehensive framework.
   - Your team is familiar with TypeScript and appreciates strict conventions.
   - You need a full-stack solution with built-in tools for routing, state management, and form handling.
   - You're working on an enterprise-level application with stringent coding standards.

- **Use React When:**
   - You want to build reusable UI components that can be easily integrated into different projects.
   - You prefer a more flexible and unopinionated approach to architecture and tooling.
   - Your project's primary focus is on the user interface, and you're open to selecting additional libraries for routing, state management, etc.
   - You're building a progressive web app (PWA) or a mobile app using React Native.

Ultimately, the choice between Angular and React depends on your specific project requirements, team expertise, and development philosophy. Both have their strengths and are capable of building high-quality web applications when used appropriately.

</p>
</details>

---

###### 21. Subject vs Behaviour Subject vs Reply Subject

<details><summary><b>Answer</b></summary>
<p>
In RxJS, `Subject`, `BehaviorSubject`, and `ReplaySubject` are all types of subjects, which are a special type of observable that can be used to multicast values to multiple observers. However, they have different behaviors and use cases. Let's compare these three types of subjects:

1. **Subject:**

   - A `Subject` is a basic type of subject in RxJS. It does not have an initial value and does not store previous values.
   - When a new observer subscribes to a `Subject`, it will only receive values that are emitted after the subscription.
   - `Subject` is commonly used when you want to manually control the emission of values to subscribers.

   ```javascript
   const subject = new Subject();

   subject.subscribe((value) => console.log(`Observer 1: ${value}`));

   subject.next(1); // Observer 1: 1

   subject.subscribe((value) => console.log(`Observer 2: ${value}`));

   subject.next(2); // Observer 1: 2, Observer 2: 2
   ```

2. **BehaviorSubject:**

   - A `BehaviorSubject` is a type of subject that has an initial value and always replays the most recent value to new subscribers when they subscribe.
   - When you create a `BehaviorSubject`, you provide an initial value. New subscribers will immediately receive this initial value, and then they will receive any subsequent values as they are emitted.
   - `BehaviorSubject` is commonly used when you want to share and maintain the current state or value, such as in state management in applications.

   ```javascript
   const behaviorSubject = new BehaviorSubject(0);

   behaviorSubject.subscribe((value) => console.log(`Observer 1: ${value}`));
   // Observer 1: 0

   behaviorSubject.next(1);

   behaviorSubject.subscribe((value) => console.log(`Observer 2: ${value}`));
   // Observer 2: 1 (immediately) and Observer 1: 1 (due to the replay)
   ```

3. **ReplaySubject:**

   - A `ReplaySubject` is a type of subject that replays a specified number of previously emitted values to new subscribers when they subscribe. This allows late subscribers to receive historical values.
   - When you create a `ReplaySubject`, you specify the number of historical values to replay. For example, `new ReplaySubject(2)` will replay the last two values to new subscribers.
   - `ReplaySubject` is useful when you want to share historical data with late subscribers.

   ```javascript
   const replaySubject = new ReplaySubject(2);

   replaySubject.subscribe((value) => console.log(`Observer 1: ${value}`));

   replaySubject.next(1);

   replaySubject.subscribe((value) => console.log(`Observer 2: ${value}`));

   replaySubject.next(2);

   replaySubject.next(3);

   // Observer 1: 1, Observer 1: 2, Observer 2: 1, Observer 2: 2, Observer 1: 3, Observer 2: 3
   ```

In summary:

- Use a `Subject` when you need a basic subject without any initial value or replay behavior.
- Use a `BehaviorSubject` when you want to maintain and share the current state or value, and new subscribers should receive the most recent value immediately.
- Use a `ReplaySubject` when you want to replay a specific number of historical values to late subscribers, allowing them to access past data. The number of historical values to replay is determined when you create the `ReplaySubject`.
</p>
</details>

---

###### 22. Insertion Sort & Merge Sort

<details><summary><b>Answer</b></summary>
<p>
Sure, I can provide you with JavaScript implementations of both Insertion Sort and Merge Sort. These are two classic sorting algorithms used to sort arrays. Let's start with Insertion Sort:

**Insertion Sort in JavaScript:**

Insertion Sort works by taking each element from an unsorted part of the array and inserting it into its correct position within the sorted part of the array.

```javascript
function insertionSort(arr) {
  const n = arr.length;
  for (let i = 1; i < n; i++) {
    let currentVal = arr[i];
    let j = i - 1;
    while (j >= 0 && arr[j] > currentVal) {
      arr[j + 1] = arr[j];
      j--;
    }
    arr[j + 1] = currentVal;
  }
  return arr;
}

const unsortedArray = [12, 11, 13, 5, 6];
const sortedArray = insertionSort(unsortedArray);
console.log(sortedArray); // Output: [5, 6, 11, 12, 13]
```

Now, let's move on to Merge Sort:

**Merge Sort in JavaScript:**

Merge Sort is a divide-and-conquer algorithm that divides the input array into two halves, recursively sorts them, and then merges the sorted halves to produce a sorted array.

```javascript
function mergeSort(arr) {
  if (arr.length <= 1) {
    return arr;
  }

  const middle = Math.floor(arr.length / 2);
  const left = arr.slice(0, middle);
  const right = arr.slice(middle);

  return merge(mergeSort(left), mergeSort(right));
}

function merge(left, right) {
  let result = [];
  let leftIndex = 0;
  let rightIndex = 0;

  while (leftIndex < left.length && rightIndex < right.length) {
    if (left[leftIndex] < right[rightIndex]) {
      result.push(left[leftIndex]);
      leftIndex++;
    } else {
      result.push(right[rightIndex]);
      rightIndex++;
    }
  }

  return result.concat(left.slice(leftIndex), right.slice(rightIndex));
}

const unsortedArray = [12, 11, 13, 5, 6];
const sortedArray = mergeSort(unsortedArray);
console.log(sortedArray); // Output: [5, 6, 11, 12, 13]
```

These are basic implementations of Insertion Sort and Merge Sort in JavaScript. Both algorithms have their own advantages and use cases. Insertion Sort is efficient for small datasets and nearly sorted arrays, while Merge Sort is a stable and efficient sorting algorithm for larger datasets.

</p>
</details>

---

###### 23. Flexbox vs Grid

<details><summary><b>Answer</b></summary>
<p>

Flexbox and CSS Grid are both layout systems in CSS that enable web developers to design and structure the layout of web pages. However, they have different use cases and approaches to achieving layout, and each has its strengths and weaknesses. Let's compare Flexbox and CSS Grid:

**Flexbox:**

1. **One-Dimensional Layout:**
   - Flexbox is primarily designed for one-dimensional layouts, either as rows or columns.
   - It's excellent for distributing items along a single axis, making it suitable for creating navigation bars, flexible lists, and aligning items within a container.

2. **Dynamic Sizing:**
   - Flexbox is great for distributing available space among items or resizing items to fit the container's dimensions.
   - It automatically adjusts the size of items based on their content or specified proportions.

3. **Alignment:**
   - Flexbox provides excellent control over the alignment of items along the main and cross axes.
   - You can easily center items both vertically and horizontally within a container.

4. **Nesting:**
   - You can nest flex containers within other flex containers, allowing for complex layouts with varying levels of flexibility.

5. **Flow Control:**
   - Flexbox can reorder items, which is particularly useful for responsive layouts or reordering items based on screen size.

**CSS Grid:**

1. **Two-Dimensional Layout:**
   - CSS Grid is designed for two-dimensional layouts with rows and columns.
   - It allows you to create complex grid structures, align items both horizontally and vertically, and define explicit row and column sizes.

2. **Grid Template:**
   - Grid layouts are defined explicitly using the `grid-template-columns` and `grid-template-rows` properties, making it suitable for creating consistent, grid-based designs.

3. **Alignment:**
   - CSS Grid provides precise control over the alignment of items in both directions (rows and columns).
   - You can align items to specific grid lines, distribute space evenly, and control item placement with ease.

4. **Auto Placement:**
   - CSS Grid can automatically place items within the grid, making it convenient for responsive designs and situations where the number of items is variable.

5. **Grid Gaps:**
   - You can specify gaps (margins) between rows and columns in a grid layout using the `grid-gap` property.

**When to Use Which:**

- **Use Flexbox When:**
   - You need to align items along a single axis, such as in navigation menus or aligning items within a container.
   - You want items to expand or shrink to fill available space dynamically.
   - You're dealing with one-dimensional layouts, like lists or cards.

- **Use CSS Grid When:**
   - You need to create complex, two-dimensional layouts with both rows and columns.
   - You want to create consistent grid structures, such as a grid of images, a calendar, or a dashboard.
   - You have a specific grid template in mind and want fine-grained control over rows, columns, and item placement.

In practice, many layouts benefit from a combination of both Flexbox and CSS Grid. You can use Flexbox to control the alignment and sizing of items within individual grid cells, combining the strengths of both layout systems to create versatile and responsive designs.
</p>
</details>

---

###### 24. Tell us some of the runtime performance and load time optimization techniques you followed.

<details><summary><b>Answer</b></summary>
<p>
Optimizing the runtime performance and load time of a web application is crucial for providing a fast and responsive user experience. Here are some common techniques and best practices that developers and performance engineers follow to achieve these optimizations:

**1. Code Splitting:**
   - Splitting your code into smaller, more manageable chunks using techniques like dynamic imports or Webpack's code splitting feature can reduce the initial load time. Only load the code that is required for the current page, and load additional code as needed.

**2. Minification and Compression:**
   - Minify JavaScript, CSS, and HTML files to remove unnecessary characters and whitespace. Use gzip or Brotli compression to reduce file sizes further.

**3. Browser Caching:**
   - Set appropriate cache headers for static assets to instruct the browser to cache them. Cached assets can be reused on subsequent visits, reducing the need for repeated downloads.

**4. Lazy Loading:**
   - Implement lazy loading for images and other non-critical assets. Load assets only when they come into the viewport or are about to be viewed by the user.

**5. Critical CSS and JavaScript:**
   - Inline critical CSS directly into the HTML to ensure that the page can be rendered with minimal blocking. Load non-critical CSS asynchronously.
   - Similarly, prioritize loading critical JavaScript first and defer the loading of non-essential scripts.

**6. Image Optimization:**
   - Optimize images by compressing them, using modern image formats (e.g., WebP), and specifying appropriate image dimensions to avoid unnecessary resizing in the browser.

**7. Content Delivery Networks (CDNs):**
   - Use a CDN to deliver static assets from servers located closer to the user, reducing latency and improving load times.

**8. Prefetching and Preloading:**
   - Use the `rel="prefetch"` and `rel="preload"` attributes to hint to the browser which resources it should fetch in advance, such as fonts or scripts required for subsequent pages.

**9. Critical Rendering Path Optimization:**
   - Optimize the critical rendering path by minimizing the number of critical resources and reducing their size.
   - Prioritize rendering of above-the-fold content to ensure a fast initial paint.

**10. Tree Shaking:**
    - Use tree shaking in JavaScript bundlers like Webpack to eliminate unused code and dependencies from your production bundle.

**11. Server-Side Rendering (SSR):**
    - Consider server-side rendering for JavaScript-heavy applications to improve initial load times and SEO.

**12. Database and Server Optimization:**
    - Optimize database queries, server-side code, and database indexing to reduce server response times.

**13. HTTP/2 and HTTP/3:**
    - Use the latest HTTP protocols (HTTP/2 and HTTP/3) that support multiplexing and reduced latency.

**14. Performance Monitoring:**
    - Implement performance monitoring tools to continuously analyze and identify bottlenecks in your application. Tools like Google Lighthouse, WebPageTest, and real-user monitoring (RUM) solutions can help.

**15. CDN and Content Optimization:**
    - Use CDNs and content optimization services to deliver assets more efficiently, perform image transformations on the fly, and optimize content delivery based on the user's location and device.

**16. Progressive Web Apps (PWAs):**
    - Convert your web application into a PWA to enable offline access, improve caching strategies, and provide a more native app-like experience.

**17. Content Delivery Optimization:**
    - Optimize the delivery of dynamic content using server-side caching mechanisms, like Redis or Memcached.

**18. Error Handling and Monitoring:**
    - Implement proper error handling and monitoring to detect and resolve issues quickly, reducing the impact on user experience.

These techniques are not exhaustive, and the specific optimizations you implement may vary depending on your application's architecture and requirements. Regular performance testing and profiling are essential to identify and address bottlenecks in your application.

</p>
</details>

---

###### 25. How do you approach a hotfix in production?

<details><summary><b>Answer</b></summary>
<p>
Approaching a hotfix in a production environment requires careful planning and execution to minimize downtime, avoid introducing new issues, and ensure the stability of your application. Here's a step-by-step approach to handling a hotfix in production:

1. **Identify the Issue:**
   - First, you need to clearly understand and document the issue that requires a hotfix. Ensure that you have a precise description of the problem and the expected behavior.

2. **Prioritize the Hotfix:**
   - Assess the severity and impact of the issue to determine its priority. Critical and high-impact issues should be addressed immediately, while less critical issues can be scheduled for regular updates.

3. **Develop and Test the Fix:**
   - Create a fix for the issue in a development or staging environment. Ensure that the fix is thoroughly tested to verify that it resolves the problem without introducing new bugs.
   - Write unit tests or, if applicable, integration tests to validate the fix.

4. **Code Review:**
   - Have the fix reviewed by one or more team members to ensure code quality, maintainability, and adherence to coding standards.

5. **Release Planning:**
   - Plan the release of the hotfix during a maintenance window or at a time when it will cause the least disruption to users. Consider factors like user activity and traffic patterns.

6. **Notify Stakeholders:**
   - Inform relevant stakeholders, including product owners, project managers, and support teams, about the upcoming hotfix and its expected downtime (if any).

7. **Backup and Rollback Plan:**
   - Before deploying the hotfix, make a backup of the production environment to ensure you can roll back quickly in case of unexpected issues.

8. **Deployment:**
   - Deploy the hotfix to the production environment according to your deployment plan. This may involve updating code, configuration, or database schema changes, depending on the nature of the fix.

9. **Monitoring:**
   - Monitor the production environment closely during and after the hotfix deployment to detect any anomalies or issues that may arise.

10. **Verification:**
    - After the hotfix is deployed, perform thorough testing to confirm that the issue is resolved and that the application is functioning correctly.

11. **Communication:**
    - Keep stakeholders informed about the progress of the hotfix deployment. If the deployment is taking longer than expected or if any issues are encountered, provide regular updates.

12. **Post-Deployment Validation:**
    - Continue monitoring the production environment and review logs to ensure that the hotfix did not introduce new problems or negatively impact performance.

13. **Documentation:**
    - Document the hotfix, including details of the issue, the fix applied, and any necessary steps taken during deployment.

14. **Communication of Resolution:**
    - Once the hotfix is confirmed to be successful, communicate with stakeholders, including end-users if necessary, to let them know that the issue has been resolved.

15. **Review and Post-Mortem:**
    - After the hotfix is completed, conduct a post-mortem meeting to analyze the root cause of the issue and identify ways to prevent similar incidents in the future.

16. **Close the Loop:**
    - Ensure that the hotfix is also applied to development, staging, and any other relevant environments to maintain consistency.

Remember that hotfixes should be approached with caution, and they should only address critical issues that cannot wait for the next regular release cycle. Proper testing and validation are crucial to ensure that the hotfix does not introduce new problems or compromise the stability of the production environment.

</p>
</details>

---

###### 26. Implement retry Api call on failure in js

<details><summary><b>Answer</b></summary>
<p>
Implementing retry logic for API calls in JavaScript can help improve the reliability of your application by automatically retrying failed requests. Here's an example of how to implement a simple retry mechanism using JavaScript and the Fetch API:

```javascript
function retryApiCall(url, maxRetries, delayBetweenRetries) {
  let retries = 0;

  function doRequest() {
    return fetch(url)
      .then((response) => {
        if (!response.ok) {
          throw new Error(`HTTP error! Status: ${response.status}`);
        }
        return response.json();
      })
      .catch((error) => {
        retries++;
        if (retries <= maxRetries) {
          console.log(`Retrying (attempt ${retries})...`);
          return new Promise((resolve) => setTimeout(resolve, delayBetweenRetries)).then(doRequest);
        } else {
          throw error; // If max retries reached, propagate the error
        }
      });
  }

  return doRequest();
}

// Example usage:
const apiUrl = 'https://example.com/api/data';
const maxRetries = 3;
const delayBetweenRetries = 1000; // 1 second

retryApiCall(apiUrl, maxRetries, delayBetweenRetries)
  .then((data) => {
    console.log('API call succeeded:', data);
  })
  .catch((error) => {
    console.error('API call failed after retries:', error);
  });
```

In this example:

- `retryApiCall` is a function that takes the API URL, the maximum number of retries, and the delay between retries as parameters.

- Inside `doRequest`, we use the Fetch API to make the API call and handle the response. If the response is not successful (e.g., a non-200 status code), we catch the error and initiate a retry.

- If the maximum number of retries (`maxRetries`) has not been reached, we use `setTimeout` to introduce a delay between retries (to avoid overloading the server). We then recursively call `doRequest` to retry the API call.

- If the maximum number of retries is reached, we propagate the error to the caller.

You can adjust the values of `maxRetries` and `delayBetweenRetries` to suit your specific requirements. This example demonstrates a basic retry mechanism, and you can extend it to include more advanced features like exponential backoff or custom error handling based on your application's needs.

</p>
</details>

---

###### 27. Cancel the API call with a timeout of 5 seconds in js

<details><summary><b>Answer</b></summary>
<p>
To cancel an API call with a timeout of 5 seconds in JavaScript, you can use the `AbortController` API. Here's an example of how to do this using the Fetch API:

```javascript
function fetchWithTimeout(url, timeout) {
  const controller = new AbortController();
  const signal = controller.signal;

  const timeoutId = setTimeout(() => {
    controller.abort(); // Abort the request when the timeout is reached
  }, timeout);

  const fetchPromise = fetch(url, { signal })
    .then((response) => {
      clearTimeout(timeoutId); // Clear the timeout if the request succeeds
      if (!response.ok) {
        throw new Error(`HTTP error! Status: ${response.status}`);
      }
      return response.json();
    });

  return fetchPromise;
}

// Example usage:
const apiUrl = 'https://example.com/api/data';
const timeout = 5000; // 5 seconds

fetchWithTimeout(apiUrl, timeout)
  .then((data) => {
    console.log('API call succeeded:', data);
  })
  .catch((error) => {
    if (error.name === 'AbortError') {
      console.error('API call timed out:', error.message);
    } else {
      console.error('API call failed:', error.message);
    }
  });
```

In this example:

- We create an `AbortController` instance and obtain its `signal`. We also set up a `setTimeout` that will call `controller.abort()` after the specified `timeout` duration (5 seconds).

- We pass the `signal` to the `fetch` function as an option. If the timeout occurs before the request is complete, the request will be aborted.

- We also clear the `setTimeout` using `clearTimeout` if the request succeeds to prevent it from aborting the request.

- If the request times out, the `catch` block handles the `AbortError`, and you can handle it as a timeout error.

This approach allows you to set a timeout for API calls and gracefully handle cases where the request exceeds the specified timeout duration.

</p>
</details>

---

###### 28. Create a simple menu in HTML and CSS, also please be considerate about semantic elements.

<details><summary><b>Answer</b></summary>
<p>
Certainly! Here's an example of a simple menu created using HTML and CSS, with a focus on using semantic HTML elements:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Simple Menu</title>
    <style>
        /* Basic styles for the menu */
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
        }
        
        header {
            background-color: #333;
            color: #fff;
            padding: 10px 0;
            text-align: center;
        }
        
        nav {
            background-color: #444;
            text-align: center;
        }
        
        ul {
            list-style: none;
            padding: 0;
            margin: 0;
        }
        
        li {
            display: inline;
            margin-right: 20px;
        }
        
        a {
            text-decoration: none;
            color: #fff;
            font-weight: bold;
        }
        
        /* Style for hover effect on links */
        a:hover {
            text-decoration: underline;
        }
    </style>
</head>
<body>
    <header>
        <h1>My Website</h1>
    </header>
    <nav>
        <ul>
            <li><a href="#">Home</a></li>
            <li><a href="#">About</a></li>
            <li><a href="#">Services</a></li>
            <li><a href="#">Portfolio</a></li>
            <li><a href="#">Contact</a></li>
        </ul>
    </nav>
    <main>
        <!-- Your main content goes here -->
        <p>Welcome to my website. This is a simple menu example.</p>
    </main>
</body>
</html>
```

In this example:

- We use semantic HTML elements like `<header>`, `<nav>`, `<ul>`, `<li>`, and `<a>` to structure and style the menu.

- The menu items are placed within an unordered list (`<ul>`) and list items (`<li>`) for better semantic structure.

- CSS is used to style the header and menu items. We've added basic styles for readability and a hover effect on the links.

Feel free to modify the content and styles to match your specific website's needs.

</p>
</details>

---

###### 29. How will you upgrade Angular 6 to latest version of angular.

<details><summary><b>Answer</b></summary>
<p>


</p>
</details>






###### 30. Deep copy and shallow copy

<details><summary><b>Answer</b></summary>
<p>
Deep copy and shallow copy are two concepts related to copying objects or data structures in programming. They differ in how they handle nested objects or references within the copied data. Let's explore each concept:

**Shallow Copy:**

A shallow copy of an object or data structure creates a new copy of the top-level structure but does not recursively create copies of nested objects or references. Instead, it copies references to the nested objects. As a result, changes made to nested objects in the copied structure are reflected in both the original and copied structures.

In JavaScript, you can create a shallow copy of an object using methods like `Object.assign()`, the spread operator (`...`), or by iterating over the properties and manually copying them.

Here's an example of creating a shallow copy in JavaScript:

```javascript
const originalObject = {
  name: 'John',
  address: {
    street: '123 Main St',
    city: 'Exampleville',
  },
};

// Shallow copy using the spread operator
const shallowCopy = { ...originalObject };

shallowCopy.name = 'Jane'; // Changes the name property in the shallow copy

// Changes the city property in the nested object, affecting both the original and the shallow copy
shallowCopy.address.city = 'Newville';

console.log(originalObject); // Original object is also affected
console.log(shallowCopy);    // Shallow copy
```

**Deep Copy:**

A deep copy of an object or data structure creates a completely independent copy of the entire structure, including all nested objects and references. Changes made to nested objects in the copied structure do not affect the original structure, and vice versa.

Creating a deep copy often requires recursion to traverse the entire structure and create new instances of nested objects and values. Libraries like Lodash provide functions to perform deep copies.

Here's an example of creating a deep copy in JavaScript using Lodash:

```javascript
const _ = require('lodash'); // Import the Lodash library

const originalObject = {
  name: 'John',
  address: {
    street: '123 Main St',
    city: 'Exampleville',
  },
};

// Deep copy using Lodash's cloneDeep
const deepCopy = _.cloneDeep(originalObject);

deepCopy.name = 'Jane'; // Changes the name property in the deep copy

// Changes the city property in the nested object, but does not affect the original object
deepCopy.address.city = 'Newville';

console.log(originalObject); // Original object remains unchanged
console.log(deepCopy);      // Deep copy
```

In summary, a shallow copy replicates the top-level structure and references to nested objects, while a deep copy creates a completely independent copy of the entire structure, including nested objects. The choice between shallow and deep copy depends on the specific requirements of your program and whether you want changes in one copy to affect the other.

</p>
</details>

---

###### 31. Pure pipe and impure pipe

<details><summary><b>Answer</b></summary>
<p>
In Angular, pipes are used for data transformation in templates, allowing you to format and manipulate data before it's displayed. Angular pipes can be categorized into two main types: pure pipes and impure pipes. These categories determine how and when pipes execute their transformations.

**Pure Pipes:**

1. **Immutable**: Pure pipes are considered immutable because they do not modify the input data. Instead, they transform the data and return a new instance with the transformed result.

2. **Efficiency**: Pure pipes are highly efficient and performant because they execute their transformation logic only when the input data changes. Angular checks if the input data has changed using a mechanism called change detection. If the input data is the same as the previous value, the pipe's transformation is skipped.

3. **Use Cases**: Pure pipes are suitable for scenarios where the input data should not be modified, and the transformation logic is deterministic and does not rely on external factors. Common use cases for pure pipes include formatting dates, numbers, and currency, as well as sorting and filtering arrays.

4. **Syntax**: In Angular, you can create a pure pipe by implementing the `PipeTransform` interface and specifying the `pure: true` option in the `@Pipe` decorator.

**Impure Pipes:**

1. **Mutable**: Impure pipes are considered mutable because they can modify the input data or execute their transformation logic even when the input data has not changed. This means that impure pipes may run more frequently, potentially impacting performance.

2. **Efficiency**: Impure pipes are less efficient than pure pipes because they execute their transformation logic on every change detection cycle, regardless of whether the input data has changed or not.

3. **Use Cases**: Impure pipes are suitable for scenarios where the transformation logic depends on external factors or where the input data can change frequently. However, it's essential to use impure pipes judiciously to avoid unnecessary performance overhead.

4. **Syntax**: In Angular, you can create an impure pipe by implementing the `PipeTransform` interface without specifying the `pure` option in the `@Pipe` decorator. Impure pipes are the default type if the `pure` option is not provided.

To summarize, the main difference between pure and impure pipes in Angular is their behavior regarding change detection and efficiency. Pure pipes are efficient and execute only when the input data changes, while impure pipes can execute their logic more frequently and may not always produce the same output for the same input data. The choice between them depends on the specific requirements of your application and the nature of the data transformations you need to perform.

</p>
</details>

---

###### 32. Pseudo class in css

<details><summary><b>Answer</b></summary>
<p>
Pseudo-classes in CSS are used to define special states or behaviors of HTML elements that cannot be targeted with simple element selectors alone. Pseudo-classes are denoted by a colon (`:`) followed by their name. They allow you to style elements based on user interactions, structural properties, and other criteria. Here are some commonly used pseudo-classes in CSS:

1. **`:hover`**: This pseudo-class is used to style an element when a user hovers their mouse pointer over it. It's commonly used for creating hover effects, such as changing the color of a link when it's hovered over.

   ```css
   a:hover {
     color: red;
   }
   ```

2. **`:active`**: This pseudo-class is applied to an element while it's being activated by a user. For example, when a user clicks on a link, it enters the `:active` state.

   ```css
   button:active {
     background-color: #f00;
   }
   ```

3. **`:focus`**: The `:focus` pseudo-class is applied to an element when it receives keyboard focus or is clicked with the mouse. It's often used to style form elements like input fields and buttons that are currently selected.

   ```css
   input:focus {
     border-color: blue;
   }
   ```

4. **`:nth-child()`**: This pseudo-class allows you to select elements based on their position within a parent element. You can specify a formula to target elements at specific positions.

   ```css
   li:nth-child(odd) {
     background-color: lightgray;
   }
   ```

5. **`:not()`**: The `:not()` pseudo-class lets you select elements that do not match a given selector. It's handy for excluding specific elements from a CSS rule.

   ```css
   p:not(.special) {
     font-style: italic;
   }
   ```

6. **`:first-child` and `:last-child`**: These pseudo-classes target the first and last child elements of a parent element.

   ```css
   ul li:first-child {
     font-weight: bold;
   }

   ul li:last-child {
     font-style: italic;
   }
   ```

7. **`:nth-of-type()`**: Similar to `:nth-child()`, this pseudo-class selects elements based on their position within their parent element but considers their type (tag name).

   ```css
   p:nth-of-type(2n) {
     color: blue;
   }
   ```

8. **`:checked`**: This pseudo-class targets input elements (such as checkboxes or radio buttons) that are checked.

   ```css
   input[type="checkbox"]:checked {
     background-color: yellow;
   }
   ```

These are just a few examples of the many pseudo-classes available in CSS. Pseudo-classes allow you to create dynamic and interactive styles in your web pages by targeting elements based on various states and conditions.

</p>
</details>

---

###### 33. How to bootstrap the angular application.

<details><summary><b>Answer</b></summary>
<p>
The flow from hitting `ng serve` to loading a component in an Angular application involves several steps. Here's a high-level overview of the process:

1. **Command Execution:**
   - You open your terminal or command prompt and run the `ng serve` command within the root directory of your Angular application.

2. **Webpack Compilation:**
   - Angular CLI uses Webpack to bundle and compile your application's code.
   - Webpack reads the application's entry point (usually `src/main.ts`) and starts processing the code and assets.

3. **Module Loading:**
   - Webpack loads the root module of your Angular application (e.g., `AppModule`) as specified in the `main.ts` file.
   - The module contains metadata about how to bootstrap your application.

4. **Bootstrapping:**
   - Angular's bootstrapping process begins. This process initializes the Angular framework and prepares it for execution.
   - It creates an instance of the root module and configures the application.

5. **Component Loading:**
   - The root component of your Angular application (often named `AppComponent`) is loaded. This component is defined in the root module.
   - The component's HTML template and associated CSS styles are compiled and processed.

6. **HTML Element Binding:**
   - Angular searches for the HTML element specified by the root component's selector (e.g., `<app-root></app-root>`) in the `index.html` file.
   - It binds the root component to this HTML element.

7. **Component Initialization:**
   - The `AppComponent` (or the root component of your application) is initialized. Its constructor is called, and any necessary services or dependencies are injected.

8. **Rendering and DOM Manipulation:**
   - Angular takes over the rendering of the application and manages the DOM.
   - It processes the component's template, including any directives, data binding, and structural directives, to generate the initial view.
   - The view is inserted into the HTML element bound to the component.

9. **Lifecycle Hooks:**
   - Angular component lifecycle hooks, such as `ngOnInit`, `ngOnChanges`, and `ngAfterViewInit`, are called at specific points during component initialization.

10. **Live Reload (Optional):**
    - If you make changes to your code or assets while `ng serve` is running, the development server detects these changes.
    - Automatic live reload is triggered, and the browser is refreshed to reflect the updated application state.

11. **User Interaction:**
    - Users can interact with the application through the UI. Angular handles user input, events, and data flow according to the application's logic.

12. **Further Component Loading (Lazy Loading):**
    - As users navigate through the application, additional components may be loaded dynamically, especially if you're using lazy loading for feature modules.

13. **API Requests and Data Loading:**
    - Components may make API requests to fetch data from a server or use data services to retrieve or manipulate data.
    - Data is bound to the view, and the UI is updated accordingly.

14. **Routing (Optional):**
    - If your application uses Angular Router, routing components are loaded based on the defined routes, and the router manages navigation between views.

15. **Continuous Development:**
    - Developers can continue to make changes to the application code and assets.
    - The development server automatically recompiles and reloads the application, allowing developers to see the changes instantly.

This is a simplified overview of the flow from running `ng serve` to loading a component in an Angular application. The actual process involves many more details and optimizations performed by Angular and Webpack to ensure efficient execution and a responsive user experience.

</p>
</details>

---

###### 34. File architecture of Angular application

<details><summary><b>Answer</b></summary>
<p>
Certainly, here's a comprehensive overview of the file architecture of an Angular application, including Angular CLI configuration files, package.json, and testing-related files:

```plaintext
project-root/
|-- angular.json
|-- package.json
|-- src/
|   |-- app/
|   |   |-- components/
|   |   |   |-- app.component.html
|   |   |   |-- app.component.ts
|   |   |   |-- ...
|   |   |-- services/
|   |   |   |-- data.service.ts
|   |   |   |-- ...
|   |   |-- modules/
|   |   |   |-- app.module.ts
|   |   |   |-- feature-module/
|   |   |   |   |-- feature.component.ts
|   |   |   |   |-- feature-routing.module.ts
|   |   |   |   |-- ...
|   |   |-- shared/
|   |   |   |-- shared.module.ts
|   |   |   |-- shared-component/
|   |   |   |   |-- shared.component.ts
|   |   |   |   |-- ...
|-- assets/
|   |-- images/
|   |   |-- logo.png
|   |   |-- ...
|-- environments/
|   |-- environment.prod.ts
|   |-- environment.ts
|-- src/
|   |-- app/
|   |   |-- components/
|   |   |   |-- ...
|   |-- environments/
|   |   |-- ...
|   |-- app.component.spec.ts  // Component tests
|   |-- app.module.spec.ts     // Module tests
|-- e2e/
|   |-- ...
|-- node_modules/               // Installed npm packages
|-- dist/                      // Build output
|-- index.html
|-- main.ts
|-- polyfills.ts
|-- styles.css
|-- ...
|-- karma.conf.js              // Karma configuration for unit tests
|-- protractor.conf.js         // Protractor configuration for e2e tests
|-- tsconfig.json              // TypeScript configuration
|-- tslint.json                // TSLint configuration
|-- ...
```

In this comprehensive project structure:

- `project-root/` represents the root directory of your Angular project.

- `angular.json` is the Angular CLI configuration file containing project-specific settings.

- `package.json` manages project dependencies and scripts.

- `src/` contains your application's source code.

  - `app/` holds Angular components, services, modules, and shared components.

  - `assets/` stores static assets like images and fonts.

  - `environments/` contains environment-specific configuration files.

  - `app.component.spec.ts` and `app.module.spec.ts` are test files for components and modules.

- `e2e/` is the directory for end-to-end (e2e) testing using tools like Protractor.

- `node_modules/` contains the installed npm packages and dependencies.

- `dist/` is the output directory where the built application files are placed after running a build command (`ng build`).

- `index.html` is the main HTML entry point for your Angular application.

- `main.ts` initializes the Angular application.

- `polyfills.ts` includes polyfills for compatibility.

- `styles.css` contains global styles for your application.

- `karma.conf.js` is the configuration file for Karma, a test runner for unit tests.

- `protractor.conf.js` is the configuration file for Protractor, an e2e testing framework.

- `tsconfig.json` is the TypeScript configuration file that specifies how TypeScript should compile your code.

- `tslint.json` is the configuration file for TSLint, a static analysis tool for TypeScript.

This structured project layout helps you organize your Angular application code, configuration files, and testing-related files efficiently. It follows best practices and conventions to enhance maintainability and scalability in your Angular projects.
</p>
</details>

---

###### 35. Host listener

<details><summary><b>Answer</b></summary>
<p>
In Angular, the `@HostListener` decorator is used to attach event listeners to host elements in a component. Host elements are the elements in the component's template where the directive or decorator is applied. `@HostListener` allows you to respond to DOM events, such as clicks, key presses, or mouse movements, and execute specific functions or methods when those events occur. Here's how to use `@HostListener`:

```typescript
import { Component, HostListener } from '@angular/core';

@Component({
  selector: 'app-my-component',
  template: `
    <div (click)="onClick()">Click me</div>
  `,
})
export class MyComponent {
  @HostListener('window:keydown', ['$event'])
  onKeyDown(event: KeyboardEvent): void {
    // Handle keydown events on the window
    console.log(`Key pressed: ${event.key}`);
  }

  onClick(): void {
    // Handle click events on the host element
    console.log('Element clicked');
  }
}
```

In this example:

- We import `HostListener` from `@angular/core`.

- The `MyComponent` class contains a `@HostListener` decorator for both a click event on the host element (a `<div>` in this case) and a keydown event on the `window` object.

- The `@HostListener` decorator takes two arguments:
  1. The first argument is the event name (e.g., `'click'`, `'window:keydown'`) to which you want to listen.
  2. The second argument is an array of strings, where each string represents a variable that will receive the event object. In the example, we're listening for the `'window:keydown'` event and passing the `event` object to the `onKeyDown` method.

- Inside the `onKeyDown` method, we handle keydown events on the window and log the pressed key to the console.

- The click event is handled by the `onClick` method, which logs a message to the console when the host element (the `<div>`) is clicked.

You can use `@HostListener` to respond to a wide range of events on the host element and other elements in your Angular components. It's a powerful tool for adding interactivity and event-driven behavior to your Angular applications.

</p>
</details>

---

###### 36. how to unsubscribe to all the subscription at once

<details><summary><b>Answer</b></summary>
<p>
Unsubscribing from multiple subscriptions at once can be done using a pattern called "subscription management" or by utilizing libraries like `rxjs`. Below are two common approaches for unsubscribing from multiple subscriptions:

**1. Subscription Management:**

In this approach, you manually manage your subscriptions by storing them in an array and then unsubscribing from all of them when needed. Here's a step-by-step example:

```typescript
import { Component, OnInit, OnDestroy } from '@angular/core';
import { Subscription } from 'rxjs';

@Component({
  selector: 'app-my-component',
  template: `
    <!-- Your component template here -->
  `,
})
export class MyComponent implements OnInit, OnDestroy {
  private subscriptions: Subscription[] = [];

  ngOnInit(): void {
    // Create and store your subscriptions
    this.subscriptions.push(this.someObservable.subscribe(/* ... */));
    this.subscriptions.push(this.anotherObservable.subscribe(/* ... */));
  }

  ngOnDestroy(): void {
    // Unsubscribe from all stored subscriptions
    this.subscriptions.forEach((subscription) => subscription.unsubscribe());
  }
}
```

In this example:

- We import the `Subscription` class from `'rxjs'` to manage our subscriptions.

- Inside the `ngOnInit` method, we create and store our subscriptions in the `subscriptions` array.

- In the `ngOnDestroy` method (which is called when the component is destroyed), we iterate through the `subscriptions` array and unsubscribe from each subscription.

**2. Using `takeUntil` Operator:**

Another approach is to use the `takeUntil` operator from the `rxjs` library to automatically unsubscribe from multiple subscriptions when a "destroy" signal is emitted. This approach is often preferred because it reduces the need for manually managing subscription arrays.

Here's an example of how to use `takeUntil`:

```typescript
import { Component, OnDestroy } from '@angular/core';
import { Subject } from 'rxjs';
import { takeUntil } from 'rxjs/operators';

@Component({
  selector: 'app-my-component',
  template: `
    <!-- Your component template here -->
  `,
})
export class MyComponent implements OnDestroy {
  private destroy$ = new Subject<void>();

  ngOnInit(): void {
    this.someObservable.pipe(
      takeUntil(this.destroy$)
    ).subscribe(/* ... */);

    this.anotherObservable.pipe(
      takeUntil(this.destroy$)
    ).subscribe(/* ... */);
  }

  ngOnDestroy(): void {
    // Emit the "destroy" signal to unsubscribe from all subscriptions
    this.destroy$.next();
    this.destroy$.complete();
  }
}
```

In this example:

- We import the `Subject` class and the `takeUntil` operator from `'rxjs'`.

- We create a `destroy$` subject that will serve as the "destroy" signal.

- In the `ngOnInit` method, we use the `takeUntil` operator to automatically unsubscribe from the observables when `destroy$` emits a value.

- In the `ngOnDestroy` method, we emit a value into the `destroy$` subject, which triggers the unsubscription through the `takeUntil` operator.

Both of these approaches ensure that you unsubscribe from multiple subscriptions when the component is destroyed, helping to prevent memory leaks and unexpected behavior in your Angular application. The choice of which approach to use depends on your coding style and project requirements.
</p>
</details>

---

###### 37. Switchmap Vs Map

<details><summary><b>Answer</b></summary>
<p>
`switchMap` and `map` are two commonly used operators in RxJS, a library for reactive programming in JavaScript. While both operators are used for transforming data emitted by observables, they serve different purposes and have distinct behaviors:

**`map` Operator:**
- The `map` operator is used to transform the data emitted by an observable into a new form. It takes each emitted value, applies a transformation function to it, and emits the result as a new observable.

  ```javascript
  import { of } from 'rxjs';
  import { map } from 'rxjs/operators';

  const source$ = of(1, 2, 3, 4);

  source$.pipe(
    map(value => value * 2)
  ).subscribe(result => console.log(result)); // Output: 2, 4, 6, 8
  ```

- The `map` operator does not change the number of emitted values; it transforms each value one by one and emits them as they are processed.

**`switchMap` Operator:**
- The `switchMap` operator is used to handle scenarios where you have one observable and you want to switch to another observable based on each emitted value. It "switches" to a new observable whenever a new value is emitted.

  ```javascript
  import { fromEvent } from 'rxjs';
  import { switchMap } from 'rxjs/operators';

  const button = document.querySelector('button');

  const source$ = fromEvent(button, 'click');

  source$.pipe(
    switchMap(() => from(fetch('https://api.example.com/data')))
  ).subscribe(response => console.log(response));
  ```

- In this example, when a button click event is emitted, `switchMap` switches to a new observable created by the `fetch` call. This allows you to perform actions like making HTTP requests based on user interactions.

In summary:

- `map` is used for one-to-one value transformations within the same observable stream. It does not switch to a different observable.

- `switchMap` is used to switch to a new observable based on each emitted value, often used for scenarios involving asynchronous operations like HTTP requests or when you need to "cancel" the previous observable when a new value arrives.

The choice between `map` and `switchMap` depends on your specific use case. Use `map` for simple value transformations, and use `switchMap` when you need to work with multiple observables and switch between them dynamically.

</p>
</details>

---

###### 38. Mono repo & Module federation

<details><summary><b>Answer</b></summary>
<p>
Mono repo and Module Federation are two concepts related to structuring and managing complex JavaScript and front-end applications, often used in the context of modern web development. Let's explore each concept:

**Mono Repo:**

A mono repo, short for "monolithic repository," is a software development structure where multiple projects or modules are housed within a single version control repository. In the context of front-end development, a mono repo typically contains multiple related web applications, libraries, or micro-frontends that share common code and dependencies.

Key characteristics of a mono repo:

1. **Code Sharing:** All projects or modules within the mono repo can share code and dependencies. This can lead to better code reuse, consistency, and easier maintenance.

2. **Centralized Configuration:** You can manage build, test, and deployment configurations centrally, which can simplify project management and standardize development practices.

3. **Simplified Dependency Management:** Managing dependencies across projects is more streamlined since they share the same `node_modules` directory, reducing potential version conflicts.

4. **Cross-Project Refactoring:** Refactoring or making changes across multiple projects becomes more manageable since they are in the same repository.

Mono repos are commonly used in large-scale web development where multiple related applications or micro-frontends need to be developed, maintained, and deployed together.

**Module Federation:**

Module Federation is a feature introduced in Webpack 5 that facilitates the development of micro-frontends and the dynamic loading of JavaScript modules across different web applications. It enables you to build a federated architecture where individual applications can share and load modules from one another.

Key characteristics of Module Federation:

1. **Dynamic Module Loading:** Module Federation allows you to load JavaScript modules dynamically at runtime. This means you can split your code into smaller, independently deployable pieces and load them as needed.

2. **Independent Development:** You can develop and deploy micro-frontends as separate entities, each with its own repository and build process, while still allowing them to interoperate.

3. **Shared Dependencies:** Module Federation enables the sharing of dependencies between micro-frontends, reducing the need to duplicate common libraries.

4. **Runtime Integration:** Micro-frontends that use Module Federation can integrate with each other at runtime, allowing for a seamless user experience while maintaining independent development workflows.

When combined with mono repo structures, Module Federation can provide a powerful solution for building and maintaining complex applications with a modular, federated architecture.

In summary, a mono repo is a repository structure that contains multiple projects or modules, while Module Federation is a technology that facilitates the dynamic loading and sharing of modules between different web applications. These concepts can be used together to build scalable, maintainable, and modular web applications, especially in large and complex development environments.

</p>
</details>

---

###### 39. Viewchild and static

<details><summary><b>Answer</b></summary>
<p>
In Angular, `@ViewChild` is a decorator used to query and access child elements, components, or directives within a component. It allows you to obtain references to elements or components that are part of the template of the current component. `static` is an optional parameter for `@ViewChild` that affects when the query is executed. Here's how `@ViewChild` and `static` work together:

### Basic Usage of `@ViewChild`:

```typescript
import { Component, ViewChild, ElementRef } from '@angular/core';

@Component({
  selector: 'app-my-component',
  template: '<div #myDivElement>Child Element</div>',
})
export class MyComponent {
  @ViewChild('myDivElement') myDiv: ElementRef;

  ngAfterViewInit() {
    console.log(this.myDiv.nativeElement.textContent);
  }
}
```

In this example:

- We have a component (`MyComponent`) with a child `div` element in its template.

- We use `@ViewChild` to query the child `div` element using the template reference variable `#myDivElement`.

- In the `ngAfterViewInit` lifecycle hook, we can access the `div` element using `this.myDiv.nativeElement`.

### `static` Parameter:

The `static` parameter is used to control when the query should be executed. It can have two possible values:

1. `static: true` (default behavior):
   - When `static` is set to `true`, the query is executed at the earliest possible moment, which is before the `ngOnInit` lifecycle hook.
   - This means that if the queried element/component is not available at that time (e.g., it's part of a structural directive like `*ngIf` and is not in the DOM yet), the query will result in `undefined`.

   ```typescript
   @ViewChild('myDivElement', { static: true }) myDiv: ElementRef;
   ```

2. `static: false`:
   - When `static` is set to `false`, the query is executed after the `ngOnInit` lifecycle hook, during the `ngAfterViewInit` lifecycle hook.
   - This allows you to query elements/components that are created dynamically or conditionally in your template because, by the time `ngAfterViewInit` is called, the DOM is fully rendered.

   ```typescript
   @ViewChild('myDivElement', { static: false }) myDiv: ElementRef;
   ```

The choice of whether to use `{ static: true }` or `{ static: false }` depends on your specific use case. Use `{ static: false }` when you need to access elements/components that are created dynamically or are part of structural directives like `*ngIf` that may not be in the DOM during `ngOnInit`. Otherwise, the default `{ static: true }` behavior is usually sufficient.

Here's a simple rule of thumb:
- If you're querying an element or component in the template that's always present and doesn't depend on conditions or dynamic rendering, you can use the default `{ static: true }`.
- If you're querying an element or component that depends on dynamic conditions or is created dynamically, use `{ static: false }` to ensure that the query is executed after `ngOnInit`.

</p>
</details>

---

###### 40. How to fit all square in corner

<details><summary><b>Answer</b></summary>
<p>
Fitting squares into a corner, also known as a "packing problem," can be approached in various ways depending on the specific constraints and requirements of your design. Here, I'll provide a basic example of how to arrange squares in one corner of a larger square or rectangular container using HTML and CSS.

Assuming you want to fit multiple squares into the top-left corner of a container, you can achieve this using a combination of HTML and CSS. Here's a step-by-step guide:

1. **HTML Structure**:
   Start by creating the HTML structure for your container and the squares you want to fit into the corner. In this example, I'll use three squares:

   ```html
   <div class="container">
     <div class="square"></div>
     <div class="square"></div>
     <div class="square"></div>
   </div>
   ```

2. **CSS Styling**:
   Apply CSS styles to position the squares within the container. You'll want to use CSS Flexbox for this purpose. Define styles for the container and squares as follows:

   ```css
   .container {
     display: flex;
     flex-direction: column;
     justify-content: flex-start;
     align-items: flex-start;
     width: 200px; /* Adjust the container size as needed */
     height: 200px;
     border: 1px solid #000;
     padding: 10px;
   }

   .square {
     width: 50px; /* Adjust the square size as needed */
     height: 50px;
     background-color: #3498db;
     margin: 5px; /* Adjust the spacing between squares */
   }
   ```

   In this example, we're using Flexbox to position the squares in a column layout (`flex-direction: column`) and align them to the start of both the horizontal and vertical axes (`justify-content: flex-start` and `align-items: flex-start`). You can adjust the container and square sizes as well as the spacing between squares to fit your design requirements.

3. **Result**:
   With the HTML and CSS defined as above, the squares will be arranged in the top-left corner of the container. You can add more squares or adjust the sizes and spacing as needed.

   ![Fitting Squares in a Corner](https://i.imgur.com/49P6wHm.png)

This is a basic example of how to fit squares into a corner using HTML and CSS. Depending on your specific needs and constraints, you may need to explore more advanced techniques or use JavaScript for more complex layouts or dynamic adjustments.

</p>
</details>

---

###### 41. Module federation

<details><summary><b>Answer</b></summary>
<p>


</p>
</details>

---

###### 42. Architecture of NGXS

<details><summary><b>Answer</b></summary>
<p>
NgRx is a popular state management library for Angular applications, inspired by Redux. It provides a predictable and centralized way to manage the state of your Angular application, making it easier to handle complex data flows, interactions, and state changes. The architecture of NgRx consists of several key components and concepts:

1. **Store**: The central store is the heart of the NgRx architecture. It holds the entire state of your application as a single, immutable object. Each piece of state is organized into slices, usually represented by feature modules.

2. **Actions**: Actions are plain JavaScript objects that represent an intention to change the state. Actions are dispatched by components or services and describe what happened in the application. An action typically has a type and may contain additional payload data.

3. **Reducers**: Reducers are pure functions that specify how the state should change in response to dispatched actions. They take the current state and an action as input and return a new state based on the action. Reducers are responsible for returning a new state object or modifying the existing one immutably.

4. **Selectors**: Selectors are functions that allow you to query the store's state to retrieve specific pieces of data. They encapsulate the logic for extracting data from the store and can be memoized for performance optimization.

5. **Effects**: Effects are used to manage side effects in your application, such as making HTTP requests, interacting with the browser's storage, or dispatching additional actions. They listen for dispatched actions, perform asynchronous operations, and then dispatch new actions to update the state.

6. **Actions and Reducers Composition**: NgRx encourages the use of feature stores, where actions and reducers are grouped by feature. This modular approach helps manage state for different parts of your application separately.

7. **Entity State**: NgRx also provides a convenient way to manage collections of entities (e.g., lists of items) using an entity state adapter. This simplifies common CRUD operations on collections.

8. **Router Store**: NgRx includes a router store that synchronizes Angular router state with the application's store, allowing you to track and react to route changes.

9. **DevTools**: NgRx DevTools is an extension for browser developer tools that provides powerful debugging and time-traveling capabilities, allowing you to inspect and replay actions and state changes.

Here's a typical flow of how NgRx works in an Angular application:

1. A component or service dispatches an action to the store.

2. The action is passed to the relevant reducer, which calculates the new state based on the current state and the action.

3. The store updates its state with the new state returned by the reducer.

4. Selectors are used to query the store for specific data, which is then displayed in components.

5. Effects can listen for dispatched actions, perform side effects (e.g., make HTTP requests), and dispatch new actions to update the store.

By following these principles and components, NgRx provides a structured and maintainable way to manage the state of Angular applications, making them more predictable, testable, and scalable.

</p>
</details>

---

###### 43. CombineLatest

<details><summary><b>Answer</b></summary>
<p>
`combineLatest` is an operator in RxJS that combines the latest values from multiple observables into a single observable. It waits for all the input observables to emit at least one value and then emits an array containing the latest values from each of the input observables whenever any of them emits a new value.

Here's the basic syntax and usage of the `combineLatest` operator:

```javascript
import { combineLatest } from 'rxjs';

// Create two or more observables
const observable1$ = /* ... */;
const observable2$ = /* ... */;

// Combine the latest values from the observables
const combined$ = combineLatest(observable1$, observable2$);

// Subscribe to the combined observable
combined$.subscribe(([value1, value2]) => {
  // Handle the combined values here
});
```

Key points to note about `combineLatest`:

1. **Multiple Observables**: You can combine two or more observables using `combineLatest`. Pass them as arguments to the operator.

2. **Latest Values**: The `combineLatest` operator waits for all the input observables to emit at least one value, and then it emits an array containing the latest values from each of the input observables whenever any of them emits a new value.

3. **Synchronous Emission**: If all input observables have emitted at least one value by the time you subscribe to the combined observable, `combineLatest` will emit an initial combined value synchronously. If any of the observables have not emitted a value, it will not emit until they do.

4. **Continuous Updates**: Once the combined observable starts emitting, it will continue to emit new combined values whenever any of the input observables emit a new value.

Here's an example that illustrates the behavior of `combineLatest`:

```javascript
import { combineLatest, timer } from 'rxjs';

const observable1$ = timer(1000, 2000); // Emits 0, 1, 2, 3, ...
const observable2$ = timer(500, 1000);  // Emits 0, 1, 2, 3, ...

const combined$ = combineLatest(observable1$, observable2$);

combined$.subscribe(([value1, value2]) => {
  console.log(`Combined Value: [${value1}, ${value2}]`);
});

// Output:
// Combined Value: [0, 0]
// Combined Value: [1, 0]
// Combined Value: [1, 1]
// Combined Value: [2, 1]
// Combined Value: [2, 2]
// ...
```

In this example, `combineLatest` combines the latest values emitted by `observable1$` and `observable2$`. As soon as both observables emit their first values (0 in this case), the combined observable starts emitting arrays of the latest values whenever any of the observables emit new values.

</p>
</details>

---

###### 44. Different ways of creating object

<details><summary><b>Answer</b></summary>
<p>
In JavaScript, there are several ways to create objects. Each approach has its own use cases and characteristics. Here are some common ways to create objects:

1. **Object Literal**:
   You can create an object using the object literal notation, which is the most common way to create objects in JavaScript:

   ```javascript
   const person = {
     firstName: 'John',
     lastName: 'Doe',
     age: 30,
   };
   ```

2. **Constructor Function**:
   You can define a constructor function and then create objects using the `new` keyword:

   ```javascript
   function Person(firstName, lastName, age) {
     this.firstName = firstName;
     this.lastName = lastName;
     this.age = age;
   }

   const person = new Person('John', 'Doe', 30);
   ```

3. **Object.create()**:
   You can create an object using the `Object.create()` method. This method allows you to specify the prototype object:

   ```javascript
   const personPrototype = {
     greet: function() {
       console.log(`Hello, my name is ${this.firstName} ${this.lastName}`);
     },
   };

   const person = Object.create(personPrototype);
   person.firstName = 'John';
   person.lastName = 'Doe';
   person.age = 30;
   ```

4. **Class (ES6)**:
   With ES6, you can create objects using class syntax, which is a more structured way to define and create objects:

   ```javascript
   class Person {
     constructor(firstName, lastName, age) {
       this.firstName = firstName;
       this.lastName = lastName;
       this.age = age;
     }
   }

   const person = new Person('John', 'Doe', 30);
   ```

5. **Factory Function**:
   You can create objects using factory functions, which are functions that return new objects:

   ```javascript
   function createPerson(firstName, lastName, age) {
     return {
       firstName,
       lastName,
       age,
     };
   }

   const person = createPerson('John', 'Doe', 30);
   ```

6. **Singleton Pattern**:
   You can create a singleton object, ensuring that there's only one instance of the object throughout your application:

   ```javascript
   const singleton = (() => {
     let instance;

     function createInstance() {
       return {
         // Properties and methods
       };
     }

     return {
       getInstance: () => {
         if (!instance) {
           instance = createInstance();
         }
         return instance;
       },
     };
   })();

   const obj1 = singleton.getInstance();
   const obj2 = singleton.getInstance();

   console.log(obj1 === obj2); // true
   ```

These are some of the common ways to create objects in JavaScript. The choice of which method to use depends on your specific requirements and coding style. ES6 introduced class syntax, which is commonly used for creating objects in modern JavaScript applications.

</p>
</details>

---

###### 45. What is prototype?

<details><summary><b>Answer</b></summary>
<p>
In JavaScript, the prototype is a fundamental mechanism that enables object inheritance. Every JavaScript object has a prototype, which is an object itself, and it serves as a fallback source for properties and methods when they are not found on the object itself. Prototypes are used to implement inheritance, share behavior and properties among objects, and create a prototype chain.

Here's how prototypes work and some key concepts related to them:

1. **Prototype Object**: Each object in JavaScript (except for the root object, `Object.prototype`) has an associated prototype object. You can access an object's prototype using the `__proto__` property or the `Object.getPrototypeOf()` method.

2. **Prototype Chain**: When you try to access a property or method on an object, JavaScript first looks for that property or method on the object itself. If it doesn't find it, it continues the search in the object's prototype, and so on, creating a chain of prototypes until it reaches the `Object.prototype` at the top of the chain.

3. **Constructor Function**: Constructor functions, often used to create objects, have a `prototype` property that points to an object. When you create an object using a constructor function with the `new` keyword, the newly created object's prototype is set to the constructor function's `prototype` property.

   ```javascript
   function Person(name) {
     this.name = name;
   }

   Person.prototype.sayHello = function() {
     console.log(`Hello, my name is ${this.name}`);
   };

   const person = new Person('John');
   person.sayHello(); // Output: Hello, my name is John
   ```

4. **Object.create()**: You can create a new object with a specified prototype using the `Object.create()` method:

   ```javascript
   const protoObj = {
     greet: function() {
       console.log('Hello from the prototype!');
     },
   };

   const newObj = Object.create(protoObj);
   newObj.greet(); // Output: Hello from the prototype!
   ```

5. **Changing Prototypes**: You can change an object's prototype using the `Object.setPrototypeOf()` method. However, this is not commonly recommended because it can lead to unexpected behavior and performance issues.

   ```javascript
   const newObj = {};
   const protoObj = {
     greet: function() {
       console.log('Hello from the prototype!');
     },
   };

   Object.setPrototypeOf(newObj, protoObj);
   newObj.greet(); // Output: Hello from the prototype!
   ```

In summary, the prototype is a crucial concept in JavaScript's object-oriented nature. It enables inheritance and property/method lookup through a chain of objects. Understanding prototypes is fundamental when working with JavaScript's object-oriented features and when creating reusable and maintainable code.
</p>
</details>

---

###### 46. How to center a div

<details><summary><b>Answer</b></summary>
<p>
To center a `<div>` element horizontally and vertically within its parent container, you can use CSS along with various methods. Here are three common ways to center a `<div>`:

1. **Using Flexbox**:
   One of the most straightforward methods for centering a `<div>` both horizontally and vertically is by using the Flexbox layout. Here's how you can do it:

   ```css
   .parent-container {
     display: flex;
     justify-content: center; /* Horizontally center */
     align-items: center; /* Vertically center */
     height: 100vh; /* Optionally set the height to fill the viewport */
   }

   .centered-div {
     /* Your styles for the centered div */
   }
   ```

   In this example, the `.parent-container` uses Flexbox to center its child `.centered-div` both horizontally and vertically. The `justify-content` property centers horizontally, and the `align-items` property centers vertically. The `height: 100vh;` is optional and sets the parent container to fill the entire viewport.

2. **Using Absolute Positioning**:
   Another way to center a `<div>` is by using absolute positioning. You need to set the `<div>` to have an absolute position and use `top`, `left`, `right`, and `bottom` properties to center it within its parent container. This approach requires specifying the dimensions of the parent container.

   ```css
   .parent-container {
     position: relative;
     width: 100%; /* Set the width of the parent container */
     height: 100vh; /* Set the height of the parent container */
   }

   .centered-div {
     position: absolute;
     top: 50%;
     left: 50%;
     transform: translate(-50%, -50%);
   }
   ```

   In this example, the `.centered-div` is positioned absolutely and centered using the `top`, `left`, and `transform` properties, which adjust its position both horizontally and vertically.

3. **Using CSS Grid**:
   You can also center a `<div>` using CSS Grid, although this method may require additional layout elements:

   ```css
   .parent-container {
     display: grid;
     place-items: center; /* Center both horizontally and vertically */
     height: 100vh;
   }

   .centered-div {
     /* Your styles for the centered div */
   }
   ```

   Here, the `.parent-container` uses CSS Grid's `place-items` property to center its child `.centered-div` both horizontally and vertically.

Choose the method that best suits your layout and compatibility requirements. Flexbox is often preferred for simple centering tasks, while absolute positioning and CSS Grid may be useful for more complex layouts.

</p>
</details>

---

###### 47. Pollyfill of Promise.all() such that even any promise gets rejected then it should return null

<details><summary><b>Answer</b></summary>
<p>
function customPromiseAll(promises) {
  return new Promise((resolve, reject) => {
    if (!Array.isArray(promises)) {
      return reject(new TypeError('Promises must be an array'));
    }

    const results = [];
    let completedCount = 0;

    promises.forEach((promise, index) => {
      Promise.resolve(promise)
        .then((value) => {
          results[index] = value;
          completedCount++;

          if (completedCount === promises.length) {
            resolve(results);
          }
        })
        .catch((error) => {
          // If any promise is rejected, return null
          reject(null);
        });
    });
  });
}

// Usage:
const promise1 = Promise.resolve('Resolved 1');
const promise2 = Promise.reject('Rejected 2');
const promise3 = Promise.resolve('Resolved 3');

customPromiseAll([promise1, promise2, promise3])
  .then((values) => {
    console.log(values); // This will log: null
  })
  .catch((error) => {
    console.error(error); // This will not be executed because the customPromiseAll handles rejections internally
  });

</p>
</details>

---

###### 1. Implement a function that converts a JavaScript value into a JSON string.
<details><summary><b>Answer</b></summary>
<p>
  
  ```javascript
  function convertToJSON(value) {
      return JSON.stringify(value);
  }
  ```
</p>
</details>

---

###### 2. Implement a function that performs a deep copy of a value, but also handles circular references.
<details><summary><b>Answer</b></summary>
<p>
  
```javascript
function deepCopyWithCircularHandling(obj, seen = new WeakMap()) {
    if (Object(obj) !== obj || obj instanceof Function || obj instanceof Promise)
        return obj;
    if (seen.has(obj))
        return seen.get(obj);
    let newObj = Array.isArray(obj) ? [] : {};
    seen.set(obj, newObj);
    Object.keys(obj).forEach(key => {
        newObj[key] = deepCopyWithCircularHandling(obj[key], seen);
    });
    return newObj;
}
```
</p>
</details>

---
###### 3. Implement a function to construct a table of contents from an HTML document.
<details><summary><b>Answer</b></summary>
<p>
  
```javascript
function constructTableOfContents(htmlDocument) {
    // Parse the HTML document and extract headings to construct TOC
    // Example logic:
    const headings = Array.from(htmlDocument.querySelectorAll('h1, h2, h3, h4, h5, h6'));
    const tableOfContents = headings.map(heading => ({
        text: heading.textContent,
        level: parseInt(heading.tagName.charAt(1))
    }));
    return tableOfContents;
}
```
</p>
</details>

---
###### 4. Implement a function to filter rows of data matching a specified requirement.
<details><summary><b>Answer</b></summary>
<p>
  
```javascript
function filterRows(data, requirement) {
    // Filter rows based on the requirement
    // Example logic:
    return data.filter(row => row.someField === requirement);
}
```
</p>
</details>

---
###### 5. Implement a function that performs insertion sort.
<details><summary><b>Answer</b></summary>
<p>
  
```javascript
function insertionSort(array) {
    // Sort the array using insertion sort algorithm
    // Example logic:
    for (let i = 1; i < array.length; i++) {
        let key = array[i];
        let j = i - 1;
        while (j >= 0 && array[j] > key) {
            array[j + 1] = array[j];
            j = j - 1;
        }
        array[j + 1] = key;
    }
    return array;
}
```
</p>
</details>
---

###### 6. Implement a function that returns a memoized version of a function which accepts any number of arguments.
<details><summary><b>Answer</b></summary>
<p>
  
```javascript
function memoize(func) {
    const cache = {};
    return function(...args) {
        const key = JSON.stringify(args);
        if (!cache[key]) {
            cache[key] = func(...args);
        }
        return cache[key];
    }
}
```
</p>
</details>

###### 7. Implement a function that acts like setInterval but returns a function to cancel the Interval.
<details><summary><b>Answer</b></summary>
<p>
  
```javascript
function setIntervalWithCancel(callback, interval) {
    const intervalId = setInterval(callback, interval);
    return function cancel() {
        clearInterval(intervalId);
    };
}
```
</p>
</details>

---
###### 8. Implement a function that merges two objects together.
<details><summary><b>Answer</b></summary>
<p>
  
```javascript
function mergeObjects(obj1, obj2) {
    return { ...obj1, ...obj2 };
}
```
</p>
</details>
9. Implement a function to highlight text if searched terms appear within it.
<details><summary><b>Answer</b></summary>
<p>
```javascript
function highlightText(text, searchTerm) {
    // Highlight occurrences of searchTerm in the text
    // Example logic:
    return text.replace(new RegExp(searchTerm, 'gi'), '<mark>$&</mark>');
}
```
</p>
</details>
10. Implement a function to recursively transform values.
<details><summary><b>Answer</b></summary>
<p>
```javascript
function recursivelyTransformValues(value) {
    // Recursively transform values in the object
    // Example logic:
    if (typeof value === 'object') {
        for (let key in value) {
            value[key] = recursivelyTransformValues(value[key]);
        }
    } else {
        // Transformation logic for non-object values
    }
    return value;
}
```
</p>
</details>
11. Implement a function that determines if two values are deep equal.
<details><summary><b>Answer</b></summary>
<p>
```javascript
function deepEqual(value1, value2) {
    if (value1 === value2) {
        return true;
    }
    if (typeof value1 !== 'object' || typeof value2 !== 'object') {
        return false;
    }
    const keys1 = Object.keys(value1);
    const keys2 = Object.keys(value2);
    if (keys1.length !== keys2.length) {
        return false;
    }
    for (let key of keys1) {
        if (!keys2.includes(key) || !deepEqual(value1[key], value2[key])) {
            return false;
        }
    }
    return true;
}
```
</p>
</details>
12. Implement a function to highlight text if a searched term appears within it.
<details><summary><b>Answer</b></summary>
<p>
```javascript
function highlightText(text, searchTerm) {
    return text.replace(new RegExp(searchTerm, 'gi'), `<mark>${searchTerm}</mark>`);
}
```
</p>
</details>
13. Implement a function that returns a new object after squashing the input object.
<details><summary><b>Answer</b></summary>
<p>
```javascript
function squashObject(inputObject) {
    return Object.assign({}, ...Object.values(inputObject));
}
```
</p>
</details>
14. Implement a function that creates a resumable interval object.
<details><summary><b>Answer</b></summary>
<p>
```javascript
function createResumableInterval(callback, interval) {
    let intervalId = setInterval(callback, interval);
    return {
        cancel: function() {
            clearInterval(intervalId);
        },
        resume: function() {
            intervalId = setInterval(callback, interval);
        }
    };
}
```
</p>
</details>
15. Implement the functionality behaviour of Promise.any().
<details><summary><b>Answer</b></summary>
<p>
```javascript
function promiseAny(promises) {
    return new Promise((resolve, reject) => {
        let errors = [];
        promises.forEach(promise => {
            promise.then(resolve).catch(error => {
                errors.push(error);
                if (errors.length === promises.length) {
                    reject(errors);
                }
            });
        });
    });
}
```
</p>
</details>
16. Implement the functionality behaviour of Promise.allSettled().
<details><summary><b>Answer</b></summary>
<p>
```javascript
function promiseAllSettled(promises) {
    return Promise.all(promises.map(promise => {
        return promise
            .then(value => ({ status: 'fulfilled', value }))
            .catch(reason => ({ status: 'rejected', reason }));
    }));
}
```
</p>
</details>
17. Implement a function that returns a memoized version of a function which accepts a single argument.
<details><summary><b>Answer</b></summary>
<p>
```javascript
function memoizeSingleArg(func) {
    const cache = {};
    return function(arg) {
        if (!cache[arg]) {
            cache[arg] = func(arg);
        }
        return cache[arg];
    }
}
```
</p>
</details>
18. Implement a function that formats a list of items into a single readable string.
<details><summary><b>Answer</b></summary>
<p>
```javascript
function formatItemList(items) {
    return items.join(', ');
}
```
</p>
</details>

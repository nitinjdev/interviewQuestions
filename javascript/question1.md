# js question 1: what is Event loop


1\. The event loop is the mechanism that allows JavaScript to handle asynchronous operations without blocking the main thread while waiting for tasks such as timers, network responses, or user events.

2\. JavaScript executes synchronous code on the call stack. Asynchronous callbacks are scheduled to run when the relevant execution conditions are met and the stack is available.

```tsx
console.log(1);

setTimeout(() => console.log(2), 0);

Promise.resolve().then(() => {
  console.log(3);

  setTimeout(() => console.log(4), 0);
});

Promise.resolve().then(() => console.log(5));

console.log(6);

>>> 1 6 3 5 2 4
```
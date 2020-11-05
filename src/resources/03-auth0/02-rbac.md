# Encapsulation in JavaScript

Encapsulation is the bundling of data and the methods that act on that data such that access to that data is restricted from outside the bundle, or as Alan Kay describes it, "local retention and protection and hiding of state-process." In OOP, that means that an object stores its state privately, and only the object's methods have access to change it.

If you want to change encapsulated state, you don't reach out and directly mutate some object's props. Instead, you call a method on the object, and *maybe* the object will respond by updating its state. For example, if you build an application using Redux, instead of directly manipulating a view's data model, you dispatch a message called an *action object* to the data store. Redux controls the timing of when and how that message is handled. The timing and handling of that message are precisely controlled in order to create predictable, deterministic state updates. Given the same messages in the same order, Redux will always produce the same state.

Likewise, when you want to update React component state with `useState` or `setState`, those changes don't directly manipulate the component state. Instead, they may enqueue potential state changes which get applied after the render cycle has completed. You don't directly set React's component state; React does.

## Why Encapsulation?

In the 1960s and 1970s, programmers were grappling with timing dependencies and resource conflicts caused by trying to share the same memory resources between different operations running in non-deterministic sequences. They were also frustrated by the need to couple code to a specific data structure representation of the program state.

In the 1970s, Alan Kay was inspired by composite data structures from Ivan Sutherland's Sketchpad thesis, developed between 1961 and 1963 at MIT and Simula developed in the 1960s by Ole-Johan Dahl and Kristen Nygaard at the Norwegian Computing Center in Oslo. Alan Kay was involved in ARPAnet research and design, had a background in science and math, and was particularly inspired by how cells were encapsulated by a membrane and communicated via message passing.

All those ideas came together to form the the foundations of OOP: encapsulation and message passing.

The trouble with shared mutable state is that if your input state depends on some other instruction's output state, and any sort of concurrency arrises, it creates race conditions. If you change the order in which instructions are called, it can alter the results. Mix in any sort of non-determinism in sequencing, and the result is chaos: unpredictable, unprovable, seemingly random application state. Sometimes it works. Sometimes it doesn't.

Encapsulation is one approach to deal with this problem.

Encapsulation also solves another interesting problem. Imagine you have an aggregation of data you need to process. One way to do it is to first decide on a data structure to represent the data. If you start with the implementation details (let's say an array), and everything that uses it is aware of its structure, and can create tight coupling with the data structure that can make it difficult to change that implementation later. What if you eventually want to swap the array out for a stream, or a tree, or some other data structure? If everything knows the implementation, it may be too late.

But when we encapsulate those implementation details behind a public interface, and then everything that uses the object does so only through its public interface, it's easier to change the implementation details later. To illustrate, imagine you have a data structure which stores numbers, and you need a way to multiply the stored values by two:
```javascript
// Only works for arrays
const doubleAllImperative = data => {
  const doubled = [];
  for (let i = 0, length = data.length; i < length; i++) {
    doubled[i] = data[i] * 2;
  }
  return doubled;
};
// Same as above, but works for anything with the map operation.
const doubleAllInterface = data => data.map(x => x * 2);
const box = value => ({
  map: f => box(f(value)),
  toString: () => `box(${ value })`
});
console.log(
  doubleAllInterface([2,3]), // [4, 6]
  doubleAllInterface(box(2)).toString(), // box(4)
);
```
Encapsulation can be a powerful tool capable of helping you prevent bugs resulting from shared mutable state, and also for reducing tight coupling between components and the data structures they rely on. It helps you obey at least three key principles of software design:

1.  Avoid shared mutable state. "Nondeterminism = parallel processing + mutable state" --- Martin Odersky, designer of the Scala programming language
2.  "Program to an interface, not an implementation." --- Gang of Four, ["Design Patterns: Elements of Reusable Object Oriented Software"](https://www.amazon.com/Design-Patterns-Elements-Reusable-Object-Oriented/dp/0201633612/ref=as_li_ss_tl?ie=UTF8&linkCode=ll1&tag=eejs-20&linkId=7445122e05ad99f1761daaeb75239e4f&language=en_US)
3.  "A small change in requirements should necessitate a correspondingly small change in the software." --- N. D. Birrell, M. A. Ould, ["A Practical Handbook for Software Development"](https://www.amazon.com/Practical-Handbook-Software-Development-ebook/dp/B01DM2859K/ref=as_li_ss_tl?ie=UTF8&linkCode=ll1&tag=eejs-20&linkId=d47c3f2ec4505eb596bd974446104041&language=en_US)

## Idiomatic Encapsulation in JavaScript

When Brendan Eich built JavaScript in those fateful 10 days culminating in the release in 1995, he had two ideas in mind:

-   Scheme in the browser
-   Look like Java

Scheme is a functional programming language --- a dialect of LISP, which is an elegant little language dating back to 1958. Because Scheme supports very flexible higher order functions and closures, it's capable of punching well above its weight.

Java is a class-based object-oriented language. From Java, JavaScript got the notion of constructor functions, (eventually) classes, and the `new` keyword (among other things).

Brendan Eich snuck in a third major inspiration from the Self programming language --- prototypes, which makes JavaScript's notion of inheritance much more powerful and flexible than its similarly named but otherwise distant cousin, Java, but that's [another story](https://medium.com/javascript-scene/master-the-javascript-interview-what-s-the-difference-between-class-prototypal-inheritance-e4cd0a7562e9).

23 years later, this paradigm melting pot is still a bit misunderstood. One of those common misunderstandings has to do with encapsulation.

Before I get to idiomatic encapsulation for objects in JavaScript, I first want to address a common convention that is not a robust form of encapsulation. A long time ago, before a lot of JavaScript developers learned about [closures](https://medium.com/javascript-scene/master-the-javascript-interview-what-is-a-closure-b2f0d2152b36), some JavaScript developers noticed that JavaScript objects (and later classes) did not include a mechanism for private properties.

Prior to the [private fields proposal](https://github.com/tc39/proposal-class-fields) in stage 3 of the ECMAScript Specification process as I write this, there was no way to create a private property for an object in JavaScript. Instead of falling back on closures, which already support true data privacy for objects in JavaScript, some developers decided to denote private properties and methods by prefixing them with underscores, and that has become a recognized, (though awkward and controversial) convention.

This is problematic for multiple reasons:

Breaking Changes: Internal properties and methods tend to change more frequently than public properties and methods. For many, underscore prefixed methods, e.g., `myComponent._handleClick` refer to methods that they do not intend users of the API to call directly. Instead, they're used only internally, and as such, if their implementations change, or if they're entirely deleted, those developers would not consider that to be a breaking change.

Unfortunately for the users, many new developers are unaware of the underscore prefix convention, so may use the properties anyway. Experienced developers often know what it means, but figure, "I know what I'm doing", and so use them anyway --- particularly if it provides an obvious solution to a current problem. In other words, a large number of people ignore the convention, and that leads to more breaking changes than necessary.

Leaked Implementation Details: Remember the example above, where we started out supporting only arrays, but now we want to expand our support to streams? Well, if your users have direct access to the underlying data structures, they may create dependencies on those data structures, so the first time a stream is encountered, they're surprised when their code breaks.

Expanded Attack Surface for Hackers: On public APIs in particular, adding any more surface API than is necessary to use your code expands the surface area that is available for attackers to exploit. One of the most important principles of software security to limit the attack surface only to what is absolutely necessary. If you really only intend for something to be used internally, it should not be exposed externally.

Self Documenting Code: Your public API should be as self-documenting as possible. One way to do that is to expose only those methods and properties you intend for your users to use. That way users are not tempted to use unsupported and undocumented methods. If you use the underscore convention, you rely on the user to know what it means and to understand that you don't intend for them to use it. If you use encapsulation, you don't have to worry about that. They can't use what they don't have access to.

## Real Encapsulation in JavaScript

Of course, as the functional crowd knows, JavaScript has supported real data encapsulation all along. It's very easy to declare private data in JavaScript.

## Using Closures

```javascript
const createCounter = () => {
  // A variable defined in a factory or constructor function scope is private to that function.
  let count = 0;
  return ({
    // Any other functions defined in the same scope are privileged:
    // These both have access to the private `count` variable defined anywhere in their scope chain (containing function scopes).
    click: () => count += 1,
    getCount: () => count.toLocaleString()
  });
};
const counter = createCounter();counter.click();
counter.click();
counter.click();
console.log(
  counter.getCount()
);
```
A privileged method is a method which has access to the private data inside the containing function's scope (also known as the lexical environment). Privileged functions and methods have reference-based access to the containing function's variables even after the containing function has returned. Those references are live, so if the state changes in the containing function, in changes for every privileged function with access to the reference. In other words, when we call `counter.click()`, it changes the value that `counter.getCount()` sees.

It's even possible to inherit private state using [functional mixins](https://medium.com/javascript-scene/functional-mixins-composing-software-ffb66d5e731c).

## Using Private Fields

As of this writing, private fields are available in babel with the `stage-3` feature enabled. They're supported natively in Chrome, Opera, Android Browser, and Chrome for Android, so there's a good chance you can do something like this:
```javascript
class Counter {
  #count = 0

  click () {
    this.#count += 1;
  }
  getCount () {
    return this.#count.toLocaleString()
  }
}
const myCounter = new Counter();
myCounter.click();
myCounter.click();
myCounter.click();
console.log(
  myCounter.getCount()
);
```
I still have a strong preference for [factory functions](https://medium.com/javascript-scene/javascript-factory-functions-vs-constructor-functions-vs-classes-2f22ceddf33e) and true privacy using [closure-based encapsulation](https://medium.com/javascript-scene/master-the-javascript-interview-what-is-a-closure-b2f0d2152b36) over using classes and the new class fields specification, but if you actually need encapsulation, both closures and class fields are far better than underscores, because they don't rely on convention, and instead enforce true encapsulation.

<br>
<br>
<hr>
<small>Elliott, Eric. “Encapsulation in JavaScript.” Medium, JavaScript Scene, 27 Aug. 2020, medium.com/javascript-scene/encapsulation-in-javascript-26be60e325b4. </small>
<br>
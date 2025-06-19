### Asynchronous means?
JS uses synchronous style of executing code (like line-by-line) by default unless some `asynch method` is being introduced. Asynchronous pieces of code will - make tasks start, run in the background, and notify when they're done—without stopping the rest of the code. 
-- Like I am waiting for food in a restaurant along with working with laptop. When food is ready, it will be served and I can eat then. UwU {{While synch is like waiting in a line for food and not being able to do anything else other than waiting for food}}.

Because I tend to forget: when I am using async, say fetching data using async - only the data and what I want to do with the data will have to wait for it to be completed - the rest of the code will continue to be executed.
##### this code says it:
```js
console.log("Start fetching data..."); fetch("https://api.example.com/data") .then((response) => response.json()) .then((data) => { console.log("Fetched data:", data); // This waits for the fetch to complete });
console.log("I can keep doing other stuff while waiting for the data.");
```
```output
Start fetching data...
I can keep doing other stuff while waiting for the data.
Fetched data: { ... } // This prints when the data is ready
```
### So, what are the `asynch methods`??
There are three most used methods to handle async tasks-
- Callbacks: Basically a function that is passed as an argument of another function. It runs when the previous task is done.
> [!example]- Code example
> ```js
> console.log("Start");
> setTimeout(() => { console.log("Done waiting!"); }, 2000); // Wait 2 seconds
> console.log("End");
> ```

- Promises: When a task is done, `then` do that - just like it sounds
> [!example]- Code example
> ```javascript
> fetch("data link pew pew")
> 	.then(response => response.json())
> 	.then(data => console.log(data))
> 	.catch(error => console.error(error));
>```

- Async/ Await: Looks like synchronous code but we write `async` before the function and `await` before the task to be done after previous one is completed.
> [!example]- Code example
> ```js
> async function getData() {
> const response = await fetch("data link pew pew");
> const data = await response.json();
> console.log(data); }
> getData();


---
### Promises
One of the way to handle async JS is promise. A promise is an object that might produce a value at some point in the future.
A new promise is created with the `new` keyword and the promise provides `resolve` and `reject` functions to the provided callbacks.
#### Basic promise usage:
```js
var p = new Promise(function(resolve, reject) {
	
	// Do an async task async task and then...

	if(/* good condition */) {
		resolve('Success!');
	}
	else {
		reject('Failure!');
	}
});

p.then(function(result) { 
	/* do something with the result */
}).catch(function() {
	/* error :( */
}).finally(function() {
   /* executes regardless or success for failure */ 
});
```

It's up to the developer to manually call `resolve` or `reject` within the body of the callback based on the result of their given task. Which means either or both can be used/ignored.

>This is so funny that I learnt all about async and promises... but now my memory about this is a blur -,- Though I believe I still remember the key concepts

Often times returning a promise, regardless the task being async or not, is benifical. In that case instead of using `new` to create a promise, `Promise.resolve()` or `Promise.reject()` can be used. Since a promise is always returned, `then` and `catch` methods can always be applied on its return value!

> [!example]- An example of Promise without `new`
> ``` js
> var userCache = {};
> function getUserDetail(username) {
  // In both cases, cached or not, a promise will be returned
  if (userCache[username]) {
  // Return a promise without the "new" keyword
  return Promise.resolve(userCache[username]);
  }
  // Use the fetch API to get the information
  // fetch returns a promise
  return fetch('users/' + username + '.json')
  .then(function(result) {
  userCache[username] = result;
  return result;
  })
  .catch(function() {
  throw new Error('Could not find user: ' + username);
  });```
  
**then:** All promise instances get a `then` method which allows to react to the promise. It is triggered when the promise is resolved. The first `then` method receives the result given to it by the `resolve()` call. After that more `then` methods can be chained. Each of this chain `then` receives the result from its previous one.

**catch:** The `catch` callback is executed when the promise is rejected. What I put inside is up to me < B) > But normally it includes `Error` 

**finally:** This is used regardless the promised is resolved or rejected

> [!example]-  then, catch, finally:
> ```js
> new Promise(function(resolve, reject) { 
> // A mock async action using setTimeout
> setTimeout(function() { resolve(10); }, 3000);
> })
> .then(function(num) { console.log('first then: ', num); return num * 2; })
> .then(function(num) { console.log('second then: ', num); return num * 2; })
> .then(function(num) { console.log('last then: ', num);});
> // From the console:
> // first then:  10
> // second then:  20
> // last then:  40
> ---
> new Promise(function(resolve, reject) {
> // A mock async action using setTimeout
> setTimeout(function() { reject('Done!'); }, 3000);
> })
> .then(function(e) { console.log('done:', e); })
> .catch(function(e) { console.log('catch: ', e); });
> // From the console:
> // 'catch: Done!'
> ---
> (new Promise((resolve, reject) => { reject("Nope"); }))
> .then(() => { console.log("success") })
> .catch(() => { console.log("fail") })
> .finally(res => { console.log("finally") });
> // >> fail
> // >> finally
> ```

**`Promise.all`:** When I have triggering multiple async interaction and want to respond only when all of them are completed, we use this. If any promise is rejected, the `catch` fires for the *first rejection*. Just write:
```js
Promise.all([promise1, promise2])
	.then(function(results) {
	// Both promises resolved
})
	.catch(function(error) {
	// One or more promises was rejected
});
```

**`Promise.race`:** Like the previous one, it takes an array of promises. However, it is triggered as soon as any of the promise is resolved or rejected. 
Someone tell him this:
>"Life is not a race - but indeed a journey. Be honest. Work hard. Be choosy". :<

[This is a nice doc on everything I have yapped about `Promises`](https://javascript.info/promise-basics)

---

### Async Await

**Async Function:** a function always returns a promise. Other values are wrapped in a resolved promise automatically.
**Await:** The keyword `await` makes JavaScript wait until that promise settles and returns its result. Works only inside `async` functions.
`await` literally suspends the function execution until the promise settles, and then resumes it with the promise result. That doesn’t cost any CPU resources, because the JavaScript engine can do other jobs in the meantime: execute other scripts, handle events, etc


> [!example] Async & Await
> ```javascript
> async function f() {
> 	let promise = new Promise((resolve, reject) => {
> 		setTimeout(() => resolve("done!"), 1000)
> 	})
> 	
> 	let result = await promise  //wait until the promise resolves
> 	 alert(result)  //"done!
> 	}
> 	f()

We can handle error using `try...catch`:


> [!example] Handling error
> ```javascript
> async function f() {
> 	try{
> 		let response = await fetch("http:// pewpewURL")
> 		} catch(err) {
> 		/// catches error both in fetch and response.json
> 		alert(err)
> 		}
> 	}
> 	f()
> ```

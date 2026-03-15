
In 1999, Microsoft shipped Internet Explorer 5 with an object called `XMLHttpRequest`. It was designed to let Outlook Web Access fetch new emails without reloading the entire page. Nobody at the time had a name for what they were doing. It was just a hack for a specific problem.

Five years later, Jesse James Garrett wrote an essay naming the technique AJAX, Asynchronous JavaScript and XML, and the web was never the same.

---

## What XMLHttpRequest actually was

`XMLHttpRequest` let JavaScript make HTTP requests from the browser without triggering a page navigation. Before it existed, every interaction that needed new data from the server required a form submission and a page reload.

The API was not elegant:

```javascript
var xhr = new XMLHttpRequest();
xhr.onreadystatechange = function() {
  if (xhr.readyState === 4 && xhr.status === 200) {
    var data = xhr.responseText;
    // do something with data
  }
};
xhr.open('GET', '/api/data', true);
xhr.send();
```

Four states, a status code check nested inside a state check, everything happening in a callback. If you wanted to make two requests in sequence, you nested the second callback inside the first. Three sequential requests produced three levels of nesting. Developers called it callback hell, and that was accurate.

But for the early 2000s, this was transformative. Gmail used it to load new messages. Google Suggest used it to fetch search completions as you typed. Google Maps used it to load map tiles as you panned. The web started feeling like software instead of a series of documents.

---

## The browser wars made it worse

Internet Explorer had `XMLHttpRequest` first, but Firefox, Safari, and Opera implemented it slightly differently. The differences were usually minor, but discovering them required testing on every browser. Libraries like Prototype.js and then jQuery emerged to wrap the differences into a consistent API.

jQuery's `$.ajax()` became the way most developers made HTTP requests for years. It normalized behavior across browsers, handled the callback pattern cleanly, and added useful defaults. Entire web applications were built on jQuery because it solved real problems that the raw browser APIs didn't.

The dependency on jQuery for basic HTTP requests is easy to understand in retrospect. Cross-browser compatibility was genuinely hard. The alternative was writing your own normalization layer.

---

## Callbacks at scale

AJAX worked. The callback pattern worked for simple cases. But real applications needed to make multiple dependent requests, handle errors, handle timeouts, and cancel in-flight requests when the user navigated away.

Deeply nested callbacks are hard to read and harder to maintain. Error handling had to be repeated at every level. A thrown exception inside a callback often disappeared silently. The code for "fetch user, then fetch their orders, then fetch order details" looked like a pyramid turned sideways.

Libraries introduced patterns to manage this: deferred objects in jQuery, the promises pattern before it was native. The idea was to represent an asynchronous operation as an object that you could chain operations onto, rather than nesting callbacks inside each other.

---

## Promises arrive natively

ES2015 brought native `Promise` to JavaScript. A promise represents a value that isn't available yet. You chain `.then()` handlers that run when it resolves and `.catch()` handlers that run when it rejects. The key property is that `.then()` returns a new promise, so you can chain operations without nesting:

```javascript
fetch('/api/user')
  .then(response => response.json())
  .then(user => fetch(`/api/orders/${user.id}`))
  .then(response => response.json())
  .then(orders => {
    // use orders
  })
  .catch(error => {
    // handles any error in the chain
  });
```

This is the same three-step request chain as before, but flat instead of nested. Error handling is centralized at the bottom. The structure maps to the actual sequence of operations.

`fetch` replaced `XMLHttpRequest` as the native HTTP API. It returns a promise by default, handles JSON cleanly, and has a more rational design. It's also missing some things, there's no built-in request cancellation in the initial spec (though `AbortController` was added later) and it doesn't reject on HTTP error status codes, only on network failures, which surprises people the first time.

---

## async/await, promises with synchronous-looking syntax

ES2017 added `async` and `await`, which are syntactic sugar over promises. The runtime is identical, you're still working with promises. The syntax reads like synchronous code:

```javascript
async function loadUserOrders() {
  try {
    const userResponse = await fetch('/api/user');
    const user = await userResponse.json();

    const ordersResponse = await fetch(`/api/orders/${user.id}`);
    const orders = await ordersResponse.json();

    return orders;
  } catch (error) {
    // handle error
  }
}
```

The function pauses at each `await` without blocking the main thread. Error handling uses try/catch, which is what most developers already understood from synchronous code. The sequential structure mirrors the actual sequence of operations.

This is now the default way JavaScript handles asynchronous operations. React's data fetching, Node.js server code, everything in between uses async/await.

---

## What AJAX unlocked and what it cost

The shift to dynamic data loading transformed what the web could be. Email clients, maps, collaborative documents, real-time feeds, applications that would have been impossible with full page reloads became standard.

But it also moved complexity from servers to clients. Server-rendered applications had one place where state lived: the database. Once the browser started managing data fetched asynchronously, there were two sources of truth. Keeping them synchronized became a real engineering problem. Race conditions, two requests in flight, the second resolving before the first, were new and subtle bugs.

Loading states, error states, stale data, optimistic updates: these UX concepts didn't exist in the same form in server-rendered applications. They're the inevitable consequence of moving data fetching into the client.

The entire state management ecosystem in frontend, Redux, Zustand, React Query, SWR, is largely a response to the complexity that AJAX introduced. The tools are solving real problems. But the problems were created by the shift to client-side data fetching, which itself was created by the desire to build applications that felt responsive and alive.

---

*Next: How the Browser War Was Won. Internet Explorer dominated the web for a decade and nearly ended open standards. The story of how Firefox, Chrome, and WebKit broke that dominance explains why browser diversity matters and what happens when one vendor controls too much.*

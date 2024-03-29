# Can I use `fetch` to access a `localhost` server?

Yes!

Take this server for example:

```javascript
Bun.serve({
  fetch() {
    return new Response('Bun!');
  },
});
```

Run using `bun server.js` and hit http://localhost:3000 in your browser to make
sure the server works.

Next up, open any page, like Google, open the developer tools, go to the Console
tab and run this:

```javascript
fetch('http://localhost:3000').then(response => response.text()).then(text => console.log(text));
```

Your `fetch` call is still subject to CORS so you'll get this error:

```
Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at
http://localhost:3000/. (Reason: CORS header ‘Access-Control-Allow-Origin’ missing).
```

To fix this, make sure your local server serves the appropriate ACAO header:

```javascript
Bun.serve({
  fetch(req) {
    const headers = { 'Access-Control-Allow-Origin': 'https://www.google.com' };
    return new Response("Bun!", { headers });
  },
});
```

With this, your Google tab can now make `fetch` calls to your `localhost` server.
Other websites won't be able to as they won't pass the CORS check.

Change this to your web app's orgin and now your live web app can hit the users'
local server and prompt them to install and start one if the web app expects
that to work and the user hasn't done it.

To allow any and all websites to call your localhost server, use this:

```javascript
Bun.serve({
  fetch(req) {
    const headers = { 'Access-Control-Allow-Origin': '*' };
    return new Response("Bun!", { headers });
  },
});
```

**This is not a good idea.**

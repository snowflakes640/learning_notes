We have two sections one is backend and second is frontend

##  Backend_side

#### Express and MongoDB REST API

First we will just install all the necessary dependencies. We would need to write this piece of code to let node know which file to run when we're writing `npm run server` 
```js
"start": "node backend/server.js",
"server": "nodemon backend/server.js",
```

Suppose we are trying to 
The Main Server File ( `server.js` )
```js
app.use("/api/goals", require("./routes/goalRoutes"));
```

This line says:

> "For any request that starts with `/api/goals`, use the routing logic defined in `goalRoutes.js`

So what is going on in a flow is - when a request hits your server:
1. Request: `POST /api/goals`
2. Express routes it through `app.use("/api/goals",...)`
3. Goes to `goalRoutes.js` 
4. Checks if it's a `POST` to `/` → finds `setGoals`
5. Runs `protect` → verifies JWT 
6. If okay, runs `setGoals` controller logic


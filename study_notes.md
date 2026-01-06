## Python

### 1. The `@` Symbol (Decorators)

In Python, the `@` symbol is called a decorator. Think of it as a "wrapper" or a "label" that tells FastAPI exactly how to handle a specific function.

In your data workflows, you might have a script that cleans data. Without a decorator, it’s just a private function. 
By adding `@app.get("/welcome")`, you are "registering" that function with the web server. 
You're telling the application: "Whenever a user visits the URL /welcome using a 'GET' request, run this specific function."

**Analogy:** It’s like assigning a specific **Schema** to a table. It defines the rules for how users can interact with that specific piece of logic.

### 2. The `async` Keyword (Asynchron)

The `async` keyword stands for asynchronous. This is one of FastAPI's "superpowers", especially for AI and data tasks.

In a traditional (synchronous) script, Python does one thing at a time. If you're waiting for a massive SQL query to finish or an ML model to load, the whole program stops and waits.

With async, your API can "pause" a task that is waiting for data (like a database hit or a file upload) and handle other user requests in the meantime. It’s like a waiter in a restaurant: they don't stand at the kitchen window waiting for your food to cook; they go take orders from other tables while they wait.

**Analogy:** Think of a **Data Pipeline** with multiple parallel workers. async allows your API to be highly efficient by not letting the "CPU" sit idle while waiting for "I/O" (Input/Output) operations.


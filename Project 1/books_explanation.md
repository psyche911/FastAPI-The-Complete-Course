# FastAPI Books API: A Beginner's Guide

This document explains the code in `books.py`, a simple API built with FastAPI for managing a collection of books.

## What is FastAPI?

**FastAPI** is a modern, fast (high-performance) **_web framework_** for _**building APIs with Python**_. It's designed to be easy to use and intuitive, helping you build APIs quickly. One of its key features is automatic interactive documentation.

## Running the Application

To run this API, you need to have `uvicorn` and `fastapi` installed. You can install them with pip:
```bash
pip install fastapi uvicorn
```

Then, from your terminal, in the `Project 1` directory, you can run the application with the following command:

```bash
uvicorn books:app --reload
```

*   `books`: refers to the `books.py` file.
*   `app`: refers to the `app = FastAPI()` instance you created in your code.
*   `--reload`: makes the server restart after code changes.

Once the server is running, you can access the interactive documentation at `http://127.0.0.1:8000/docs`.

## The `BOOKS` Data

The `BOOKS` variable is a Python list of dictionaries. Each dictionary represents a book with a `title`, `author`, and `category`. In a real-world application, this data would typically be stored in a database.

```python
BOOKS = [
    {'title': 'Title One', 'author': 'Author One', 'category': 'science'},
    {'title': 'Title Two', 'author': 'Author Two', 'category': 'science'},
    # ... and so on
]
```

## Key Python Concepts

Before we dive into the endpoints, let's clarify some important Python and FastAPI concepts you see in the code.

### The `@` Symbol (Decorators)

The `@` symbol is used for something called a **decorator**. A decorator is a special function that adds new functionality to another function without changing the code of that function.

In our code, `@app.get("/books")` is a decorator. Here's what it does:
*   It takes the function defined right below it (`read_all_books`).
*   It registers that function with the FastAPI application (`app`).
*   It tells FastAPI that this function should handle `GET` requests to the `/books` URL.

So, instead of you having to write complex code to handle web requests, you can just "decorate" your function, and FastAPI does the heavy lifting.

### The `async` Keyword

The `async` keyword is used to declare an **asynchronous function**. These are special functions that can be "paused" while they are waiting for something to complete, allowing the program to work on other tasks.

In web development, a common waiting period is for a network request to be sent to your API and for your API to send a response back. FastAPI is built to take advantage of `async` functions to handle many requests concurrently and efficiently.

For this simple application, the performance difference isn't noticeable. However, using `async` is a best practice with FastAPI and becomes very important for building high-performance applications that might need to talk to databases or other APIs.

### Path vs. Query Parameters

In our API, we use both path and query parameters to send information to our endpoints. It's important to understand the difference:

*   **Path Parameters**: These are part of the URL path itself. They are used to identify a specific resource. In `@app.get("/books/{book_title}")`, `{book_title}` is a path parameter. You use this when the piece of data is required to identify the resource. For example, you can't get "a book" without specifying *which* book.
    *   Example URL: `/books/Title%20One`

*   **Query Parameters**: These are optional and are used to filter or sort a collection of resources. They appear at the end of the URL after a `?`, and are specified as key-value pairs. In `@app.get("/books/")`, `category` is a query parameter. You use this to filter the list of all books.
    *   Example URL: `/books/?category=science`

A _**`good rule of thumb`**_: if the parameter is **required** to **identify** a single item, use a **path parameter**. If it's for _optional filtering or sorting_, use a _query parameter_.

### Type Hinting

You'll notice in the function definitions, we have `: str` after the parameter name, like `book_title: str`. This is called a **type hint**.

It tells FastAPI (and other developers) that the `book_title` parameter is expected to be a string. FastAPI uses these type hints to provide data validation. If you try to send a number where a string is expected, FastAPI will automatically return a helpful error message.

### `Body` from FastAPI

In the `create_book` and `update_book` functions, you see `new_book=Body()`. This is a special instruction to FastAPI. It means that the data for the `new_book` will be in the **request body**.

The request body is used to send more complex data to the API, typically in JSON format. When creating or updating a book, you send all the book's details (title, author, category) as a JSON object in the body of the request.

### `casefold()` for String Comparison

When searching for books, the code uses `.casefold()`, for example `book.get('title').casefold()`. This is a Python string method that converts the string to a case-insensitive format. It's similar to `.lower()`, but `casefold()` is more aggressive and is the recommended way to do case-insensitive string comparisons.

This ensures that if a user searches for "title one" or "TITLE ONE", it will still match the book with the title "Title One".

## Understanding HTTP Methods

HTTP is the protocol used for communication on the web, and it defines a set of "methods" (sometimes called "verbs") to indicate the desired action to be performed on a resource. Our API uses the most common ones:

*   **`GET`**: Used to retrieve data. This is a read-only operation and should not change any data on the server. We use this to get all books or a specific book.
*   **`POST`**: Used to create a new resource. We use this to add a new book to our collection.
*   **`PUT`**: Used to update an existing resource. We use this to change the details of a book that already exists.
*   **`DELETE`**: Used to delete a resource. We use this to remove a book from our collection.

FastAPI uses decorators like `@app.get`, `@app.post`, etc., to link your Python functions to these specific HTTP methods and a URL path.

## API Endpoints Explained

An "endpoint" is a specific URL where your API can be accessed. Our API has several endpoints for reading and manipulating the book data.

### `GET /books` - Get All Books

This is the simplest endpoint. It retrieves the entire list of books.

```python
@app.get("/books")
async def read_all_books():
    return BOOKS
```

When you send a GET request to `/books`, the `read_all_books` function is called and it returns the `BOOKS` list. FastAPI automatically converts this list into JSON format for the API response.

### `GET /books/{book_title}` - Get a Specific Book by Title

This endpoint retrieves a single book by its title. The `{book_title}` part of the URL is a "path parameter".

```python
@app.get("/books/{book_title}")
async def read_book(book_title: str):
    for book in BOOKS:
        if book.get('title').casefold() == book_title.casefold():
            return book
```

*   `{book_title}` in the path is captured into the `book_title` function parameter.
*   The `: str` part is a type hint, telling FastAPI that `book_title` is expected to be a string.
*   The code then loops through the `BOOKS` list to find a matching book (case-insensitively) and returns it.

### `GET /books/` - Get Books by Category (Query Parameter)

This endpoint allows you to filter books by category using a "query parameter".

```python
@app.get("/books/")
async def read_category_by_query(category: str):
    books_to_return = []
    for book in BOOKS:
        if book.get('category').casefold() == category.casefold():
            books_to_return.append(book)
    return books_to_return
```

To use this, you would make a request to a URL like `/books/?category=science`.
*   `category: str` in the function parameters tells FastAPI to look for a query parameter named `category`.
*   The function then filters the `BOOKS` list and returns only the books that match the given category.

### `GET /books/byauthor/` - Get Books by Author

This is similar to filtering by category, but it filters by author.

```python
@app.get("/books/byauthor/")
async def read_books_by_author_path(author: str):
    # ... (logic is similar to filtering by category)
```
You would make a request to a URL like `/books/byauthor/?author=Author%20One`.

### `GET /books/{book_author}/` - Get Books by Author and Category

This endpoint combines a path parameter (`book_author`) and a query parameter (`category`) to allow for more specific filtering.

```python
@app.get("/books/{book_author}/")
async def read_author_category_by_query(book_author: str, category: str):
    # ...
```
A request to this endpoint would look like `/books/Author%20One/?category=science`. It will return all books by "Author One" in the "science" category.

### `POST /books/create_book` - Add a New Book

This endpoint allows you to add a new book to the collection. It uses the `POST` method, which is standard for creating new resources.

```python
from fastapi import Body

@app.post("/books/create_book")
async def create_book(new_book=Body()):
    BOOKS.append(new_book)
```

*   `new_book=Body()` tells FastAPI to expect the book data in the "body" of the POST request.
*   In the interactive documentation, you can provide a JSON object representing the new book, and FastAPI will pass it as the `new_book` argument.
*   The function then appends this new book to the `BOOKS` list.

### `PUT /books/update_book` - Update a Book

This endpoint is for updating an existing book. It uses the `PUT` method, which is standard for updating resources.

```python
@app.put("/books/update_book")
async def update_book(updated_book=Body()):
    for i in range(len(BOOKS)):
        if BOOKS[i].get('title').casefold() == updated_book.get('title').casefold():
            BOOKS[i] = updated_book
```

*   Similar to creating a book, the updated book data is sent in the request body.
*   The function finds the existing book in the `BOOKS` list by its title and replaces it with the `updated_book` data.

### `DELETE /books/delete_book/{book_title}` - Delete a Book

This endpoint removes a book from the collection using the `DELETE` method.

```python
@app.delete("/books/delete_book/{book_title}")
async def delete_book(book_title: str):
    for i in range(len(BOOKS)):
        if BOOKS[i].get('title').casefold() == book_title.casefold():
            BOOKS.pop(i)
            break
```

*   This uses a path parameter `{book_title}` to identify which book to delete.
*   The function finds the book by its title and removes it from the `BOOKS` list using `.pop()`.

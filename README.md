# Unity Finland - React Developer Quiz

## Q1: What controversies do you see in the following API example?

User object API doc

```
GET /users
POST /users/new
POST /users/:id/update
POST /users/:id/rename
POST /users/:id/update-timezone 
DELETE /users/delete?id=:id
```

Here are some examples of the behavior:

```
POST /users/new HTTP/1.1 {
    "name": "Cthulhu"
  }
HTTP/1.1 200
"Error: Username already exists"
```

A: 
1. `/new` is reduntant in `POST /users/new` since POST can be used on collection URI for creating new source
2. It's better to use PUT for `POST /users/:id/update`, `POST /users/:id/rename` and `POST /users/:id/update-timezone` since PUT should be used to create or replace the state of the target resource with the state defined by the representation enclosed in the request.
3. `DELETE /users/delete?id=:id` should be `DELETE /users/:id`
4. In the example, with "Error: Username already exists" it shouldn't be 200. The response code should be 4xx instead, e.g. `409: Conflict` or `422 Unprocessable Entity`.

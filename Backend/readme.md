# Users - Register Endpoint

## Endpoint

- **POST** `/users/register`

## Description

Create a new user and return an authentication token plus the created user object. The endpoint accepts either a nested `fullname` object or top-level `firstname`/`lastname` fields.

## Request Body (JSON)

Accepted shapes (either form works):

- Nested `fullname` form:

```json
{
  "fullname": {
    "firstname": "Rahul",
    "lastname": "Sharma"
  },
  "email": "rahul.sharma@example.com",
  "password": "Rahul@123"
}
```

- Top-level name fields:

```json
{
  "firstname": "Rahul",
  "lastname": "Sharma",
  "email": "rahul.sharma@example.com",
  "password": "Rahul@123"
}
```

## Required fields & validation rules

- `email` (string) — required, must be a valid email address.
- `password` (string) — required, minimum length 6 characters.
- `firstname` (string) — required, minimum length 3 characters. Can be provided as `firstname` or `fullname.firstname`.
- `lastname` (string) — optional, if provided minimum length 3 characters. Can be provided as `lastname` or `fullname.lastname`.

If any required field is missing or validation fails, the endpoint responds with `400 Bad Request` and an array of validation errors.

## Responses

- **201 Created**
  - Success. Returns a JSON object with a `token` and the created `user`.
  - Example:

```json
{
  "token": "<jwt-token>",
  "user": {
    "_id": "<user-id>",
    "fullname": { "firstname": "Rahul", "lastname": "Sharma" },
    "email": "rahul.sharma@example.com"
  }
}
```

- **400 Bad Request**
  - Validation errors. Example:

```json
{
  "errors": [
    {
      "type": "field",
      "msg": "Invalid Email",
      "path": "email",
      "location": "body"
    },
    {
      "type": "field",
      "msg": "First name must be at least 3 characters long",
      "path": "firstname",
      "location": "body"
    }
  ]
}
```

- **500 Internal Server Error**
  - Unexpected server error. Returns an error message.

## Example curl

```bash
curl -X POST http://localhost:3000/users/register \
  -H "Content-Type: application/json" \
  -d '{"firstname":"Rahul","lastname":"Sharma","email":"rahul.sharma@example.com","password":"Rahul@123"}'
```

## Notes

- The controller supports both request shapes; the service stores names inside `fullname` in the database.
- The `password` is hashed before saving — clients should send raw passwords and expect only a token/user in the response.

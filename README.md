# Backend API Documentation

## Overview

This documentation is for interviewees: your task is to build a client on top of this API.

---
The Front End Stack
NextJS
Tailwind
ShadCN

---

## Getting Started

- **Base URL:**
  
  `https://flybackend-misty-feather-6458.fly.dev/`

- **Authentication:**
  
  Register or log in to receive a JWT token. Use this token in the `Authorization` header for all protected endpoints.

---

## Quick Start

### 1. Register a New User

**POST** `https://flybackend-misty-feather-6458.fly.dev/auth/register`

**Body:**
```json
{
  "firstName": "Jane",
  "lastName": "Doe",
  "email": "jane@example.com",
  "password": "password123"
}
```
**Response:**
```json
{
  "message": "User registered successfully",
  "token": "<JWT_TOKEN>"
}
```

### 2. Login

**POST** `https://flybackend-misty-feather-6458.fly.dev/auth/login`

**Body:**
```json
{
  "email": "jane@example.com",
  "password": "password123"
}
```
**Response:**
```json
{
  "token": "<JWT_TOKEN>"
}
```

### 3. Authenticated Calories Request

**POST** `https://flybackend-misty-feather-6458.fly.dev/get-calories`

**Headers:**
```
Authorization: Bearer <JWT_TOKEN>
Content-Type: application/json
```
**Body:**
```json
{
  "dish_name": "chicken salad",
  "servings": 2
}
```
**Response:**
```json
{
  "dish_name": "Chicken Salad",
  "servings": 2,
  "calories_per_serving": 350,
  "total_calories": 700,
  "source": "USDA FoodData Central"
}
```

---

## Authentication Flow

1. **Register** or **login** to receive a JWT token.
2. For all protected endpoints, include the token in the `Authorization` header:
   ```
   Authorization: Bearer <JWT_TOKEN>
   ```
3. The token is valid for 1 hour.

---

## User Model

**File:** `src/models/user.model.ts`

```ts
interface IUser {
    firstName: string;
    lastName: string;
    email: string;
    password: string; // Hashed
    comparePassword(candidate: string): Promise<boolean>;
}
```

- Passwords are hashed using bcrypt before storage.
- The model includes a method to compare a plaintext password with the hashed password.

---

## User Endpoints

### Register a New User

**POST** `/auth/register`

**Body:**
```json
{
  "firstName": "Jane",
  "lastName": "Doe",
  "email": "jane@example.com",
  "password": "password123"
}
```

**Response:**
```json
{
  "message": "User registered successfully",
  "token": "<JWT_TOKEN>"
}
```

### Login

**POST** `/auth/login`

**Body:**
```json
{
  "email": "jane@example.com",
  "password": "password123"
}
```

**Response:**
```json
{
  "token": "<JWT_TOKEN>"
}
```

---

## Calories/Nutrition Endpoint

### Overview
This endpoint allows authenticated users to estimate the calories for a given dish name and number of servings, using the USDA FoodData Central API.

### Endpoint
**POST** `/get-calories`

**Authentication:** Requires JWT in the `Authorization` header.

**Request Body:**
```json
{
  "dish_name": "chicken salad",
  "servings": 2
}
```
- `dish_name` (string): Name of the dish (letters, numbers, spaces, hyphens, commas, apostrophes; 1-100 chars)
- `servings` (number): Number of servings (0.1 to 1000, positive)

**Response (Success):**
```json
{
  "dish_name": "Chicken Salad",
  "servings": 2,
  "calories_per_serving": 350,
  "total_calories": 700,
  "source": "USDA FoodData Central"
}
```

**Response (Errors):**
- `400 Bad Request`: Validation failed or invalid dish name
- `401 Unauthorized`: Missing or invalid JWT
- `404 Not Found`: Dish not found or no nutrition data available
- `500 Server Error`: Unexpected error or missing calories data

### Example Request
```sh
curl -X POST https://flybackend-misty-feather-6458.fly.dev/get-calories \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <JWT_TOKEN>" \
  -d '{"dish_name":"chicken salad","servings":2}'
```

### Input Validation
- `dish_name` is sanitized and must match: letters, numbers, spaces, hyphens, commas, apostrophes
- `servings` must be a positive number between 0.1 and 1000

### Notes
- The endpoint uses fuzzy matching to find the best match for the dish name in the USDA database.
- The calories are calculated based on the best match and the number of servings provided.
- The response includes the matched dish name, calories per serving, total calories, and the data source.

---

## Error Handling

- **400 Bad Request:** Validation failed (missing/invalid fields).
- **401 Unauthorized:** Invalid credentials or missing/invalid token.
- **500 Server Error:** Unexpected server error.

---

## Project Structure (FYI)

```
src/
  app.ts                  # Express app setup
  models/
    user.model.ts         # User schema/model
  repositories/
    user.repository.ts    # User DB operations
  controllers/
    auth.controller.ts    # Auth logic
  middlewares/
    auth.middleware.ts    # JWT verification
  routes/
    auth.routes.ts        # Auth endpoints
```

---

## Contact

If you have any questions or issues, please contact the maintainer at [your-email@example.com].

---

## (Optional) Backend Setup for Local Development

If you want to run the backend locally (not required for the interview):

- Set the following environment variables:
  - `MONGODB_URI`: MongoDB connection string.
  - `JWT_SECRET`: Secret for signing JWT tokens.
- Install dependencies and start the server:
  ```sh
  npm install
  npm run dev
  ``` 
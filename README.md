# project-p2-andiadisasmita

# Boardgame Rental Backend System

## Overview
This project implements a backend system for a boardgame rental application. It uses PostgreSQL as the database, with Supabase hosting, and is designed to manage users, boardgames, stock, rentals, payments, and reviews. The system ensures proper lifecycle tracking and robust relationships among its entities.

---

## ERD
Below is the Entity-Relationship Diagram (ERD) for the project:

![ERD](ERD.png)

---

## Table Descriptions

### **1. Users**
- Tracks user accounts, deposit balances, and credentials.
- **Columns**:
  - `id`: Unique identifier for the user (Primary Key).
  - `email`: User's email (unique, required).
  - `password`: Hashed password for secure authentication.
  - `deposit_amt`: Amount deposited by the user (default is `0.00`).
  - `created_at`: Timestamp of when the user was created.

---

### **2. Categories**
- Organizes boardgames into thematic categories.
- **Columns**:
  - `id`: Unique identifier for the category (Primary Key).
  - `name`: Category name (e.g., Strategy, Party).
  - `description`: Detailed explanation of the category.

---

### **3. Boardgames**
- Stores high-level details about boardgames available for rent.
- **Columns**:
  - `id`: Unique identifier for the boardgame (Primary Key).
  - `name`: Name of the boardgame.
  - `availability`: Number of copies currently available for rental.
  - `rental_cost`: Cost to rent the boardgame.
  - `category_id`: Links to the category (Foreign Key).
  - `created_at`: Timestamp of when the boardgame was added.

---

### **4. Stock**
- Tracks each physical copy of a boardgame.
- **Columns**:
  - `id`: Unique identifier for the stock item (Primary Key).
  - `boardgame_id`: Links to the boardgame (Foreign Key).
  - `status`: Current status of the stock (e.g., `warehouse`, `with_user`, `to_user`, `to_warehouse`).
  - `location`: Physical location of the stock item (aligned with `status`).

---

### **5. RentalHistory**
- Records rentals initiated by users.
- **Columns**:
  - `id`: Unique identifier for the rental (Primary Key).
  - `user_id`: Links to the renting user (Foreign Key).
  - `stock_id`: Links to the rented stock item (Foreign Key).
  - `rental_date`: Timestamp of when the rental was initiated.
  - `return_date`: Timestamp of when the rental was returned.
  - `rental_cost`: Cost of the rental.
  - `status`: Status of the rental (e.g., `reserved`, `with_user`, `returned`).

---

### **6. Payments**
- Tracks payment details for rentals.
- **Columns**:
  - `id`: Unique identifier for the payment (Primary Key).
  - `rental_id`: Links to the rental (Foreign Key).
  - `amount`: Payment amount.
  - `status`: Status of the payment (e.g., `paid`, `unpaid`).
  - `paid_at`: Timestamp of when the payment was made.

---

### **7. Reviews**
- Allows users to leave reviews for boardgames they have rented.
- **Columns**:
  - `id`: Unique identifier for the review (Primary Key).
  - `user_id`: Links to the user leaving the review (Foreign Key).
  - `boardgame_id`: Links to the reviewed boardgame (Foreign Key).
  - `rating`: Numeric rating between 1 and 5.
  - `comment`: Textual feedback from the user.
  - `created_at`: Timestamp of when the review was submitted.

---

## Relationships
| **Table**         | **Relationship**                                                            |
|--------------------|-----------------------------------------------------------------------------|
| `Users`           | Linked to `RentalHistory` (via `user_id`).                                  |
| `Categories`      | Linked to `Boardgames` (via `category_id`).                                 |
| `Boardgames`      | Linked to `Stock` (via `boardgame_id`).                                     |
| `Stock`           | Linked to `RentalHistory` (via `stock_id`).                                 |
| `RentalHistory`   | Linked to `Payments` (via `rental_id`).                                     |
| `RentalHistory`   | Linked to `Users` and `Stock` for user and stock tracking.                  |
| `Reviews`         | Linked to `Users` (via `user_id`) and `Boardgames` (via `boardgame_id`).    |

---

## Testing Rest API Using Postman (if preferred)

### **Testing Public Routes**

1. Register a User
Method: POST
Endpoint: /users/register
Headers: Content-Type: application/json
Body:
{
  "username": "testuser",
  "email": "testuser@example.com",
  "password": "password123"
}
Expected Responses: 
{
  "message": "User registered successfully"
}

2. Login a User
Method: POST
Endpoint: /users/login
Headers: Content-Type: application/json
Body:
{
  "email": "testuser@example.com",
  "password": "password123"
}
Expected Responses: 
{
  "token": "jwt-token"
}

### **Testing Protected Routes**
For all protected routes, include the Authorization Header:
Key: Authorization
Value: Bearer <JWT_TOKEN> (replace <JWT_TOKEN> with the token obtained from the /users/login endpoint).

3. Fetch All Boardgames
Method: GET
Endpoint: /boardgames
Headers:
    Authorization: Bearer <JWT_TOKEN>
Expected Response:
[
  {
    "id": 1,
    "name": "Chess",
    "description": "Classic strategy game",
    "price": 20.00
  }
]

4. Fetch Boardgame by ID
Method: GET
Endpoint: /boardgames/:id
Headers:
    Authorization: Bearer <JWT_TOKEN>
Replace :id with the boardgame ID.
Expected Response:
{
  "id": 1,
  "name": "Chess",
  "description": "Classic strategy game",
  "price": 20.00
}

5. Fetch User Rentals
Method: GET
Endpoint: /rentals
Headers:
    Authorization: Bearer <JWT_TOKEN>
Expected Response:
[
  {
    "id": 1,
    "user_id": 1,
    "boardgame_id": 1,
    "rental_period": 7
  }
]

6. Create a Rental
Method: POST
Endpoint: /rentals
Headers:
    Authorization: Bearer <JWT_TOKEN>
    Content-Type: application/json
Body:
{
  "user_id": 1,
  "boardgame_id": 1,
  "rental_period": 7
}
Expected Responses: 
{
  "message": "Rental created successfully"
}

7. Update a Rental
Method: PUT
Endpoint: /rentals/:id --> Replace :id with the rental ID.
Headers:
    Authorization: Bearer <JWT_TOKEN>
    Content-Type: application/json
Body:
{
  "rental_period": 14
}
Expected Responses: 
{
  "message": "Rental updated successfully"
}

8. Fetch Rental History
Method: GET
Endpoint: /rentals/history
Headers:
    Authorization: Bearer <JWT_TOKEN>
Expected Response:
[
  {
    "id": 1,
    "user_id": 1,
    "boardgame_id": 1,
    "rental_period": 7,
    "status": "returned"
  }
]

9. Fetch All Payments
Method: GET
Endpoint: /payments
Headers:
    Authorization: Bearer <JWT_TOKEN>
Expected Response:
[
  {
    "id": 1,
    "rental_id": 1,
    "amount": 100.00
  }
]

10. Create a Payment
Method: POST
Endpoint: /payments
Headers:
    Authorization: Bearer <JWT_TOKEN>
    Content-Type: application/json
Body:
{
  "rental_id": 1,
  "amount": 100.00
}
Expected Response: 
{
  "message": "Payment created successfully"
}

11. Fetch Reviews by Boardgame ID
Method: GET
Endpoint: /reviews/:boardgame_id
Headers:
    Authorization: Bearer <JWT_TOKEN>
    Replace :boardgame_id with the boardgame ID.
Expected Response:
[
  {
    "id": 1,
    "boardgame_id": 1,
    "user_id": 1,
    "rating": 5,
    "comment": "Great game!"
  }
]

12. Submit a Review
Method: POST
Endpoint: /reviews
Headers:
    Authorization: Bearer <JWT_TOKEN>
    Content-Type: application/json
Body:
{
  "boardgame_id": 1,
  "rating": 5,
  "comment": "Great game!"
}
Expected Response: 
{
  "message": "Review submitted successfully"
}

13. Create Payment Invoice
Method: POST
Endpoint: /payments/invoice
Headers:
    Content-Type: application/json
    Authorization: Bearer <JWT_TOKEN> (replace <JWT_TOKEN> with the token obtained from /users/login)
Body:
{
  "rental_id": 1,
  "amount": 100.00,
  "description": "Rental for Chess Game"
}
Expexted Response: 
{
  "message": "Invoice created successfully",
  "invoice_url": "https://checkout.xendit.co/invoice/12345"
}

14. Check Invoice Status
Method: GET
Endpoint: /payments/status/:invoice_id
    Replace :invoice_id with the actual invoice ID you want to check.
Headers:
    Authorization: Bearer <JWT_TOKEN> (replace <JWT_TOKEN> with the token obtained from /users/login)
Expected Response:
If the invoice is successful
{
  "status": "SUCCESS"
}
If the invoice is pending or failed
{
  "status": "PENDING" // or "FAILED"
}


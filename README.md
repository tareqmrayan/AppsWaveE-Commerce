# E-Commerce API

A RESTful E-Commerce API built with ASP.NET Core featuring secure authentication, product catalog management, and invoice generation with role-based access control.

---

## Table of Contents
* [Project Overview](#project-overview)
* [Key Features](#key-features)
* [Technology Stack](#technology-stack)
* [User Roles & Permissions](#user-roles--permissions)
* [Data Models](#data-models)
* [Getting Started](#getting-started)
* [API Documentation](#api-documentation)
* [Testing with Postman](#testing-with-postman)
* [Project Structure](#project-structure)

---

## Project Overview

This is a fully functional e-commerce backend API that provides essential features for managing an online store. The API handles user authentication, product inventory management, and order processing through invoice generation.

**What this API does:**
- Allows users to register and login securely using JWT tokens
- Enables administrators to manage the product catalog
- Lets customers browse products and create purchase invoices
- Maintains a complete transaction history with detailed invoice records

**Ideal for:**
- Building e-commerce web or mobile applications
- Learning ASP.NET Core Web API development
- Understanding role-based authorization patterns
- Implementing JWT authentication in real-world scenarios

---

## Key Features

### Authentication & Authorization
* **Secure Registration**: Create user accounts with email and password validation
* **JWT-Based Login**: Token-based authentication for stateless API security
* **Role-Based Access**: Separate permissions for Admins and regular Users

### Product Management
* **Full CRUD Operations**: Create, read, update, and delete products
* **Soft Delete**: Products are marked as deleted rather than permanently removed
* **Bilingual Support**: Store product names in both Arabic and English
* **Pagination**: Efficiently browse large product catalogs with page-based results

### Invoice System
* **Purchase Tracking**: Generate invoices for product purchases
* **Detailed Records**: Each invoice includes customer info, products, quantities, and total amount
* **Invoice Retrieval**: View complete invoice history with all transaction details

### Developer Experience
* **Swagger Documentation**: Interactive API explorer with built-in testing
* **Database Seeding**: Pre-populated sample data for immediate testing
* **Postman Collection**: Ready-to-use API endpoint collection

---

## Technology Stack

- **Framework**: ASP.NET Core Web API
- **Authentication**: JWT (JSON Web Tokens)
- **ORM**: Entity Framework Core
- **Database**: SQL Server (configurable)
- **Documentation**: Swagger/OpenAPI
- **Language**: C#

---

## User Roles & Permissions

### Admin Role
**Full system access including:**
- Create, update, and delete products
- Manage all user accounts
- View and manage all invoices
- Access all administrative endpoints

### User Role (Visitor)
**Standard customer permissions:**
- Browse product catalog with pagination
- View individual product details
- Create purchase invoices
- View their own invoice history

> **Note**: The first registered admin user is created through database seeding. Additional admins can be registered via the `RegisterAdmin` endpoint.

---

## Data Models

### User Entity
Represents registered users in the system with authentication credentials and role information.

- **Id** (int) - Unique identifier for the user
- **FullName** (string) - User's complete name
- **Email** (string) - User's email address, must be unique across the system
- **Username** (string) - Login username, must be unique across the system
- **Password** (string) - Hashed password stored securely
- **Role** (string) - User role defining permissions (Admin or User)

### Product Entity
Represents items available for purchase in the e-commerce catalog.

- **Id** (int) - Unique identifier for the product
- **ArabicName** (string) - Product name in Arabic language
- **EnglishName** (string) - Product name in English language
- **Price** (decimal) - Current price of the product
- **IsDeleted** (bool) - Soft delete flag, when true the product is hidden from listings

### Invoice Entity
Represents a purchase transaction made by a user.

- **Id** (int) - Unique identifier for the invoice
- **Date** (DateTime) - Date and time when the invoice was created
- **UserId** (int) - Reference to the user who made the purchase
- **TotalAmount** (decimal) - Total cost of all products in the invoice
- **InvoiceDetails** (List<InvoiceDetail>) - Collection of products included in this purchase

### Invoice Detail Entity
Represents individual product line items within an invoice.

- **Id** (int) - Unique identifier for the invoice detail record
- **InvoiceId** (int) - Reference to the parent invoice
- **ProductId** (int) - Reference to the purchased product
- **Price** (decimal) - Unit price of the product at the time of purchase
- **Quantity** (int) - Number of units purchased

---

## Getting Started

### Prerequisites
- .NET 8.0 SDK or higher
- SQL Server (LocalDB, Express, or Full)
- Visual Studio 2022 or VS Code (optional)

### Installation Steps

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd ECommerceAPI
   ```

2. **Restore NuGet packages**
   ```bash
   dotnet restore
   ```

3. **Configure database connection**
   
   Open `appsettings.json` and update the connection string:
   ```json
   {
     "ConnectionStrings": {
       "DefaultConnection": "Server=localhost;Database=ECommerceDB;Trusted_Connection=True;"
     }
   }
   ```

4. **Apply database migrations**
   ```bash
   dotnet ef database update
   ```
   This creates the database schema and seeds initial data.

5. **Run the application**
   ```bash
   dotnet run
   ```

### Initial Seeded Data

**Products:**
- 3 sample products with Arabic and English names

**Users:**
- 1 Admin account 
- 1 Regular user account

---

## API Documentation

### Swagger UI

Swagger provides an interactive interface to explore and test all API endpoints.

**Access Swagger:**
1. Run the application
2. Click "Authorize" button to add your JWT token
3. Format: `Bearer <your-token-here>`
4. Test endpoints directly from the browser

### Main Endpoint Categories

**Authentication** (`/api/Auth`)
- `POST /Login` - Authenticate and receive JWT token
- `POST /RegisterUser` - Create new customer account
- `POST /RegisterAdmin` - Create new admin account (Admin only)

**Products** (`/api/Products`)
- `GET /GetProduct?id={id}` - Get single product details
- `GET /GetProducts?page={page}&pageSize={size}` - List products with pagination
- `POST /AddProduct` - Create new product (Admin only)
- `PUT /UpdateProduct` - Modify existing product (Admin only)
- `DELETE /DeleteProduct?id={id}` - Soft delete product (Admin only)

**Invoices** (`/api/Invoices`)
- `POST /AddInvoice` - Create new purchase invoice
- `GET /GetInvoice?id={id}` - Retrieve invoice details

---

## Testing with Postman

A complete Postman collection is included for comprehensive API testing.

### Import Collection

1. Open Postman
2. Click **Import** → **Upload Files**
3. Select the included JSON file
4. Collection appears in your workspace

### Environment Setup

1. Create a new environment named "E-Commerce API"
2. Add variable: `BaseURL` = `API URL`
3. Add variable: `Token` = (leave empty, auto-populated after login)

### Testing Workflow

**Step 1: Login**
```
POST {{BaseURL}}/api/Auth/Login
Body: {
  "username": "admin",
  "password": "122333"
}
```
Copy the returned token.

**Step 2: Set Authorization**
- Go to collection settings
- Authorization tab → Type: Bearer Token
- Token field: Paste your JWT token

**Step 3: Test Endpoints**
- All requests now include authentication
- Try creating products, viewing invoices, etc.

---

## Project Structure

```
ECommerceAPI/
├── Controllers/          # API endpoint controllers
│   ├── AuthController.cs
│   ├── ProductsController.cs
│   └── InvoicesController.cs
├── Models/              # Entity data models
│   ├── APIResponse.cs
│   ├── User.cs
│   ├── Product.cs
│   ├── Invoice.cs
│   └── InvoiceDetail.cs
├── Data/                # Database context
│   ├── ApplicationDbContext.cs
├── Services/            # Business logic layer
├── DTOs/               # Data transfer objects
├── appsettings.json    # Configuration
└── Program.cs          # Application entry point
```

---

**Version**: 1.0.0

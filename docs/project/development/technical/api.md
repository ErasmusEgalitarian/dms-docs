### API Documentation: Content and User Management System

[← Back to Main Page](../../../index.md)

## API Endpoints

This section details the available API endpoints, their functionalities, and expected request/response formats. All endpoints are located under `/api`.

### Authentication

- **`POST /api/auth/login`**
  - **Description**: Authenticates a user based on CPF and password.
  - **Request Body**:
    ```json
    {
      "cpf": "user_cpf",
      "password": "user_password"
    }
    ```
  - **Response**:
    - **Success (200)**: Sets an `auth_token` (JWT) cookie and returns user details.
      ```json
      {
        "message": "Login realizado com sucesso",
        "user": {
          "id": "user_id",
          "name": "User Name",
          "full_name": "User Full Name",
          "userType": 0, // Or other user type integer
          "notFound": false // True if user was not found in DB and a fallback/sample was used
        }
      }
      ```
    - **Error (400)**: If `cpf` or `password` are not provided.
      ```json
      { "message": "CPF e senha são obrigatórios" }
      ```
    - **Error (500)**: Server-side error during login.
      ```json
      { "message": "Erro no servidor" }
      ```

- **`POST /api/auth/logout`**
  - **Description**: Logs out the current user by clearing the `auth_token` cookie.
  - **Response**:
    - **Success (200)**:
      ```json
      { "message": "Logout realizado com sucesso" }
      ```
    - **Error (500)**: Server-side error during logout.
      ```json
      { "message": "Erro no servidor" }
      ```

### Birthdays

- **`GET /api/birthdays`**
  - **Description**: Fetches users (specifically workers, identified by `user_type: 1`) whose birthdays occur in the current month.
  - **Query Parameters**: None.
  - **Response**:
    - **Success (200)**: An array of birthday objects. Returns sample data if no matching birthdays are found in the database for the current month.
      ```json
      [
        { "name": "User Full Name", "date": "DD/MM" },
        // ... more users
      ]
      ```
      Example Sample Data (if no DB entries for current month):
      ```json
      [
        { "name": "João Silva", "date": "15/06" },
        // ...
      ]
      ```
    - **Error (500)**: Server-side error.
      ```json
      { "error": "Failed to fetch birthdays data", "details": "Error message string" }
      ```

### Cooperatives

- **`GET /api/cooperatives`**
  - **Description**: Fetches a list of cooperatives.
  - **Query Parameters**: To be determined by code review.
  - **Response**:
    - **Success (200)**: Array of cooperative objects.
    - **Error Responses**: (500) Server Error.
  - **Note**: Methods (GET, POST) inferred from route file presence. Actual implementation details need code review.

- **`POST /api/cooperatives`**
  - **Description**: Creates a new cooperative.
  - **Request Body**: Cooperative data. (To be determined by code review).
  - **Response**:
    - **Success (201)**: Cooperative object created.
    - **Error Responses**: (400) Bad Request, (500) Server Error.
  - **Note**: Methods (GET, POST) inferred from route file presence. Actual implementation details need code review.

### Debug Endpoints

These endpoints appear to be for development and debugging purposes.

- **`GET /api/debug/collections`**
  - **Description**: Lists all collections in the connected MongoDB database and provides a sample document from each.
  - **Response (200)**:
    ```json
    {
      "collections": ["collection_name_1", "collection_name_2", "..."],
      "samples": {
        "collection_name_1": {
          "fields": ["field1", "field2", "..."],
          "sample": { "field1": "value1", "..." }
        },
        // ... more collections
      }
    }
    ```
  - **Error (500)**: Server-side error.

- **`GET /api/debug/check-data`**
  - **Description**: Checks some data consistency or status in the database.
  - **Query Parameters**: To be determined by code review.
  - **Response**:
    - **Success (200)**: Status message or data check results.
    - **Error Responses**: (500) Server Error.
  - **Note**: Actual implementation details need code review.

- **`POST /api/debug/create-test-user`**
  - **Description**: Creates a test user in the system.
  - **Request Body**: Test user data (To be determined by code review).
  - **Response**:
    - **Success (201)**: Created test user object.
    - **Error Responses**: (400) Bad Request, (500) Server Error.
  - **Note**: Actual implementation details need code review.

- **`GET /api/debug/wastepickers`**
  - **Description**: Attempts to find a specific wastepicker (user) using various query methods (by `wastepicker_id`, CPF, ObjectId). It may also attempt to insert a test user if not found.
  - **Response (200)**: An object containing results of different find/insert attempts.
    ```json
    {
      "byWastepickerId": { /* user doc */ } // or null
      "byCpf": { /* user doc */ } // or null
      "byObjectId": { /* user doc */ } // or null
      "collections": ["users", "..."], // list of collections
      "insertAttempt": { /* MongoDB update result */ }
      "afterInsert": { /* User document after upsert */ }
    }
    ```
  - **Error (500)**: Server-side error.

### Data Utilities

- **`POST /api/recalculate-contributions`**
  - **Description**: Triggers a recalculation of contributions or similar aggregate data.
  - **Request Body**: Potentially parameters for recalculation (To be determined by code review).
  - **Response**:
    - **Success (200)**: Message confirming recalculation started or completed.
    - **Error Responses**: (400) Bad Request, (500) Server Error.
  - **Note**: Actual implementation details need code review.

### Earnings Comparison

- **`GET /api/earnings-comparison`**
  - **Description**: Fetches aggregated earnings data from the `sales` collection for comparison over different time periods.
  - **Query Parameters**:
    - `material_id` (optional `String`): ID of the material to filter earnings by.
    - `period_type` (optional `String`): `weekly`, `monthly` (default), or `yearly`. Defines the aggregation period.
  - **Response**:
    - **Success (200)**: An array of earnings data points.
      ```json
      [
        { "period": "Period Label", "earnings": 1234.56 },
        // ... more periods (typically 6 for monthly/weekly, variable for yearly)
      ]
      ```
      Period Label format examples:
        - Weekly: "DD/MM - DD/MM"
        - Monthly: "jun", "jul" (abbreviated month name)
        - Yearly: "YYYY"
    - **No Data (200)**: If no sales data is found for the criteria.
      ```json
      {
        "noData": true,
        "message": "Não há vendas registradas para este material" // or general message
      }
      ```
    - **Error (500)**: Server-side error.
      ```json
      {
        "noData": true, // Often returns noData structure on error too
        "message": "Erro ao buscar dados de vendas. Por favor, tente novamente mais tarde."
      }
      ```

### Materials

- **`GET /api/materials`**
  - **Description**: Fetches a list of all materials from the `materials` collection.
  - **Query Parameters**: None.
  - **Response**:
    - **Success (200)**: An array of material objects.
      ```json
      [
        {
          "_id": "material_object_id", // MongoDB ObjectId
          "material_id": "material_id_value", // Application-specific ID, or _id if material_id is missing
          "name": "Material Name", // Preferred name, or derived from material.material
          // ... other fields from the material document in the collection
        },
        // ... more materials
      ]
      ```
    - **Error (404)**: If no materials are found in the database.
      ```json
      { "error": "No materials available", "details": "No materials were found in the database" }
      ```
    - **Error (500)**: Server-side error.
      ```json
      { "error": "Failed to fetch materials", "details": "Error message string" }
      ```

- **`POST /api/materials`**
  - **Description**: Creates a new material.
  - **Request Body**: Material data (To be determined by code review).
  - **Response**:
    - **Success (201)**: Material object created.
    - **Error Responses**: (400) Bad Request, (500) Server Error.
  - **Note**: Actual implementation details need code review.

- **`GET /api/materials/{id}`**
  - **Description**: Fetches a specific material by its ID.
  - **Path Parameters**:
    - `id`: The ID of the material.
  - **Query Parameters**: None.
  - **Response**:
    - **Success (200)**: Material object.
    - **Error Responses**: (404) Not Found, (500) Server Error.
  - **Note**: Actual implementation details need code review.

- **`PUT /api/materials/{id}`**
  - **Description**: Updates a specific material by its ID.
  - **Path Parameters**:
    - `id`: The ID of the material to update.
  - **Request Body**: Updated material data (To be determined by code review).
  - **Response**:
    - **Success (200)**: Updated material object.
    - **Error Responses**: (400) Bad Request, (404) Not Found, (500) Server Error.
  - **Note**: Actual implementation details need code review.

- **`DELETE /api/materials/{id}`**
  - **Description**: Deletes a specific material by its ID.
  - **Path Parameters**:
    - `id`: The ID of the material to delete.
  - **Response**:
    - **Success (200)**: Message confirming deletion.
    - **Error Responses**: (404) Not Found, (500) Server Error.
  - **Note**: Actual implementation details need code review.

### Price Fluctuation

- **`GET /api/price-fluctuation`**
  - **Description**: Fetches price fluctuation data for materials, sourced from the `sales` collection.
  - **Query Parameters**:
    - `material_id` (optional `String`): ID of the specific material to fetch price history for.
  - **Response**:
    - **Success (200, with `material_id`)**: An array of the last 10 sales records for the specified material, sorted chronologically.
      ```json
      [
        {
          "date": "ISO_date_string", // e.g., "2023-06-15T00:00:00.000Z"
          "material": "Material Name",
          "price": 12.34, // price/kg
          "dateLabel": "DD Mmm YY", // e.g., "15 Jun 23"
          "timestamp": 1678886400000 // Unix timestamp milliseconds
        },
        // ... up to 10 records
      ]
      ```
    - **Success (200, without `material_id`)**: Data for the top 5 materials with the most recent sales. For each of these, the last 10 sales records are aggregated by date.
      ```json
      {
        "materials": ["Material Name 1", "Material Name 2", "..."], // Top 5 material names
        "priceData": [
          {
            "weekLabel": "DD Mmm YY", // Unique date label across all sales
            "date": "ISO_date_string",
            "materials": {
              "Material Name 1": 10.50, // Price if available for this date
              "Material Name 2": 12.75,
              // ... other materials might be null if no sale on this date
            }
          },
          // ... more date points
        ]
      }
      ```
    - **No Data (200)**: If no relevant sales data is found for the criteria.
      ```json
      {
        "noData": true,
        "message": "Não há histórico de preços para este material" // or general message
      }
      ```
    - **Error (Returns 200 with `noData:true`)**: If an error occurs during fetching, it often returns a 200 status with a `noData` structure.
      ```json
      {
        "noData": true,
        "message": "Erro ao buscar dados de preços"
      }
      ```

### Sales

- **`GET /api/sales`**
  - **Description**: Fetches a list of sales records.
  - **Query Parameters**: To be determined by code review (e.g., filters for material, date range).
  - **Response**:
    - **Success (200)**: Array of sales objects.
    - **Error Responses**: (500) Server Error.
  - **Note**: Actual implementation details need code review.

- **`POST /api/sales`**
  - **Description**: Creates a new sales record.
  - **Request Body**: Sales data (To be determined by code review).
  - **Response**:
    - **Success (201)**: Sales object created.
    - **Error Responses**: (400) Bad Request, (500) Server Error.
  - **Note**: Actual implementation details need code review.

- **`GET /api/sales/{id}`**
  - **Description**: Fetches a specific sales record by its ID.
  - **Path Parameters**:
    - `id`: The ID of the sales record.
  - **Query Parameters**: None.
  - **Response**:
    - **Success (200)**: Sales object.
    - **Error Responses**: (404) Not Found, (500) Server Error.
  - **Note**: Actual implementation details need code review.

- **`PUT /api/sales/{id}`**
  - **Description**: Updates a specific sales record by its ID.
  - **Path Parameters**:
    - `id`: The ID of the sales record to update.
  - **Request Body**: Updated sales data (To be determined by code review).
  - **Response**:
    - **Success (200)**: Updated sales object.
    - **Error Responses**: (400) Bad Request, (404) Not Found, (500) Server Error.
  - **Note**: Actual implementation details need code review.

- **`DELETE /api/sales/{id}`**
  - **Description**: Deletes a specific sales record by its ID.
  - **Path Parameters**:
    - `id`: The ID of the sales record to delete.
  - **Response**:
    - **Success (200)**: Message confirming deletion.
    - **Error Responses**: (404) Not Found, (500) Server Error.
  - **Note**: Actual implementation details need code review.

- **`GET /api/sales/buyers`**
  - **Description**: Fetches a list of unique buyers from sales records.
  - **Query Parameters**: To be determined by code review.
  - **Response**:
    - **Success (200)**: Array of buyer names or objects.
    - **Error Responses**: (500) Server Error.
  - **Note**: Actual implementation details need code review.

### Stock

- **`GET /api/stock`**
  - **Description**: Fetches current stock data. It aggregates weights from the `measurements` collection and subtracts sold quantities from the `sales` collection.
  - **Query Parameters**:
    - `material_id` (optional `String`): ID of the material to filter stock for.
  - **Response**:
    - **Success (200)**: An object where keys are material names and values are their current stock weight in kg. Returns sample data if no stock data is found in the DB.
      ```json
      {
        "Material Name 1": 150.75,
        "Material Name 2": 200.00,
        // ... more materials
      }
      ```
    - **No Data (200, for specific `material_id`)**: If `material_id` is provided but no stock is found for it.
      ```json
      { "noData": true, "message": "Não há estoque deste material" }
      ```
    - **Sample Data (if DB is empty or error occurs)**:
      ```json
      {
        "Papelão": 950,
        "Papel Branco": 850,
        // ... more sample materials
      }
      ```
    - **Error**: In case of a server error, it often falls back to returning sample stock data with a 200 status.

### User

- **`GET /api/user`**
  - **Description**: Fetches details for a specific user.
  - **Query Parameters**:
    - `id` (required `String`): The MongoDB ObjectId or application-specific ID of the user to fetch.
  - **Response**:
    - **Success (200)**: User object (sensitive fields like password hash are omitted).
      ```json
      {
        "_id": "user_mongodb_object_id",
        "full_name": "User Full Name",
        // ... other user fields
      }
      ```
    - **Error (400)**: If `id` query parameter is not provided.
      ```json
      { "message": "User ID is required" }
      ```
    - **Error (404)**: If user with the given ID is not found.
      ```json
      { "message": "User not found" }
      ```
    - **Error (500)**: Server-side error.
      ```json
      { "message": "Error fetching user data" }
      ```

- **`POST /api/user/change-password`**
  - **Description**: Allows an authenticated user to change their password.
  - **Request Body**:
    ```json
    {
      "currentPassword": "current_user_password",
      "newPassword": "new_user_password"
    }
    ```
    (Details to be confirmed by code review)
  - **Response**:
    - **Success (200)**: Message confirming password change.
    - **Error Responses**: (400) Bad Request (e.g., incorrect current password, weak new password), (401) Unauthorized, (500) Server Error.
  - **Note**: Actual implementation details need code review.

- **`PUT /api/user/update`**
  - **Description**: Allows an authenticated user to update their own profile information.
  - **Request Body**: User data to update (e.g., email, phone; To be determined by code review).
  - **Response**:
    - **Success (200)**: Updated user object.
    - **Error Responses**: (400) Bad Request, (401) Unauthorized, (500) Server Error.
  - **Note**: Actual implementation details need code review.

### Users

- **`GET /api/users`**
  - **Description**: Fetches a list of "wastepickers" (workers, identified by `user_type: 1`).
  - **Query Parameters**: None.
  - **Response**:
    - **Success (200)**: An array of user objects (sensitive fields omitted). Returns sample data if no workers are found in the DB.
      ```json
      [
        {
          "wastepicker_id": "WP001", // Application-specific worker ID
          "full_name": "Worker Full Name",
          "user_type": 1,
          // ... other user fields
        },
        // ... more users
      ]
      ```
      Sample Data (if no DB entries for workers):
      ```json
      [
        { "wastepicker_id": "WP001", "full_name": "João Silva", "user_type": 1, ... },
        // ...
      ]
      ```
    - **Error (500)**: Server-side error.
      ```json
      { "error": "Failed to fetch users", "details": "Error message string" }
      ```

- **`GET /api/users/all`**
  - **Description**: Fetches all users from the `users` collection, regardless of their `user_type`.
  - **Query Parameters**: None.
  - **Response**:
    - **Success (200)**: An array of all user objects (sensitive fields omitted).
      ```json
      [
        {
          "_id": "user_mongodb_object_id",
          "full_name": "User Full Name",
          // ... other user fields including user_type
        },
        // ... more users
      ]
      ```
    - **Error (500)**: Server-side error.
      ```json
      { "message": "Error fetching users data" }
      ```

- **`POST /api/users/create`**
  - **Description**: Creates a new user.
  - **Request Body**: User data for the new user (To be determined by code review).
  - **Response**:
    - **Success (201)**: Created user object.
    - **Error Responses**: (400) Bad Request, (500) Server Error.
  - **Note**: Actual implementation details need code review.

- **`PUT /api/users/update`**
  - **Description**: Updates an existing user's information. Likely requires user ID in the body or as a query parameter.
  - **Request Body**: User data to update, including ID of the user (To be determined by code review).
  - **Response**:
    - **Success (200)**: Updated user object.
    - **Error Responses**: (400) Bad Request, (404) Not Found, (500) Server Error.
  - **Note**: Actual implementation details need code review. The path does not include an ID, so ID must be in payload.

- **`DELETE /api/users/delete`**
  - **Description**: Deletes a user. Likely requires user ID in the body or as a query parameter.
  - **Request Body**: Object containing the ID of the user to delete, e.g. `{ "id": "user_id" }` (To be determined by code review).
  - **Response**:
    - **Success (200)**: Message confirming deletion.
    - **Error Responses**: (400) Bad Request, (404) Not Found, (500) Server Error.
  - **Note**: Actual implementation details need code review. The path does not include an ID, so ID must be in payload or query.

- **`POST /api/users/assign-wastepicker-ids`**
  - **Description**: Assigns `wastepicker_id` to users who are workers and do not have one.
  - **Request Body**: None expected, or potentially parameters for assignment batch. (To be determined by code review).
  - **Response**:
    - **Success (200)**: Report of assignments made.
    - **Error Responses**: (500) Server Error.
  - **Note**: Actual implementation details need code review.

### Worker Collections

- **`GET /api/worker-collections`**
  - **Description**: Fetches data about material collections by workers, primarily from the `measurements` collection.
  - **Query Parameters**:
    - `worker_id` (optional `String`): ID of the worker to filter collections for.
    - `material_id` (optional `String`): ID of the material to filter collections for.
    - `period_type` (optional `String`): `weekly`, `monthly` (default), or `yearly`. Defines the time period for aggregation.
  - **Response**:
    - **Success (200, `grouped: false`)**: Typically when `material_id` is specified or `period_type` is not 'yearly'. Returns top 10 workers by total weight collected for the criteria.
      ```json
      {
        "grouped": false,
        "data": [
          {
            "wastepicker_id": "WP001",
            "worker_name": "Worker Name",
            "totalWeight": 123.45 // in kg
          },
          // ... more workers
        ]
      }
      ```
    - **Success (200, `grouped: true`)**: Typically for `period_type: 'yearly'` without a `material_id` filter. Returns data structured for a stacked bar chart, showing material breakdown for top 10 workers.
      ```json
      {
        "grouped": true,
        "workers": [ // Top 10 workers by total weight
          {
            "wastepicker_id": "WP001",
            "worker_name": "Worker Name",
            "totalWeight": 500.50,
            "material_id_1": 100.20, // Weight for material_id_1 (actual material ID as key)
            "material_id_2": 200.30, // Weight for material_id_2
            // ... weights for other materials collected by this worker
          },
          // ... more workers
        ],
        "materials": [ // List of unique materials collected by these top workers
          { "id": "material_id_1", "name": "Material Name 1" },
          { "id": "material_id_2", "name": "Material Name 2" },
          // ... more materials
        ]
      }
      ```
    - **No Data (200)**: If no collection data is found for the given filters.
      ```json
      {
        "noData": true,
        "message": "Não há coletas deste material em este período" // or similar
      }
      ```
    - **Error (500, often returns `noData:true`)**: Server-side error.
      ```json
      {
        "noData": true,
        "message": "Erro ao buscar dados de coletas. Por favor, tente novamente mais tarde."
      }
      ```

### Worker Productivity

- **`GET /api/worker-productivity`**
  - **Description**: Fetches data related to worker productivity metrics.
  - **Query Parameters**: To be determined by code review (e.g., `worker_id`, `period_type`).
  - **Response**:
    - **Success (200)**: Productivity data for workers.
    - **Error Responses**: (500) Server Error.
  - **Note**: Actual implementation details need code review.


[← Back to Main Page](../../../index.md)

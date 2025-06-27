
# **Database Schema**

[← Back to Main Page](../../../index.md)

## Data Models

The application uses Mongoose to define schemas and interact with the MongoDB database. The primary Mongoose models are defined in `src/models/index.ts`. However, as noted below, some API endpoints derive data from collections not strictly governed by these explicit models or use different field names than defined in the schemas.

### Defined Mongoose Models

The following models have schemas defined in `src/models/index.ts`:

1.  **Material (`MaterialSchema`)**
    -   **Collection Name**: `materials` (as specified in the schema: `{ collection: 'materials' }`).
    -   **Description**: Represents a type of waste material.
    -   **Schema Fields**:
        -   `material_id`: `Number` (Required, Unique) - An application-specific identifier for the material.
        -   `material`: `String` (Required) - The name of the material (e.g., "Papelão", "Plástico PET"). This seems to be the primary name field in the schema.
        -   `name`: `String` - Not explicitly in the schema but often used in API responses (e.g., in `/api/materials`). This might be an alias for `material` or populated from a different field in the `materials` collection if it's distinct from `waste_type`.

2.  **User (`UserSchema`)**
    -   **Collection Name**: `users`
    -   **Description**: Represents users of the system, including administrators and workers (wastepickers).
    -   **Schema Fields**:
        -   **Important Note on Field Usage**: Developers should be aware that some API routes might expect or use field names not explicitly defined in this schema or with different casing/spacing. For instance, the login API (`/api/auth/login`) uses a `cpf` field which is not in the schema, and the birthdays API (`/api/birthdays`) refers to `"Birth date"` (with a space) which differs from the `birthdate` schema field. Always cross-verify with the specific API implementation.
        -   `wastepicker_id`: `Number` (Unique, Sparse) - Specific identifier for wastepickers.
        -   `user_id`: `Number` (Required, Unique) - General unique user identifier.
        -   `user_type`: `Number` (Required) - Type of user (e.g., `0` for admin, `1` for wastepicker/worker).
        -   `username`: `String` (Required, Unique) - Login username.
        -   `password_hash`: `String` - Hashed password for the user. (Login API expects `password` in request, implying hashing before storage or comparison with this field).
        -   `full_name`: `String` - Full name of the user.
        -   `email`: `String` - Email address.
        -   `phone`: `String` - Phone number.
        -   `birthdate`: `Date` - User's birthdate. (Note: As mentioned above, the `/api/birthdays` API specifically uses a field named `"Birth date"` (with a space) from the database for workers, which might be an alternative or legacy field name not matching this schema field directly).
        -   `active`: `Boolean` (Default: `true`) - Whether the user account is active.
        -   `created_at`: `Date` (Default: `Date.now`) - Timestamp of user creation.
    -   **Other Fields Observed in DB/APIs (potentially not in schema but used, illustrating the note above)**:
        -   `cpf`: `String` (Used in login API `/api/auth/login`).
        -   `coopeative_id`: `String` (Typo for `cooperative_id`?).
        -   `"Entry date"`: `Date` (Entry date of the worker).
        -   `PIS`: `String` (PIS number).
        -   `RG`: `String` (RG number).
        -   `gender`: `String`.

3.  **Stock (`StockSchema`)**
    -   **Collection Name**: `stock`
    -   **Description**: Intended to represent the current stock of a particular material.
    -   **Schema Fields**:
        -   `material_id`: `Number` (Required, Ref: `Material`) - Reference to the material.
        -   `weight`: `Number` (Required) - Weight of the material in stock.
        -   `date`: `Date` (Default: `Date.now`) - Date when the stock was recorded.
    -   **Note on Usage**: While the `StockSchema` exists and defines a `stock` collection, the primary API route for dashboard stock data (`/api/stock`) dynamically calculates current stock levels primarily from the `measurements` (for additions) and `sales` (for subtractions) collections.
        This `stock` collection and its Mongoose model might be intended for specific backend operations, historical data storage, manual data entry, or could be a legacy structure not fully utilized by the current dashboard's read APIs. Developers should verify its specific use case if they intend to interact directly with this collection.

4.  **Collection (`CollectionSchema`)**
    -   **Collection Name**: `collections`
    -   **Description**: Intended to represent a record of materials collected by a worker.
    -   **Schema Fields**:
        -   `wastepicker_id`: `Number` (Required, Ref: `User`) - Reference to the worker.
        -   `material_id`: `Number` (Required, Ref: `Material`) - Reference to the collected material.
        -   `weight`: `Number` (Required) - Weight of the material collected.
        -   `date`: `Date` (Default: `Date.now`) - Date of the collection.
    -   **Note on Usage**: While the `CollectionSchema` exists and defines a `collections` collection (intended for worker collections), the primary API route for dashboard worker collection data (`/api/worker-collections`) dynamically calculates this data primarily from the `measurements` collection.
        This `collections` collection and its Mongoose model might be intended for specific backend operations, historical data storage, manual data entry, or could be a legacy structure not fully utilized by the current dashboard's read APIs. Developers should verify its specific use case if they intend to interact directly with this collection.

5.  **Price (`PriceSchema`)**
    -   **Collection Name**: `prices`
    -   **Description**: Intended to represent the price of a material at a certain point in time.
    -   **Schema Fields**:
        -   `material_id`: `Number` (Required, Ref: `Material`) - Reference to the material.
        -   `price`: `Number` (Required) - Price of the material (likely per unit of weight, e.g., per kg).
        -   `date`: `Date` (Default: `Date.now`) - Date when the price was set.
    -   **Note on Usage**: While the `PriceSchema` exists and defines a `prices` collection, the primary API route for dashboard price fluctuation data (`/api/price-fluctuation`) dynamically calculates this data primarily from the `sales` collection (using fields like `price_kg`).
        This `prices` collection and its Mongoose model might be intended for specific backend operations, historical data storage (e.g., base prices, price lists), manual data entry, or could be a legacy structure not fully utilized by the current dashboard's read APIs. Developers should verify its specific use case if they intend to interact directly with this collection.

### Other Important Collections (Inferred from API Routes)

Several API routes heavily rely on collections that might not have explicit Mongoose schemas defined in `src/models/index.ts` or are used with more flexibility than the defined schemas suggest. These are crucial for the application's functionality:

-   **`measurements`**
    -   **Description**: Stores records of individual material measurements or collections by workers. This collection is central to calculating current stock (total input) and worker collection statistics.
    -   **Key Fields (inferred from API usage)**:
        -   `material_id`: String or Number (referencing a material, often the `_id` from the `materials` collection or an application-specific ID).
        -   `Weight`: Number (weight of material, typically in kg).
        -   `timestamp`: Date or Number (when the material was measured/collected).
        -   `wastepicker_id`: String or Number (e.g., `WP001`, referencing a user/worker).

-   **`sales`**
    -   **Description**: Stores records of material sales. This collection is used for calculating earnings, price fluctuations, and current stock (total output).
    -   **Key Fields (inferred from API usage)**:
        -   `material_id`: String or Number (referencing a material).
        -   `date`: Date or String (date of the sale).
        -   `price_kg`: Number (price per kg, sometimes seen as `price/kg` or `unit_price`).
        -   `weight_sold`: Number (quantity sold, typically in kg).
        -   `buyer`: String (name or ID of the buyer, if available).

### Important Note on Data Model Usage

A key takeaway for developers is that while Mongoose schemas are defined in `src/models/index.ts`, their application and usage can vary, especially concerning API data retrieval for the dashboard.

**1. API Data Sourcing vs. Defined Schemas:**
-   **`Stock`, `Collection`, `Price` Models**: As detailed in their respective notes, the Mongoose models `StockSchema`, `CollectionSchema`, and `PriceSchema` (defining `stock`, `collections`, and `prices` collections) are not the primary sources for the main dashboard API routes (`/api/stock`, `/api/worker-collections`, `/api/price-fluctuation`). These routes dynamically calculate data primarily from the `measurements` and `sales` collections. The `stock`, `collections`, and `prices` collections might be for other backend uses, historical data, manual entries, or are legacy.
-   **`Material` Model**: The `MaterialSchema` correctly defines its collection as `materials`.

**2. Field Name and Usage Variations:**
-   **User Model Example**: The `UserSchema` defines fields like `birthdate`. However, some APIs expect or use fields not explicitly in the schema (e.g., `cpf` for `/api/auth/login`) or use different naming conventions (e.g., `"Birth date"` with a space by `/api/birthdays`, as highlighted in the `User` model section).
-   Other similar variations might exist across different models and API routes.

**3. Implications for Development:**
-   The Mongoose models in `src/models/index.ts` might not always be the single source of truth for understanding data structures as used by all API endpoints, particularly read-heavy dashboard APIs.
-   Some models might represent a target schema, are used for specific write operations, or are partially legacy.
-   The application employs a dynamic approach for some data aggregation and retrieval, interacting directly with MongoDB collections.

**Recommendation:**
**Developers must always cross-verify data models, collection names, and field structures by inspecting the relevant API route implementations (`src/app/api/...`) and, if possible, the actual database contents.** This is crucial for understanding how data is fetched, processed, and returned. The descriptions for `measurements` and `sales` collections are based on their inferred usage in the current API routes.

[← Back to Main Page](../../../index.md)

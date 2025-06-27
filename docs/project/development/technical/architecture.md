# Architecture

[← Back to Main Page](../../../index.md)
## Project Structure

The project follows a standard Next.js project structure using the App Router:

- **`src/app`**: Contains the core application code, including pages, layouts, and API routes.
  - **`src/app/api`**: Houses all backend API route handlers (Route Handlers).
  - **`src/app/login`**: Contains the login page UI and related components.
  - **`src/app/page.tsx`**: The main dashboard page component of the application.
  - **`src/app/layout.tsx`**: The root layout component for the application.
- **`src/components`**: Contains reusable React components used across different parts of the application.
  - **`src/components/Layout.tsx`**: The layout component (sidebar, header) for authenticated sections of the application.
- **`src/lib`**: Contains utility functions and library configurations.
  - **`src/lib/mongodb.ts`**: Handles the connection to the MongoDB database.
- **`src/models`**: Defines Mongoose schemas and models for interacting with the database.
  - **`src/models/index.ts`**: Exports all defined models and provides a database connection function.
- **`public`**: Contains static assets like images, SVGs, and fonts that are served directly.
- **`pages`**: (Note: This project primarily uses the `app` directory for routing. While Next.js supports the `pages` directory for routing, this project does not currently have one. If added, files here would also become routes.)

Key configuration files at the root:

- **`next.config.ts`**: Configuration file for Next.js features and build process.
- **`tailwind.config.ts`**: Configuration file for Tailwind CSS utility classes and theming.
- **`tsconfig.json`**: Configuration file for the TypeScript compiler.
- **`package.json`**: Lists project dependencies, scripts (like `dev`, `build`, `start`), and project metadata.
- **`generatepass.js`**: A standalone Node.js script for generating bcrypt password hashes. See the ["Password Generation Script (`generatepass.js`)"](#password-generation-script-generatepassjs) subsection under "Project Structure" for full details.
- **`eslint.config.mjs`**: Configuration for ESLint, a static code analysis tool for identifying problematic patterns in JavaScript/TypeScript code.
- **`postcss.config.mjs`**: Configuration for PostCSS, a tool for transforming CSS with JavaScript plugins.

### Password Generation Script (`generatepass.js`)

-   **Purpose**: The `generatepass.js` script is a utility for generating password hashes using `bcrypt`. This is necessary because passwords should be stored in a hashed format for security, rather than as plain text.
-   **How to Use**:
    1.  **Modify Script**: Open `generatepass.js` in a text editor. Locate the `password` variable (which defaults to `'123456'`) and change its value to the desired plain-text password you want to hash.
        ```javascript
        // Inside generatepass.js
        const password = 'your-desired-password-here';
        // ... rest of the script
        ```
    2.  **Run from Command Line**: Execute the script using Node.js:
        ```bash
        node generatepass.js
        ```
    3.  **Output**: The script will print the original plain-text password and the generated bcrypt hash to the console.
        ```
        Plaintext password: your-desired-password-here
        Hashed password: $2b$10$.....................................................
        ```
-   **Typical Use Cases**:
    -   **Initial User Setup**: When creating the first admin user or other initial users directly in the database.
    -   **Test Accounts**: For creating test user accounts with known passwords for development and testing purposes.
    -   **Manual Database Entries**: If you need to manually insert a user into the database and require a correctly hashed password.
-   **Security Note**:
    -   Always change the default password (`'123456'`) in the script to your desired password before running it.
    -   This script is a development utility. It is not part of the production application's runtime and should not be deployed or made accessible in a production environment. Ensure it is used in a secure, local development context.

[← Back to Main Page](../../../index.md)

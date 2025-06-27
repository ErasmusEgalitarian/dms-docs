# Getting Started

[← Back to Main Page](../index.md)

This section explains how to set up and run the project locally.

## Prerequisites

- Node.js (version recommended by Next.js, e.g., 18.x or later)
- npm, yarn, pnpm, or bun (package manager)
- Access to a MongoDB instance (local or cloud-hosted)
- Git (for cloning the repository)

---

### 1. Clone the Repository

If you haven't already, clone the project repository to your local machine:

```bash
git clone <repository_url> # Replace <repository_url> with the actual URL
cd <project_directory>   # Replace <project_directory> with the folder name
```

### 2. Install Dependencies

Install the project dependencies using your preferred package manager:

```bash
npm install
# or
yarn install
# or
pnpm install
# or
bun install
```

### 3. Configure Environment Variables

The application requires a MongoDB connection URI and a JWT secret for authentication. Create a `.env.local` file in the root of the project and add the following:

```env
MONGODB_URI="your_mongodb_connection_string"
MONGODB_DB="your_database_name" # Optional: defaults to 'DMS' if not set. Used by src/models/index.ts.
JWT_SECRET="your_strong_jwt_secret_key" # Crucial for security. Used by /api/auth/login.
```

- Replace `your_mongodb_connection_string` with the actual URI for your MongoDB database (e.g., `mongodb://localhost:27017/dms_dashboard` or a cloud provider string).
- Replace `your_database_name` with the name of your database if it's different from `DMS`.
- Replace `your_strong_jwt_secret_key` with a long, random, and secret string for signing JSON Web Tokens (JWTs). **The default value 'dms-dashboard-secret-key' found in the code is insecure and must not be used in production.**

**Note**:
- `src/lib/mongodb.ts` primarily uses `MONGODB_URI` to establish a database connection.
- `src/models/index.ts` uses `MONGODB_DB` to specify the database name.
- The login API route (`src/app/api/auth/login/route.ts`) uses `JWT_SECRET` for token generation.

### 4. Database Setup (If applicable)

- Ensure your MongoDB instance is running and accessible with the credentials provided in `MONGODB_URI`.
- The application interacts with several collections (e.g., `users`, `materials`, `measurements`, `sales`). If starting with an empty database:
    - The application may attempt to create collections as new data is added if Mongoose models are used for insertion.
    - However, for full functionality and to avoid relying on sample data fallbacks in API routes, you might need to manually populate essential data for collections such as `users` and `materials`.
    - **Regarding Database Seeding**: Code comments in some parts of the application mention a Python script named `repopulate_db.py` for database seeding. However, this script is **not provided** in the current project repository.
    - Therefore, developers starting with an empty database should:
        - Be prepared to create initial user accounts (e.g., an admin user). The `generatepass.js` script can be used to create password hashes for manual insertion into the `users` collection.
        - Add some initial materials to the `materials` collection.
        - Other collections like `measurements` and `sales` will be populated as data is generated through application usage.
- Without initial or correctly structured data (especially for `users` and `materials`), some dashboard features might have limited functionality or might default to sample data as coded in some API endpoints.

### 5. Run the Development Server

Start the Next.js development server:

```bash
npm run dev
# or
yarn dev
# or
pnpm dev
# or
bun dev
```

This will typically start the application on `http://localhost:3000`. Open this URL in your browser. The page auto-updates as you edit files.

### 6. Building for Production

To create an optimized production build:

```bash
npm run build
# or
yarn build
# or
pnpm build
# or
bun build
```

After building, you can start the production server:

```bash
npm run start
# or
yarn start
# or
pnpm start
# or
bun start
```

[← Back to Main Page](../index.md)

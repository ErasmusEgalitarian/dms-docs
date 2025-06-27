# Front-End

[← Back to Main Page](../../../index.md)

## Frontend Components

The frontend is built using Next.js (App Router) and React with TypeScript. Tailwind CSS is used for styling, and `react-chartjs-2` for data visualization.

### Main Layout (`src/app/layout.tsx`)

-   **Description**: This is the root layout for the entire application. It establishes the basic HTML structure (`<html>`, `<body>`).
-   **Functionality**:
    -   Applies global styles from `globals.css`.
    -   Configures and applies the Geist Sans and Geist Mono fonts using `next/font`.
    -   Sets default metadata (title, description) for the application, which can be overridden by individual pages.
    -   Wraps `children` components, meaning all pages will inherit this structure.

### Application Layout (`src/components/Layout.tsx`)

-   **Description**: Provides the main navigational structure (sidebar and header) for authenticated sections of the application, such as the main dashboard.
-   **Functionality**:
    -   Displays a sidebar navigation menu with links to different sections (e.g., "Dashboard", "Trabalhadores", "Materiais").
    -   Highlights the currently active path in the sidebar.
    -   Includes a header, typically for user information or actions like logout.
    -   Manages user authentication state:
        -   Checks for an `auth_token` in `localStorage` on the client-side.
        -   Redirects to `/login` if the token is missing.
        -   Provides a `handleLogout` function to clear the token and redirect to the login page.
    -   Wraps the main content of the authenticated pages (`children`).

### Dashboard Page (`src/app/page.tsx`)

-   **Description**: The main dashboard page displayed after successful user login. It provides an overview of various metrics related to material collection, sales, stock, and worker activity.
-   **Functionality**:
    -   **User Authentication & Welcome**: Retrieves user data from `localStorage` and potentially fetches more details from `/api/user`. Displays a welcome message.
    -   **Data Fetching**: Asynchronously fetches data from multiple API endpoints:
        -   `/api/materials` (for material filter options, mapping IDs to names)
        -   `/api/users` (for worker filter options)
        -   `/api/stock` (for current stock levels)
        -   `/api/earnings-comparison` (for historical earnings)
        -   `/api/worker-collections` (for materials collected by workers)
        -   `/api/price-fluctuation` (for material price history)
        -   `/api/birthdays` (for upcoming worker birthdays)
        -   Manages loading states while data is being fetched.
    -   **Filtering**: Provides dropdown filters that affect the data displayed in charts:
        -   **Material Filter**: For Stock, Earnings, Worker Collections, Price Fluctuation charts.
        -   **Worker Filter**: For the Worker Collections chart.
        -   **Period Filter**: For Earnings and Worker Collections charts (Weekly, Monthly, Yearly).
    -   **Statistics Cards**: Displays key summary statistics with icons (e.g., total materials, total workers, total stock, current month's earnings).
    -   **Charts (using `react-chartjs-2`)**:
        -   **Estoque Atual (Current Stock)**: Doughnut chart showing stock quantities per material.
        -   **Ganhos (Earnings)**: Line chart showing earnings over the selected period.
        -   **Coletas de Trabalhadores (Worker Collections)**: Horizontal Bar chart (or Stacked Bar for yearly overview) showing worker collection performance.
        -   **Flutuação de Preços (Price Fluctuation)**: Line chart showing price trends for selected materials or top materials.
        -   Each chart handles "no data" scenarios appropriately.
    -   **Birthdays Section**: Lists workers having birthdays in the current month.
    -   **Helper Functions**: Includes utility functions like `formatCurrency` and `formatWeight`.
    -   **State Management**: Uses React hooks (`useState`, `useEffect`, `useMemo`) for managing component state, data fetching logic, and memoizing calculations.

### Other Notable Components (Inferred)

-   The structure of `src/app/page.tsx` suggests that it internally manages distinct UI sections that could be (or are implicitly) sub-components, such as:
    -   Filter control group.
    -   Individual statistic display cards.
    -   Container components for each chart.
    -   List items for birthdays.
    While these might be part of the main page file, they represent logical component blocks.

[← Back to Main Page](../../../index.md)
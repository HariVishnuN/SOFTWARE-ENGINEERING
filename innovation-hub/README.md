# Rural Innovation Hub

Rural Innovation Hub is a digital platform for discovering, documenting, sharing, validating, and scaling affordable grassroots inventions. 

## Features
- **Innovation Discovery:** Search, filter, and discover rural innovations.
- **Roles and Permissions:** Profiles for Innovators, Students, Researchers, NGOs, Industry partners, and Admins.
- **Collaboration:** Request collaboration on scalable solutions.
- **Community:** Rate, comment, bookmark, and save items into collections.
- **Dashboard:** Role-aware dashboards for managing workflows.

## Technology Stack
- **Frontend:** React, Vite, TypeScript, React Router, Tailwind CSS, TanStack Query, Zustand, React Hook Form.
- **Backend:** Node.js, Express, TypeScript, Mongoose.
- **Database:** MongoDB.

## Getting Started

### Prerequisites
- Node.js v18+
- MongoDB instance (local or Atlas)

### Setup
1. Clone the repository and install dependencies from the root:
   ```bash
   npm install
   ```

2. Configure environment variables. Never commit this file or its values.
   - Copy `server/.env.example` to `server/.env`, then fill in only the values you need:
    ```env
    PORT=5001
    MONGODB_URI=mongodb://localhost:27017/rural-innovation-hub
    JWT_SECRET=use_a_long_random_value
    JWT_REFRESH_SECRET=use_a_different_long_random_value
    # Required for production. Comma-separate multiple approved frontend origins.
    CLIENT_ORIGIN=https://your-ruralhub-domain.example
    # Optional server-only news integration. Never prefix these with VITE_.
    NEWS_API_PROVIDER=gnews
    NEWS_API_BASE_URL=https://gnews.io/api/v4/search
    NEWS_API_KEY=
    ```

3. Run the development servers (frontend and backend concurrently):
   ```bash
   npm run dev
   ```

4. The client starts on `http://localhost:3000` when that port is available and proxies API calls to the server at `http://localhost:5001`.

### Deterministic development data

Run the seed after MongoDB is available:

```bash
npm run seed -w server
```

It upserts six clearly fictional `@example.test` development users and 60 stable innovation records across states, sectors, and statuses. The development-only password for those users is `DevOnly123!`; never use it outside a local database. `npm run seed:reset -w server` deletes only those test users and seeded innovations before rebuilding them.

### News provider decision

The backend implements a GNews adapter behind `/api/v1/news`; the browser never receives `NEWS_API_KEY`. It caches requests for 15 minutes and supplies visibly labelled fictional fallback articles when a key is missing or the provider fails. GNews’ current free tier is intended for development/testing, delayed, and rate-limited, so configure an appropriate paid/approved plan before a public commercial launch. See [GNews pricing](https://gnews.io/pricing) and [rate-limit guidance](https://docs.gnews.io/error-handling).

## Deployment and asset notes

- The application uses local, versioned image assets under `client/public/images/`. They are served with stable `/images/...` paths, reserve dimensions in cards, and use lazy loading below the fold.
- Verify that these project-owned assets have the appropriate usage rights before public release. No external image URLs are required for the core feed, discovery, widgets, or authentication screens.
- Set `NODE_ENV=production`, `JWT_SECRET`, `JWT_REFRESH_SECRET`, `MONGODB_URI`, and `CLIENT_ORIGIN` in the deployment environment. The server refuses to start in production without both JWT secrets.
- Build the project with `npm run build`. Serve the generated client bundle through a host configured to return `index.html` for application routes.
- Restrict `CLIENT_ORIGIN` in production, set secrets through the deployment host, and run the health check at `/api/v1/health` after deployment. The API uses Helmet, explicit CORS, HTTP-only refresh cookies, server-side provider keys, and no stack traces in responses.
- The static India selector lives in `client/src/data/indiaLocations.ts`. Refresh it from the iGOD administrative listing before a production release because district boundaries and names change over time.

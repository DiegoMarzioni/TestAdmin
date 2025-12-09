# Backend Deployment Guide - Render

## Prerequisites

1. PostgreSQL database on Render
2. Node.js environment

## Deployment Steps for Render

### 1. Create PostgreSQL Database

1. Go to Render Dashboard
2. Click "New" → "PostgreSQL"
3. Configure:
   - Name: `testadmin-db`
   - Database: `testadmin`
   - User: (auto-generated)
   - Region: Choose closest to you
4. Copy the **Internal Database URL** (starts with `postgresql://`)

### 2. Deploy Backend

1. Go to Render Dashboard
2. Click "New" → "Web Service"
3. Connect your GitHub repository
4. Configure:
   - **Name**: `testadmin-backend`
   - **Region**: Same as database
   - **Branch**: `main`
   - **Root Directory**: `back`
   - **Environment**: `Node`
   - **Build Command**: `npm install && npx prisma generate && npx prisma migrate deploy && npm run build`
   - **Start Command**: `npm run start:prod`

### 3. Environment Variables

Add these environment variables in Render:

```
DATABASE_URL=<Your PostgreSQL Internal Database URL>
JWT_SECRET=<Generate a strong random string>
JWT_EXPIRES_IN=24h
NODE_ENV=production
PORT=3001
FRONTEND_URL=<Your frontend URL when deployed>
```

### 4. Initialize Database

After deployment:

1. Go to your web service Shell tab
2. Run: `npm run db:seed`

## Local Development with PostgreSQL

1. Install PostgreSQL locally or use Docker:
   ```bash
   docker run --name testadmin-postgres -e POSTGRES_PASSWORD=postgres -e POSTGRES_DB=testadmin -p 5432:5432 -d postgres:15
   ```

2. Update `.env`:
   ```
   DATABASE_URL="postgresql://postgres:postgres@localhost:5432/testadmin?schema=public"
   ```

3. Install dependencies:
   ```bash
   npm install
   ```

4. Generate Prisma client:
   ```bash
   npx prisma generate
   ```

5. Create initial migration:
   ```bash
   npx prisma migrate dev --name init
   ```

6. Seed database:
   ```bash
   npm run db:seed
   ```

7. Run development server:
   ```bash
   npm run start:dev
   ```

## Useful Commands

- `npx prisma studio` - Open Prisma Studio to view/edit data
- `npx prisma migrate dev` - Create new migration
- `npx prisma migrate deploy` - Apply migrations (production)
- `npm run db:seed` - Seed database with initial data

## Troubleshooting

### Migration Issues
If migrations fail, you can reset the database:
```bash
npx prisma migrate reset
npm run db:seed
```

### Connection Issues
Verify your DATABASE_URL format:
```
postgresql://USER:PASSWORD@HOST:PORT/DATABASE?schema=SCHEMA
```

For Render internal database:
```
postgresql://testadmin_db_user:password@dpg-xxxx.oregon-postgres.render.com/testadmin_db
```

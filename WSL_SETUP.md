# SurfSense WSL Setup Guide

This guide provides instructions for setting up and running the SurfSense project on the Windows Subsystem for Linux (WSL).

## 1. Prerequisites

- **WSL Installed:** Ensure you have a WSL distribution (like Ubuntu) installed and running on your Windows machine.
- **Node.js & npm:** Install Node.js (which includes npm) in your WSL distribution. We recommend using nvm (Node Version Manager) to manage Node.js versions.
- **Python:** Install Python 3.11 or later in your WSL distribution.
- **PostgreSQL:** Install PostgreSQL in your WSL distribution.

## 2. Backend Setup

1.  **Navigate to the Backend Directory:**
    ```bash
    cd surfsense_backend
    ```

2.  **Create and Activate a Virtual Environment:**
    ```bash
    python3 -m venv venv
    source venv/bin/activate
    ```

3.  **Install Dependencies:**

    This project uses `pyproject.toml` to manage its Python dependencies. To install them, you'll first need to install `pip-tools`, which will allow you to compile a `requirements.txt` file.

    - **Install pip-tools:**
      ```bash
      pip install pip-tools
      ```

    - **Generate `requirements.txt`:**
      ```bash
      pip-compile pyproject.toml -o requirements.txt
      ```

    - **Install the dependencies from the newly created file:**
      ```bash
      pip install -r requirements.txt
      ```

4.  **Set Up the Database:**
    - Start the PostgreSQL service:
      ```bash
      sudo service postgresql start
      ```
    - Create the database and user. You can use the following commands, replacing the credentials as needed:
      ```sql
      CREATE DATABASE surfsense;
      CREATE USER postgres WITH PASSWORD 'postgres';
      ALTER ROLE postgres SET client_encoding TO 'utf8';
      ALTER ROLE postgres SET default_transaction_isolation TO 'read committed';
      ALTER ROLE postgres SET timezone TO 'UTC';
      GRANT ALL PRIVILEGES ON DATABASE surfsense TO postgres;
      ```

5.  **Configure Environment Variables:**
    - Copy the example environment file:
      ```bash
      cp .env.example .env
      ```
    - Edit the `.env` file and set the `DATABASE_URL` to your local PostgreSQL instance:
      ```
      DATABASE_URL=postgresql+asyncpg://postgres:postgres@localhost:5432/surfsense
      ```
    - Fill in any other required API keys or secrets in the `.env` file.

6.  **Run Database Migrations:**
    ```bash
    alembic upgrade head
    ```

7.  **Start the Backend Server:**
    ```bash
    uvicorn main:app --reload
    ```
    The backend should now be running on `http://localhost:8000`.

## 3. Frontend Setup

1.  **Navigate to the Frontend Directory:**
    ```bash
    cd surfsense_web
    ```

2.  **Install Dependencies:**
    ```bash
    npm install
    ```

3.  **Configure Environment Variables:**
    - Copy the example environment file:
      ```bash
      cp .env.example .env.local
      ```
    - The default `NEXT_PUBLIC_FASTAPI_BACKEND_URL` should already be set to `http://localhost:8000`, which is correct for this setup.

4.  **Start the Frontend Development Server:**
    ```bash
    npm run dev
    ```
    The frontend should now be running on `http://localhost:3001`.

## 4. Running the Application

Once both the backend and frontend servers are running, you can access the SurfSense application by opening your web browser and navigating to `http://localhost:3001`.

This guide should help you get the project running on WSL. If you encounter any issues, please refer to the specific error messages or the documentation for the tools involved.
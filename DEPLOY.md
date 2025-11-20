# Deployment Guide

This guide explains how to deploy the SynergySphere application.

## Prerequisites

- **Google Cloud Platform (GCP) Account**: You need a GCP project with billing enabled.
- **Netlify Account**: For frontend deployment.
- **gcloud CLI**: Installed and authenticated (`gcloud auth login`).
- **Node.js & npm**: Installed.

## 1. Backend Deployment (Google Cloud Run)

The backend is deployed as a containerized application on Google Cloud Run.

### Setup

1.  **Login to Google Cloud**:
    ```bash
    gcloud auth login
    gcloud config set project [YOUR_PROJECT_ID]
    ```

2.  **Enable Services**:
    Ensure the following APIs are enabled in your GCP project:
    - Cloud Run API
    - Cloud Build API
    - Container Registry API

3.  **Environment Variables**:
    You will need to set the following environment variables in Cloud Run after the initial deployment (or add them to the `deploy.sh` script):
    - `SQLALCHEMY_DATABASE_URI`: Connection string for your PostgreSQL database.
    - `REDIS_URL`: Connection string for your Redis instance.
    - `CLOUDINARY_CLOUD_NAME`, `CLOUDINARY_API_KEY`, `CLOUDINARY_API_SECRET`: Cloudinary credentials.
    - `MAIL_USERNAME`, `MAIL_PASSWORD`: Email credentials.
    - `FRONTEND_URL`: The URL of your deployed frontend (Netlify URL).

### Deploy

1.  Navigate to the `backend` directory:
    ```bash
    cd backend
    ```

2.  Make the script executable:
    ```bash
    chmod +x deploy.sh
    ```

3.  Run the deployment script:
    ```bash
    ./deploy.sh
    ```

4.  **Note**: The first deployment might fail if environment variables are missing. Go to the Google Cloud Console -> Cloud Run -> [Service Name] -> Edit & Deploy New Revision -> Variables & Secrets, and add the required variables.

## 2. Frontend Deployment (Netlify)

The frontend is deployed to Netlify.

### Deploy via Netlify CLI (Recommended)

1.  Install Netlify CLI:
    ```bash
    npm install -g netlify-cli
    ```

2.  Login to Netlify:
    ```bash
    netlify login
    ```

3.  Deploy from the root directory:
    ```bash
    netlify deploy --prod
    ```
    - **Build Command**: `npm run build`
    - **Publish Directory**: `frontend/dist`

### Deploy via Git (Continuous Deployment)

1.  Push your code to a Git repository (GitHub, GitLab, Bitbucket).
2.  Log in to Netlify and click "New site from Git".
3.  Select your repository.
4.  Netlify should automatically detect the settings from `netlify.toml`.
    - If not, ensure:
        - **Base directory**: `frontend`
        - **Build command**: `npm run build`
        - **Publish directory**: `dist`

## 3. Post-Deployment Configuration

1.  **Update Backend CORS**:
    - Once the frontend is deployed, get the Netlify URL (e.g., `https://your-site.netlify.app`).
    - Update the `FRONTEND_URL` environment variable in your Cloud Run service with this URL.

2.  **Update Frontend API URL**:
    - Get the Cloud Run URL (e.g., `https://synergysphere-backend-xyz.a.run.app`).
    - Update your frontend configuration (usually in `.env` or a config file) to point to this backend URL.

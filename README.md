# Backend Flask Project with Nginx Reverse Proxy (Docker)

---

## Backend Flask Project

### Process (Initial Database Setup)

Step 1:
-->> Create an AWS RDS MySQL instance  
-->> Configure the security group:
- Allow port 3306
- Allow inbound traffic from the application server  

-->> Connect to the RDS instance using MySQL Workbench with RDS credentials  
-->> Create the database and tables using Workbench  

---

## Python Backend Testing (Flask)

A small Flask backend used for testing MySQL / AWS RDS connectivity and basic CRUD operations on a `users` table.

---

## Overview

This project contains a minimal Flask application (`app.py`) that connects to a MySQL-compatible database (local or AWS RDS) and exposes REST APIs to manage users.

---

## Prerequisites

- Python 3.8 or newer
- pip
- MySQL / MariaDB or AWS RDS
- Docker
- Git (optional)

---

## Database Setup (Example)

  ```bash
    CREATE DATABASE dev;
    USE dev;

    CREATE TABLE users (
      id INT AUTO_INCREMENT PRIMARY KEY,
      name VARCHAR(100) NOT NULL,
      email VARCHAR(100) NOT NULL UNIQUE
    );
```
---

## Environment Configuration

Edit `app.py` and update database credentials  
OR export environment variables before running:
```bash
    DB_HOST=your-db-host
    DB_USER=your-db-user
    DB_PASS=your-db-password
    DB_NAME=dev
```
---

## Run Flask App (Local)

    python app.py

The application runs on port 5000 by default:

    http://127.0.0.1:5000

---

## API Endpoints

- GET /users  
  Returns all users  

- GET /users/<id>  
  Returns user by ID  

- POST /users/add  
  Adds a new user  
  JSON Body:
    {"name":"John","email":"john@example.com"}

- PUT /users/update/<id>  
  Updates a user  

- DELETE /users/delete/<id>  
  Deletes a user  

---

## API Examples

    curl -X GET http://localhost:5000/users

    curl -X POST http://localhost:5000/users/add \
      -H "Content-Type: application/json" \
      -d '{"name":"John Doe","email":"john@example.com"}'

    curl -X PUT http://localhost:5000/users/update/1 \
      -H "Content-Type: application/json" \
      -d '{"name":"John Updated","email":"john.updated@example.com"}'

    curl -X DELETE http://localhost:5000/users/delete/1

---

## Run Flask in Background (Linux)

    nohup python3 app.py > flask.log 2>&1 &
    ps aux | grep app.py
    pkill -f app.py

---

## Project Files

- app.py — Main Flask application
- requirements.txt — Python dependencies
- test.sql — Example SQL (if available)

---

## Nginx Reverse Proxy with Docker

This setup uses Nginx inside the frontend container as a reverse proxy to route `/users` requests to the Flask backend.

Architecture:

    Browser
       |
       v
    Nginx (Frontend Container)
       |---- /        -> Static Frontend (HTML)
       |---- /users   -> Flask Backend (Docker)

Only port 80 is exposed publicly.

---

## Docker Build & Run (No Compose)

### Build and Run Backend

    docker build -t backend ./backend
    docker run -d --name backend backend

Backend runs internally on port 5000.

---

### Build and Run Frontend (Nginx + Reverse Proxy)

    docker build -t frontend ./frontend
    docker run -d -p 80:80 --name frontend --link backend:backend frontend

---

## Access Application

Open in browser:

    http://<EC2_PUBLIC_IP>

Frontend loads on `/`  
API works on `/users`

---

## Notes

- Do NOT expose port 5000 publicly
- Security Group should allow:
  - HTTP (80)
  - MySQL (3306) only from app server
- This setup avoids CORS issues
- Clean production-style deployment using minimal Docker commands

---

## Repository Structure

    backend/
      app.py
      Dockerfile
      requirements.txt

    frontend/
      index.html
      nginx.conf
      Dockerfile

    README.md

---

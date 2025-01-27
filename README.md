# Soccer Stats API with Docker 🚀⚽

Welcome! If you’ve been trying to understand Docker containers but find most tutorials confusing, you're in the right place. We’re going to build something real and practical—a **containerized soccer statistics API**—that will teach you Docker and API development in a beginner-friendly way.

---

## What We’re Building (And Why It Matters)

We’ll create a **containerized soccer statistics API** that:
- Fetches real-time data
- Processes it efficiently
- Runs consistently everywhere

Here’s what you’ll learn along the way:
- **Docker containerization** that actually makes sense
- **FastAPI** for building APIs
- **Environment secrets management** (keeping your API keys safe!)
- Writing **clean, well-structured code**

---

## Getting Started: Setting Up the Project

First, let’s set up our project directory. Run these commands in your terminal:

```bash
mkdir soccer-stats-docker
cd soccer-stats-docker
mkdir src tests
touch src/__init__.py src/soccer_stats.py
touch Dockerfile requirements.txt README.md .env
Project Structure
Here’s what our project looks like:
soccer-stats-docker/
│
├── src/
│   ├── __init__.py
│   └── soccer_stats.py
├── tests/
├── Dockerfile
├── requirements.txt
├── README.md
└── .env

Step 1: Dependencies (Add These to requirements.txt)

fastapi==0.68.1        # Web framework
uvicorn==0.15.0        # ASGI server for FastAPI
requests==2.26.0       # For API calls
python-dotenv==0.19.0  # Secrets management
pytest==6.2.5          # Testing framework

Step 2: Secrets Management (Environment Variables)
Create a .env file and add your API key:
RAPID_API_KEY=your_api_key_here
Important: Add .env to your .gitignore file to avoid accidentally sharing your API key on GitHub.

Step 3: Build the API (Code for src/soccer_stats.py)
Here’s how the code is structured:

Import Dependencies and Initialize FastAPI

import os
import requests
from fastapi import FastAPI, HTTPException
from dotenv import load_dotenv
from datetime import datetime

# Load environment variables
load_dotenv()

# Initialize FastAPI
app = FastAPI(title="Soccer Stats API")

Core Class for Soccer Stats

class SoccerStats:
    def __init__(self):
        self.api_key = os.getenv('RAPID_API_KEY')
        if not self.api_key:
            raise ValueError("RAPID_API_KEY environment variable is not set")
        self.base_url = "https://api-football-v1.p.rapidapi.com/v3"
        self.headers = {
            "X-RapidAPI-Key": self.api_key,
            "X-RapidAPI-Host": "api-football-v1.p.rapidapi.com"
        }

Endpoints
Add these FastAPI routes:


@app.get("/")
async def root():
    return {
        "message": "Welcome to Soccer Stats API!",
        "version": "1.0",
        "endpoints": [
            "/player/{player_id} - Get player statistics",
            "/topscorers/{league_id} - Get top scorers for a league"
        ]
    }

@app.get("/health")
async def health_check():
    return {"status": "healthy", "timestamp": datetime.now().isoformat()}

Player Stats and Top Scorers

Methods to fetch player stats:

async def get_player_stats(self, player_id: int, season: int = 2023):
    url = f"{self.base_url}/players"
    params = {"id": player_id, "season": season}
    response = requests.get(url, headers=self.headers, params=params)
    if response.status_code != 200:
        raise HTTPException(status_code=response.status_code, detail=f"API Error: {response.text}")
    return response.json()

async def get_top_scorers(self, league_id: int = 39, season: int = 2023):
    url = f"{self.base_url}/players/topscorers"
    params = {"league": league_id, "season": season}
    response = requests.get(url, headers=self.headers, params=params)
    if response.status_code != 200:
        raise HTTPException(status_code=response.status_code, detail=f"API Error: {response.text}")
    return response.json()


Step 4: Containerization (Dockerfile)
Here’s the Dockerfile explained:

# Use Python 3.9 slim base image
FROM python:3.9-slim

# Set working directory
WORKDIR /app

# Copy dependencies first
COPY requirements.txt .

# Install dependencies
RUN pip install --no-cache-dir -r requirements.txt

# Copy the rest of the app
COPY . .

# Expose port 8000
EXPOSE 8000

# Run the app
CMD ["uvicorn", "src.soccer_stats:app", "--host", "0.0.0.0", "--port", "8000"]

Running Locally
Install dependencies:

pip install -r requirements.txt
Run the API:
uvicorn src.soccer_stats:app --reload
Running in Docker
Build the image:
docker build -t soccer-stats .
Run the container:
docker run -p 8000:8000 --env-file .env soccer-stats
Cleaning Up
When you’re done, clean up your Docker resources:

docker stop $(docker ps -q --filter ancestor=soccer-stats)
docker rm $(docker ps -aq --filter ancestor=soccer-stats)
docker rmi soccer-stats

What You’ve Learned
Docker Basics: How to containerize your app
FastAPI Integration: Build real-world APIs
Environment Variables: Keep secrets safe
Clean Code: Structure and organization
Resource Management: Responsible Docker usage
Enjoy your containerized soccer stats API! 🎉
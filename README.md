# py-fastapi-firestore
### Project Structure
``` 
my_fastapi_app/
│
├── app/
│   ├── __init__.py
│   ├── main.py
│   ├── api/
│   │   ├── __init__.py
│   │   ├── endpoints/
│   │   │   ├── __init__.py
│   │   │   ├── user.py
│   │   │   └── item.py
│   │   ├── dependencies/
│   │   │   ├── __init__.py
│   │   │   └── auth.py
│   ├── core/
│   │   ├── __init__.py
│   │   ├── config.py
│   │   └── security.py
│   ├── models/
│   │   ├── __init__.py
│   │   ├── user.py
│   │   └── item.py
│   ├── schemas/
│   │   ├── __init__.py
│   │   ├── user.py
│   │   └── item.py
│   ├── services/
│   │   ├── __init__.py
│   │   ├── user_service.py
│   │   └── item_service.py
│   └── firebase/
│       ├── __init__.py
│       ├── firebase_config.py
│       └── firebase_service.py
│
├── tests/
│   ├── __init__.py
│   ├── test_user.py
│   └── test_item.py
│
├── requirements.txt
├── .env
└── README.md
```
**Create a Virtual Environment**:
``` bash
python -m venv venv
source venv/bin/activate  # On Windows use `venv\Scripts\activate`
```
**Install FastAPI and Firebase Dependencies**:
```bash
pip install fastapi uvicorn python-dotenv firebase-admin
```
**Create `requirements.txt`**:
```
# requirements.txt 

fastapi
uvicorn
python-dotenv
firebase-admin
```
**Environment Variables**: Create a `.env` file for sensitive information
```
FIREBASE_CONFIG = <your_firebase_config_json>
```
**Core Configuration**: In `app/core/config.py`, load environment variables:
```py
import os
from dotenv import load_dotenv

load_dotenv()

FIREBASE_CONFIG = os.getenv("FIREBASE_CONFIG")
```
**Firebase Setup**: In `app/firebase/firebase_config.py`, set up Firebase Admin SDK:
```py
import json
import firebase_admin
from firebase_admin import credentials

from app.core.config import FIREBASE_CONFIG

cred = credentials.Certificate(json.loads(FIREBASE_CONFIG))
firebase_admin.initialize_app(cred)
```
**Create Models**: Define your data models in `app/models/`. For example, `user.py` might look like:
```
from pydantic import BaseModel

class User(BaseModel):
    id: str
    name: str
    email: str
```
**Define Schemas**: Create Pydantic schemas for data validation in `app/schemas/`.

**Create Services**: Implement business logic in the `app/services/` folder. For example, `user_service.py` can handle user-related functions.

**Set Up API Endpoints**: In `app/api/endpoints/user.py`, define your API endpoints:
```py
from fastapi import APIRouter
from app.schemas.user import User
from app.services.user_service import create_user

router = APIRouter()

@router.post("/users/", response_model=User)
async def register_user(user: User):
    return await create_user(user)
```
**Main Application File**: In `app/main.py`, include routers:
```py
from fastapi import FastAPI
from app.api.endpoints import user

app = FastAPI()

app.include_router(user.router, prefix="/api/v1")
```
**Testing**: Set up tests in the `tests/` folder using `pytest`:
```py
import pytest
from fastapi.testclient import TestClient
from app.main import app

client = TestClient(app)

def test_register_user():
    response = client.post("/api/v1/users/", json={"name": "John", "email": "john@example.com"})
    assert response.status_code == 200
```
**Running the Application**: Run your FastAPI app using Uvicorn:
```bash
uvicorn app.main:app --reload
```

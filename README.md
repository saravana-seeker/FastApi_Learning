# FastApi_Learning
FastAPI Learning 
https://fastapi.tiangolo.com/

## Intro


## Installation 
```
pip install fastapi
pip install uvicorn
pip install python-multipart
```
## Create Simple App
```py

from fastapi import FastAPI
app = FastAPI()

@app.get("/")
async def index():
    return {"message": "Hello world"}

@app.post("/user")
async def user():
    return {"user":"user"}

```
## Run on Server
```
uvicorn main:app --reload
```


## Default Documentation
http://127.0.0.1:8000/docs#/


## Url Path
```py

from fastapi import FastAPI
app = FastAPI()
student = {
    1:{
        "Name":"saravana",
        "Age":"22",
        "Class":"ECE"
    },
    2:{
        "Name":"santhosh",
        "Age":"20",
        "Class":"CSC"
    }
}

@app.get("/")
async def index():
    return {"message": "Hello world"}
#{student_id} --> url path /student/1
@app.get("/students/{student_id}")
async def get_student(student_id:int):
    return student[student_id]
    
#/user
@app.post("/user")
async def user():
    return {"user":"user"}
```
## URL OPtional Path 
```py
from typing import Union

from fastapi import FastAPI

app = FastAPI()


@app.get("/items/{item_id}")
async def read_item(item_id: str, q: Union[str, None] = None):
    if q:
        return {"item_id": item_id, "q": q}
    return {"item_id": item_id}

```

## Url Query
```py
from fastapi import FastAPI

app = FastAPI()

fake_items_db = [{"item_name": "Foo"}, {"item_name": "Bar"}, {"item_name": "Baz"}]


@app.get("/items/")
async def read_item(skip: int = 0, limit: int = 10):
    return fake_items_db[skip : skip + limit]
#out -> http://127.0.0.1:8000/items/?skip=0&limit=10

```

## Pydantic Module
https://pydantic-docs.helpmanual.io/

### Intro
Data validation and settings management using python type annotations.
pydantic enforces type hints at runtime, and provides user friendly errors when data is invalid.
Define how data should be in pure, canonical python; validate it with pydantic.

```py
from datetime import datetime
from typing import List, Optional
from pydantic import BaseModel


class User(BaseModel):
    id: int
    name = 'John Doe'
    signup_ts: Optional[datetime] = None
    friends: List[int] = []


external_data = {
    'id': '123',
    'signup_ts': '2019-06-01 12:22',
    'friends': [1, 2, '3'],
}
user = User(**external_data)
print(user.id)
#> 123
print(repr(user.signup_ts))
#> datetime.datetime(2019, 6, 1, 12, 22)
print(user.friends)
#> [1, 2, 3]
print(user.dict())
"""
{
    'id': 123,
    'signup_ts': datetime.datetime(2019, 6, 1, 12, 22),
    'friends': [1, 2, 3],
    'name': 'John Doe',
}
"""

```
## POST body
https://fastapi.tiangolo.com/tutorial/body/
```py
from typing import Union

from fastapi import FastAPI
from pydantic import BaseModel


class Item(BaseModel):
    name: str
    description: Union[str, None] = None
    price: float
    tax: Union[float, None] = None


app = FastAPI()


@app.post("/items/")
async def create_item(item: Item):
    return item

```

## File Upload
```py

@app.post("/upload")
async def upload(file: UploadFile = File(...)):
    try:
        contents = await file.read()
        with open(file.filename, 'wb') as f:
            f.write(contents)
    except Exception:
        return {"message": "There was an error uploading the file"}
    finally:
        await file.close()  
    return {"message": f"Successfuly uploaded {file.filename}"}

```


## Response Model
https://fastapi.tiangolo.com/tutorial/response-model/
```py
from typing import List, Union

from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()


class Item(BaseModel):
    name: str
    description: Union[str, None] = None
    price: float
    tax: Union[float, None] = None
    tags: List[str] = []


@app.post("/items/", response_model=Item)
async def create_item(item: Item):
    return item

```

## Background Jobs

```py
from fastapi import BackgroundTasks, FastAPI
app = FastAPI()

def write_notification(email: str, message=""):
    with open("log.txt", mode="w") as email_file:
        content = f"notification for {email}: {message}"
        email_file.write(content)

@app.post("/send-notification/{email}")
async def send_notification(email: str, background_tasks: BackgroundTasks):
    background_tasks.add_task(write_notification, email, message="some notification")
    return {"message": "Notification sent in the background"}

```

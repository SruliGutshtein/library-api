# **LIBRARY API**

A management system for books and library members built on a FASTAPI server with a MYSQL database
---
---

## how to run MySql on docker
### The code for creating and run a docker container with MySql
```
docker run --name mysql
    -e MYSQL_ROOT_PASSWORD = <your password>
    -e MYSQL_DATABASE = library_db
    -p 3306:3306
    -d mysql:8
```
### to run the container if it exist
```
docker exec -it mysql mysql -u root -p
```

---

---
## **Folder structure**

```
library-api/  
│  
├── app/  
│   ├── main.py  
│   ├── database/  
│   │   ├── db_connection.py  
│   │   ├── book_db.py  
│   │   └── member_db.py  
│   ├── routes/  
│   │   ├── book_routes.py  
│   │   ├── member_routes.py  
│   │   └── report_routes.py  
│   └── logs/  
│       └── app.log  
│  
├── README.md  
└── requirements.txt   
```
- `app/` - where the web app is running 
- `database/` - where all the data is stored
- `routes/` - the end point for the server
- `logs/` - logging
#


---
## **Table structure**


### `books` Table

| Field | Type | Attributes | Description |
| :--- | :--- | :--- | :--- |
| `id` | INT | PK, AUTO_INCREMENT | מזהה ייחודי של הספר |
| `title` | VARCHAR(50) | NOT NULL | שם הספר |
| `author` | VARCHAR(50) | NOT NULL | שם הסופר |
| `genre` | ENUM(Fiction, Non-Fiction, Science, History, Other) | NOT NULL | ז'אנר הספר|
| `is_available` | BOOLEAN | DEFAULT TRUE, NOT NULL | האם הספר זמין להשאלה |
| `borrowed_by_member_id` | INT | DEFAULT null | מזהה החבר שמחזיק את הספר |


### `members` Table

| Field | Type | Attributes | Description |
| :--- | :--- | :--- | :--- |
| `id` | INT | PK, AUTO_INCREMENT | מזהה ייחודי של הספר |
| `name` | VARCHAR(50) | NOT NULL | שם החבר |
| `email` | VARCHAR(50) | NOT NULL | כתובת מייל  |
| `is_active` | BOOLEAN | NOT NULL | האם החבר פעיל |
| `borrows_total` | INT | NOT NULL, DEFAULT 0 | מונה סה"כ השאלות |


## Rules

| Rule | Description |
| :--- | :--- |
| `creating new book` | member sends title/author/genre and the system adds is_available=True, borrowed_by=NULL |
| `genre` | most be one of (Fiction, Non-Fiction, Science, History, Other) else the system send an error |
| `create new member` | user sends name/email and the system adds active_is =True,
total_borrows=0 |
| `email` | most be unique else the system send an error |
| `inactive member` | if is_active=False can not borrow a book |
| `inactive book` | if is_active=False can not borrow this book |
| `max books` | a member can not borrow more then 3 books |
| `returning a book` | each book can be returned only by the member who borrowed it |

#
---
## Endpoints

### Books
| Method | Endpoint | description |
| :--- | :--- | :--- |
| `POST` | /books | create new book |
| `GET` | /books | all books |
| `GET` | /books/{id} | book by id |
| `PHTCH` | /books/{id} | update book |
| `PHTCH` | /books/{id}/borrow/{member_id} | borrowing a book to member |
| `PHTCH` | /books/{id}/return/{member_id} | returning a book by a member |

### Members
| Method | Endpoint | description |
| :--- | :--- | :--- |
| `POST` | /members | create new member |
| `GET` | /members | all members |
| `GET` | /members/{id} | member by id |
| `PHTCH` | /members/{id} | update member |
| `PHTCH` | /members/{id}/deactivate | deactivate a member |
| `PHTCH` | /members/{id}/activate | activate a member |

### Reports
| Method | Endpoint | description |
| :--- | :--- | :--- |
| `GET` | /reports/summary | general reports |
| `GET` | /reports/books-by-genre | books by genre |
| `GET` | /reports/top-member | top active member |

#
---
## System flow

The client sends HTTP requests to the FastApi server and each endpoint redirects to the appropriate function that creates or gets data from the MYSQL database.

## how to run

* create a virtual environment
```
# on windows 
python3 -m venv .venv
./.venv/Scripts/activate
```
```
# on mac
python3 -m venv .venv
source .venv/bin/activate
```

* install requirements
```
pip install -r requirements.txt
```

* run `python main.py` to activate the server 
```
cd <where the project is>/Library-Api
python main.py
```

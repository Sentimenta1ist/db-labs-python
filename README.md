# Database Labs: Social Network on PostgreSQL

Three labs for a database course around one schema, a small social network (users, groups, friends, walls, posts), implemented in PostgreSQL with a console Python client.

Year: 2020 · Stack: PostgreSQL 12, Python 3.7, psycopg2 · Context: university coursework (KPI), course "Databases and DBMS"

## What's inside

- `lab1/` - database design. `my_script.sql` is a pg_dump of the schema: tables `users`, `groups`, `friends`, `walls`, `posts` and the many-to-many `users_groups_rel`, with identity primary keys and foreign keys (users are referenced by phone number). `structure*.png` are the ER/logical model diagrams.
- `LAB2/` - a console CRUD application in an MVC layout: `model.py` (database access via psycopg2), `view.py` (table printer), `controller.py` (menu loop), `config.py` + `config.ini` (connection settings). Menu: list tables, read, insert, delete, update, three predefined join searches, and fill a table with random rows.
- `lab3/` - the same MVC code with a different random fill (users instead of groups). The topic of lab 3 was DBMS optimization: moving the model to an ORM (SQLAlchemy), comparing index types and writing a trigger. That ORM/index/trigger code is not in the repository.

## Interesting bits

- `LAB2/model.py`: random test data is generated inside PostgreSQL, not in Python. `gen_string_req` builds `chr(trunc(65+random()*25)::int)||...` expressions, `gen_date` a random timestamp, and `insert` passes values prefixed with `!` through unquoted so they run as SQL expressions. `fill_random` inserts N rows in one multi-row `INSERT`.
- `LAB2/model.py`: `get_request` commits on success and rolls back on any psycopg2 error, so a bad query does not leave a broken transaction.
- `LAB2/model.py`: `search` has three multi-table join queries (posts on a user's wall filtered by phone, date and text; users in a group filtered by date and phone prefix; post likes by title). Their filter values are hardcoded in `controller.py` and `model.py`; the interactive input lines are commented out.
- `LAB2/view.py`: `printer` formats query results as a width-aligned text table, truncating columns at 40 characters.
- Queries are built with string formatting, so the code is open to SQL injection; this was a coursework prototype for a local database.

## Run

Not re-tested here (needs a local PostgreSQL and psycopg2, which are not installed in this environment). Based on the project files:

```
psql -U postgres -c "CREATE DATABASE social_network"
psql -U postgres -d social_network -f lab1/my_script.sql
pip install psycopg2-binary
cd LAB2 && python controller.py
```

Set the real password in `config.ini` first (it is `CHANGE_ME` in this repository).

## Note

Written in 2020, before AI coding assistants. Kept as-is for history.

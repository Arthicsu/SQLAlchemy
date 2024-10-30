```python
pip install sqlalchemy
```

# Код без ORM:
```python
from sqlalchemy import create_engine, text

# Создание базы данных
engine = create_engine("sqlite:///database.db", echo=True)

# Создание таблицы
with engine.connect() as connection:
    connection.execute(text('''
    CREATE TABLE IF NOT EXISTS users (
        id INTEGER PRIMARY KEY,
        name TEXT
    )
    '''))
    
    # Вставка нового пользователя
    connection.execute(text('INSERT INTO users (id, name) VALUES (:id, :name)'), {"id": 1, "name": "Arthicsu"})

    # Чтение всех пользователей
    result = connection.execute(text('SELECT * FROM users'))
    users = result.fetchall()
    for user in users:
        print(f"User ID: {user.id}, Name: {user.name}")

    # Обновление имени
    connection.execute(text('UPDATE users SET name = :name WHERE id = :id'), {"name": "Nikita", "id": 1})

    # Удаление
    connection.execute(text('DELETE FROM users WHERE id = :id'), {"id": 1})
```
# Код с ORM:
```python
from sqlalchemy import create_engine, Column, Integer, String
from sqlalchemy.orm import declarative_base, sessionmaker

# Создание базы данных
engine = create_engine("sqlite+pysqlite:///database.db", echo=True, future=True)

# Определение базового класса
Base = declarative_base()

# Определение модели
class User(Base):
    __tablename__ = 'users'

    id = Column(Integer, primary_key=True)
    name = Column(String)


# Создание таблицы
Base.metadata.create_all(engine)

##Вставка (INSERT)
# Создание сессии
Session = sessionmaker(bind=engine)
session = Session()

# Вставка нового пользователя
new_user = User(id=1, name="Arthicsu")
session.add(new_user)
session.commit()

##Чтение (SELECT)
users = session.query(User).all()
for user in users:
    print(f"User ID: {user.id}, Name: {user.name}")

## Обновление (UPDATE)
# Обновление имени пользователя
user_to_update = session.query(User).filter_by(id=1).first()
if user_to_update:
    user_to_update.name = "Nikita"
    session.commit()
## Удаление (DELETE)
# Удаление пользователя
user_to_delete = session.query(User).filter_by(id=1).first()
if user_to_delete:
    session.delete(user_to_delete)
    session.commit()

# Вывод оставшихся пользователей
users = session.query(User).all()
for user in users:
    print(f"User ID: {user.id}, Name: {user.name}")

session.close()
```

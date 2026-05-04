# task-manager-pro**Advanced Task Management System** with SQLAlchemy, PostgreSQL, FastAPI, and Alembic.

## 🎯 Features

- ✅ **User Management**: Role-based access control (Admin, Manager, User)
- ✅ **Project Management**: Create projects with team members
- ✅ **Task Management**: Full lifecycle management with priorities and deadlines
- ✅ **Comments**: Discussion threads on tasks
- ✅ **Tags**: Many-to-many task categorization
- ✅ **Soft Delete**: Safe deletion with recovery capability
- ✅ **Audit Logs**: Complete change tracking for compliance
- ✅ **Advanced Search**: Multi-criteria task filtering
- ✅ **Pagination**: Efficient data loading
- ✅ **Transactions**: ACID compliance
- ✅ **Indexes**: Query performance optimization
- ✅ **Docker Support**: Easy containerized deployment

## 🛠️ Tech Stack

- **Framework**: FastAPI 0.109.0
- **Database**: PostgreSQL 16 + SQLAlchemy 2.0+
- **Migrations**: Alembic 1.12.0
- **Security**: Passlib + Bcrypt
- **Validation**: Pydantic 2.5+
- **Testing**: Pytest 7.4+
- **Containers**: Docker & Docker Compose

## 📁 Project Structure

```
task_manager_pro/
├── app/
│   ├── __init__.py
│   ├── models_pro.py       # SQLAlchemy ORM models
│   ├── database_pro.py     # Database configuration
│   ├── schemas_pro.py      # Pydantic schemas
│   ├── crud_pro.py         # CRUD operations
│   └── main_pro.py         # FastAPI app
│
├── tests/
│   ├── conftest_pro.py     # Pytest fixtures
│   ├── test_crud_pro.py    # CRUD tests
│   └── test_api_pro.py     # API tests
│
├── docker-compose.yml      # Docker Compose config
├── Dockerfile              # Container image
├── requirements.txt        # Dependencies
├── .env.example           # Environment template
└── README.md              # This file
```

## 🗄️ Database Schema

**8 Core Tables:**
- `users` - User accounts with roles
- `projects` - Projects with owner/members
- `tasks` - Tasks with status, priority, deadline
- `comments` - Task discussions
- `tags` - Task categories
- `project_members` - User↔Project association
- `task_tags` - Task↔Tag association
- `audit_logs` - Change history

**Key Indexes:**
- Task search: `(project_id, status)`, `(assignee_id, status)`
- Priority/deadline: `(priority, deadline)`
- User lookup: `(email, is_active)`

## 🚀 Quick Start

### 1. Installation

```bash
cd task manager pro
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate
pip install -r requirements.txt
```

### 2. Environment Setup

```bash
cp .env.example .env
# Edit .env with your settings
```

### 3. Run Development Server

```bash
uvicorn app.main_pro:app --reload --port 8000
```

Visit: http://localhost:8000/docs (Swagger UI)

### 4. Docker Deployment

```bash
docker-compose up -d
docker-compose logs -f app
```

## 📚 API Endpoints

### Users
```
POST   /api/users
GET    /api/users
GET    /api/users/{id}
PUT    /api/users/{id}
DELETE /api/users/{id}
```

### Projects
```
POST   /api/projects
GET    /api/projects
GET    /api/projects/{id}
PUT    /api/projects/{id}
DELETE /api/projects/{id}
POST   /api/projects/{id}/members/{uid}
DELETE /api/projects/{id}/members/{uid}
POST   /api/projects/search
```

### Tasks
```
POST   /api/tasks
GET    /api/tasks/{id}
GET    /api/projects/{id}/tasks
PUT    /api/tasks/{id}
DELETE /api/tasks/{id}
POST   /api/tasks/search
```

### Comments
```
POST   /api/tasks/{id}/comments
GET    /api/tasks/{id}/comments
GET    /api/comments/{id}
PUT    /api/comments/{id}
DELETE /api/comments/{id}
```

### Tags
```
POST   /api/tags
GET    /api/tags
GET    /api/tags/{id}
PUT    /api/tags/{id}
DELETE /api/tags/{id}
```

### Audit Logs
```
GET    /api/audit-logs
GET    /api/tasks/{id}/audit-logs
GET    /api/projects/{id}/audit-logs
```

## 🧪 Testing

```bash
# Run all tests
pytest tests/ -v

# With coverage
pytest tests/ --cov=app --cov-report=html

# Specific test class
pytest tests/test_crud_pro.py::TestUserCRUD -v
```

## 🔑 Key Models

### Enums
- `UserRole`: admin, manager, user
- `TaskStatus`: todo, in_progress, review, done, archived
- `TaskPriority`: low, medium, high, critical

### CRUD Functions

**Users**
```python
create_user(db, user_create) -> User
get_user(db, user_id) -> Optional[User]
update_user(db, user_id, update_data) -> Optional[User]
delete_user(db, user_id) -> bool
```

**Projects**
```python
create_project(db, project_create, owner_id) -> Project
search_projects(db, params) -> Tuple[List[Project], int]
add_project_member(db, project_id, user_id, actor_id) -> bool
```

**Tasks**
```python
create_task(db, task_create, creator_id) -> Task
search_tasks(db, params) -> Tuple[List[Task], int]
update_task(db, task_id, update_data, user_id) -> Optional[Task]
```

**Comments**
```python
create_comment(db, task_id, user_id, comment_create) -> Optional[Comment]
get_task_comments(db, task_id) -> Tuple[List[Comment], int]
```

**Audit**
```python
get_audit_logs(db, entity_type, entity_id) -> Tuple[List[AuditLog], int]
```

## 🔍 Advanced Features

### Task Search/Filter

```python
TaskFilterParams(
    project_id=1,
    assignee_id=5,
    status="in_progress",
    priority="high",
    tag_ids=[1, 2, 3],
    search="bug",
    skip=0,
    limit=20,
    order_by="deadline",
    order_direction="asc"
)
```

### Soft Delete

All entities support soft deletion:
```python
task.is_deleted = True
task.deleted_at = datetime.utcnow()
db.commit()
```

### Audit Logging

Every change is tracked:
```python
create_audit_log(
    db, user_id, "task", "update",
    old_values={"status": "todo"},
    new_values={"status": "in_progress"}
)
```

## 🐘 PostgreSQL Features

- Connection pooling (10 connections + 20 overflow)
- Foreign key constraints with CASCADE delete
- JSON columns for audit log values
- UTC timezone-aware timestamps
- Query performance indexes

## 🔐 Security

- ✅ Password hashing (bcrypt)
- ✅ SQL injection prevention (parameterized queries)
- ✅ Role-based access ready
- ✅ Audit trail for compliance
- ✅ CORS enabled (configure for production)

## 📋 Environment Variables

```
DATABASE_URL=postgresql://user:pass@host:5432/db
SQL_ECHO=False                  # Log SQL
API_HOST=0.0.0.0
API_PORT=8000
ENVIRONMENT=development
SECRET_KEY=your-secret-key
```

## 🐛 Troubleshooting

**Database connection issues:**
```bash
# Check PostgreSQL is running
# Verify DATABASE_URL is correct
# Check credentials and permissions
```

**Import errors:**
```bash
# Install dependencies
pip install -r requirements.txt

# Add to PYTHONPATH if needed
export PYTHONPATH="${PYTHONPATH}:/path/to/project"
```

**Tests failing:**
```bash
# Verbose output
pytest tests/ -vv -s

# Check fixtures
pytest tests/conftest_pro.py -v
```

## 📈 Performance Tips

1. Use pagination for large datasets
2. Filter early in search queries
3. Monitor with `SQL_ECHO=True`
4. Use appropriate indexes
5. Consider caching for read-heavy endpoints

## 🚀 Deployment

### Docker Compose (Recommended)

```bash
docker-compose up -d
docker-compose logs -f
docker-compose down
```

### Manual PostgreSQL

```bash
# Create database
createdb task_manager_db

# Run migrations
alembic upgrade head

# Start server
uvicorn app.main_pro:app --host 0.0.0.0 --port 8000
```

## 📝 Example Usage

```python
from app import crud_pro
from app.schemas_pro import ProjectCreate, TaskCreate

# Create project
project = crud_pro.create_project(
    db,
    ProjectCreate(title="Website Redesign"),
    owner_id=1
)

# Create task
task = crud_pro.create_task(
    db,
    TaskCreate(
        title="Design Homepage",
        project_id=project.id,
        status="todo",
        priority="high"
    ),
    creator_id=1
)

# Search tasks
tasks, total = crud_pro.search_tasks(
    db,
    TaskFilterParams(
        project_id=project.id,
        priority="high",
        limit=10
    )
)
```

## 📞 Support

- Check `/docs` endpoint for interactive API documentation
- Review test examples for usage patterns
- Check audit logs for debugging
- Examine models for schema details

## 📄 License

MIT License

---

**Built with ❤️ using FastAPI + SQLAlchemy + PostgreSQL**

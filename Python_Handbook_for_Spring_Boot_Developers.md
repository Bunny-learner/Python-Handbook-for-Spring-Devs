# Python Handbook
### For Spring Boot Developers

*FastAPI  ·  Pydantic  ·  LangGraph  ·  LangFuse · Full CRUD · Python vs Spring Boot · 2026 Edition*

Written for developers who already know what they're doing — just in a different language.

## Chapter 0: Python Basics for Java Developers

> **💡 Note**
>
> If you've been writing Java for years, Python is going to feel weirdly short. No semicolons, no closing braces — but it's very real code. Every concept below maps directly to something you already know from Java and Spring Boot.

### 0.1 Setup & Virtual Environments

Java uses Maven/Gradle to manage dependencies per project. Python uses virtual environments (venv) to isolate dependencies. Think of it as your personal project sandbox — like creating a new Maven project folder, but at the runtime level.

| Java / Spring Boot | Python Equivalent |
|---|---|
| mvn install / gradle build | pip install -r requirements.txt |
| pom.xml / build.gradle | requirements.txt / pyproject.toml |
| mvn spring-boot:run | uvicorn main:app --reload |
| New Maven project folder | python -m venv myenv |

**Example — Creating and activating a virtual environment**

```
# Create a virtual environment
python -m venv myenv

# Activate (Linux/Mac)
source myenv/bin/activate

# Activate (Windows)
myenv\Scripts\activate

# Install packages (like Maven dependencies)
pip install fastapi pydantic langgraph

# Save dependencies (like pom.xml)
pip freeze > requirements.txt
```

### 0.2 Variables, Types & Type Hints

Java is statically typed — you must declare String name = "Alice". Python is dynamically typed, meaning you can assign any type to any variable. However, Python also supports optional type hints (introduced in Python 3.5+) which Pydantic and FastAPI both rely on heavily. Type hints don't enforce types at runtime by themselves — they are documentation + tooling support.

> **🔄 Java Analogy**
>
> Java's "String name" tells the compiler the type. Python's "name: str" tells your IDE and tools the type. Same intent, different enforcement.

**Example — Variables with and without type hints**

```python
# Java:   String name = "Alice";  int age = 22;  boolean active = true;
# Python (no hints): name = "Alice";  age = 22;  active = True

# Python with type hints (recommended — used by FastAPI + Pydantic)
name: str = "Alice"
age: int = 22
active: bool = True
scores: list[int] = [95, 87, 92]
metadata: dict[str, str] = {"role": "admin", "tier": "pro"}

# f-strings (like String.format() in Java)
greeting = f"Hello {name}, you are {age} years old"
print(greeting)   # Hello Alice, you are 22 years old
```

| Spring Boot (Java) | Python |
|---|---|
| int age = 25; | age = 25  or  age: int = 25 |
| String s = "hi"; | s = "hi"  or  s: str = "hi" |
| boolean flag = true; | flag = True |
| List<String> items = new ArrayList<>(); | items: list[str] = [] |
| Map<String,Object> map = new HashMap<>(); | data: dict[str, any] = {} |
| null | None |

### 0.3 Control Flow

Python uses indentation (whitespace) to define code blocks — there are no curly braces {}. This is not optional: wrong indentation = syntax error. The standard is 4 spaces per level. PEP 8 (Python's style guide) enforces this.

> **⚠️ Warning**
>
> Python blocks are defined by indentation — 4 spaces per level. NEVER mix tabs and spaces. Use an editor like VS Code with Python extension to handle this automatically.

**Example — if/elif/else, for loops, and list comprehensions**

```python
# if/elif/else  (indentation = the block — no braces!)
score = 85
if score >= 90:
    grade = "A"
elif score >= 75:
    grade = "B"
else:
    grade = "C"

# for loop (like Java's enhanced for)
fruits = ["apple", "banana", "mango"]
for fruit in fruits:
    print(fruit)

# range (like for(int i=0; i<5; i++))
for i in range(5):      # 0, 1, 2, 3, 4
    print(i)

# List comprehension — no Java equivalent, very Pythonic
squares = [x**2 for x in range(10)]
evens   = [x for x in range(20) if x % 2 == 0]
```

### 0.4 Functions

Python functions are defined with the def keyword. Type hints are optional but strongly recommended. Default arguments, *args, and **kwargs make Python functions extremely flexible — features that Java only partially supports via overloading.

**Example — Functions with defaults and *args**

```python
# Basic function with type hints
def greet(name: str) -> str:
    return f"Hello, {name}!"

# Default arguments (requires overloading in Java)
def connect(host: str, port: int = 8080, secure: bool = False) -> str:
    protocol = "https" if secure else "http"
    return f"{protocol}://{host}:{port}"

connect("localhost")               # http://localhost:8080
connect("prod.api.com", 443, True) # https://prod.api.com:443

# *args and **kwargs (like Java varargs)
def log(*messages: str, level: str = "INFO"):
    for msg in messages:
        print(f"[{level}] {msg}")

# Lambda (like Java's lambda, but simpler)
double = lambda x: x * 2
add    = lambda a, b: a + b
```

### 0.5 Classes & OOP

Python OOP is very similar to Java — classes, constructors, inheritance, method overriding. The key difference: self is the Python equivalent of Java's this, and it must be explicitly declared as the first argument of every instance method. __init__ is the constructor. __repr__ is like toString().

**Example — Class, constructor, inheritance**

```python
class User:
    user_count: int = 0          # Class variable (static in Java)

    def __init__(self, name: str, email: str):  # Constructor
        self.name = name                         # Instance variable
        self.email = email
        User.user_count += 1

    def greet(self) -> str:
        return f"Hi, I'm {self.name}"

    def __repr__(self) -> str:                  # like toString()
        return f"User(name={self.name})"

# Inheritance (extends User)
class AdminUser(User):
    def __init__(self, name: str, email: str, role: str):
        super().__init__(name, email)            # super() call
        self.role = role

    def greet(self) -> str:                     # Override
        return f"Hi, I'm Admin {self.name} [{self.role}]"
```

### 0.6 Exception Handling

Python's try/except/else/finally maps almost 1-to-1 to Java's try/catch/finally. The else block is Python-specific — it runs only if no exception was raised, which can be useful for clean flow separation.

**Spring Boot (Java)**

```java
try {
    result = riskyOp();
} catch (IOException e) {
    // handle IO error
} catch (Exception e) {
    // generic catch
    throw e;  // re-throw
} finally {
    cleanup();
}

```

**Python**

```python
try:
    result = risky_op()
except IOError as e:
    # handle IO error
    print(f"IO error: {e}")
except Exception as e:
    # generic catch-all
    raise              # re-raise
else:
    print("No errors!")
finally:
    cleanup()
```

### 0.7 Async/Await — Critical for FastAPI

Python's async/await is similar to Java's CompletableFuture, but cleaner and more widely used. FastAPI is built entirely around async — every route handler can (and should) be async def. The event loop is managed by FastAPI/uvicorn, so you don't need to manually call asyncio.run().

> **💡 Tip**
>
> In Spring Boot, async is something you opt into with @Async and CompletableFuture. In FastAPI, async is just how it works. Mark your route handlers as "async def" and you're already most of the way there.

**Example — async/await basics**

```python
import asyncio

# async function — returns a coroutine, not a direct value
async def fetch_data(url: str) -> dict:
    await asyncio.sleep(1)            # Non-blocking wait
    return {"url": url, "data": "..."}

# await pauses this function until fetch_data completes
async def main():
    result = await fetch_data("https://api.example.com/users")
    print(result)

    # Run multiple concurrently (like CompletableFuture.allOf)
    t1, t2 = asyncio.create_task(fetch_data("url1")), asyncio.create_task(fetch_data("url2"))
    results = await asyncio.gather(t1, t2)

asyncio.run(main())
```

## Chapter 1: Core Python Skills & Best Practices

### 1.1 Data Structures — List, Tuple, Set, Dict

Python has four built-in collection types. Choosing the right one matters for both correctness and performance:

| Python Type | Java Equivalent | Key Property |
|---|---|---|
| list | ArrayList<T> | Ordered, mutable |
| tuple | ImmutableList / record | Ordered, immutable |
| set | HashSet<T> | Unordered, unique values |
| dict | HashMap<K,V> | Key-value pairs |

**Example — All four collection types**

```
# List — ordered, mutable (ArrayList<T>)
items: list[str] = ["a", "b", "c"]
items.append("d")       # add to end
items.insert(0, "z")    # insert at index
items.remove("b")       # remove first match
sorted_copy = sorted(items, reverse=True)

# Tuple — ordered, IMMUTABLE (use for fixed data like coordinates)
point = (3, 5)
x, y = point            # unpacking

# Set — unordered, unique (HashSet<T>)
tags: set[str] = {"python", "backend", "api"}
tags.add("fastapi")     # add element
union_set = tags | {"rest", "python"}   # union
intersect = tags & {"python", "go"}     # intersection

# Dict — key-value (HashMap<K,V>)
user: dict[str, any] = {"id": 1, "name": "Alice", "active": True}
user["email"] = "alice@example.com"      # add/update
name = user.get("name", "Unknown")       # safe get with default
user.pop("active", None)                 # safe delete
```

#### Slicing — Python's Secret Weapon

Python's slicing syntax [start:stop:step] works on lists, strings, and tuples. There's no equivalent in Java without writing loops.

**Example — Slicing syntax**

```
data = [10, 20, 30, 40, 50, 60, 70]

data[1:4]    # [20, 30, 40]       — index 1 up to (not including) 4
data[:3]     # [10, 20, 30]       — first 3
data[-3:]    # [50, 60, 70]       — last 3
data[::2]    # [10, 30, 50, 70]   — every 2nd element
data[::-1]   # [70,60,50,40,30,20,10] — reversed

# Works on strings too!
s = "HelloWorld"
s[:5]        # "Hello"
s[-5:]       # "World"
s[::-1]      # "dlroWolleH"
```

### 1.2 Decorators — Python's @Annotations

Decorators are Python's equivalent of Java annotations like @Transactional, @Cacheable, @Retry. The key difference: Python decorators are actual functions that wrap other functions — there's no compiler magic. This means you can write your own decorator in 10 lines, and you'll instantly understand what Spring was doing under the hood.

> **🔄 Java Analogy**
>
> Java @Transactional wraps your method in a transaction — Spring does it via AOP. Python @timer wraps your function with timing logic — you write the wrapper yourself. Same pattern, full transparency.

**Example — Custom timing decorator (like @Timed in Micrometer)**

```python
import time
from functools import wraps

def timer(func):
    @wraps(func)        # Preserves original function metadata (name, docstring)
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)   # Call original function
        elapsed = time.time() - start
        print(f"{func.__name__} took {elapsed:.4f}s")
        return result
    return wrapper

@timer                  # Same as: process_data = timer(process_data)
def process_data(n: int):
    return sum(range(n))

process_data(1_000_000)   # process_data took 0.0321s
```

### 1.3 Generators — Memory-Efficient Processing

A generator produces values one at a time using the yield keyword — it doesn't build the entire collection in memory. Think of it like Java Streams, but even lazier. Generators are ideal for processing large files, paginated APIs, or infinite sequences.

**Example — Fibonacci generator and large file processing**

```python
def fibonacci():
    a, b = 0, 1
    while True:
        yield a          # suspends here, returns value
        a, b = b, a + b

fib = fibonacci()
print(next(fib))  # 0
print(next(fib))  # 1
print(next(fib))  # 1

# Generator expression (lazy list comprehension — no memory spike)
large_sum = sum(x**2 for x in range(10_000_000))

# Process huge files line by line
def read_large_file(path: str):
    with open(path) as f:
        for line in f:
            yield line.strip()   # one line in memory at a time
```

### 1.4 Python Best Practices & Naming Conventions

Python has PEP 8 — the official style guide, equivalent to Google's Java Style Guide. Following it is not optional in professional Python code. Most teams enforce it with Black (auto-formatter) or Ruff.

| Convention | Java | Python |
|---|---|---|
| Variables & methods | camelCase | snake_case |
| Classes | PascalCase | PascalCase |
| Constants | UPPER_SNAKE_CASE | UPPER_SNAKE_CASE |
| Private (convention) | private keyword | _single_underscore |
| Special methods | N/A | __double_underscore__ |

## Chapter 2: Pydantic — Python's Data Validation Library

> **🔄 Java Analogy**
>
> Pydantic = @Entity + @Valid + Bean Validation + Jackson ObjectMapper — all in one class. No boilerplate, no getters/setters, no Lombok required.

Pydantic is the foundation of the entire Python AI/API stack. FastAPI, LangGraph, and LangFuse all use it internally. Before you can write a FastAPI route, you need to understand Pydantic models. Think of a Pydantic model as a DTO + validator + JSON serializer all merged into one clean class.

### 2.1 What is Pydantic?

A Pydantic model is a Python class that inherits from BaseModel. When you create an instance of it, Pydantic automatically validates all fields, converts types where possible (e.g. "22" → 22 for an int field), and raises a detailed error if validation fails. This happens at object creation time — no separate validation step needed.

**Example — Basic Pydantic model with validation**

```python
from pydantic import BaseModel, Field, EmailStr
from typing import Optional
from datetime import datetime

# A Pydantic model = Spring @Entity / DTO / Request class
class User(BaseModel):
    id: int
    name: str
    email: EmailStr           # Automatically validates email format
    age: int = Field(ge=0, le=150)    # 0 <= age <= 150
    bio: Optional[str] = None         # Optional with default None
    created_at: datetime = Field(default_factory=datetime.utcnow)

# Creating instances — validation happens automatically on creation
user = User(id=1, name="Alice", email="alice@example.com", age=22)
print(user.name)           # Alice
print(user.model_dump())   # {"id":1, "name":"Alice", ...}

# Validation error on bad data
try:
    bad = User(id=1, name="Bob", email="not-an-email", age=-5)
except Exception as e:
    print(e)  # ValidationError: 2 validation errors...
```

### 2.2 Field Validation

The Field() function lets you add constraints to any field — minimum/maximum values, string length limits, regex patterns, and custom descriptions. You can also add custom validators using @field_validator (for single fields) and @model_validator (for cross-field logic).

| Spring Boot Bean Validation | Pydantic Field |
|---|---|
| @NotNull | Field(...)  — required, no default |
| @Size(min=2, max=50) | Field(min_length=2, max_length=50) |
| @Min(0) @Max(100) | Field(ge=0, le=100) |
| @Email | EmailStr type |
| @Pattern(regexp=...) | Field(pattern=r"...") |
| @AssertTrue / custom | @field_validator |
| objectMapper.writeValueAsString() | model.model_dump_json() |
| objectMapper.readValue(json, T.class) | T.model_validate_json(json) |

**Example — Field constraints and custom validators**

```python
from pydantic import BaseModel, Field, field_validator, model_validator

class Product(BaseModel):
    name: str = Field(min_length=2, max_length=100)
    price: float = Field(gt=0, description="Price must be positive")
    discount: float = Field(ge=0, le=1, default=0.0)  # 0-100%
    sku: str = Field(pattern=r"^[A-Z]{2}-\d{4}$")    # regex pattern

    # Like @AssertTrue or custom constraint in Spring
    @field_validator("name")
    @classmethod
    def name_must_not_contain_special(cls, v: str) -> str:
        if any(c in v for c in ["!", "@", "#"]):
            raise ValueError("Name must not contain special characters")
        return v.strip().title()   # Can also transform the value

    # Validates across multiple fields (like @ScriptAssert in Spring)
    @model_validator(mode="after")
    def discounted_price_check(self) -> "Product":
        if self.discount > 0 and self.price < 10:
            raise ValueError("Discounts not allowed on items under $10")
        return self
```

### 2.3 Nested Models & Serialization

Pydantic models can be nested inside other models — exactly like embedding an @Embedded entity in Spring/JPA. Validation is automatically applied recursively. Serialization and deserialization use model_dump() and model_validate() respectively.

**Example — Nested models and JSON serialization**

```python
from pydantic import BaseModel
from typing import List

class Address(BaseModel):
    street: str
    city: str
    pincode: str

class Order(BaseModel):
    order_id: str
    address: Address         # Nested model — auto-validated
    items: List[str]
    status: str = "pending"

# Serialization
user = User(id=1, name="Alice", email="alice@example.com", age=22)

d = user.model_dump()                      # To dict
d = user.model_dump(exclude={"id"})        # Exclude fields
d = user.model_dump(include={"name","email"})  # Include only

json_str = user.model_dump_json()          # To JSON string
user2 = User.model_validate_json(json_str) # From JSON string
user3 = User.model_validate({"id":2,...})  # From dict
```

## Chapter 3: FastAPI — High-Performance API Framework

> **🔄 Java Analogy**
>
> FastAPI is basically what Spring Boot would look like if it went on a diet, learned async, and returned with auto-generated Swagger docs built in. The moment you run your server and open /docs — seeing your entire API documented and testable without a single Swagger config line — is genuinely exciting.

FastAPI is built on Starlette (HTTP layer) and Pydantic (data validation). Route handlers can be async or sync. It generates OpenAPI (Swagger) docs automatically from your Pydantic models and function signatures. Startup time is milliseconds vs seconds for Spring Boot.

### 3.1 Project Setup

**Installation**

```
pip install fastapi uvicorn[standard] pydantic[email]
```

**Example — main.py (your Spring Boot Application class equivalent)**

```python
from fastapi import FastAPI, HTTPException, Depends, status
from pydantic import BaseModel
from typing import List, Optional

app = FastAPI(
    title="My API",
    description="FastAPI backend for my service",
    version="1.0.0",
    docs_url="/docs",      # Swagger UI — auto-generated!
    redoc_url="/redoc"     # ReDoc alternative
)

# Run: uvicorn main:app --reload   (like mvn spring-boot:run)
# --reload enables hot reload in development
```

### 3.2 Routes & HTTP Methods

FastAPI routes are defined using decorators directly on async functions — no controller class required (though you can use APIRouter for organization). Path parameters are automatically parsed to the declared type (int, str, etc.).

| Spring Boot Annotation | FastAPI Decorator |
|---|---|
| @GetMapping | @app.get("/path") |
| @PostMapping | @app.post("/path") |
| @PutMapping | @app.put("/path/{id}") |
| @PatchMapping | @app.patch("/path/{id}") |
| @DeleteMapping | @app.delete("/path/{id}") |
| @PathVariable Long id | def fn(user_id: int)  ← in path |
| @RequestParam int page | def fn(page: int = 0)  ← query param |
| @RequestBody @Valid DTO | def fn(body: MyModel)  ← Pydantic model |

**Example — CRUD routes for a User resource**

```python
users_db: dict[int, dict] = {}  # In-memory store

# GET /users — list all (like @GetMapping)
@app.get("/users", response_model=List[UserResponse], tags=["Users"])
async def get_users():
    return list(users_db.values())

# GET /users/{id} — get one (like @PathVariable)
@app.get("/users/{user_id}", response_model=UserResponse)
async def get_user(user_id: int):
    if user_id not in users_db:
        raise HTTPException(status_code=404, detail="User not found")
    return users_db[user_id]

# POST /users — create (body auto-validated via Pydantic)
@app.post("/users", response_model=UserResponse, status_code=201)
async def create_user(user: CreateUserRequest):
    user_id = len(users_db) + 1
    users_db[user_id] = {"id": user_id, **user.model_dump()}
    return users_db[user_id]

# Query parameters (like @RequestParam)
@app.get("/users/search")
async def search_users(q: str, page: int = 1, size: int = 10):
    pass  # GET /users/search?q=alice&page=2&size=5
```

### 3.3 Dependency Injection with Depends()

Spring Boot uses @Autowired and constructor injection. FastAPI uses Depends() — a function whose return value is injected into the route handler. This is how you inject database sessions, authentication context, service classes, and more. FastAPI resolves the dependency graph automatically, just like Spring's IoC container.

**Example — Database session and auth dependency injection**

```python
from fastapi import Depends
from sqlalchemy.orm import Session

# Database session (like @Autowired DataSource)
def get_db():
    db = SessionLocal()       # Open DB connection
    try:
        yield db              # Provide to route handler
    finally:
        db.close()            # Cleanup after request finishes

# Auth dependency (like Spring Security filter)
security = HTTPBearer()

def verify_token(credentials = Depends(security)):
    if not is_valid_token(credentials.credentials):
        raise HTTPException(status_code=401, detail="Invalid token")
    return decode_token(credentials.credentials)

# Both injected automatically via Depends()
@app.get("/users/me")
async def get_current_user(
    db: Session = Depends(get_db),          # DB injection
    current_user = Depends(verify_token)    # Auth injection
):
    return db.query(User).filter(User.id == current_user["id"]).first()
```

### 3.4 Exception Handling (Global)

FastAPI's global exception handling uses @app.exception_handler() — equivalent to Spring's @ControllerAdvice. You define a handler function that returns a JSONResponse, giving you full control over error format, status code, and body.

**Example — Global exception handler (like @ControllerAdvice)**

```python
from fastapi import Request
from fastapi.responses import JSONResponse

class BusinessException(Exception):
    def __init__(self, message: str, code: str):
        self.message = message
        self.code = code

# @app.exception_handler = @ControllerAdvice
@app.exception_handler(BusinessException)
async def business_exception_handler(request: Request, exc: BusinessException):
    return JSONResponse(
        status_code=400,
        content={"error": exc.code, "message": exc.message}
    )
```

## Chapter 4: LangGraph — Stateful AI Agent Workflows

> **🔄 Java Analogy**
>
> If you've ever used Spring State Machine or drawn a flowchart for a complex business process — LangGraph is exactly that, but the nodes talk to an LLM. Instead of one giant prompt doing everything, you break it into steps, give each step a job, and let the graph decide what happens next.

LangGraph models your AI pipeline as a directed graph. Each node is a Python function that processes the current state and returns an updated partial state. Edges define transitions between nodes. A special conditional edge lets the graph branch based on the current state — exactly like a Router in Spring MVC.

### 4.1 Core Concepts

| LangGraph Concept | Spring Boot Equivalent | What It Does |
|---|---|---|
| StateGraph | State Machine | Defines the graph structure |
| Node | Service / Handler method | Processing step (a Python function) |
| Edge | Transition / Route | Connection between nodes |
| State (TypedDict) | Shared context object | Data passed between all nodes |
| Conditional Edge | Router / if-else flow | Branching based on state |
| compile() | Build application context | Assembles the runnable graph |

### 4.2 Installation & Basic Agent Graph

**Installation**

```
pip install langgraph langchain langchain-openai
```

The state is a TypedDict — a typed dictionary passed between all nodes. Each node receives the full state and returns a dict containing only the fields it wants to update. LangGraph merges these partial updates back into the shared state automatically.

**Example — Define state and build a 3-node RAG agent graph**

```python
from typing import TypedDict, Annotated
from langchain_core.messages import BaseMessage, HumanMessage, AIMessage
from langchain_openai import ChatOpenAI
from langgraph.graph import StateGraph, END
import operator

# State shape — flows between ALL nodes (like shared context)
class AgentState(TypedDict):
    messages: Annotated[list[BaseMessage], operator.add]  # append-only
    user_input: str
    context: str
    final_answer: str

llm = ChatOpenAI(model="gpt-4o-mini", temperature=0)

# Node 1 — classify intent
def classify_intent(state: AgentState) -> dict:
    response = llm.invoke([HumanMessage(content=f'Classify: {state["user_input"]}')] )
    return {"context": response.content}

# Node 2 — retrieve docs (RAG step)
def retrieve_context(state: AgentState) -> dict:
    docs = vector_store.search(state["user_input"], k=3)
    return {"context": "\n".join([d.page_content for d in docs])}

# Node 3 — generate answer
def generate_answer(state: AgentState) -> dict:
    prompt = f'Context: {state["context"]}\nQuestion: {state["user_input"]}'
    response = llm.invoke([HumanMessage(content=prompt)])
    return {"final_answer": response.content, "messages": [AIMessage(content=response.content)]}

# Router — decides next node based on state
def route_by_intent(state: AgentState) -> str:
    return "retrieve" if "SEARCH" in state["context"].upper() else "generate"

# Build the graph
workflow = StateGraph(AgentState)
workflow.add_node("classify", classify_intent)
workflow.add_node("retrieve", retrieve_context)
workflow.add_node("generate", generate_answer)

workflow.set_entry_point("classify")
workflow.add_conditional_edges("classify", route_by_intent, {"retrieve":"retrieve","generate":"generate"})
workflow.add_edge("retrieve", "generate")
workflow.add_edge("generate", END)

app = workflow.compile()
result = app.invoke({"user_input": "What is FastAPI?", "messages": [], "context": "", "final_answer": ""})
print(result["final_answer"])
```

### 4.3 Memory & Persistence

LangGraph supports checkpointing — saving the state of a conversation between invocations. This gives your agent multi-turn memory. MemorySaver stores state in RAM (like Spring Session in-memory). SqliteSaver persists to disk across restarts.

**Example — Multi-turn memory with MemorySaver**

```python
from langgraph.checkpoint.memory import MemorySaver

memory = MemorySaver()
app = workflow.compile(checkpointer=memory)

# thread_id identifies the conversation session
config = {"configurable": {"thread_id": "user-123-session-1"}}

# First message
result1 = app.invoke({"user_input": "My name is Alice", "messages": []}, config)

# Second message — LangGraph remembers the full conversation!
result2 = app.invoke({"user_input": "What is my name?", "messages": []}, config)
# Agent knows context from the previous turn
```

### 4.4 CRUD Operations with LangGraph

LangGraph can act as an intelligent orchestration layer for CRUD operations. Instead of a fixed REST controller, the agent decides which CRUD action to perform based on natural language input. Each CRUD operation becomes a node in the graph.

**Example — CRUD-aware LangGraph agent**

```python
from langgraph.graph import StateGraph, END
from typing import TypedDict

class CRUDState(TypedDict):
    action: str      # "create" | "read" | "update" | "delete"
    user_id: int | None
    payload: dict
    result: dict
    error: str | None

# Node — classify which CRUD operation is needed
def classify_crud(state: CRUDState) -> dict:
    intent = state["action"].lower()
    return {"action": intent}

# Node — Create operation
def create_record(state: CRUDState) -> dict:
    user = UserCreate(**state["payload"])
    created = user_service.create(user)
    return {"result": created.model_dump()}

# Node — Read operation
def read_record(state: CRUDState) -> dict:
    user = user_service.get(state["user_id"])
    return {"result": user.model_dump() if user else {}, "error": None if user else "Not found"}

# Node — Update operation
def update_record(state: CRUDState) -> dict:
    update = UserUpdate(**state["payload"])
    updated = user_service.update(state["user_id"], update)
    return {"result": updated.model_dump()}

# Node — Delete operation
def delete_record(state: CRUDState) -> dict:
    success = user_service.delete(state["user_id"])
    return {"result": {"deleted": success}}

# Router — pick the right node
def route_crud(state: CRUDState) -> str:
    return state["action"]  # "create" | "read" | "update" | "delete"

g = StateGraph(CRUDState)
g.add_node("classify", classify_crud)
g.add_node("create", create_record)
g.add_node("read", read_record)
g.add_node("update", update_record)
g.add_node("delete", delete_record)

g.set_entry_point("classify")
g.add_conditional_edges("classify", route_crud, {"create":"create","read":"read","update":"update","delete":"delete"})
for node in ["create","read","update","delete"]:
    g.add_edge(node, END)

crud_agent = g.compile()
```

## Chapter 5: LangFuse — LLM Observability & Monitoring

> **🔄 Java Analogy**
>
> You'd never deploy a Spring Boot service without Actuator, Micrometer, and distributed tracing. LangFuse is exactly that — but for LLM apps. Without it, you're flying blind: no idea which prompt is slow, which is expensive, or why a user got a bad answer. Add it from day one.

LangFuse provides traces (entire request), spans (individual steps), and generations (LLM calls with token tracking). It has a visual dashboard showing latency, cost, error rates, and prompt versions — the full observability stack for AI applications.

### 5.1 Setup & Configuration

**Installation & Environment**

```
pip install langfuse

# .env file (like application.properties in Spring)
LANGFUSE_PUBLIC_KEY=pk-lf-...
LANGFUSE_SECRET_KEY=sk-lf-...
LANGFUSE_HOST=https://cloud.langfuse.com   # Or self-hosted URL
```

**Example — Initialize LangFuse client**

```python
from langfuse import Langfuse
import os

langfuse = Langfuse(
    public_key=os.getenv("LANGFUSE_PUBLIC_KEY"),
    secret_key=os.getenv("LANGFUSE_SECRET_KEY"),
    host=os.getenv("LANGFUSE_HOST", "https://cloud.langfuse.com")
)
```

### 5.2 Tracing with @observe Decorator

The @observe decorator is the easiest way to add tracing. When you decorate a function with @observe, LangFuse automatically creates a span for that function — recording input, output, duration, and any metadata you attach. Nested @observe calls create a parent-child span tree, just like distributed tracing in OpenTelemetry.

**Example — Nested spans with @observe (like @Span in Micrometer)**

```python
from langfuse.decorators import observe, langfuse_context
from langchain_openai import ChatOpenAI
from langchain_core.messages import HumanMessage

llm = ChatOpenAI(model="gpt-4o-mini")

# Child span — tracked as a sub-operation
@observe()
def generate_summary(text: str) -> str:
    response = llm.invoke([HumanMessage(content=f"Summarize: {text}")])
    return response.content

# Parent span — contains child spans in the trace tree
@observe()
def process_document(doc_id: str, content: str) -> dict:
    langfuse_context.update_current_trace(
        user_id="user-123",
        session_id="session-abc",
        tags=["document-processing"],
        metadata={"doc_id": doc_id}
    )

    summary = generate_summary(content)    # Child span auto-created

    langfuse_context.score_current_observation(
        name="relevance",
        value=0.9,
        comment="High quality output"
    )

    return {"summary": summary}
```

### 5.3 Manual Tracing (Fine-Grained Control)

For complex pipelines where you need fine-grained control, you can manually create traces, spans, and generation objects. This is equivalent to manually managing OpenTelemetry spans — useful when you want to track token usage, model parameters, and cost.

**Example — Manual trace with span and generation tracking**

```
# Create a trace for the entire request
trace = langfuse.trace(
    name="rag-pipeline",
    user_id="user-456",
    input={"query": "What is machine learning?"}
)

# Retrieval span
retrieval_span = trace.span(name="document-retrieval", input={"query": "machine learning"})
docs = vector_db.search("machine learning", k=5)
retrieval_span.end(output={"num_docs": len(docs)})

# LLM generation with token tracking
generation = trace.generation(
    name="llm-generation",
    model="gpt-4o-mini",
    model_parameters={"temperature": 0, "max_tokens": 500}
)
response = llm.invoke(messages)
generation.end(
    output=response.content,
    usage={"prompt_tokens": response.usage.prompt_tokens,
           "completion_tokens": response.usage.completion_tokens}
)

langfuse.flush()   # Ensure all data is sent (important in async/batch)
```

### 5.4 Prompt Management

LangFuse acts as a central Prompt Registry — version-controlled, environment-separated, and A/B testable. This is like externalizing your prompts to application.yml, but with a version history and the ability to switch prompt versions without redeploying your app.

**Example — Prompt versioning and A/B testing**

```
# Push a prompt to LangFuse registry (do once, or via the UI)
langfuse.create_prompt(
    name="summarization-prompt",
    prompt="Summarize in {{num_sentences}} sentences:\n{{text}}",
    labels=["production"]
)

# Pull prompt at runtime (auto-tracks which version was used)
prompt_template = langfuse.get_prompt("summarization-prompt")

# Compile with variables (like String.format())
compiled = prompt_template.compile(num_sentences=3, text="Your long text...")
response = llm.invoke([HumanMessage(content=compiled)])

# A/B test: switch to staging version without code change
v2 = langfuse.get_prompt("summarization-prompt", label="staging")
```

### 5.5 LangFuse CRUD Tracking

You can use LangFuse to track CRUD operations performed by an AI agent — recording what action was taken, on which resource, with what input and output. This gives you full auditability of AI-driven data operations.

**Example — Tracing CRUD operations via LangFuse**

```python
from langfuse.decorators import observe, langfuse_context

@observe(name="crud-create-user")
def traced_create_user(payload: dict) -> dict:
    langfuse_context.update_current_trace(
        tags=["crud", "create"],
        metadata={"resource": "user", "action": "create"}
    )
    user = user_service.create(UserCreate(**payload))
    return user.model_dump()

@observe(name="crud-read-user")
def traced_read_user(user_id: int) -> dict:
    langfuse_context.update_current_trace(
        tags=["crud", "read"],
        metadata={"resource": "user", "user_id": user_id}
    )
    user = user_service.get(user_id)
    return user.model_dump() if user else {}

@observe(name="crud-update-user")
def traced_update_user(user_id: int, payload: dict) -> dict:
    langfuse_context.update_current_trace(tags=["crud","update"])
    updated = user_service.update(user_id, UserUpdate(**payload))
    return updated.model_dump()

@observe(name="crud-delete-user")
def traced_delete_user(user_id: int) -> dict:
    langfuse_context.update_current_trace(tags=["crud","delete"])
    success = user_service.delete(user_id)
    return {"deleted": success, "user_id": user_id}
```

### 5.6 LangFuse with LangGraph

LangFuse integrates directly with LangGraph via CallbackHandler. Pass it as a config when invoking your compiled graph — all LLM calls within every node are automatically traced with full token usage, latency, and input/output recorded.

**Example — LangFuse callback in LangGraph**

```python
from langfuse.callback import CallbackHandler

langfuse_handler = CallbackHandler(
    public_key=os.getenv("LANGFUSE_PUBLIC_KEY"),
    secret_key=os.getenv("LANGFUSE_SECRET_KEY"),
    session_id="session-001",
    user_id="user-123",
    trace_name="my-agent-workflow"
)

config = {
    "callbacks": [langfuse_handler],
    "configurable": {"thread_id": "thread-001"}
}

result = langgraph_app.invoke(
    {"messages": [HumanMessage(content="Hello!")]},
    config=config   # All LLM calls inside the graph are now traced
)
# View full trace tree in your LangFuse dashboard
```

## Chapter 6: Full CRUD — School Management System

> **🏫  The Story: EduTrack — A School Management System**
>
> We are building EduTrack — a backend system for a school that manages Students, Courses, and Enrollments. Every section of this chapter tells the next part of the same story, using the same data, the same IDs, the same names.
> The four technologies work together like this:
> Pydantic — validates all data going in and out (the gatekeeper)
> FastAPI — exposes the REST endpoints teachers & admins call
> LangGraph — powers an AI assistant that handles enrollment queries via natural language
> LangFuse — observes every operation: API call, AI decision, DB write — fully traceable

**Pydantic — Validates data  →  FastAPI — Exposes REST API  →  LangGraph — AI orchestration  →  LangFuse — Observability**

### 6.1 Project Structure — EduTrack School System

EduTrack manages three resources: Students, Courses, and Enrollments. Each resource has its own file per layer. The folder structure below is the same whether you're using Spring Boot or FastAPI — only the filenames and annotations differ.

| FastAPI File | Spring Boot Equivalent |
|---|---|
| models/student.py  (SQLAlchemy ORM) | Student.java  (@Entity) |
| models/course.py   (SQLAlchemy ORM) | Course.java   (@Entity) |
| models/enrollment.py | Enrollment.java  (@Entity, FK to both) |
| schemas/student_schema.py  (Pydantic) | StudentDTO.java / CreateStudentDTO.java |
| schemas/course_schema.py   (Pydantic) | CourseDTO.java |
| schemas/enrollment_schema.py | EnrollmentDTO.java |
| repositories/student_repo.py | StudentRepository.java  (JpaRepository) |
| services/student_service.py | StudentService.java  (@Service) |
| routes/students.py | StudentController.java  (@RestController) |
| routes/courses.py | CourseController.java |
| routes/enrollments.py | EnrollmentController.java |
| agent/enrollment_agent.py | (LangGraph AI agent — no Spring equivalent) |
| main.py | Application.java  (@SpringBootApplication) |

#### Step 1 of 4 · Pydantic — Define & Validate All Data

Before writing a single route or database query, we define all our data shapes with Pydantic. These models are the single source of truth: FastAPI uses them to validate incoming requests, LangGraph uses them to validate agent outputs, and LangFuse logs them as structured metadata.

> **💡 Note**
>
> Every field in these models will be used consistently across all four technologies. The same StudentCreate model that validates the HTTP POST body is also what the LangGraph agent outputs when enrolling a student.

**schemas/student_schema.py — Student data contracts (Pydantic)**

```python
from pydantic import BaseModel, Field, EmailStr
from typing import Optional
from datetime import datetime

# CREATE — what the admin sends when registering a new student
# Used by: FastAPI POST /students  +  LangGraph enroll_node
class StudentCreate(BaseModel):
    name: str = Field(min_length=2, max_length=100)
    email: EmailStr                          # auto-validates format
    roll_number: str = Field(pattern=r"^STU-\d{4}$")  # e.g. STU-0042
    grade: int = Field(ge=1, le=12)

# UPDATE — partial edit (PATCH), all fields optional
# Used by: FastAPI PATCH /students/{id}
class StudentUpdate(BaseModel):
    name: Optional[str] = Field(None, min_length=2, max_length=100)
    grade: Optional[int] = Field(None, ge=1, le=12)

# RESPONSE — what the API returns (never expose internal DB ids raw)
# Used by: all GET endpoints + as LangGraph output
class StudentResponse(BaseModel):
    id: int
    name: str
    email: str
    roll_number: str
    grade: int
    enrolled_at: datetime
    model_config = {"from_attributes": True}  # for SQLAlchemy ORM compat
```

**schemas/course_schema.py — Course data contracts (Pydantic)**

```python
from pydantic import BaseModel, Field
from typing import Optional

class CourseCreate(BaseModel):
    title: str = Field(min_length=3, max_length=200)
    code: str = Field(pattern=r"^[A-Z]{2,4}-\d{3}$")  # e.g. CS-101
    teacher_name: str = Field(min_length=2)
    max_seats: int = Field(ge=1, le=500, default=30)

class CourseUpdate(BaseModel):
    title: Optional[str] = None
    teacher_name: Optional[str] = None
    max_seats: Optional[int] = Field(None, ge=1, le=500)

class CourseResponse(BaseModel):
    id: int
    title: str
    code: str
    teacher_name: str
    max_seats: int
    enrolled_count: int = 0
    model_config = {"from_attributes": True}
```

**schemas/enrollment_schema.py — Enrollment (links Student ↔ Course)**

```python
from pydantic import BaseModel
from datetime import datetime

# CREATE — admin enrolls student_id into course_id
class EnrollmentCreate(BaseModel):
    student_id: int
    course_id: int

# RESPONSE — returned after successful enrollment
class EnrollmentResponse(BaseModel):
    id: int
    student_id: int
    course_id: int
    student_name: str      # denormalized for readability
    course_title: str      # denormalized for readability
    enrolled_at: datetime
    model_config = {"from_attributes": True}
```

#### Step 2 of 4 · FastAPI — Expose the REST Endpoints

Now that our data shapes are locked down by Pydantic, we wire them into FastAPI routes. The Pydantic schemas we just wrote are directly used as request body types and response_model values — FastAPI reads the schema, validates input, and serialises output automatically.

> **💡 Tip**
>
> Notice: the same StudentCreate, CourseCreate, EnrollmentCreate classes from Step 1 appear in all the route signatures below. You write the schema once in Pydantic — FastAPI, LangGraph, and LangFuse all consume the same class.

#### Student Routes — Full CRUD

**routes/students.py — Student CRUD (uses StudentCreate / StudentResponse from Step 1)**

```python
from fastapi import APIRouter, Depends, HTTPException, status
from sqlalchemy.orm import Session
from database import get_db
from schemas.student_schema import StudentCreate, StudentUpdate, StudentResponse
from services.student_service import StudentService
from typing import List

router = APIRouter(prefix="/api/students", tags=["Students"])

def get_svc(db: Session = Depends(get_db)): return StudentService(db)

# CREATE — admin registers a new student
# POST /api/students  →  body: StudentCreate  →  returns StudentResponse
@router.post("/", response_model=StudentResponse, status_code=201)
async def create_student(data: StudentCreate, svc = Depends(get_svc)):
    """Register a new student. Roll number must be unique."""
    return svc.create(data)

# READ ALL — list every student (with pagination)
# GET /api/students?skip=0&limit=20
@router.get("/", response_model=List[StudentResponse])
async def list_students(skip:int=0, limit:int=20, svc=Depends(get_svc)):
    return svc.get_all(skip=skip, limit=limit)

# READ ONE — fetch student by ID
# GET /api/students/42
@router.get("/{student_id}", response_model=StudentResponse)
async def get_student(student_id: int, svc=Depends(get_svc)):
    return svc.get_or_404(student_id)

# UPDATE — teacher updates grade or name (partial PATCH)
# PATCH /api/students/42  →  body: StudentUpdate
@router.patch("/{student_id}", response_model=StudentResponse)
async def update_student(student_id:int, data:StudentUpdate, svc=Depends(get_svc)):
    return svc.update(student_id, data)

# DELETE — remove student (also cascades to Enrollments)
# DELETE /api/students/42
@router.delete("/{student_id}", status_code=204)
async def delete_student(student_id: int, svc=Depends(get_svc)):
    svc.delete(student_id)
```

#### Course Routes — Full CRUD

**routes/courses.py — Course CRUD (same pattern as students)**

```python
from fastapi import APIRouter, Depends, status
from schemas.course_schema import CourseCreate, CourseUpdate, CourseResponse
from services.course_service import CourseService
from typing import List

router = APIRouter(prefix="/api/courses", tags=["Courses"])

def get_svc(db=Depends(get_db)): return CourseService(db)

# CREATE — admin adds a new course (e.g. CS-101, Physics-202)
@router.post("/", response_model=CourseResponse, status_code=201)
async def create_course(data: CourseCreate, svc=Depends(get_svc)):
    return svc.create(data)

# READ ALL — list all courses with seat availability
@router.get("/", response_model=List[CourseResponse])
async def list_courses(skip:int=0, limit:int=20, svc=Depends(get_svc)):
    return svc.get_all(skip, limit)

# READ ONE
@router.get("/{course_id}", response_model=CourseResponse)
async def get_course(course_id: int, svc=Depends(get_svc)):
    return svc.get_or_404(course_id)

# UPDATE — change teacher or expand seats
@router.patch("/{course_id}", response_model=CourseResponse)
async def update_course(course_id:int, data:CourseUpdate, svc=Depends(get_svc)):
    return svc.update(course_id, data)

# DELETE — remove course (only if 0 active enrollments)
@router.delete("/{course_id}", status_code=204)
async def delete_course(course_id: int, svc=Depends(get_svc)):
    svc.delete(course_id)
```

#### Enrollment Routes — The Join Between Students & Courses

Enrollment is the most important resource — it links a Student to a Course. Creating an enrollment increments the course's enrolled_count. Deleting one frees a seat. This is where all three models interact with each other for the first time.

**routes/enrollments.py — Enrollment CRUD (references both Student & Course)**

```python
from fastapi import APIRouter, Depends
from schemas.enrollment_schema import EnrollmentCreate, EnrollmentResponse
from services.enrollment_service import EnrollmentService
from typing import List

router = APIRouter(prefix="/api/enrollments", tags=["Enrollments"])

def get_svc(db=Depends(get_db)): return EnrollmentService(db)

# CREATE — enroll student_id into course_id
# POST /api/enrollments  →  body: {"student_id": 42, "course_id": 7}
# Validates: student exists, course exists, seat available, not duplicate
@router.post("/", response_model=EnrollmentResponse, status_code=201)
async def enroll_student(data: EnrollmentCreate, svc=Depends(get_svc)):
    """Enroll a student into a course. Checks seat capacity and duplicates."""
    return svc.enroll(data)

# READ — get all enrollments for a specific student
# GET /api/enrollments?student_id=42
@router.get("/", response_model=List[EnrollmentResponse])
async def list_enrollments(student_id:int=None, course_id:int=None,
                           svc=Depends(get_svc)):
    return svc.list(student_id=student_id, course_id=course_id)

# DELETE — unenroll (frees a seat in the course)
# DELETE /api/enrollments/15
@router.delete("/{enrollment_id}", status_code=204)
async def unenroll_student(enrollment_id: int, svc=Depends(get_svc)):
    """Drop a course. This decrements the course enrolled_count."""
    svc.unenroll(enrollment_id)
```

#### Enrollment Service — Where Business Rules Live

The service layer is where we enforce the business rules: a student can't enroll in a course that's full, and can't enroll twice. Notice how StudentCreate, CourseResponse, and EnrollmentCreate from Step 1 are all used together here.

**services/enrollment_service.py — Business rules for enrollment**

```python
from fastapi import HTTPException
from sqlalchemy.orm import Session
from models.enrollment import EnrollmentORM
from models.student import StudentORM
from models.course import CourseORM
from schemas.enrollment_schema import EnrollmentCreate, EnrollmentResponse

class EnrollmentService:
    def __init__(self, db: Session):
        self.db = db

    def enroll(self, data: EnrollmentCreate) -> EnrollmentResponse:
        # 1. Verify student exists (uses student_id from EnrollmentCreate)
        student = self.db.query(StudentORM).filter(
            StudentORM.id == data.student_id).first()
        if not student:
            raise HTTPException(404, f"Student {data.student_id} not found")

        # 2. Verify course exists and has seats available
        course = self.db.query(CourseORM).filter(
            CourseORM.id == data.course_id).first()
        if not course:
            raise HTTPException(404, f"Course {data.course_id} not found")
        if course.enrolled_count >= course.max_seats:
            raise HTTPException(409, f"Course {course.code} is full")

        # 3. Check for duplicate enrollment
        existing = self.db.query(EnrollmentORM).filter(
            EnrollmentORM.student_id == data.student_id,
            EnrollmentORM.course_id == data.course_id).first()
        if existing:
            raise HTTPException(409, "Student already enrolled in this course")

        # 4. Create enrollment + increment seat counter
        enrollment = EnrollmentORM(**data.model_dump())
        enrollment.student_name = student.name
        enrollment.course_title = course.title
        self.db.add(enrollment)
        course.enrolled_count += 1   # update seat count atomically
        self.db.commit()
        self.db.refresh(enrollment)
        return EnrollmentResponse.model_validate(enrollment)

    def unenroll(self, enrollment_id: int) -> None:
        row = self.db.query(EnrollmentORM).filter(
            EnrollmentORM.id == enrollment_id).first()
        if not row:
            raise HTTPException(404, "Enrollment not found")
        # Free the seat back
        course = self.db.query(CourseORM).filter(
            CourseORM.id == row.course_id).first()
        if course: course.enrolled_count -= 1
        self.db.delete(row)
        self.db.commit()
```

#### Step 3 of 4 · LangGraph — AI Enrollment Assistant

The school principal wants a natural-language assistant: "Enroll Arjun in CS-101", "Show me all students in Grade 11", "Drop Priya from Physics-202". Instead of making the admin know exact IDs, the LangGraph agent parses the natural language, looks up the right IDs via the same FastAPI services, and performs the CRUD operation.

> **🔄 Java Analogy**
>
> Think of LangGraph as a smart controller that sits above FastAPI. The routes from Step 2 are still doing all the actual DB work — LangGraph just figures out which route to call and what data to pass based on what the user typed.

The agent state carries the same Pydantic models from Step 1. When the agent decides to enroll a student, it produces an EnrollmentCreate object — exactly what the EnrollmentService.enroll() from Step 2 expects. Everything connects.

**agent/enrollment_agent.py — LangGraph agent using the same Pydantic models**

```python
from typing import TypedDict, Literal
from langgraph.graph import StateGraph, END
from langchain_openai import ChatOpenAI
from langchain_core.messages import HumanMessage
from schemas.enrollment_schema import EnrollmentCreate     # same as Step 1!
from schemas.student_schema import StudentResponse         # same as Step 1!
from services.enrollment_service import EnrollmentService
from services.student_service import StudentService

# ── Agent State ──────────────────────────────────────────────────
# All the data the agent needs to complete one school operation
class SchoolAgentState(TypedDict):
    user_query: str          # raw text: "Enroll Arjun in CS-101"
    intent: str              # "enroll" | "list_students" | "drop" | "unknown"
    student_name: str        # extracted from query
    course_code: str         # extracted from query (e.g. "CS-101")
    student: StudentResponse | None  # resolved student (from DB)
    result: dict             # final output to return to user
    error: str | None

llm = ChatOpenAI(model="gpt-4o-mini", temperature=0)

# ── Node 1: Parse Intent ─────────────────────────────────────────
# Extracts what the user wants + which student + which course
def parse_intent(state: SchoolAgentState) -> dict:
    prompt = f"""
    You are an EduTrack assistant. Parse the admin query and respond ONLY in JSON.
    Query: "{state["user_query"]}"
    Respond with: {{"intent": "enroll"|"list_students"|"drop"|"unknown",
                    "student_name": "...", "course_code": "..."}}
    """
    response = llm.invoke([HumanMessage(content=prompt)])
    import json
    parsed = json.loads(response.content)
    return {"intent": parsed["intent"],
            "student_name": parsed.get("student_name", ""),
            "course_code": parsed.get("course_code", "")}

# ── Node 2: Resolve Student ──────────────────────────────────────
# Look up student by name — returns a StudentResponse (from Step 1)
def resolve_student(state: SchoolAgentState, db) -> dict:
    svc = StudentService(db)
    student = svc.find_by_name(state["student_name"])
    if not student:
        return {"error": f"No student named {state['student_name']} found",
                "result": {}}
    return {"student": student, "error": None}

# ── Node 3: Enroll ───────────────────────────────────────────────
# Uses EnrollmentCreate (same Pydantic model from Step 1)
# Calls EnrollmentService.enroll() (same service from Step 2)
def enroll_node(state: SchoolAgentState, db) -> dict:
    if state.get("error"): return {"result": {"error": state["error"]}}

    from services.course_service import CourseService
    course = CourseService(db).find_by_code(state["course_code"])
    if not course:
        return {"result": {"error": f"Course {state['course_code']} not found"}}

    # Build EnrollmentCreate — same model FastAPI uses for POST /enrollments
    payload = EnrollmentCreate(
        student_id=state["student"].id,
        course_id=course.id
    )
    enrollment_svc = EnrollmentService(db)
    result = enrollment_svc.enroll(payload)   # reuse the service from Step 2
    return {"result": result.model_dump(),
            "result_msg": f"Enrolled {state['student'].name} in {course.title}"}

# ── Node 4: Drop Enrollment ──────────────────────────────────────
def drop_node(state: SchoolAgentState, db) -> dict:
    if state.get("error"): return {"result": {"error": state["error"]}}
    enrollments = EnrollmentService(db).list(student_id=state["student"].id)
    target = next((e for e in enrollments if state["course_code"] in e.course_title), None)
    if not target:
        return {"result": {"error": "Enrollment not found to drop"}}
    EnrollmentService(db).unenroll(target.id)  # reuse service from Step 2
    return {"result": {"dropped": True, "student": state["student"].name}}

# ── Node 5: List Students ────────────────────────────────────────
def list_students_node(state: SchoolAgentState, db) -> dict:
    students = StudentService(db).get_all()   # reuse service from Step 2
    return {"result": {"students": [s.model_dump() for s in students]}}

# ── Router: Which node to visit? ─────────────────────────────────
def route_intent(state: SchoolAgentState) -> str:
    intent_map = {"enroll": "enroll", "drop": "drop",
                  "list_students": "list_students"}
    return intent_map.get(state["intent"], END)

# ── Build the Graph ───────────────────────────────────────────────
g = StateGraph(SchoolAgentState)
g.add_node("parse_intent", parse_intent)
g.add_node("resolve_student", resolve_student)
g.add_node("enroll", enroll_node)
g.add_node("drop", drop_node)
g.add_node("list_students", list_students_node)

g.set_entry_point("parse_intent")
g.add_edge("parse_intent", "resolve_student")
g.add_conditional_edges("resolve_student", route_intent,
    {"enroll": "enroll", "drop": "drop", "list_students": "list_students"})
for node in ["enroll", "drop", "list_students"]:
    g.add_edge(node, END)

school_agent = g.compile()

# ── Example Usage ────────────────────────────────────────────────
# result = school_agent.invoke({"user_query": "Enroll Arjun in CS-101", ...})
# result["result"]  →  {"id":15, "student_name":"Arjun", "course_title":"CS-101"}
```

#### Step 4 of 4 · LangFuse — Observe Every Operation End-to-End

Now we add full observability to everything we built in Steps 1-3. LangFuse traces the complete lifecycle of every enrollment operation: the FastAPI request, the LangGraph agent's decisions, the LLM call, the DB write, and the final result — all visible in one trace tree on the LangFuse dashboard.

> **💡 Tip**
>
> LangFuse uses the same student_id, course_id, and enrollment_id from Steps 1-3 as metadata on every trace. This means you can filter the dashboard by "student_id = 42" and see every operation ever performed on that student across REST and AI.

**agent/observed_enrollment_agent.py — Full observability added to Step 3 agent**

```python
from langfuse.decorators import observe, langfuse_context
from langfuse.callback import CallbackHandler
from schemas.enrollment_schema import EnrollmentCreate

# ── Wrap the FastAPI Enrollment endpoint with a trace ─────────────
# Add this decorator to the service method — not the route
@observe(name="fastapi.enroll_student")
def traced_enroll(data: EnrollmentCreate, db) -> dict:
    langfuse_context.update_current_trace(
        tags=["crud", "enrollment", "create"],
        metadata={
            "student_id": data.student_id,
            "course_id": data.course_id,
            "resource": "enrollment"
        }
    )
    # This calls the exact same EnrollmentService from Step 2
    result = EnrollmentService(db).enroll(data)
    langfuse_context.score_current_observation(name="success", value=1.0)
    return result.model_dump()

# ── Wrap the LangGraph agent invocation ──────────────────────────
# The CallbackHandler auto-traces every LLM call inside the graph
@observe(name="agent.enrollment_query")
def run_school_agent(query: str, db) -> dict:
    langfuse_context.update_current_trace(
        tags=["ai", "langgraph", "school-assistant"],
        metadata={"query": query}
    )

    # LangFuse callback traces all LLM calls inside the graph
    langfuse_handler = CallbackHandler(
        session_id="admin-session-001",
        trace_name="school-agent-run"
    )
    config = {"callbacks": [langfuse_handler]}

    # Invoke the graph from Step 3 — all nodes are now traced
    result = school_agent.invoke({
        "user_query": query,
        "intent": "", "student_name": "", "course_code": "",
        "student": None, "result": {}, "error": None
    }, config=config)

    # Score the AI result quality
    langfuse_context.score_current_observation(
        name="enrollment_success",
        value=1.0 if not result.get("error") else 0.0
    )
    return result["result"]

# ── Observe individual CRUD operations ───────────────────────────
@observe(name="crud.read_student")
def traced_get_student(student_id: int, db) -> dict:
    langfuse_context.update_current_trace(
        tags=["crud","read"], metadata={"student_id": student_id})
    student = StudentService(db).get_or_404(student_id)
    return student.model_dump()

@observe(name="crud.update_student")
def traced_update_student(student_id: int, data: dict, db) -> dict:
    langfuse_context.update_current_trace(
        tags=["crud","update"], metadata={"student_id": student_id, "fields": list(data.keys())})
    from schemas.student_schema import StudentUpdate
    updated = StudentService(db).update(student_id, StudentUpdate(**data))
    langfuse_context.score_current_observation(name="update_success", value=1.0)
    return updated.model_dump()

@observe(name="crud.delete_student")
def traced_delete_student(student_id: int, db) -> dict:
    langfuse_context.update_current_trace(
        tags=["crud","delete"], metadata={"student_id": student_id})
    StudentService(db).delete(student_id)
    return {"deleted": True, "student_id": student_id}
```

### 6.5 Putting It All Together — End-to-End Flow

Here is the complete journey of a single natural-language enrollment request — "Enroll Arjun in CS-101" — flowing through all four technologies simultaneously:

| # | Technology | What happens |
|---|---|---|
| 1 | Admin types | "Enroll Arjun in CS-101" → POST /api/agent/query |
| 2 | LangFuse | Opens trace: agent.enrollment_query, tags=["ai","langgraph"] |
| 3 | LangGraph | parse_intent node → LLM extracts intent="enroll", student_name="Arjun", course_code="CS-101" |
| 4 | LangFuse | Logs the LLM call span: model, tokens used, latency, input/output |
| 5 | LangGraph | resolve_student node → calls StudentService.find_by_name("Arjun") → returns StudentResponse(id=42) |
| 6 | Pydantic | StudentResponse validates the returned student object — fails fast if DB returned garbage |
| 7 | LangGraph | route_intent → "enroll" → go to enroll node |
| 8 | LangGraph | enroll_node → builds EnrollmentCreate(student_id=42, course_id=7) ← same Pydantic model as Step 1 |
| 9 | FastAPI | EnrollmentService.enroll() runs: checks student exists ✓, checks course exists ✓, checks seats available ✓, checks not duplicate ✓ |
| 10 | Pydantic | EnrollmentCreate validated again at service entry. EnrollmentResponse returned. |
| 11 | LangFuse | score_current_observation(name="enrollment_success", value=1.0) |
| 12 | LangFuse | Trace closed. Dashboard shows: 3 spans, 1 LLM call, 2 DB writes, 320ms total |

> **💡 Note**
>
> The same student_id=42 and course_id=7 appear in every step. LangFuse, LangGraph, FastAPI, and Pydantic are all working on the same piece of data — just handling different concerns: AI reasoning, HTTP transport, business validation, and observability.

### 6.6 Final Annotation Mapping

| Spring Boot | FastAPI / Python |
|---|---|
| @SpringBootApplication | FastAPI() + uvicorn main:app --reload |
| @RestController + @RequestMapping | APIRouter(prefix=...) + @router.get/post... |
| @RequestBody @Valid DTO | Pydantic model as fn param (auto-validated) |
| @PathVariable Long id | Path param: def fn(student_id: int) |
| @RequestParam int page | Query param: def fn(skip: int = 0) |
| @Service | Service class injected via Depends() |
| @Repository / JpaRepository | SQLAlchemy ORM + Repository class |
| @Autowired | Depends() |
| @Entity + @Column | SQLAlchemy Column definitions |
| @Valid + Bean Validation | Pydantic Field validators |
| @ControllerAdvice | @app.exception_handler() |
| HttpStatus.NOT_FOUND | HTTPException(status_code=404) |
| ResponseEntity.ok(body) | return body (response_model= handles it) |
| SpringDoc Swagger | Auto-generated at /docs — no config needed |
| application.properties | .env + os.getenv() |
| Micrometer + Actuator | LangFuse traces + @observe decorator |
| Spring State Machine | LangGraph StateGraph + nodes + edges |

## Chapter A: Quick Reference Cheatsheet

#### Install All Libraries

```
pip install fastapi uvicorn[standard] pydantic[email] \
            sqlalchemy psycopg2-binary alembic \
            langgraph langchain langchain-openai \
            langfuse python-dotenv
```

#### FastAPI Route Decorators

| Decorator | Spring Boot Annotation |
|---|---|
| @app.get("/path") | @GetMapping |
| @app.post("/path") | @PostMapping |
| @app.put("/path/{id}") | @PutMapping |
| @app.patch("/path/{id}") | @PatchMapping |
| @app.delete("/path/{id}") | @DeleteMapping |

#### Pydantic Field Constraints

| Field Constraint | Meaning |
|---|---|
| Field(gt=0) | greater than 0 (exclusive) |
| Field(ge=0) | greater than or equal to 0 |
| Field(lt=100) | less than 100 (exclusive) |
| Field(le=100) | less than or equal to 100 |
| Field(min_length=2) | minimum string length |
| Field(max_length=50) | maximum string length |
| Field(pattern=r"...") | regex pattern match |
| Field(default=None) | default value |
| Field(...) | required — no default allowed |

#### HTTP Status Codes

| Status Code | When to Use |
|---|---|
| 200 OK | Successful GET / PUT / PATCH |
| 201 Created | Successful POST (resource created) |
| 204 No Content | Successful DELETE |
| 400 Bad Request | Validation error / bad input |
| 401 Unauthorized | Missing / invalid auth token |
| 403 Forbidden | Valid token but no permission |
| 404 Not Found | Resource does not exist |
| 409 Conflict | Duplicate resource (e.g. email already taken) |
| 422 Unprocessable | Pydantic validation failure (FastAPI default) |
| 500 Internal Error | Unhandled server exception |

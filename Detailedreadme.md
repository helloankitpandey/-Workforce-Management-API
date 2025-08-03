# 🚀 Workforce Management System - Complete Project Documentation

## 📋 Table of Contents
- [Project Overview](#-project-overview)
- [Architecture & Design](#-architecture--design)
- [Technology Stack](#-technology-stack)

# 🚀 Workforce Management System - Complete Project Documentation

## 📋 Table of Contents
- [Project Overview](#-project-overview)
- [Architecture & Design](#-architecture--design)
- [Technology Stack](#-technology-stack)
- [Project Structure](#-project-structure)
- [Data Models](#-data-models)
- [API Endpoints](#-api-endpoints)
- [Business Logic](#-business-logic)
- [Known Issues & Bugs](#-known-issues--bugs)
- [Setup & Installation](#-setup--installation)
- [Testing Guide](#-testing-guide)
- [Development Guidelines](#-development-guidelines)
- [Future Enhancements](#-future-enhancements)

## 🎯 Project Overview

The **Workforce Management System** is a Spring Boot-based REST API application designed for managing tasks within an organization. It provides comprehensive task management capabilities including task creation, assignment, status updates, and filtering operations.

### Key Features
- ✅ **Task Management**: Create, read, update, and delete tasks
- 👥 **Assignment Management**: Assign tasks to specific employees
- 📅 **Date-based Filtering**: Retrieve tasks within specific date ranges
- 🔄 **Status Tracking**: Track task progress through different states
- 🎯 **Priority Management**: Handle tasks with different priority levels
- 📊 **Reference-based Operations**: Manage tasks based on reference entities

### Business Context
This system is designed for organizations that need to:
- Track work assignments across different departments
- Manage customer-related tasks (invoicing, pickup arrangements, payments)
- Handle entity-specific operations (customer assignments to sales personnel)
- Monitor task completion and deadlines

## 🏗️ Architecture & Design

### Architectural Pattern
The application follows a **layered architecture** pattern with clear separation of concerns:

```
┌─────────────────────────────────────┐
│           Controller Layer          │  ← REST API endpoints
├─────────────────────────────────────┤
│            Service Layer            │  ← Business logic
├─────────────────────────────────────┤
│          Repository Layer           │  ← Data access
├─────────────────────────────────────┤
│            Model Layer              │  ← Domain entities
└─────────────────────────────────────┘
```

### Design Patterns Used
1. **Repository Pattern**: Abstracts data access logic
2. **DTO Pattern**: Separates internal models from API contracts
3. **Mapper Pattern**: Converts between DTOs and domain models
4. **Service Layer Pattern**: Encapsulates business logic
5. **Exception Handling Pattern**: Centralized error management

### Key Design Decisions
- **In-Memory Storage**: Uses ConcurrentHashMap for data persistence (suitable for demo/testing)
- **MapStruct Integration**: Automatic mapping between DTOs and entities
- **Snake Case JSON**: API uses snake_case for JSON properties
- **Centralized Exception Handling**: Global exception handler for consistent error responses

## 🛠️ Technology Stack

### Core Technologies
- **Java 17**: Latest LTS version with modern language features
- **Spring Boot 3.2.4**: Latest Spring Boot with enhanced performance
- **Spring Web**: RESTful web services framework
- **Gradle 8.x**: Build automation and dependency management

### Libraries & Dependencies
- **Lombok**: Reduces boilerplate code with annotations
- **MapStruct 1.5.3**: Compile-time bean mapping
- **Jackson**: JSON serialization/deserialization
- **Spring Boot Starter Test**: Testing framework integration

### Development Tools
- **IntelliJ IDEA**: Recommended IDE (project includes .idea configuration)
- **Gradle Wrapper**: Ensures consistent build environment
- **Spring Boot DevTools**: Hot reloading during development

## 📁 Project Structure

```
src/main/java/com/railse/hiring/workforcemgmt/
├── 📁 common/                          # Shared components
│   ├── 📁 exception/                   # Exception handling
│   │   ├── CustomExceptionHandler.java # Global exception handler
│   │   ├── ResourceNotFoundException.java
│   │   └── StatusCode.java             # Error codes enum
│   └── 📁 model/
│       ├── 📁 enums/
│       │   └── ReferenceType.java      # Reference type enum
│       └── 📁 response/
│           ├── Pagination.java         # Pagination support
│           ├── Response.java           # Standard API response
│           └── ResponseStatus.java     # Response status wrapper
├── 📁 controller/                      # REST controllers
│   └── TaskManagementController.java   # Main API controller
├── 📁 dto/                            # Data Transfer Objects
│   ├── AssignByReferenceRequest.java   # Assignment request DTO
│   ├── TaskCreateRequest.java          # Task creation DTO
│   ├── TaskFetchByDateRequest.java     # Date filtering DTO
│   ├── TaskManagementDto.java          # Task response DTO
│   └── UpdateTaskRequest.java          # Task update DTO
├── 📁 mapper/                         # Object mapping
│   └── ITaskManagementMapper.java      # MapStruct mapper interface
├── 📁 model/                          # Domain models
│   ├── TaskManagement.java            # Main task entity
│   └── 📁 enums/
│       ├── Priority.java              # Task priority levels
│       ├── Task.java                  # Task types
│       └── TaskStatus.java            # Task status states
├── 📁 repository/                     # Data access layer
│   ├── InMemoryTaskRepository.java    # In-memory implementation
│   └── TaskRepository.java            # Repository interface
├── 📁 service/                        # Business logic
│   ├── TaskManagementService.java     # Service interface
│   └── 📁 impl/
│       └── TaskManagementServiceImpl.java # Service implementation
└── Application.java                   # Spring Boot main class
```

## 📊 Data Models

### Core Entity: TaskManagement

```java
public class TaskManagement {
    private Long id;                    // Unique identifier
    private Long referenceId;           // Reference to external entity
    private ReferenceType referenceType; // Type of reference (ENTITY/ORDER/ENQUIRY)
    private Task task;                  // Type of task to perform
    private String description;         // Task description
    private TaskStatus status;          // Current status
    private Long assigneeId;           // Assigned employee ID
    private Long taskDeadlineTime;     // Deadline timestamp
    private Priority priority;         // Task priority level
}
```

### Enumerations

#### ReferenceType
```java
public enum ReferenceType {
    ENTITY,   // Customer/Transporter
    ORDER,    // Order-related tasks
    ENQUIRY   // Enquiry-related tasks
}
```

#### Task Types
```java
public enum Task {
    ASSIGN_CUSTOMER_TO_SALES_PERSON(ENTITY),
    CREATE_INVOICE(ORDER),
    ARRANGE_PICKUP(ORDER),
    COLLECT_PAYMENT(ORDER)
}
```

#### TaskStatus
```java
public enum TaskStatus {
    ASSIGNED,   // Task assigned but not started
    STARTED,    // Work in progress
    COMPLETED,  // Task finished
    CANCELLED   // Task cancelled
}
```

#### Priority
```java
public enum Priority {
    LOW,
    MEDIUM,
    HIGH
}
```

## 🔌 API Endpoints

### Base URL: `http://localhost:8080/task-mgmt`

### 1. Get Task by ID
```http
GET /task-mgmt/{id}
```

**Response:**
```json
{
    "data": {
        "id": 1,
        "reference_id": 101,
        "reference_type": "ORDER",
        "task": "CREATE_INVOICE",
        "description": "This is a seed task.",
        "status": "ASSIGNED",
        "assignee_id": 1,
        "task_deadline_time": 1728192000000,
        "priority": "HIGH"
    },
    "status": {
        "code": "SUCCESS",
        "message": "Operation completed successfully"
    }
}
```

### 2. Create Tasks
```http
POST /task-mgmt/create
Content-Type: application/json
```

**Request Body:**
```json
{
    "requests": [
        {
            "reference_id": 105,
            "reference_type": "ORDER",
            "task": "CREATE_INVOICE",
            "assignee_id": 1,
            "priority": "HIGH",
            "task_deadline_time": 1728192000000
        }
    ]
}
```

**Response:**
```json
{
    "data": [
        {
            "id": 7,
            "reference_id": 105,
            "reference_type": "ORDER",
            "task": "CREATE_INVOICE",
            "description": "New task created.",
            "status": "ASSIGNED",
            "assignee_id": 1,
            "task_deadline_time": 1728192000000,
            "priority": "HIGH"
        }
    ],
    "status": {
        "code": "SUCCESS",
        "message": "Operation completed successfully"
    }
}
```

### 3. Update Task Status
```http
POST /task-mgmt/update
Content-Type: application/json
```

**Request Body:**
```json
{
    "requests": [
        {
            "task_id": 1,
            "task_status": "STARTED",
            "description": "Work has been started on this invoice."
        }
    ]
}
```

### 4. Assign Tasks by Reference
```http
POST /task-mgmt/assign-by-ref
Content-Type: application/json
```

**Request Body:**
```json
{
    "reference_id": 201,
    "reference_type": "ENTITY",
    "assignee_id": 5
}
```

**Response:**
```json
{
    "data": "Tasks assigned successfully for reference 201",
    "status": {
        "code": "SUCCESS",
        "message": "Operation completed successfully"
    }
}
```

### 5. Fetch Tasks by Date Range
```http
POST /task-mgmt/fetch-by-date/v2
Content-Type: application/json
```

**Request Body:**
```json
{
    "start_date": 1672531200000,
    "end_date": 1735689599000,
    "assignee_ids": [1, 2]
}
```

## 🧠 Business Logic

### Task Creation Flow
1. **Validation**: Verify task type is compatible with reference type
2. **Default Values**: Set status to "ASSIGNED" and default description
3. **ID Generation**: Auto-generate unique task ID
4. **Persistence**: Save to repository
5. **Response Mapping**: Convert to DTO for API response

### Task Assignment Logic
1. **Reference Validation**: Check if reference exists
2. **Task Type Resolution**: Get applicable tasks for reference type
3. **Existing Task Check**: Find existing incomplete tasks
4. **Assignment Strategy**: 
   - If tasks exist: Reassign to new assignee
   - If no tasks: Create new tasks for all applicable types

### Status Update Flow
1. **Task Lookup**: Find task by ID
2. **Status Validation**: Ensure valid status transition
3. **Update Fields**: Modify status and description if provided
4. **Persistence**: Save changes
5. **Response**: Return updated task details

### Date-based Filtering
1. **Assignee Filter**: Get tasks for specified assignees
2. **Date Range Filter**: Filter by task deadline (currently incomplete)
3. **Status Filter**: Should exclude cancelled tasks (currently buggy)
4. **Result Mapping**: Convert to DTOs

## 🐛 Known Issues & Bugs

### Bug #1: Assign by Reference Issue
**Location**: `TaskManagementServiceImpl.assignByReference()`

**Problem**: When assigning tasks by reference, the system reassigns ALL existing incomplete tasks instead of assigning one and cancelling the rest.

**Current Behavior**:
```java
// BUG: Reassigns ALL tasks
for (TaskManagement taskToUpdate : tasksOfType) {
    taskToUpdate.setAssigneeId(request.getAssigneeId());
    taskRepository.save(taskToUpdate);
}
```

**Expected Behavior**: Should assign the first task and cancel the remaining ones.

**Impact**: Multiple assignees can have the same task, leading to confusion and duplicate work.

### Bug #2: Fetch by Date Includes Cancelled Tasks
**Location**: `TaskManagementServiceImpl.fetchTasksByDate()`

**Problem**: The date filtering endpoint incorrectly includes cancelled tasks in the results.

**Current Behavior**:
```java
// BUG: Doesn't filter out CANCELLED tasks
List<TaskManagement> filteredTasks = tasks.stream()
    .filter(task -> {
        return true; // Returns all tasks
    })
    .collect(Collectors.toList());
```

**Expected Behavior**: Should exclude tasks with status "CANCELLED" and properly filter by date range.

**Impact**: Users see cancelled tasks in their active task lists, causing confusion.

### Additional Issues
1. **Date Range Filtering**: Not implemented in fetch-by-date endpoint
2. **Task Validation**: No validation for task-reference type compatibility
3. **Concurrent Access**: In-memory repository may have race conditions
4. **Error Messages**: Generic error messages don't provide specific context

## 🚀 Setup & Installation

### Prerequisites
- **Java 17** or higher
- **Gradle 8.x** (or use included wrapper)
- **IDE**: IntelliJ IDEA, VSCode, or Eclipse

### Installation Steps

1. **Clone/Download Project**
   ```bash
   # If using Git
   git clone <repository-url>
   cd workforce-management
   ```

2. **Verify Java Version**
   ```bash
   java -version
   # Should show Java 17 or higher
   ```

3. **Build Project**
   ```bash
   # Using Gradle wrapper (recommended)
   ./gradlew build
   
   # Or using system Gradle
   gradle build
   ```

4. **Run Application**
   ```bash
   # Using Gradle
   ./gradlew bootRun
   
   # Or run the JAR
   java -jar build/libs/whydoidothis-0.0.1-SNAPSHOT.jar
   
   # Or from IDE: Run Application.java main method
   ```

5. **Verify Installation**
   ```bash
   curl http://localhost:8080/task-mgmt/1
   ```

### Configuration

**Application Properties** (`src/main/resources/application.properties`):
```properties
server.port=8080
```

**Build Configuration** (`build.gradle`):
- Spring Boot 3.2.4
- Java 17 compatibility
- MapStruct for object mapping
- Lombok for code generation

## 🧪 Testing Guide

### Manual Testing with cURL

#### 1. Test Get Task
```bash
curl --location 'http://localhost:8080/task-mgmt/1'
```

#### 2. Test Create Task
```bash
curl --location 'http://localhost:8080/task-mgmt/create' \
--header 'Content-Type: application/json' \
--data '{
    "requests": [
        {
            "reference_id": 999,
            "reference_type": "ORDER",
            "task": "CREATE_INVOICE",
            "assignee_id": 10,
            "priority": "HIGH",
            "task_deadline_time": 1728192000000
        }
    ]
}'
```

#### 3. Test Update Task
```bash
curl --location 'http://localhost:8080/task-mgmt/update' \
--header 'Content-Type: application/json' \
--data '{
    "requests": [
        {
            "task_id": 1,
            "task_status": "STARTED",
            "description": "Started working on invoice creation."
        }
    ]
}'
```

#### 4. Test Assignment (Bug #1)
```bash
curl --location 'http://localhost:8080/task-mgmt/assign-by-ref' \
--header 'Content-Type: application/json' \
--data '{
    "reference_id": 201,
    "reference_type": "ENTITY",
    "assignee_id": 5
}'
```

#### 5. Test Date Filtering (Bug #2)
```bash
curl --location 'http://localhost:8080/task-mgmt/fetch-by-date/v2' \
--header 'Content-Type: application/json' \
--data '{
    "start_date": 1672531200000,
    "end_date": 1735689599000,
    "assignee_ids": [1, 2]
}'
```

### Seed Data
The application comes with pre-loaded test data:

| ID | Reference | Type | Task | Assignee | Status | Priority |
|----|-----------|------|------|----------|--------|----------|
| 1 | 101 | ORDER | CREATE_INVOICE | 1 | ASSIGNED | HIGH |
| 2 | 101 | ORDER | ARRANGE_PICKUP | 1 | COMPLETED | HIGH |
| 3 | 102 | ORDER | CREATE_INVOICE | 2 | ASSIGNED | MEDIUM |
| 4 | 201 | ENTITY | ASSIGN_CUSTOMER_TO_SALES_PERSON | 2 | ASSIGNED | LOW |
| 5 | 201 | ENTITY | ASSIGN_CUSTOMER_TO_SALES_PERSON | 3 | ASSIGNED | LOW |
| 6 | 103 | ORDER | COLLECT_PAYMENT | 1 | CANCELLED | MEDIUM |

### Testing Scenarios

#### Scenario 1: Normal Task Creation
- Create a new task with valid data
- Verify task is assigned with correct defaults
- Check response format and status codes

#### Scenario 2: Task Status Updates
- Update task status from ASSIGNED to STARTED
- Update task description
- Verify changes are persisted

#### Scenario 3: Bug #1 Reproduction
- Use reference_id: 201 (has duplicate tasks)
- Assign to a new assignee
- Observe that both tasks get reassigned instead of one being cancelled

#### Scenario 4: Bug #2 Reproduction
- Fetch tasks for assignee_ids: [1]
- Notice that cancelled task (ID: 6) is included in results
- Verify date filtering is not working

## 👨‍💻 Development Guidelines

### Code Style
- **Java Naming**: CamelCase for classes, camelCase for methods/variables
- **Package Structure**: Feature-based packaging
- **Annotations**: Use Lombok for reducing boilerplate
- **JSON Naming**: snake_case for API contracts

### Best Practices
1. **Separation of Concerns**: Keep controllers thin, services focused
2. **Error Handling**: Use custom exceptions with meaningful messages
3. **Validation**: Validate input at controller level
4. **Documentation**: Comment complex business logic
5. **Testing**: Write unit tests for service layer

### Adding New Features

#### 1. Add New Task Type
```java
// In Task.java enum
NEW_TASK_TYPE(List.of(ReferenceType.ORDER), "Description");
```

#### 2. Add New Endpoint
```java
// In TaskManagementController.java
@PostMapping("/new-endpoint")
public Response<DataType> newEndpoint(@RequestBody RequestDto request) {
    return new Response<>(service.newOperation(request));
}
```

#### 3. Add New Business Logic
```java
// In TaskManagementService.java interface
DataType newOperation(RequestDto request);

// In TaskManagementServiceImpl.java
@Override
public DataType newOperation(RequestDto request) {
    // Implementation
}
```

### Database Migration (Future)
When moving from in-memory to persistent storage:
1. Add JPA dependencies
2. Convert TaskManagement to @Entity
3. Replace InMemoryTaskRepository with JPA repository
4. Add database configuration

## 🔮 Future Enhancements

### High Priority
1. **Fix Known Bugs**: Address Bug #1 and Bug #2
2. **Database Integration**: Replace in-memory storage with PostgreSQL/MySQL
3. **Authentication**: Add JWT-based authentication
4. **Input Validation**: Add comprehensive request validation
5. **Date Range Filtering**: Complete the date filtering implementation

### Medium Priority
1. **Pagination**: Add pagination support for large datasets
2. **Sorting**: Allow sorting by different fields
3. **Audit Trail**: Track task changes and history
4. **Notifications**: Email/SMS notifications for task assignments
5. **Bulk Operations**: Support bulk task operations

### Low Priority
1. **Task Dependencies**: Support for dependent tasks
2. **Task Templates**: Predefined task templates
3. **Reporting**: Task completion reports and analytics
4. **File Attachments**: Support for task-related documents
5. **Comments**: Task comments and collaboration features

### Technical Improvements
1. **Caching**: Redis caching for frequently accessed data
2. **Monitoring**: Application metrics and health checks
3. **API Documentation**: Swagger/OpenAPI integration
4. **Docker Support**: Containerization for easy deployment
5. **CI/CD Pipeline**: Automated testing and deployment

### Performance Optimizations
1. **Database Indexing**: Optimize database queries
2. **Connection Pooling**: Efficient database connections
3. **Async Processing**: Background task processing
4. **Load Balancing**: Support for horizontal scaling

## 📝 Notes

### Project Context
This appears to be a **take-home assignment** for a Backend Engineer position, focusing on:
- Spring Boot proficiency
- REST API design
- Bug identification and fixing
- Code organization and best practices

### Assignment Objectives
Based on the code structure and comments, the assignment likely tests:
1. **Bug Fixing Skills**: Identifying and resolving the two documented bugs
2. **Code Understanding**: Comprehending existing codebase architecture
3. **API Testing**: Using provided cURL commands for testing
4. **Documentation**: Creating clear documentation (like this README)

### Submission Requirements
The `SUBMISSION.md` file suggests candidates need to provide:
1. Git repository branch link
2. Video demonstration link
3. Likely explanation of bug fixes and improvements

---

**Created by**: Backend Engineering Candidate  
**Last Updated**: August 2025  
**Version**: 1.0.0  
**License**: Private (Assignment Purpose)


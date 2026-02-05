# Lab 4: Full-Stack Project Management App

**ITWS 4500 - Modern Application Development**
**Rensselaer Polytechnic Institute**

## Overview

In this lab, your team will build a **Project Management application from scratch**. Unlike previous labs where you filled in pre-written functions, you will design and implement the entire application: frontend, backend, and database schema.

The application manages **Projects** and **Tasks**, where tasks belong to projects. You'll implement full CRUD (Create, Read, Update, Delete) operations for both entities.

**Total Points: 100**

---

## Learning Objectives

By completing this lab, you will:

1. **Build a Next.js frontend from scratch** - Create React components, pages, and API integration
2. **Build an Express API from scratch** - Design routes, controllers, and middleware
3. **Design a MongoDB schema** - Model relationships between Projects and Tasks
4. **Practice collaborative git workflows** - Use feature branches, pull requests, and code review
5. **Present technical solutions** - Explain your implementation decisions to peers

---

## Flipped Classroom Structure

This lab uses a **flipped classroom** approach. Teams work independently, and individuals present key solutions during class sessions.

### Presentation Schedule

| Session | Topic | Presenter Selection | Points |
|---------|-------|---------------------|--------|
| **Session 1** | Database Schema Design | 1 student per team presents their Mongoose models | 5 |
| **Session 2** | API Architecture | 1 student per team explains their route/controller structure | 5 |
| **Session 3** | Frontend State Management | 1 student per team shows how they handle data fetching and state | 5 |
| **Session 4** | Integration Demo | Full team demos working application | 5 |

### Presentation Requirements

Each presentation should be **3-5 minutes** and cover:
- What you built
- Key design decisions and tradeoffs
- One challenge you faced and how you solved it
- Live demo or code walkthrough

**Note:** Different team members must present each session. All members present at least once.

---

## Prerequisites

- Completed Labs 1-3
- Node.js v18+ and npm installed
- Docker and Docker Compose running
- Git and GitHub account
- Understanding of React, Express, and MongoDB fundamentals

---

## Team Structure

Each team (3-4 members) works independently on their own application. Within your team:

- Divide work across feature branches
- All code changes must go through Pull Requests
- At least one teammate must review and approve each PR
- The `main` branch is protected - no direct pushes

---

## Application Requirements

### Data Model

```
Project
├── _id: ObjectId
├── name: String (required)
├── description: String
├── createdAt: Date
└── updatedAt: Date

Task
├── _id: ObjectId
├── projectId: ObjectId (required, references Project)
├── title: String (required)
├── description: String
├── status: String (enum: "todo", "in-progress", "done")
├── createdAt: Date
└── updatedAt: Date
```

### Required Features

#### Projects
- View list of all projects
- Create a new project
- View single project with its tasks
- Update project details
- Delete project (and associated tasks)

#### Tasks
- View tasks within a project
- Create a new task in a project
- Update task details and status
- Delete a task

---

## Project Structure

```
├── client/                     # Next.js Frontend
│   ├── src/
│   │   ├── app/               # Next.js App Router pages
│   │   ├── components/        # React components
│   │   └── lib/               # Utilities, API client
│   ├── package.json
│   └── Dockerfile
│
├── server/                     # Express Backend
│   ├── src/
│   │   ├── index.js           # Server entry point
│   │   ├── config/            # Database configuration
│   │   ├── models/            # Mongoose models
│   │   ├── routes/            # API route definitions
│   │   ├── controllers/       # Request handlers
│   │   └── middleware/        # Error handling, validation
│   ├── package.json
│   └── Dockerfile
│
├── .github/
│   └── workflows/
│       └── ci.yml             # GitHub Actions CI
│
├── docker-compose.yml          # Container orchestration
├── API.md                      # API contract specification
├── answers.yml                 # Your responses
└── README.md                   # This file
```

---

## Git Workflow Requirements

### Branch Structure

Your repository must use the following branch structure:

```
main (protected)
├── feature/projects-backend    # Projects API endpoints
├── feature/projects-frontend   # Projects UI components
├── feature/tasks-backend       # Tasks API endpoints
├── feature/tasks-frontend      # Tasks UI components
└── feature/integration         # Final integration work
```

### Pull Request Rules

1. **No direct commits to `main`** - All changes via PR
2. **Minimum 1 approval required** - Teammate must review
3. **Descriptive PR titles** - e.g., "Add project creation endpoint"
4. **Link related issues** - If using GitHub Issues

### Graded Git Practices

We will evaluate:
- Meaningful commit messages
- Logical branch organization
- PR descriptions and review comments
- All team members contributing via PRs

---

## Part 1: Project Setup & Database (15 points)

**Presentation Topic: Session 1 - Schema Design**

### 1.1 Initialize the Project (5 points)
- Set up the repository structure
- Configure `docker-compose.yml` with MongoDB
- Verify database connectivity

### 1.2 Create Mongoose Models (10 points)
- Implement `Project` model with validation
- Implement `Task` model with project reference
- Add timestamps to both models

**Deliverables:**
- Working `docker-compose up` command
- `server/src/models/Project.js`
- `server/src/models/Task.js`

**Presentation Focus:** Explain your schema design choices. Why did you structure the relationship this way? What validations did you add and why?

---

## Part 2: Backend API (30 points)

**Presentation Topic: Session 2 - API Architecture**

Implement the REST API according to the specification in `API.md`.

### 2.1 Projects API (15 points)

| Endpoint | Method | Description | Points |
|----------|--------|-------------|--------|
| `/api/projects` | GET | List all projects | 3 |
| `/api/projects` | POST | Create project | 3 |
| `/api/projects/:id` | GET | Get single project | 3 |
| `/api/projects/:id` | PUT | Update project | 3 |
| `/api/projects/:id` | DELETE | Delete project | 3 |

### 2.2 Tasks API (15 points)

| Endpoint | Method | Description | Points |
|----------|--------|-------------|--------|
| `/api/projects/:projectId/tasks` | GET | List tasks in project | 3 |
| `/api/projects/:projectId/tasks` | POST | Create task | 3 |
| `/api/tasks/:id` | GET | Get single task | 3 |
| `/api/tasks/:id` | PUT | Update task | 3 |
| `/api/tasks/:id` | DELETE | Delete task | 3 |

**Requirements:**
- Proper HTTP status codes (200, 201, 400, 404, 500)
- JSON request/response bodies
- Error handling middleware
- Input validation

**Presentation Focus:** Walk through your route organization and controller pattern. How do you handle errors? Show a request/response cycle.

---

## Part 3: Frontend - Projects (20 points)

**Presentation Topic: Session 3 - State Management**

Build the Projects UI using Next.js App Router.

### 3.1 Projects List Page (8 points)
- Display all projects in a list or grid
- Link to individual project pages
- "Create Project" button

### 3.2 Project Detail Page (6 points)
- Show project name and description
- Display tasks belonging to project (from Part 4)
- Edit and Delete buttons

### 3.3 Project Forms (6 points)
- Create project form with validation
- Edit project form (pre-populated)
- Handle form submission and errors

**Routes:**
- `/projects` - Projects list
- `/projects/new` - Create project
- `/projects/[id]` - Project detail
- `/projects/[id]/edit` - Edit project

**Presentation Focus:** Explain how you fetch and manage data. Do you use hooks? How do you handle loading and error states?

---

## Part 4: Frontend - Tasks (20 points)

Build the Tasks UI within project context.

### 4.1 Tasks List (8 points)
- Display tasks within project detail page
- Show task status with visual indicator
- "Add Task" button

### 4.2 Task Forms (6 points)
- Create task form with project association
- Edit task form with status dropdown
- Form validation and error handling

### 4.3 Task Actions (6 points)
- Update task status (todo/in-progress/done)
- Delete task with confirmation
- Inline editing or modal (your choice)

---

## Part 5: Integration & Polish (10 points)

**Presentation Topic: Session 4 - Integration Demo**

### 5.1 Navigation & Layout (4 points)
- Consistent header/navigation
- Loading states while fetching data
- Error boundaries for failures

### 5.2 Docker Integration (3 points)
- Client Dockerfile works
- Server Dockerfile works
- Full stack runs with `docker-compose up`

### 5.3 Code Quality (3 points)
- Consistent code formatting
- No console errors/warnings
- README updated with setup instructions

**Presentation Focus:** Live demo of full application. Show the complete user flow from creating a project to managing tasks.

---

## Part 6: Conceptual Questions (5 points)

Answer these questions in `answers.yml`:

**Q1: Schema Design (1 point)**
Why did you choose to store `projectId` in the Task model rather than storing an array of task IDs in the Project model? What are the tradeoffs?

**Q2: API Design (1 point)**
Explain your choice of nested routes (`/projects/:id/tasks`) vs flat routes (`/tasks?projectId=xxx`). What are the advantages of your approach?

**Q3: State Management (1 point)**
How did you manage state in your Next.js frontend? Did you use any global state management, or rely on component-level state and data fetching?

**Q4: Git Collaboration (2 points)**
Describe a merge conflict your team encountered and how you resolved it. If you had no conflicts, explain what practices helped you avoid them.

---

## Getting Started

### 1. Clone and Setup

```bash
# Clone your team's repository
git clone <your-repo-url>
cd <repo-name>

# Install dependencies
npm run install:all
```

### 2. Start Development Environment

```bash
# Start all services (MongoDB, server, client)
docker-compose up

# Or run locally:
# Terminal 1: Start MongoDB
docker-compose up mongo

# Terminal 2: Start server
cd server && npm run dev

# Terminal 3: Start client
cd client && npm run dev
```

### 3. Verify Setup

- MongoDB: `mongodb://localhost:27017`
- Server API: `http://localhost:3000`
- Client: `http://localhost:3001`
- Mongo Express (DB UI): `http://localhost:8081`

### 4. Create Your First Branch

```bash
git checkout -b feature/projects-backend
# Make changes, commit, push
git push -u origin feature/projects-backend
# Create PR on GitHub
```

---

## Grading Rubric

| Part | Points | Description |
|------|--------|-------------|
| Part 1: Setup & Database | 15 | Docker, models, connectivity |
| Part 2: Backend API | 30 | Projects + Tasks CRUD endpoints |
| Part 3: Frontend Projects | 20 | List, detail, forms |
| Part 4: Frontend Tasks | 20 | List, forms, actions |
| Part 5: Integration | 10 | Navigation, Docker, quality |
| Part 6: Questions | 5 | Conceptual answers |
| **Subtotal** | **100** | |

### Presentation Points (Bonus)

| Presentation | Points |
|--------------|--------|
| Session 1: Schema Design | +5 |
| Session 2: API Architecture | +5 |
| Session 3: State Management | +5 |
| Session 4: Integration Demo | +5 |
| **Max Presentation Bonus** | **+20** |

### Git Workflow Adjustments

- **+5 points:** Excellent PR history with meaningful reviews
- **-10 points:** Direct commits to main (bypassing PRs)
- **-5 points:** Single team member did >80% of commits

---

## Submission Requirements

### Required in Repository

1. **`answers.yml`** - Completed with all responses
2. **`server/`** - Working Express API
3. **`client/`** - Working Next.js frontend
4. **`docker-compose.yml`** - Full stack runs
5. **PR History** - Visible branch/merge workflow

### Submission Checklist

- [ ] All CRUD operations work for Projects
- [ ] All CRUD operations work for Tasks
- [ ] `docker-compose up` starts entire application
- [ ] At least 4 merged PRs from different branches
- [ ] All team members have commits
- [ ] `answers.yml` completed
- [ ] No secrets committed (.env in .gitignore)
- [ ] Each team member presented at least once

---

## Troubleshooting

### MongoDB Connection Failed
```bash
# Check if MongoDB container is running
docker ps

# View MongoDB logs
docker-compose logs mongo

# Restart containers
docker-compose down && docker-compose up
```

### Port Already in Use
```bash
# Find process using port
lsof -i :3000
lsof -i :3001

# Kill process
kill -9 <PID>
```

### Next.js Cache Issues
```bash
cd client
rm -rf .next
npm run dev
```

### Branch Protection Issues
Make sure you're creating PRs, not pushing directly to main. Check repository Settings > Branches for protection rules.

---

## Resources

- [Next.js Documentation](https://nextjs.org/docs)
- [Express.js Guide](https://expressjs.com/en/guide/routing.html)
- [Mongoose Documentation](https://mongoosejs.com/docs/)
- [GitHub Pull Requests](https://docs.github.com/en/pull-requests)
- [REST API Design](https://restfulapi.net/)

---

## Academic Integrity

This is a team assignment. You may:
- Collaborate fully within your team
- Use documentation and official resources
- Ask clarifying questions on Piazza

You may not:
- Share code between teams
- Copy code from other teams or online solutions
- Use AI to generate entire solutions without understanding

All team members must contribute meaningfully. We review git history.

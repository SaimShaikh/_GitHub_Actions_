## What is GitHub Workflow ?

A GitHub workflow is an automated process defined in a YAML file that runs when a specific event happens in a GitHub repository.


## 📂 Where Is a Workflow Stored?

## Every workflow must be stored inside Your Project Directory (Root Location):  
` .github/workflows/ `


## 🧠 What does “One file = one workflow” mean?

Each YAML file inside .github/workflows/ represents ONE separate workflow. 

📂 Example Folder:
```bash
.github/
└── workflows/
    ├── ci.yml
    ├── cd.yml
    └── security-scan.yml
```

but we can multipe task in one YAMl file also without any issue .
for eg:

```bash
name: CI-CD Workflow

on:
  push:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - run: echo "Building app"

  test:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - run: echo "Running tests"

  deploy:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - run: echo "Deploying app"

```


## Workflow Components 

1. Events: Triggers decide when to  start the workflow 
Types of Events : 

- push : The workflow runs whenever code is pushed to the repository.

- pull_request : The workflow runs when a pull request is created, updated, or reopened.

- workflow_dispatch : The workflow can be started manually from the GitHub UI.

- schedule : The workflow runs at a specific time, like a cron job.


2. Jobs: A job is a set of steps that run together on the same runner (machine) inside a GitHub workflow.

- Jobs Run on a Runner : runs-on: ubuntu-latest (That runner is created when the job starts and destroyed after it finishes (for GitHub-hosted runners).)

- Jobs Contain Steps :
```bash
steps:
  - run: npm install
  - run: npm test
```
- Jobs Run in Parallel by Default : If a workflow has multiple jobs, they run at the same time unless you control the order.

``` bash
jobs:
  build:
    runs-on: ubuntu-latest

  test:
    runs-on: ubuntu-latest

``` 

- Job Dependencies (needs) : Use needs to control execution order. (👉 deploy runs only after build succeeds.)

```bash
jobs:
  build:
    runs-on: ubuntu-latest

  deploy:
    needs: build
    runs-on: ubuntu-latest

```

- Jobs Are Isolated

  - Each job runs on a fresh environment
  - Files from one job are not shared with another job
  - To share data, use artifacts or cache
 
3.  Runner :  A runner is a machine that executes your job in a GitHub workflow.

   - 🔧 Why Do We Need a Runner?

       - Your workflow has jobs
       - Jobs need a machine to run commands
       - That machine is the runner

Types of Runner :

1. GitHub-hosted runners are ideal for most users, offering automatic scaling, maintenance, and preconfigured environments.  They are free for public repositories and available with limited minutes for private ones. Larger runners with more CPU, RAM, or GPU support are available for enterprise plans.

## eg
- runs-on: ubuntu-latest
- runs-on: windows-latest
- runs-on: macos-latest

Best for:

- CI pipelines

- Small to medium projects

- Quick setup

2. Self-hosted runners provide greater control over hardware, software, and security.  They are useful when you need specific dependencies, persistent storage, custom configurations, or to run long-running or resource-intensive jobs like end-to-end testing. They are free to use with GitHub Actions, but you are responsible for maintaining the underlying infrastructure.

eg AWS EC2
- runs-on: self-hosted


Best for:
- Your own machine (VM, server, EC2, laptop)

- You manage OS, tools, security

- More control, more responsibility

4. Steps : GitHub Steps are the individual tasks or commands that make up a job in a GitHub Actions workflow.  Each step runs sequentially within the same job and shares the same runner environment, allowing data to be passed between steps using the filesystem or environment variables. 

## 📍 Where Do Steps Exist?

Steps live inside a job.

```bash
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - run: echo "This is a step"
```

## What Can a Step Do?

A step can do two main things:
1️⃣ Run a Command (run)

Executes shell commands.

- run: npm install

2️⃣ Use an Action (uses)

Runs a reusable action (prebuilt logic).

- uses: actions/checkout@v4

  This step:

- Clones your repository

- Saves you from writing Git commands


5. Environment Variables : Environment variables are key–value pairs used to store configuration data that your workflow, job, or step can use.

## Levels of ENV

- Workflow-Level env

Available to all jobs and steps in the workflow. Use when the value is common for the whole workflow.

env:
  APP_ENV: production
  REGION: ap-south-1

- Job-Level env

Available to all steps inside one job only. Other jobs cannot access this variable.

jobs:
  build:
    runs-on: ubuntu-latest
    env:
      NODE_ENV: development

- Step-Level env

Available to only one specific step. Most restricted scope → safest.

- name: Run tests
  run: npm test
  env:
    NODE_ENV: test


6. Secrets : Secrets are encrypted, sensitive values used in workflows, such as passwords, tokens, and keys.

🤔 Why Do We Need Secrets?

- Because you should never hardcode sensitive data in code or YAML files.

- Secrets are used for:

- AWS access keys

- Docker Hub passwords

- API tokens

- Database credentials

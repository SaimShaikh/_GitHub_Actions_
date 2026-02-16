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
 
3.  

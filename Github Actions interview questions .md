
## Q1. What is the difference between on: push and on: pull_request, and how would you trigger a workflow only when a specific file type—like a .js file—is modified?

on: push triggers the workflow whenever code is pushed to a branch. For example, if a developer commits code and runs git push to the main or any configured branch, the workflow will start automatically. This is commonly used for tasks like building the application, running tests, or deploying code.

on: pull_request triggers the workflow when a pull request is created or updated. For example, when a developer opens a PR to merge code into the main branch, the workflow runs to validate the changes. This is useful for running tests, code quality checks, or security scans before the code is merged.

If we want the workflow to run only when a specific file type like .js is modified, we can use the paths filter in the workflow trigger.

```bash
on:
  push:
    paths:
      - '**.js'

```



---


## Q2. In what scenario would you recommend a Self-Hosted Runner over a GitHub-hosted runner, and what is the primary security concern with self-hosted runners on public repositories?

I would use a self-hosted runner when the workflow needs custom environments, internal network access, or specific hardware that GitHub-hosted runners don’t provide.
The main security risk in public repositories is that untrusted pull request code could execute on the runner and potentially access internal resources or secrets.


---

## Q3. How do you securely pass a Docker Hub password to a workflow, and why should you never use echo to print that secret for debugging?

To securely pass a Docker Hub password, I store it in GitHub Secrets and reference it in the workflow using ${{ secrets.SECRET_NAME }} during the Docker login step.
We should never print secrets using echo because it exposes sensitive credentials in the workflow logs, which could lead to a security breach.”

---

## Q4. What is a Matrix Strategy, and how does it help in cross-platform compatibility testing?

A Matrix Strategy allows a workflow job to run multiple times with different configurations, such as different operating systems, language versions, or environments. It helps in cross-platform testing because the same tests can run automatically on multiple platforms like Linux, Windows, and macOS, ensuring the application works correctly everywhere.

---

## Q5. What is the fundamental difference between creating a Custom Action and a Reusable Workflow?

A Custom Action is used to package a specific task or piece of logic so it can be reused across workflows. It’s like creating a small tool that performs a particular function, such as building a Docker image, sending a notification, or running a script. Custom actions are usually written using JavaScript or Docker, and then they can be referenced in workflows as a step.

On the other hand, a Reusable Workflow allows you to reuse an entire workflow configuration. Instead of copying the same CI/CD pipeline across multiple repositories, you define the workflow once and call it from other workflows using workflow_call. This helps maintain consistency and reduces duplication in pipelines.

--- 

## Q6. What are GitHub Actions Contexts, and how would you use the github context to make a workflow run only if the branch name is main?

GitHub Actions contexts provide runtime information about the workflow, like repository details, event data, and branch information. Using the github context, we can access the branch name and add a condition like if: github.ref == 'refs/heads/main' to ensure the workflow runs only when the main branch triggers it.

---

## Q7. How do you speed up a Node.js workflow that installs hundreds of npm packages every time it runs?

To speed up a Node.js workflow that installs many npm packages, I would use dependency caching in GitHub Actions. By caching npm dependencies based on the package-lock.json file, the workflow can reuse previously downloaded packages instead of installing them again, which significantly reduces build time.


---

## Q8. You want to ensure that a deployment to "Production" only happens after a Lead Engineer manually approves it. How do you configure this?

To require approval before deploying to Production, I would configure a Production environment with required reviewers in GitHub repository settings. Then in the workflow, I reference that environment in the deployment job. When the job runs, GitHub pauses the workflow until the Lead Engineer approves the deployment.

---

## Q9. By default, jobs in a workflow run in parallel. How do you make a deploy job wait until a test job completes successfully?

To make a deploy job wait for a test job, I use the needs keyword. This creates a dependency so the deploy job runs only after the test job completes successfully.

```bash
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - name: Run tests
        run: npm test

  deploy:
    runs-on: ubuntu-latest
    needs: test
    steps:
      - name: Deploy application
        run: ./deploy.sh

```


## Q10. What is the difference between a Secret and an Artifact, and how do you pass a build file (like a .jar or .zip) from a build job to a deploy job?

A Secret is used to store sensitive information securely, such as passwords, API keys, tokens, or credentials. Secrets are encrypted and accessed in workflows using the secrets context. They are mainly used for authentication, like logging into Docker Hub, AWS, or other services.

An Artifact, on the other hand, is used to store and share files generated during a workflow run. For example, build outputs like .jar, .zip, binaries, logs, or reports can be uploaded as artifacts so they can be used by other jobs or downloaded later.

If I want to pass a build file like a .jar or .zip from a build job to a deploy job, I use the upload-artifact and download-artifact actions.


```bash
- name: Upload build artifact
  uses: actions/upload-artifact@v4
  with:
    name: app-build
    path: build/app.jar

```

```bash
- name: Download artifact
  uses: actions/download-artifact@v4
  with:
    name: app-build

```


---

## Q11 Why is using OpenID Connect (OIDC) considered more secure than storing AWS or Azure credentials as GitHub Secrets, and how do you implement it?

Using OpenID Connect (OIDC) is considered more secure than storing AWS or Azure credentials in GitHub Secrets because it avoids storing long-term credentials in the repository.

When we store credentials like AWS access keys in GitHub Secrets, those keys are static. If they are leaked or misused, they could provide long-term access to the cloud account.

With OIDC, GitHub Actions requests a temporary identity token from GitHub and exchanges it with the cloud provider to get short-lived credentials. These credentials expire automatically after a short time and are generated only during the workflow run. This reduces the risk of credential leakage and eliminates the need to manage static secrets.


```bash
permissions:
  id-token: write
  contents: read

steps:
  - name: Configure AWS Credentials
    uses: aws-actions/configure-aws-credentials@v4
    with:
      role-to-assume: arn:aws:iam::123456789012:role/github-actions-role
      aws-region: us-east-1

```

In this setup, GitHub uses OIDC to authenticate with AWS and obtain temporary credentials without storing access keys in secrets.

So the key advantage of OIDC is short-lived, automatically rotated credentials and no static secrets stored in the repository.”


---

##  Q12. How do you prevent an older workflow run from overwriting a newer deployment if multiple developers merge to main at the same time?

To prevent this, we use the concurrency feature in GitHub Actions. Concurrency allows us to define a group for workflow runs and cancel any previous runs if a new one starts.

For example, we can configure concurrency like this:

```bash
concurrency:
  group: production-deployment
  cancel-in-progress: true

```
With this configuration, if a new workflow run starts for the same group, GitHub will automatically cancel the previous run that is still in progress. This ensures that only the latest workflow run continues to deployment, preventing an older run from overwriting a newer deployment.


---

## Q13. What is an Ephemeral Runner, and why is it the preferred pattern for self-hosted infrastructure in large organizations?

An Ephemeral Runner is a temporary self-hosted runner that runs a single job and is destroyed afterward. Large organizations prefer this pattern because it provides better security, job isolation, and a clean environment for every workflow run while also allowing scalable infrastructure.


---

## Q14. You need to run integration tests against a real Redis database during your CI run. How do you manage this without manually installing Redis on the runner?

To run integration tests with Redis in CI, I would use service containers in GitHub Actions. Redis can be started as a Docker service inside the workflow, which allows the tests to connect to it without manually installing Redis on the runner.

```bash

jobs:
  test:
    runs-on: ubuntu-latest

    services:
      redis:
        image: redis
        ports:
          - 6379:6379

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Run integration tests
        run: npm test


```

## Q15. How do you pass a dynamically generated value (like a Semantic Version tag) from one job to a completely different job in the same workflow? or How do you pass data between steps in a job?

to pass a dynamically generated value between jobs, I use job outputs. The first job sets the value using $GITHUB_OUTPUT, and the next job accesses it using needs.<job>.outputs.<name>

```bash

## In the first job, I store the value and define it as a job output.

jobs:
  build:
    runs-on: ubuntu-latest
    outputs:
      version: ${{ steps.version_step.outputs.version }}
    steps:
      - name: Generate version
        id: version_step
        run: echo "version=1.2.3" >> $GITHUB_OUTPUT

## Then in the next job, I can access this value using needs.

  deploy:
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Use version
        run: echo "Deploying version ${{ needs.build.outputs.version }}"

```

## Q16. How do you create a manual trigger for a workflow that requires the user to choose an environment and provide a specific "Reason for Deployment"?

To create a manual workflow trigger, I use workflow_dispatch. It allows defining input parameters like environment and deployment reason. When someone manually runs the workflow from the Actions tab, they must provide those inputs, which can then be accessed inside the workflow.

```bash

on:
  workflow_dispatch:
    inputs:
      environment:
        description: "Select the deployment environment"
        required: true
        type: choice
        options:
          - staging
          - production
      reason:
        description: "Reason for deployment"
        required: true

```

## Q17. By default, the GITHUB_TOKEN may have broad read/write access. What is the best practice for securing this token at the workflow level?

The best practice is to apply the principle of least privilege by explicitly defining GITHUB_TOKEN permissions in the workflow using the permissions field. This limits the token to only the access required instead of giving it broad default permissions.

For example:

```bash
permissions:
  contents: read

jobs:
  deploy:
    permissions:
      contents: write

```

---

## Q18. If a variable named NODE_ENV is defined at the Workflow level, the Job level, and the Step level, which value wins during execution?
the precedence is Step > Job > Workflow. So if NODE_ENV is defined at all three levels, the step-level value overrides the others during execution.

---

## Q19. You have a "Linter" step that you want to run, but you don't want the entire pipeline to fail if the linter finds issues. How do you achieve this?

To run a linter without failing the pipeline, I use continue-on-error: true for that step. This allows the workflow to continue even if the linter command returns an error.

---

## Q20. How do you define a workflow in GitHub Actions?

A workflow in GitHub Actions is defined using a YAML file placed in the .github/workflows/ directory of the repository. The file specifies triggers, jobs, and steps that automate tasks like building, testing, or deploying an application.

---

## Q21. What is a runner and how do you define it? 

A runner is the machine that executes GitHub Actions workflow jobs. It can be GitHub-hosted or self-hosted, and we define it in the workflow using the runs-on field.

---

## Q22. How can you trigger a workflow in response to a pull request or merge request? 

To trigger a workflow for pull requests, we use the pull_request event in the workflow YAML. This allows the pipeline to run automatically when a pull request is opened, updated, or reopened.

---

## Q23. What is the difference between a job and a step?

A job is a set of steps that run on the same runner, while a step is an individual task within that job, such as running a command or executing an action.

---

## Q24. How do you set up environment variables for a workflow? 

In GitHub Actions, environment variables can be defined using the env keyword. They can be set at different levels depending on where we want them to apply.


---

## Q25. Where do you store artifacts?

In GitHub Actions, artifacts are stored in GitHub’s artifact storage linked to the workflow run. They are uploaded using actions/upload-artifact and can be downloaded later from the Actions tab or by other jobs in the workflow.

---

## Q26. What measures would you take to improve workflow performance?

To improve workflow performance, I would use dependency caching, run independent jobs in parallel, use matrix strategies efficiently, avoid rebuilding artifacts, trigger workflows only when necessary, and use optimized runners when needed.


---


## Q27. How can you customize the logging or output of GitHub Action steps?

We can customize logging in GitHub Actions using workflow commands like ::group::, ::warning::, and ::error:: to format logs, and $GITHUB_OUTPUT to pass step outputs. This helps improve log readability and debugging.


---


## Q28. What are the key component’s of github actions workflow 


- The first component is the Workflow itself. A workflow is a YAML file stored in the .github/workflows/ directory and defines the complete automation process.

- The second component is Events. Events are the triggers that start the workflow, such as a push, pull request, schedule, or manual trigger.

- The third component is Jobs. A workflow can contain multiple jobs, and each job runs on a runner and performs a set of tasks.

- The fourth component is Steps. Steps are individual tasks inside a job, like running commands or using actions.

- Another component is Actions. Actions are reusable pieces of code that perform specific tasks, such as checking out the repository or setting up a programming language.

- Then we have Runners, which are the machines that execute the jobs. They can be GitHub-hosted or self-hosted.

- Finally, we also have Environment variables and secrets, which are used to manage configuration values and sensitive information securely.

---

## Q29. How do you trigger workflow 


A workflow in GitHub Actions is triggered using events defined with the on keyword, such as push, pull_request, schedule, or workflow_dispatch for manual execution.

---

## Q30. How do you create reusable actions in GitHub Actions?

To create reusable actions in GitHub Actions, we create a custom action with an action.yml file defining inputs and execution steps. Then we reuse that action in workflows using the uses keyword.


---

## Q31.What happens if a jobfails? Can others still run?

If a job fails, independent jobs can still run because jobs run in parallel by default. But if another job depends on the failed job using needs, it will be skipped unless we use conditions like if: always().


---


## Q32. What does continue-on- error: true do in a step?

continue-on-error: true allows a step to fail without causing the entire job or workflow to fail.

Normally, if a step returns a non-zero exit code, the job stops and the workflow is marked as failed. But when we set continue-on-error: true, GitHub Actions will mark that step as failed but still continue executing the remaining steps in the job.

---


## Q33. Can you trigger one workflow from another?

Yes, one workflow can trigger another using the workflow_run event. This allows a workflow to start after another workflow completes,

Another way is using workflow_dispatch with the GitHub API to manually trigger another workflow programmatically.


---


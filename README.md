# sa-mcp-test

SuperAnnotate – Automated CI/CD Sync for Custom Actions
Automatically publish and update your SuperAnnotate custom actions whenever you push code to your repository.
-
📘 Overview
This integration allows you to version-control your custom actions and automatically sync them to SuperAnnotate without using the web UI.
 Whenever you push changes to your repository (typically to the main branch), the CI/CD pipeline:
Reads each action folder under actions/


Validates configuration


Packages and encodes the Python script


Sends the action to SuperAnnotate using the Zimmer API


Creates new tasks or updates existing ones


This ensures deployments are consistent, automated, and always up to date.
-
🗂️ Required Repository Structure
Your repository must contain an actions/ directory with one folder per custom task, for example:
actions/
  my_task_1/
    main.py
    config.yaml
  my_task_2/
    script.py
    config.yaml

Each folder must contain:
Exactly one Python file (*.py)


A config.yaml file describing the task


Example config.yaml
name: "armenia"
description: "This task processes incoming data streams."
memory: 256
interpreter: "3.11"
time_limit: 300
concurrency: 1
requirements:
  - "requests>=2.0.0"
  - "pandas"
  - "numpy"

If name is omitted, the folder name is used as the task name.
-
⚙️ How the CI/CD Sync Works
For every subfolder inside actions/, the pipeline:
Parses config.yaml


Identifies the Python script


URL-encodes the Python file content


Builds the required JSON payload:


name
description
memory
time_limit
concurrency
config:
  interpreter
  requirements
file (encoded source)

Calls the Zimmer API:


GET  /custom_task?name=<NAME>
POST /custom_task            (if new)
PATCH /custom_task/{id}      (if exists)

Outputs detailed logs:


Task name


Whether it was created or updated


Parsed values from config.yaml


Errors for invalid YAML or missing files


-
🔐 Authentication
The integration uses a secure API token stored in your CI/CD secret variables.
Platform
Secret Variable Name
GitHub
${{ secrets.SA_TOKEN }}
GitLab
$SA_TOKEN
Bitbucket
${SA_TOKEN}
The token is automatically sanitized (whitespace removed) before use.
-
🚀 Enabling CI/CD Integration
✓ GitHub Actions
Add this file to your repo:
 .github/workflows/superannotate-sync.yml


Create a repository secret named PLATFORM_TOKEN:


Settings → Secrets → Actions


Push changes to main.


The workflow will trigger whenever files under actions/** change.
-
✓ GitLab CI/CD
Add .gitlab-ci.yml to your repository.


Create a CI/CD variable named PLATFORM_TOKEN
 Settings → CI/CD → Variables


Commit to main.


The job will run only when actions/** is modified.
-
✓ Bitbucket Pipelines
Add bitbucket-pipelines.yml to your repository.


Add a Repository Variable named PLATFORM_TOKEN:


Repository Settings → Pipelines → Variables


Push to main.


Bitbucket will attempt to detect changes under actions/.
-
🧩 What Problems This Solves
No more manual uploads to SuperAnnotate UI


Version control becomes the source of truth


Teams can collaborate using standard CI/CD practices


All updates are repeatable and auditable


Supports unlimited custom action folders

---   
title: Github Actions for Beginners  
author: ajaytekam   
date: 2023-10-15 15:53:00 +0530   
img_path: /assets/posts/20231015/ 
categories: [Github-Actions, DevOps, CICD]
tags: [Github-Actions, Github]
image:
  path: github-actions.png  
---    

## Contents 

- [Introduction](#introduction)  
- [Definition](#definition)  
- [Use Cases](#use-cases)  
- [Workflow File](#workflow-file)  
- [Steps Sections](#steps-sections)  
- [Managing Secrets](#managing-secrets)  
- [Setting up Environment Variables](#setting-up-environment-variables)   
- [Setting up Different Events](#setting-up-different-events)   

## Introduction  

- GitHub Actions is a powerful automation tool offered by GitHub. 
- Used to automate various workflows and tasks associated with software development and deployment, and get processed directly within the GitHub repository.  
- You have to define custom workflows using YAML files, known as workflow files.
- These workflows consist of one or more jobs, each containing a set of steps that execute actions. 
- An action is a reusable unit of code that performs specific tasks.  

## Definition  

GitHub Actions helps teams to streamline development processes, improve code quality, and increase collaboration among development teams by automating repetitive tasks and providing a standardized way to manage workflows within GitHub repositories.  

## Use Cases 

- __Continuous Integration (CI)__ : Automatically building and testing code changes to ensure they do not introduce errors.   
- __Continuous Deployment (CD)__ : Automatically deploying code changes to staging or production environments.   
- __Code Quality Assurance__ : Running linters, code style checks, and static code analysis.   
- __Release Management__ : Creating release notes, tagging releases, and publishing artifacts.  
- __Custom Automation__ : Automating any other custom tasks or workflows specific to your project.  

## Workflow file   

- A YAML-formatted configuration file that defines a series of automated steps or jobs to be executed whenever specific events occur within a GitHub repository.  
- The workflow events can be triggered by 
    - Code pushes
    - Pull requests
    - Issue comments
    - Scheduled events   
- The workflow file location on git repository is `.github/workflows/myworkflow.yaml` where the name of the workflow file could be anything.    

__Sample workflow file :__  

```yaml    
name: My workflow file  

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      - name: Run Commands
        run: |
          apt update -y
          echo "This is a simple test for github workflow" 
```   

__Details :__  

- `name` : name of the workflow, which is displayed on the GitHub Actions tab for your repository.  
- `on` : Specifies event/events that trigger the workflow ( push, pull_request, schedule or a custom event defined in your repository).   
    - `branches` : Specifies changes on which branch will trigger the event.  
- `jobs` : 
    - Job is a series of steps that GitHub Actions will execute in a specific environment.   
    - Jobs can run in parallel or sequentially, depending on your configuration.  
    - A workflow typically consists of one or more jobs.   
- `runs-on` : Specifies the type of runner or virtual machine to use for the job.   
    - GitHub Actions provides several default runners, including Ubuntu, Windows, and macOS.   
    - You can also use self-hosted runners for more customization.  
    - `name` attribute is a human-readable name or label for the step. It helps identify the purpose of the step and is displayed in the GitHub Actions UI for easier tracking.   
    - `uses` attribute reference an external action defined in a GitHub repository. The action is executed with its predefined behavior and inputs.  
    - `run` : Execute script or shell command. It can be a single command or a multi-line script using the | symbol for multi-line scripting.   
 

__Example of Workflow file :__   

```yaml    
name: Sample Workflow File  

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest
    permissions:
      # Give the default GITHUB_TOKEN write permission to commit and push the changed files back to the repository.
      contents: write
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      - name: Run Commands
        run: |
          echo "This is a simple test for github workflow" > file.txt
          echo "This is just another line" >> file.txt
      - name: Commit file.txt
        uses: stefanzweifel/git-auto-commit-action@v4
        with:
            commit_message: file.txt is commited
```


- The action will be triggered when the commit on main branch is pushed.  
- We are using `ubuntu-latest` as runner. 
- The `permissions` is used to allow the actions to `write new content` on the git repository.   
- The `actions/checkout@v3` is used to fetch repository's code, including all its branches and history, so that you can use it within your workflow.  
- `run` run shell commands.   
- `stefanzweifel/git-auto-commit-action@v4` used to commit the generated files into the repository itself.  

## Steps Sections    

- `Input Parameters` :  These parameters are provided using the with keyword and are specific to the action being used.  

```yaml  
- name: Step 3
  uses: actions/setup-node@v2
  with:
    node-version: '14'
```

- `Conditional Execution` : You can add conditions to steps to control whether they should run based on the outcome of previous steps or other conditions. This is achieved using the if keyword.  

```yaml   
- name: Conditional Step
  run: echo "This step will only run if a condition is met"
  if: ${{ github.event_name == 'push' }}
```  

- `Working Directory` : By default, steps run in the repository's root directory. You can specify a different working directory using the working-directory attribute.  

```yaml   
- name: Step in a Subdirectory
  run: echo "This step runs in the subdirectory"
  working-directory: ./subdirectory
```  

- `Timeouts` : You can set a maximum execution time for a step using the `timeout-minutes` attribute. If a step exceeds this time limit, it will be terminated.  

```yaml  
- name: Long-Running Step
  run: sleep 600
  timeout-minutes: 10
```  

## Managing Secrets     

- Secrets are used to store sensitive information, such as API keys, access tokens, and passwords, securely.   
- These secrets are encrypted and can only be accessed by workflows running within the same repository.  

__Adding Secrets :__ You can add secrets to your repository by going to the "Settings" tab of your GitHub repository, selecting "Secrets," and then clicking on the "New repository secret" button. Give the secret a name and provide the actual secret value.  

__Accessing Secrets :__ To access secrets within your workflow, you can use the secrets context. Secrets are stored as environment variables with the format secrets.SECRET_NAME. For example:   

```yaml   
steps:
  - name: Use Secret
    run: echo "My API Key is ${{ secrets.API_KEY }}"
```  

Note: Secrets are encrypted and can only be accessed during the workflow run. They are never exposed in logs or visible to anyone with access to the repository settings. However, it's essential to be cautious and avoid inadvertently exposing secrets in your workflow outputs or logs.  

## Setting up Environment Variables    

- Setting up environment variables in GitHub Actions involves defining and using variables within your workflow steps.   
- These environment variables can store configuration settings, data to pass between steps, or other values you need for your workflow.  

__Set Environment Variables within Workflow Steps :__  

```yaml  
jobs:
  my_job:
    runs-on: ubuntu-latest
    steps:
      - name: Set Custom Environment Variable
        run: echo "::set-env name=MY_VARIABLE::custom_value"
      - name: Use Custom Environment Variable
        run: echo "My variable is $MY_VARIABLE"
```  

__GitHub-Provided Environment Variables :__  

```yaml  
jobs:
  my_job:
    runs-on: ubuntu-latest
    steps:
      - name: Display Event Name
        run: echo "Event name: ${{ github.event_name }}"
```  

__Matrix Builds and Environment Variables :__  

You can define matrix entries in your workflow YAML file to create multiple job instances with different environment variable values.  

```yaml  
jobs:
  my_job:
    strategy:
      matrix:
        OS: [ubuntu-latest, windows-latest]
    runs-on: ${{ matrix.OS }}
    steps:
      - name: Display OS
        run: echo "Running on ${{ matrix.OS }}"
```  

__Passing Secrets as Environment Variables :__  

```yaml  
jobs:
  my_job:
    runs-on: ubuntu-latest
    steps:
      - name: Set Secret as Environment Variable
        run: echo "::set-env name=SECRET_API_KEY::${{ secrets.API_KEY }}"
      - name: Use Secret Environment Variable
        run: echo "My secret API key is $SECRET_API_KEY"
```  

## Setting up Different Events  

__Basic Workflow with a Single Event :__  

```yaml  
name: CI on Push to Main

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      # Your workflow steps here
```  

In this example, the workflow named "CI on Push to Main" is configured to run on the `push` event to the "main" branch.  

__Multiple Events and Event Types :__  

```yaml  
name: CI on Push and Pull Request

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      # Your workflow steps here
```  

In this case, the workflow will run on both the `push` and `pull_request` events for the "main" branch.  

__Scheduled Events :__  

```yaml 
name: Daily Scheduled Workflow

on:
  schedule:
    - cron: '0 0 * * *' # Run every day at midnight UTC

jobs:
  scheduled:
    runs-on: ubuntu-latest

    steps:
      # Your workflow steps here
```

It is simply utilizing cron jobs.  

__Custom Events :__  

You can also define custom events to trigger your workflows. Custom events are events that you define in your repository and then trigger manually or through other automation. To use a custom event in your workflow, you can specify its name within the on key. For example:  

```yaml  
name: Custom Event Workflow

on:
  custom_event:
    types:
      - my_custom_event

jobs:
  custom:
    runs-on: ubuntu-latest

    steps:
      # Your workflow steps here
```  

You would need to trigger this custom event using GitHub's event creation mechanisms, such as the GitHub API or by manually triggering it in the GitHub Actions UI.  

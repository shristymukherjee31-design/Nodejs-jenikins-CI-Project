# Node.js Jenkins CI Pipeline

## 1. Project Overview

This project demonstrates a basic **Continuous Integration (CI) pipeline for a Node.js application using Jenkins**.

The Node.js application is a simple HTTP server that returns:

```text
Hello Node!
```

Jenkins automatically:

1. Checks out the source code from GitHub
2. Checks Node.js and npm versions
3. Installs project dependencies
4. Starts the Node.js application
5. Tests the application using `curl`
6. Stops the application
7. Reports the final pipeline status

---

## 2. Objective

The main objective of this project is to understand how Jenkins can automate the CI process of a Node.js application.

### CI Flow

```text
Developer
    |
    v
GitHub Repository
    |
    v
Jenkins Pipeline
    |
    +--> Checkout Code
    |
    +--> Check Node.js & npm
    |
    +--> Install Dependencies
    |
    +--> Start Application
    |
    +--> Test Application
    |
    +--> Stop Application
    |
    v
Build SUCCESS / FAILURE
```

---

## 3. Technologies Used

| Technology | Purpose |
|---|---|
| Node.js | JavaScript runtime used to run the application |
| npm | Package manager for Node.js |
| Git | Version control |
| GitHub | Source code repository |
| Jenkins | CI automation server |
| Linux / WSL | Development and Jenkins environment |
| Shell | Used for Jenkins build commands |

---

## 4. GitHub Repository

The project source code is available on GitHub:

**Repository:**  
https://github.com/shristymukherjee31-design/nodejs-jenkins-ci

---

## 5. Project Structure

```text
nodejs-jenkins-ci/
│
├── index.js
├── package.json
├── package-lock.json
└── README.md
```

### File Description

| File | Description |
|---|---|
| `index.js` | Main Node.js application |
| `package.json` | Project configuration and npm scripts |
| `package-lock.json` | Locks dependency versions |
| `README.md` | Project documentation |

---

## 6. Node.js Application

The application is created using Node.js's built-in HTTP module.

The server listens on port `3000`.

### Application Configuration

```text
Port: 3000
Response: Hello Node!
Host: 0.0.0.0
```

The application can be started using:

```bash
npm start
```

Expected output:

```text
Server running on http://localhost:3000/
```

---

## 7. Local Application Testing

First, install the project dependencies:

```bash
npm install
```

Then start the application:

```bash
npm start
```

The server starts on port `3000`.

### Test Using curl

Open another terminal and run:

```bash
curl http://localhost:3000
```

Expected output:

```text
Hello Node!
```

### Screenshot 1 — Local Application Running

 `npm start` successfully starting the Node.js server.

<img width="783" height="305" alt="Screenshot 2026-09-15 215559" src="https://github.com/user-attachments/assets/fd2b1d6c-afe7-4994-a47e-2dc1e97e82ce" />

### Screenshot 2 — Local curl Test

showing the `curl` command and `Hello Node!` response.

<img width="1920" height="1080" alt="Screenshot 2026-09-15 213245" src="https://github.com/user-attachments/assets/30196500-5ca3-4c0a-8258-a191c876efd7" />



---

# 8. GitHub Setup

The project was initialized as a Git repository and pushed to GitHub.

Basic Git commands used:

```bash
git init
git add .
git commit -m "Initial Node.js project"
git branch -M main
git remote add origin <repository-url>
git push -u origin main
```

The final source code is maintained in the `main` branch.

### Screenshot 3 — GitHub Repository

> Add screenshot of the GitHub repository showing the project files.

<img width="1920" height="1080" alt="Screenshot 2026-09-15 214752" src="https://github.com/user-attachments/assets/4cb1eb4f-28af-4ba7-8e79-dbfc7a20901b" />

---

# 9. Jenkins Configuration

A Jenkins Pipeline job was created with the name:

```text
NodeJS-CI-Pipeline
```

The pipeline is configured to execute the Node.js CI workflow automatically.

### Jenkins Pipeline Stages

The pipeline contains the following stages:

```text
Checkout
   ↓
Environment Check
   ↓
Install Dependencies
   ↓
Application Test
   ↓
Pipeline Result
```

---

## 10. Jenkins Pipeline

The Jenkins pipeline used for this project is:

```groovy
pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/shristymukherjee31-design/nodejs-jenkins-ci.git'
            }
        }

        stage('Environment Check') {
            steps {
                sh '''
                    node --version
                    npm --version
                '''
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm ci'
            }
        }

        stage('Application Test') {
            steps {
                sh '''
                    node index.js > app.log 2>&1 &
                    PID=$!

                    sleep 2

                    echo "Testing Node.js application..."
                    curl -f http://localhost:3000/

                    STATUS=$?

                    kill $PID || true

                    exit $STATUS
                '''
            }
        }
    }

    post {
        success {
            echo 'Node.js CI Pipeline completed successfully.'
        }

        failure {
            echo 'Node.js CI Pipeline failed.'
        }

        always {
            echo 'Pipeline execution completed.'
        }
    }
}
```

---

# 11. Pipeline Stage Explanation

## Stage 1 — Checkout

```groovy
stage('Checkout')
```

This stage downloads the latest source code from the GitHub repository into the Jenkins workspace.

```text
GitHub
   ↓
Jenkins Workspace
```

The pipeline checks out the `main` branch.

---

## Stage 2 — Environment Check

```groovy
stage('Environment Check')
```

Jenkins verifies the installed Node.js and npm versions.

Commands:

```bash
node --version
npm --version
```

This helps confirm that the required runtime environment is available before executing the application.

---

## Stage 3 — Install Dependencies

```groovy
stage('Install Dependencies')
```

The command:

```bash
npm ci
```

installs the dependencies defined by the project's `package-lock.json`.

`npm ci` is commonly preferred in CI environments because it provides a clean and reproducible dependency installation.

---

## Stage 4 — Application Test

```groovy
stage('Application Test')
```

The Node.js application is started in the background:

```bash
node index.js > app.log 2>&1 &
```

The process ID is stored:

```bash
PID=$!
```

Jenkins waits for the server to start:

```bash
sleep 2
```

The application is then tested using:

```bash
curl -f http://localhost:3000/
```

If the application responds successfully, the test passes.

Finally, Jenkins stops the Node.js process:

```bash
kill $PID || true
```

---

# 12. Jenkins Workspace

Jenkins performs the build inside its workspace.

For this project, the workspace is similar to:

```text
/var/lib/jenkins/workspace/NodeJS-CI-Pipeline
```

The GitHub project is checked out into this directory before the remaining pipeline stages execute.

### Jenkins Workspace Flow

```text
GitHub Repository
        |
        v
Jenkins Controller
        |
        v
Jenkins Workspace
        |
        +--> index.js
        +--> package.json
        +--> package-lock.json
        |
        v
npm ci
        |
        v
Node.js Application Test
```

---

# 13. Jenkins Build Execution

After creating the Pipeline job:

```text
Jenkins
   ↓
NodeJS-CI-Pipeline
   ↓
Build Now
```

Jenkins executes all pipeline stages sequentially.

### Screenshot 4 — Jenkins Pipeline

> showing the Jenkins Pipeline stages.

<img width="1920" height="1080" alt="Screenshot 2026-09-15 215147" src="https://github.com/user-attachments/assets/f1b7edb9-1868-4689-99e8-0898caa175db" />


# 14. Jenkins Console Output

The console output shows each command executed by Jenkins.

Important output includes:

```text
node --version
npm --version
```

followed by dependency installation and application testing.

The application test should show:

```text
Testing Node.js application...
Hello Node!
```

Finally:

```text
Node.js CI Pipeline completed successfully.
Pipeline execution completed.
Finished: SUCCESS
```

### Screenshot 5 — Jenkins Console Output

> showing the successful Jenkins console output, especially the final `Finished: SUCCESS`.

<img width="1920" height="1080" alt="Screenshot 2026-09-15 214851" src="https://github.com/user-attachments/assets/a81e2a99-0bcb-4ae9-8d71-79ff444e8dcf" />


---

# 15. Successful CI Flow

The complete CI workflow of this project is:

```text
             GitHub
                |
                v
        Jenkins Pipeline
                |
                v
          Checkout Code
                |
                v
       Environment Check
          Node + npm
                |
                v
        npm ci
                |
                v
       Start Node.js App
                |
                v
       curl localhost:3000
                |
                v
       "Hello Node!"
                |
                v
        Stop Application
                |
                v
             SUCCESS
```

---

# 16. Important Commands

### Install dependencies

```bash
npm install
```

### Start application

```bash
npm start
```

### Test application

```bash
curl http://localhost:3000
```

### Check Node.js version

```bash
node --version
```

### Check npm version

```bash
npm --version
```

### Git status

```bash
git status
```

### Push changes

```bash
git add .
git commit -m "Update project"
git push
```

---

# 17. Why Jenkins Is Used

Without Jenkins, the developer would manually perform:

```text
Pull Code
   ↓
Install Dependencies
   ↓
Start Application
   ↓
Test Application
   ↓
Check Result
```

With Jenkins, these steps are automated:

```text
GitHub
   ↓
Jenkins
   ↓
Checkout
   ↓
Install
   ↓
Test
   ↓
Result
```

This makes the CI process faster, repeatable and less dependent on manual execution.

---

# 18. CI vs CD in This Project

This project demonstrates **Continuous Integration (CI)**.

The pipeline:

- Gets the latest source code
- Installs dependencies
- Runs the application
- Tests the application
- Reports SUCCESS or FAILURE

It does **not** perform production deployment.

Therefore:

```text
This Project = CI
```

Deployment to a server would be part of a future **Continuous Delivery / Continuous Deployment (CD)** implementation.

---

# 19. Troubleshooting

## Problem: `npm start` does not work

Check Node.js:

```bash
node --version
```

Check npm:

```bash
npm --version
```

Then install dependencies:

```bash
npm install
```

---

## Problem: Port 3000 is already in use

Check the process:

```bash
lsof -i :3000
```

Stop the existing process if required.

---

## Problem: Jenkins Build Fails During npm ci

Check that:

```text
package.json
package-lock.json
```

are present in the GitHub repository.

Also verify the Node.js version available to Jenkins.

---

## Problem: curl Test Fails

Check whether the application has started successfully.

The Jenkins pipeline starts the application and waits for a short period before executing:

```bash
curl -f http://localhost:3000/
```

---

# 20. Screenshot Checklist

For final project documentation, the following screenshots are recommended:

| No. | Screenshot | Purpose |
|---|---|---|
| 1 | Local Node.js Server | Shows application running |
| 2 | curl Test | Shows application response |
| 3 | GitHub Repository | Shows source code repository |
| 4 | Jenkins Pipeline | Shows Jenkins stages |
| 5 | Jenkins Console Output | Shows successful CI execution |

Recommended folder:

```text
screenshots/
│
├── 01-local-app-running.png
├── 02-curl-test.png
├── 03-github-repository.png
├── 04-jenkins-pipeline.png
└── 05-jenkins-console-success.png
```

---

# 21. Final Result

The Node.js application was successfully integrated with Jenkins.

The Jenkins CI pipeline performs:

```text
Checkout
   ↓
Environment Check
   ↓
Dependency Installation
   ↓
Application Start
   ↓
Application Testing
   ↓
Application Stop
   ↓
Build SUCCESS
```

This project demonstrates the basic implementation of a **Node.js Continuous Integration pipeline using Jenkins and GitHub**.

---

# 22. Conclusion

This project provided practical understanding of how Jenkins can automate the CI process of a Node.js application.

The complete workflow connects **GitHub, Jenkins, Node.js and npm** to automatically obtain the source code, install dependencies, execute the application and verify that it is working correctly.

The successful Jenkins build confirms that the application passed the defined CI validation steps.

---

## Project Summary

```text
Application       : Node.js Hello World
Source Control    : Git
Repository        : GitHub
CI Tool           : Jenkins
Package Manager   : npm
Application Port  : 3000
Pipeline Type     : Declarative Pipeline
CI Status         : SUCCESS
```

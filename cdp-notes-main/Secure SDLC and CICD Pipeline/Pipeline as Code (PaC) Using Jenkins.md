### Pipeline as Code using Jenkinsfile

#### Overview
- **Pipeline as Code**: Allows Jenkins users to define pipelined job processes with code stored and versioned in a source repository.
- **Jenkinsfile**: Used to define the pipeline in Jenkins.

#### Features of Jenkinsfile
- **Functions**: Jenkins allows creating functions to define a set of instructions/commands, which can be reused in any job.
- **Pipeline Types**:
  - **Declarative Pipeline**
  - **Scripted Pipeline**

#### Example Declarative Pipeline
```groovy
pipeline {
    agent any
    stages {
        stage('build') {
            steps {
                echo "This is a build step"
            }
        }
        stage('test') {
            steps {
                echo "This is a test step"
            }
        }
        stage('integration') {
            steps {
                catchError(buildResult: 'SUCCESS', stageResult: 'UNSTABLE') {
                    echo "This is an integration step"
                    sh "exit 1"
                }
            }
        }
        stage('prod') {
            steps {
                input "Deploy to production?"
                echo "This is a deploy step."
            }
        }
    }
}
```

#### Key Points:
- **Stages**: Define stages inside the `stages` block.
- **Steps**: Use `steps` to define the actions within each stage.
- **Error Handling**: Use `catchError` to allow a stage to fail but continue the pipeline.

#### Migrating from GitLab CI to Jenkins
1. **Remove `.gitlab-ci.yml`**:
   - Prevent GitLab from running CI jobs by deleting the `.gitlab-ci.yml` file from the repository.

2. **Create Jenkinsfile**:
   - Add a new file named `Jenkinsfile` to the repository.
   - Copy the pipeline code into the `Jenkinsfile`.

#### Running and Verifying the Pipeline
- **Execution**: The pipeline starts executing as soon as any change is made to the repository.
- **Result Verification**: Visit the Jenkins job URL to see the job output and pipeline results.

#### Methods for Running Tools
- **Native Installation**: Direct installation on the system.
- **Package Manager/Binary**: Installing via a package manager or using binary files.
- **Docker**: Running the tool within a Docker container (recommended for CI/CD).

#### Additional Tips
- **Jenkins Slaves**: Use more resources than GitLab Runners.
- **Stuck Jobs**: Ensure multiple jobs aren’t running simultaneously to avoid stuck jobs. Stop previous jobs if necessary.

#### Summary:
- Jenkinsfile provides flexibility and reusability through functions and stages.
- Docker is preferred for CI/CD integration due to its smooth operation without dependencies.
- Verify pipeline results through the Jenkins job console output.

#### References:
- [Jenkins Pipeline as Code](https://www.jenkins.io/doc/book/pipeline/pipeline-as-code/)
- [Jenkins Job URL](https://jenkins-oiahxczk.lab.practical-devsecops.training/job/django.nv)

# Challenges

## Jenkinsfile for CI/CD Pipeline with Four Stages and Artifact Archiving

### Overview
- The Jenkinsfile uses Declarative Pipeline syntax to structure workflow into distinct stages.
- Each stage performs a simple echo command to indicate the stage name.
- Artifacts generated during the pipeline are archived for later access.

### Jenkinsfile
```groovy
pipeline {
    agent any

    stages {
        stage("build") {
            steps {
                // Echo a message indicating the build stage
                echo "this is build stage"
            }
        }
        stage("test") {
            steps {
                // Echo a message indicating the test stage
                echo "this is test stage"
            }
        }
        stage("integration") {
            steps {
                // Execute a shell command to echo a message into output.txt
                sh 'echo "this is integration" > output.txt'
            }
        }
        stage("prod") {
            steps {
                // Echo a message indicating the production stage
                echo "this is prod stage"
            }
        }
    }
    
    post {
        always {
            // Archive all .txt files as artifacts
            archiveArtifacts artifacts: '*.txt', fingerprint: true
        }
    }
}
```

### Explanation
1. **Pipeline Block**: Coordinates all tasks and stages.
2. **Agent**: Defines where the pipeline will run (`agent any` runs on any available agent).
3. **Stages**:
    - **Build Stage**: Echoes "this is build stage".
    - **Test Stage**: Echoes "this is test stage".
    - **Integration Stage**: Creates `output.txt` with "this is integration".
    - **Prod Stage**: Echoes "this is prod stage".
4. **Post Actions**:
    - **Always**: Archives all `.txt` files generated during the pipeline execution.

### Benefits
- **Stage Visibility**: Clearly labeled stages for build, test, integration, and production.
- **Artifact Creation**: Uses shell execution to generate an `output.txt` file.
- **Artifact Archiving**: Efficiently archives the `output.txt` file for easy access post-build.

### Notes
- **Why use `sh` for output redirection?**: The `sh` command executes shell commands, allowing for output redirection (`>`), which is not possible with just `echo`.
- **Artifact Types**: Jenkins supports various artifact types, including JSON, CSV, and others.

### References
- [Jenkins Pipeline as Code](https://www.jenkins.io/doc/book/pipeline/pipeline-as-code/)
- [Artifact Archiving](https://www.jenkins.io/doc/book/pipeline/artifacts/#using-artifacts-in-pipeline)
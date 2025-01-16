
# Challenge Solution

## Jenkinsfile for CI/CD Pipeline with Parallel Stages and Artifact Creation

### Overview
- The Jenkinsfile uses Declarative Pipeline syntax to structure the workflow into distinct stages.
- Stages `build` and `test` run in parallel to speed up the pipeline.
- An additional stage `artifact` is added to create and archive an artifact file.

#### Jenkinsfile
```groovy
pipeline {
    agent any

    options {
        gitLabConnection('gitlab')
    }

    stages {
        stage("build and test") {
            parallel {
                stage("build") {
                    agent {
                        docker {
                            image 'python:3.6'
                            args '-u root'
                        }
                    }
                    steps {
                        sh """
                        pip3 install --user virtualenv
                        python3 -m virtualenv env
                        . env/bin/activate
                        pip3 install -r requirements.txt
                        python3 manage.py check
                        """
                    }
                }
                stage("test") {
                    agent {
                        docker {
                            image 'python:3.6'
                            args '-u root'
                        }
                    }
                    steps {
                        sh """
                        pip3 install --user virtualenv
                        python3 -m virtualenv env
                        . env/bin/activate
                        pip3 install -r requirements.txt
                        python3 manage.py test taskManager
                        """
                    }
                }
            }
        }
        stage("integration") {
            steps {
                catchError(buildResult: 'SUCCESS', stageResult: 'UNSTABLE') {
                    echo "This is an integration step"
                    sh "exit 1"
                }
            }
        }
        stage("prod") {
            steps {
                input "Deploy to production?"
                echo "This is a deploy step"
            }
        }
        stage("artifact") {
            steps {
                script {
                    // Create a simple text file using the echo command
                    sh 'echo "This is an artifact file" > artifact.txt'

                    // Archive the artifact file
                    archiveArtifacts artifacts: 'artifact.txt'
                }
            }
        }
    }
    post {
        failure {
            updateGitlabCommitStatus(name: "${env.STAGE_NAME}", state: 'failed')
        }
        unstable {
            updateGitlabCommitStatus(name: "${env.STAGE_NAME}", state: 'failed')
        }
        success {
            updateGitlabCommitStatus(name: "${env.STAGE_NAME}", state: 'success')
        }
        aborted {
            updateGitlabCommitStatus(name: "${env.STAGE_NAME}", state: 'canceled')
        }
        always { 
            deleteDir() // Clean up workspace
        }
    }
}
```

#### Explanation
1. **Pipeline Block**: Coordinates all tasks and stages.
2. **Agent**: Defines where the pipeline will run (`agent any` runs on any available agent).
3. **Options**: Sets GitLab connection.
4. **Stages**:
    - **Build and Test**: Runs build and test stages in parallel using Docker agents.
        - **Build Stage**: Installs dependencies and checks the application.
        - **Test Stage**: Installs dependencies and runs tests.
    - **Integration Stage**: Allows stage to fail but continues the pipeline.
    - **Prod Stage**: Requires manual approval to deploy to production.
    - **Artifact Stage**: Creates and archives an artifact file (`artifact.txt`).
5. **Post Actions**: Updates GitLab commit status and cleans up the workspace after the pipeline execution.

#### Benefits
- **Parallel Execution**: Speeds up the pipeline by running build and test stages simultaneously.
- **Artifact Creation**: Generates and archives an artifact file for later access.
- **Stage Visibility**: Clearly labeled stages for build, test, integration, production, and artifact creation.

#### Notes
- **Using Docker Agents**: Ensures isolated and reproducible environments with the required dependencies.
- **Resource Management**: Running stages in parallel increases resource consumption, which should be managed appropriately.

#### References
- [Jenkins Pipeline as Code](https://www.jenkins.io/doc/book/pipeline/pipeline-as-code/)
- [Artifact Archiving](https://www.jenkins.io/doc/book/pipeline/artifacts/#using-artifacts-in-pipeline)
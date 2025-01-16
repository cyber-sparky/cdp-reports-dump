
## Jenkinsfile for running gosec in jenkins 

```groovy
pipeline {
    agent any

    options {
        gitLabConnection('gitlab')
    }

    stages {
        stage("build") {
            steps {
                echo "This is a build step"
            }
        }
        stage("sast") {
            steps {
                sh "docker run --rm -w /src -v \$(pwd):/src securego/gosec -fmt json -out /src/gosec-output.json /src/..."
            }
            post {
                always {
                    archiveArtifacts artifacts: 'gosec-output.json', fingerprint: true
                }
            }
        }
        stage("integration") {
            steps {
                // Allow the stage to fail
                catchError(buildResult: 'SUCCESS', stageResult: 'UNSTABLE') {
                    echo "This is an integration step."
                    sh "exit 1"
                }
            }
        }
        stage("prod") {
            steps {
                timeout(time: 10, unit: 'SECONDS') {
                    input "Deploy to production?"
                }
                echo "This is a deploy step."
            }
        }
    }
    post {
        failure {
            updateGitlabCommitStatus(name: "\${env.STAGE_NAME}", state: 'failed')
        }
        unstable {
            updateGitlabCommitStatus(name: "\${env.STAGE_NAME}", state: 'failed')
        }
        success {
            updateGitlabCommitStatus(name: "\${env.STAGE_NAME}", state: 'success')
        }
        aborted {
            updateGitlabCommitStatus(name: "\${env.STAGE_NAME}", state: 'canceled')
        }
        always { 
            deleteDir()                     // clean up workspace
            dir("${WORKSPACE}@tmp") {       // clean up tmp directory
                deleteDir()
            }
            dir("${WORKSPACE}@script") {    // clean up script directory
                deleteDir()
            }
        }
    }
}
```

## Pipeline after setting up allow failure 

```groovy 
pipeline {
    agent any

    options {
        gitLabConnection('gitlab')
    }

    stages {
        stage("build") {
            steps {
                echo "This is a build step."
            }
        }
        stage("sast") {
            steps {
                catchError(buildResult: 'SUCCESS', stageResult: 'UNSTABLE') {
                    sh "docker run --rm -w /src -v \$(pwd):/src securego/gosec -fmt json -out /src/gosec-output.json /src/..."
                }
            }
            post {
                always {
                    archiveArtifacts artifacts: 'gosec-output.json', fingerprint: true
                }
            }
        }
        stage("integration") {
            steps {
                // Allow the stage to fail
                catchError(buildResult: 'SUCCESS', stageResult: 'UNSTABLE') {
                    echo "This is an integration step."
                    sh "exit 1"
                }
            }
        }
        stage("prod") {
            steps {
                timeout(time: 10, unit: 'SECONDS') {
                    input "Deploy to production?"
                }
                echo "This is a deploy step."
            }
        }
    }
    post {
        failure {
            updateGitlabCommitStatus(name: STAGE_NAME, state: 'failed')
        }
        unstable {
            updateGitlabCommitStatus(name: STAGE_NAME, state: 'failed')
        }
        success {
            updateGitlabCommitStatus(name: STAGE_NAME, state: 'success')
        }
        aborted {
            updateGitlabCommitStatus(name: STAGE_NAME, state: 'skipped')
        }
        always {
            deleteDir()                     // clean up workspace
        }
    }
}
```
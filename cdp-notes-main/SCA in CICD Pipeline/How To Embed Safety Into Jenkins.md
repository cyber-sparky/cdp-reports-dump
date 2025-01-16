
## Jenkinsfile 

```groovy 
pipeline {
    agent any

    options {
        gitLabConnection('gitlab')
    }

    stages {
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
        stage("oast") {
            steps {
                sh "docker run -v \$(pwd):/src --rm hysnsec/safety check -r requirements.txt --json | tee oast-results.json"
            }
        }
        stage("integration") {
            steps {
                catchError(buildResult: 'SUCCESS', stageResult: 'UNSTABLE') {
                    echo "This is an integration step."
                    sh "exit 1"
                }
            }
        }
        stage("prod") {
            steps {
                input "Deploy to production?"
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

## Allow failures

1. **Stage Failure Allowance**: 
   - For machines other than DevSecOps-Box, which auto-close after two hours of activity, you must restart the exercise from its page.
   - In DevSecOps Maturity Levels 1 and 2, allow the pipeline to continue even if security tools find issues, as these may be false positives.

2. **Using catchError Function**:
   - Use the `catchError` function in Jenkins pipelines to prevent build failure when security tools detect issues, allowing the pipeline to proceed. This is particularly useful in early stages where false positives might be high.
   - Example usage in the pipeline:
     ```groovy
     catchError(buildResult: 'SUCCESS', stageResult: 'UNSTABLE') {
         sh "docker run -v \$(pwd):/src --rm hysnsec/safety check -r requirements.txt --json | tee oast-results.json"
     }
     ```

3. **Pipeline Configuration**:
   - Maintain continuous integration and deployment processes using Jenkins, employing Docker environments and various stages like build, test, and deployment.
   - Ensure the use of the `catchError` function in critical stages to manage build outcomes effectively.

4. **Post-Build Actions**:
   - Configure post-build actions to manage artifact archiving, workspace cleanup, and GitLab status updates based on the build result (success, failure, unstable, aborted).
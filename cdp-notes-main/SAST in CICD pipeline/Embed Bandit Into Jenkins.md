
## Jenkinsfile for integrating bandit 

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
        stage("sast") {
            steps {
                sh "docker run -v \$(pwd):/src --rm hysnsec/bandit -r /src -f json -o /src/bandit-output.json"
            }
            post {
                always {
                    archiveArtifacts artifacts: 'bandit-output.json', fingerprint: true
                }
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

## Complete pipeline of integrating bandit into Jenkins with allow-failure 

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
        stage("sast") {
            steps {
                catchError(buildResult: 'SUCCESS', stageResult: 'UNSTABLE') {
                    sh "docker run -v \$(pwd):/src --rm hysnsec/bandit -r /src -f json -o /src/bandit-output.json"
                }
            }
            post {
                always {
                    archiveArtifacts artifacts: 'bandit-output.json', fingerprint: true
                }
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
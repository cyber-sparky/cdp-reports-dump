
## Bash script to automate dependency checks 

```sh
#!/bin/sh

DATA_DIRECTORY="$PWD/data"
REPORT_DIRECTORY="$PWD/reports"

if [ ! -d "$DATA_DIRECTORY" ]; then
  echo "Initially creating persistent directories"
  mkdir -p "$DATA_DIRECTORY"
  chmod -R 777 "$DATA_DIRECTORY"

  mkdir -p "$REPORT_DIRECTORY"
  chmod -R 777 "$REPORT_DIRECTORY"
fi

docker run --rm \
  --volume $(pwd):/src \
  --volume "$DATA_DIRECTORY":/usr/share/dependency-check/data \
  --volume "$REPORT_DIRECTORY":/reports \
  hysnsec/dependency-check \
  --scan /src \
  --format "JSON" \
  --project "Webgoat" \
  --failOnCVSS 4 \
  --out /reports
```

## Jenkinsfile for integrating it 

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
        stage("test") {
            steps {
                echo "This is a test step"
            }
        }
        stage("odc-backend") {
            agent {
                docker { 
                    image 'docker:20.10-dind'
                    args '-u root -v /var/run/docker.sock:/var/run/docker.sock'
                }
            }
            steps {
                sh """
                chmod +x run-depcheck.sh
                ./run-depcheck.sh
                """
            }
            post {
                always {
                    archiveArtifacts artifacts: 'reports/dependency-check-report.json', onlyIfSuccessful: false
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

## Allow Failure 

```groovy
        stage("odc-backend") {
            agent {
                docker { 
                    image 'docker:20.10-dind'
                    args '-u root -v /var/run/docker.sock:/var/run/docker.sock'
                }
            }
            steps {
                // catchError function to “not fail the build” even though the tool found security issues.
                catchError(buildResult: 'SUCCESS', stageResult: 'UNSTABLE') {
                sh """
                chmod +x run-depcheck.sh
                ./run-depcheck.sh
                """
            }
            post {
                always {
                    archiveArtifacts artifacts: 'reports/dependency-check-report.json', onlyIfSuccessful: false
                    }
                }
            }
        }
```
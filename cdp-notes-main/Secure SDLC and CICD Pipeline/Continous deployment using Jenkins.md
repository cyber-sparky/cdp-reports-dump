# Building a docker image 

To build Docker images in Jenkins and push them to GitLab Container Registry, follow these steps:

### Jenkinsfile Setup

1. **Credentials Setup**
   - Navigate to Jenkins and go to **Credentials** > **System** > **Global credentials** (domain: Global).
   - Click on **Add Credentials** and fill in the following details:
     - **Kind**: Username with password
     - **Scope**: Global
     - **Username**: root
     - **Password**: pdso-training
     - **ID**: registry-auth
     - **Description**: Credentials to login into GitLab Container Registry
   - Click **OK** to save the credentials.

2. **Edit Jenkinsfile**
   - Open your GitLab repository and locate the Jenkinsfile.
   - Add a new stage `release` after the `test` stage in your Jenkinsfile.

   ```groovy
   pipeline {
       agent any

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
           stage("release") {
               environment {
                   registryUrl = "gitlab-registry-oiahxczk.lab.practical-devsecops.training"
                   registryCreds = "registry-auth" // Credentials Id to authenticate with Docker Registry
               }
               steps {
                   script {
                       dockerImage = docker.build "${registryUrl}/root/django-nv:${BUILD_NUMBER}"
                       docker.withRegistry("https://${registryUrl}", registryCreds) {
                           dockerImage.push()
                       }
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

3. **Explanation**:
   - **Credentials**: Added `registry-auth` credentials to authenticate with GitLab Container Registry.
   - **Release Stage**:
     - Sets `registryUrl` to the GitLab Container Registry URL.
     - Uses `docker.build` to build the Docker image tagged with the Jenkins build number.
     - `docker.withRegistry` authenticates and pushes the Docker image to the registry.

4. **Commit and Run**:
   - Save changes to the Jenkinsfile and commit them to your GitLab repository.
   - Trigger the Jenkins pipeline to see the `build and test` and `release` stages execute.
   - Check the Jenkins Blue Ocean UI or the classic UI to view the pipeline execution and results.

## Deploying an Application

**Remember!**
- Except for DevSecOps-Box, every other machine closes after two hours, even if you are in the middle of the exercise.
- After two hours, in case of a 404, you need to refresh the exercise page and click on the Start the Exercise button to continue working.

### Steps to Deploy Application with SSH Agent Plugin in Jenkins

1. **Add SSH Credentials in Jenkins:**
   - Visit: `https://jenkins-oiahxczk.lab.practical-devsecops.training/credentials/store/system/domain/_/`
   - Click on "Add Credentials" on the left sidebar.
   - Select "SSH Username with private key" as Kind.
   - Add the following credentials:
     - **ID:** `ssh-prod`
     - **Description:** `Credentials to login into Production machine`
     - **Username:** `root`
     - **Private Key:** Check "Enter directly", click the Add button, and copy the private key from the Production machine (`/root/.ssh/id_rsa`).
     - **Passphrase:** Leave it blank for automatic processing. For robust security, consider using secret management systems like Hashicorp Vault.
   - Click the OK button.

2. **Modify the `prod` Stage in Jenkinsfile:**
   Replace the `prod` stage with the following content:
   ```groovy
   stage("prod") {
       steps {
           input "Deploy to production?"
           sshagent(['ssh-prod']) {
               sh """
               ssh -o StrictHostKeyChecking=no root@prod-oiahxczk '
               docker login -u root -p pdso-training $registryUrl
               docker rm -f django.nv
               docker pull $registryUrl/root/django-nv:$BUILD_NUMBER
               docker run -d --name django.nv -p 8000:8000 $registryUrl/root/django-nv:$BUILD_NUMBER
               '
               """
           }
       }
   }
   ```

3. **Move the `environment` Section:**
   Move the `environment` section from the release stage to the top of the pipeline for global accessibility:
   ```groovy
   pipeline {
       agent any

       environment {
           registryUrl = "gitlab-registry-oiahxczk.lab.practical-devsecops.training"
           registryCreds = "registry-auth"
       }

       options {
           gitLabConnection('gitlab')
       }

       stages {
           // Other stages
       }

       post {
           // Post actions
       }
   }
   ```

4. **Save and Commit Changes:**
   Save changes to the Jenkinsfile and commit them.

5. **Verify Deployment:**
   - Visit: `https://jenkins-oiahxczk.lab.practical-devsecops.training/blue/organizations/jenkins/django.nv/activity`
   - Check the output of the appropriate job to verify the `prod` stage is working.

6. **Debug SSH Issues:**
   If there are SSH issues, manually try to SSH from the DevSecOps Box to the production machine:
   ```sh
   ssh root@prod-oiahxczk
   ```

7. **Check Running Containers on Production Machine:**
   ```sh
   docker ps
   ```

**Command Output:**
```sh
CONTAINER ID        IMAGE                                                        COMMAND                  CREATED             STATUS              PORTS                    NAMES
ef365aef9d91        gitlab-registry-oiahxczk.lab.practical-devsecops.training/root/django-nv:4   "/app/run_app_docker…"   3 minutes ago       Up 3 minutes        0.0.0.0:8000->8000/tcp   django.nv
```

**Conclusion:**
- The pipeline is working fine.
- You can also check the GitLab Container Registry to see the latest Docker images.

## Challenges

### Have a look at the new prod stage. There are some hardcoded SSH hostname(s) and docker login credentials. Please replace it with variables in Jenkins. Would doing so prevent the pipeline from displaying secrets in the output?

```groovy
pipeline {
    agent any

    environment {
        SSH_HOST = credentials('ssh-hostname')
        DOCKER_CREDENTIALS = credentials('docker-login')
    }

    stages {
        // other stages

        stage('Deploy to Prod') {
            steps {
                script {
                    sshagent(['ssh-key']) {
                        sh """
                        ssh -o StrictHostKeyChecking=no user@${env.SSH_HOST} 'commands here'
                        """
                    }
                    withCredentials([usernamePassword(credentialsId: 'docker-login', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh """
                        echo ${DOCKER_PASS} | docker login -u ${DOCKER_USER} --password-stdin
                        docker pull your_image
                        docker run your_image
                        """
                    }
                }
            }
        }
    }
}
```

### Please add a condition to prod stage, so it executes only when a release tag is created

```groovy
pipeline {
    agent any

    environment {
        SSH_HOST = credentials('ssh-hostname')
        DOCKER_CREDENTIALS = credentials('docker-login')
    }

    stages {
        // other stages

        stage('Deploy to Prod') {
            when {
                tag "release-*" // Adding a tag called release
            }
            steps {
                script {
                    sshagent(['ssh-key']) {
                        sh """
                        ssh -o StrictHostKeyChecking=no user@${env.SSH_HOST} 'commands here'
                        """
                    }
                    withCredentials([usernamePassword(credentialsId: 'docker-login', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh """
                        echo ${DOCKER_PASS} | docker login -u ${DOCKER_USER} --password-stdin
                        docker pull your_image
                        docker run your_image
                        """
                    }
                }
            }
        }
    }
}
```
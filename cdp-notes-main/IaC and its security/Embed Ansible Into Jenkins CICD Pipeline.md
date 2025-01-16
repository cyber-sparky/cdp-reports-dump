
## Jenkins job for hardening using ansible 

After prod job, you can add this 

```groovy
        stage("ansible-hardening"){
            agent {
                docker {
                    image 'willhallonline/ansible:2.13-ubuntu-20.04'
                    args '-u root'
                }
            }
            steps {
                sshagent(['ssh-prod']) {
                    withCredentials([string(credentialsId: 'prod-server', variable: 'DEPLOYMENT_SERVER')]) {
                        sh """
                        mkdir ~/.ssh
                        ssh-keyscan -t rsa $DEPLOYMENT_SERVER >> ~/.ssh/known_hosts
                        echo "[prod]\n$DEPLOYMENT_SERVER" >> inventory.ini
                        ansible-galaxy install dev-sec.os-hardening
                        ansible-playbook -i inventory.ini ansible-hardening.yml
                        """
                    }
                }
            }
        }
```

You also need to add playbook file into gitlab repo 

```yml
---
- name: Playbook to harden the Ubuntu OS.
  hosts: prod
  remote_user: root
  become: yes

  roles:
    - dev-sec.os-hardening
```
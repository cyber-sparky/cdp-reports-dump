
## Install SonarQube 

```sh
export SONAR_VERSION="4.7.0.2747"
wget https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-${SONAR_VERSION}-linux.zip -O /opt/sonar-scanner.zip
unzip /opt/sonar-scanner.zip -d /opt/
chmod +x /opt/sonar-scanner-${SONAR_VERSION}-linux/bin/sonar-scanner
export PATH=/opt/sonar-scanner-${SONAR_VERSION}-linux/bin/:$PATH
```

## Creating a token 

```sh
SONARQUBE_TOKEN=$(curl -u admin:pdso-training -X POST 'https://sonarqube-oiahxczk.lab.practical-devsecops.training/api/user_tokens/generate' -d 'name=SONARQUBE_TOKENs' | jq -r '.token')
/webapp# echo $SONARQUBE_TOKEN
squ_67cd87d552e718faaa5377840a811a62e952ca24
```

## Running the scanner 

```sh
sonar-scanner \
-Dsonar.projectKey=Django \
-Dsonar.sources=. \
-Dsonar.host.url=https://sonarqube-oiahxczk.lab.practical-devsecops.training \
-Dsonar.login=$SONARQUBE_TOKEN
```


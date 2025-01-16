
## Install Java 

```sh
apt update && apt install openjdk-8-jre -y
```

## Install FindSecBugs 

```sh
wget https://github.com/find-sec-bugs/find-sec-bugs/releases/download/version-1.9.0/findsecbugs-cli-1.9.0-fix2.zip && unzip findsecbugs-cli-1.9.0-fix2.zip -d /opt/
sed -i -e 's/\r$//' /opt/findsecbugs.sh
chmod +x /opt/findsecbugs.sh
export PATH=/opt/:$PATH
```

## Running scan using Findsecbugs and generate a html report

```sh
findsecbugs.sh -progress -html -output findsecbugs-report.html webgoat-server-8.1.0.jar
```


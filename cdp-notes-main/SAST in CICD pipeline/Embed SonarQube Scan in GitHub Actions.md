
## Github action workflow for integrating sonarcube with allow failure 

Before running or triggering the action, make sure place sonar token and url as secrets variable

```yml
  sonarqube:
    runs-on: ubuntu-20.04
    steps:
    - uses: actions/checkout@v2
      with:
        fetch-depth: 0

    - name: SonarQube Scan
      uses: sonarsource/sonarqube-scan-action@master
      env:
        SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
        SONAR_HOST_URL: ${{ secrets.SONAR_HOST_URL }}
      with:
        projectBaseDir: .
        args: >
          -Dsonar.projectKey=Django
```
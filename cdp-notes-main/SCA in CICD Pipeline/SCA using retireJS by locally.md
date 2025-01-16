## Cloning the project 

We can clone the sample project from the following repo `https://gitlab.practical-devsecops.training/pdso/django.nv` using git

```bash
git clone https://gitlab.practical-devsecops.training/pdso/django.nv webapp
```

## To install npm and NodeJS 

```bash
mkdir -p /etc/apt/keyrings
curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key | gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg
NODE_MAJOR=20
echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_$NODE_MAJOR.x nodistro main" | tee /etc/apt/sources.list.d/nodesource.list
apt update
apt install nodejs -y
```

## To install RetireJS 

```bash
npm install -g retire@5.0.0
```

## Actually how package.json file looks like 

Javascript libraries needed for the application 

```bash 
/webapp# cat package.json
{
  "name": "owasp-nodejs-goat",
  "private": true,
  "version": "1.3.0",
  "description": "A tool to learn OWASP Top 10 for node.js developers",
  "main": "server.js",
  "dependencies": {
    "bcrypt-nodejs": "0.0.3",
    "body-parser": "^1.15.1",
    "consolidate": "^0.14.1",
    "csurf": "^1.8.3",
    "dont-sniff-mimetype": "^1.0.0",
    "express": "^4.13.4",
    "express-session": "^1.13.0",
    "forever": "^0.15.1",
    "helmet": "^2.0.0",
    "marked": "0.3.9",
    "mongodb": "^2.1.18",
    "node-esapi": "0.0.1",
    "serve-favicon": "^2.3.0",
    "swig": "^1.4.2",
    "underscore": "^1.8.3"
  },
  "comments": {
    "//": "a9 insecure components"
  },
  "engines": {
    "node": "4.4.x",
    "npm": "2.15.x"
  },
  "scripts": {
    "start": "node server.js",
    "test": "node node_modules/grunt-cli/bin/grunt test",
    "db:seed": "grunt db-reset",
    "precommit": "grunt precommit"
  },
  "devDependencies": {
    "async": "^2.0.0-rc.4",
    "grunt": "^1.0.1",
    "grunt-cli": "^1.2.0",
    "grunt-concurrent": "^2.3.0",
    "grunt-contrib-jshint": "^1.0.0",
    "grunt-contrib-watch": "^1.0.0",
    "grunt-env": "latest",
    "grunt-jsbeautifier": "^0.2.12",
    "grunt-mocha-test": "^0.12.7",
    "grunt-nodemon": "^0.4.2",
    "grunt-if": "https://github.com/binarymist/grunt-if/tarball/master",
    "grunt-npm-install": "^0.3.0",
    "grunt-retire": "^0.3.12",
    "mocha": "^2.4.5",
    "selenium-webdriver": "^2.53.2",
    "should": "^8.3.1",
    "zaproxy": "^0.2.0"
  },
  "repository": "https://github.com/OWASP/NodejsGoat",
  "license": "Apache 2.0"
}
```

## Scanning the Package 

### Without installing the package 

Here we are actually just taking the `package.json` and blindly scanning it using `retireJS` 

```bash
retire --outputformat json --outputpath no-npm-install-retire-output.json
```

### Installing the package through npm 

```bash
# Go into the project 
cd /webapp

# if there is package.json inside the directory
# It will install all the mentioned packages
npm install
```

## Run the scanner again 

```bash
retire --outputformat json --outputpath retire_output.json
```

## Scan Based on severity using retireJS

```bash
retire --severity critical --outputformat json --outputpath retire_output.json
```

## Exit codes 

```bash
/webapp# retire --severity critical --outputformat json --outputpath retire_output.json
/webapp# echo $?
0 # No valid findings and exit code is 0
/webapp# retire --severity medium --outputformat json --outputpath retire_output.json
/webapp# echo $?
13 # Valid findings are available for triage 
```

### RetireJS False Positive Handling

**RetireJS allows us to mark an issue or issues as False Positive (FP) using `.retireignore.json` file.**

- **Creating `.retireignore.json` File:**
  - Create the file using commands such as:
    ```bash
    touch .retireignore.json
    ```
  - Or with a vi editor:
    ```bash
    vi .retireignore.json
    ```

- **More Information:**
  - More details about the ignore feature can be found [here](https://retirejs.github.io/retire.js/).

---

**Note:**

- If you have a large number of findings, as in the `retire_output.json` file, and you are looking to filter issues that are of high severity, you can use the `jq` tool for filtering.
  
- **Identifying Components with High Severity:**
  - Before creating the ignore file, let’s try to identify the components that have high severity:
    ```bash
    cat retire_output.json | jq '.data[].results[] | select (.vulnerabilities[]?.severity=="high") | .component,.version'
    ```

- **Creating the `.retireignore.json` File:**
  - Based on the above output, we can proceed with creating the `.retireignore.json` file. Please use the following command for the final file:
    ```bash
    cat >/webapp/.retireignore.json<<EOF
    [
        {
            "component": "lodash",
            "version": "1.0.1",
            "justification" : "False Positive"
        },
        {
            "component": "dojo",
            "version": "1.4.2",
            "justification" : "False Positive"
        },
        {
            "component": "lodash",
            "version": "4.17.10",
            "justification" : "False Positive"
        },
        {
            "component": "underscore.js",
            "version": "1.8.3",
            "justification" : "False Positive"
        },
        {
            "component": "lodash",
            "version": "2.4.2",
            "justification" : "False Positive"
        },
        {
            "component":"handlebars",
            "version": "4.0.5",
            "justification" : "False Positive"
        },
        {
            "component":"tinyMCE",
            "version": "4.0.26",
            "justification" : "False Positive"
        }
    ]
    EOF
    ```

- **Avoiding Issues in Subsequent Scans:**
  - Once we mark an issue as FP, we need to use the following command while scanning to avoid showing these issues in the subsequent scans:
    ```bash
    retire --severity high --ignorefile .retireignore.json --outputformat json --outputpath retire_output.json
    ```

- **Rechecking High Vulnerabilities:**
  - Let’s try to check again if the high vulnerability still exists or not:
    ```bash
    cat retire_output.json | jq '.data[].results[] | select (.vulnerabilities[]?.severity=="high") | .component,.version'
    ```
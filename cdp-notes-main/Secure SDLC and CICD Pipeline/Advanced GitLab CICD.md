
# Simple CI/CD Pipeline

**Gitlab CI/CD**: An implementation of the CI/CD pipeline, useful for creating a deployment pipeline for your project.

- **Learning Gitlab CI/CD**: Helps you automatically learn many CI/CD systems like Travis CI, Circle CI, and Bitbucket CI with minor changes.
- **Alternatives**: Jenkins, Travis, Circle CI, Bitbucket Pipelines, and Drone CI.

## Simple CI/CD Pipeline Example

**YAML File Example**:
```yaml
# This is how a comment is added to a YAML file; please read them carefully.

stages:   # Dictionary
 - build   # this is build stage
 - test    # this is test stage
 - integration # this is an integration stage
 - prod       # this is prod/production stage

build:       # this is job named build, it can be anything, job1, job2, etc.,
  stage: build    # this job belongs to the build stage. Here both job name and stage name is the same, i.e., build
  script:
    - echo "This is a build step."  # We are running an echo command, but it can be any command.

test:
  stage: test
  script:
    - echo "This is a test step."
    - exit 1         # Non-zero exit code, fails a job.

integration:        # integration job under stage integration.
  stage: integration
  script:
    - echo "This is an integration step."

prod:
  stage: prod
  script:
    - echo "This is a deploy step."
```

## Logging into GitLab

**Details**:
- **URL**: [GitLab CI/CD Pipeline](https://gitlab-ce-oiahxczk.lab.practical-devsecops.training/root/django-nv/-/blob/main/.gitlab-ci.yml)
- **Username**: root
- **Password**: pdso-training

**Steps**:
1. Create a CI/CD pipeline by replacing the existing content in `.gitlab-ci.yml` file with the above content.
2. Click on the Edit button to start replacing the content.
3. Save changes to the file using the Commit changes button.

### Verify the Pipeline Run

As soon as a change is made to the repository, the pipeline starts executing the jobs.

**Check Results**:
- Visit [Pipeline Results](https://gitlab-ce-oiahxczk.lab.practical-devsecops.training/root/django-nv/pipelines).
- Click on the appropriate job name to see the output.

## Failing a Build Using Exit Code

**YAML File Example**:
```yaml
stages:   # Dictionary
 - build   # this is build stage
 - test    # this is test stage
 - integration # this is an integration stage
 - prod       # this is prod/production stage

build:       # this is job named build, it can be anything, job1, job2, etc.,
  stage: build    # this job belongs to the build stage. Here both job name and stage name is the same i.e., build
  script:
    - echo "This is a build step"  # We are running an echo command, but it can be any command.

test:
  stage: test
  script:
    - echo "This is a test step"
    # ************ Non-zero exit code, fails a job. ************ #
    - exit 1

integration:        # integration job under stage integration.
  stage: integration
  script:
    - echo "This is an integration step."

prod:
  stage: prod
  script:
    - echo "This is a deploy step."
```

**Explanation**:
- The `test` job has `exit 1`, which fails the build due to a non-zero exit code.
- CI/CD systems use exit codes to determine if a job passed or failed.

**Note**: By default, most CI/CD systems take the last command’s exit code. If you have multiple commands and some failed, but the last one passed, then the CI system will mark the job as passed.

## Allowing Job Failure

**YAML File Example**:
```yaml
test:
  stage: test
  script:
     - execute_script_that_will_fail
  allow_failure: true   #<--- allow the build to fail but don't mark it as such
```

**Explanation**:
- The `allow_failure: true` tag ensures that even if the job fails, it won't block other jobs and stages.

## Managing Found Vulnerabilities

To store scan results in a file and save it on the CI system for further processing, use the `artifacts` tag.

**YAML File Example**:
```yaml
someScan:
  script: ./security-tool.sh    # <-- this tool generates vulnerabilities.json as output
  artifacts:                    # <--- To save results, we use artifacts tag
    paths:                      # <--- We then give the path/paths of the scan result files we want to store for further processing
    - vulnerabilities.json      #<--- The filename
    expire_in: 1 week           # <--- To save disk space, we want to store only for 1 week
```

**Explanation**:
- Specify the output file’s path and the expiration under the `artifacts` tag.

## Human Approval Process

Using a `when: manual` tag, you can enforce human intervention to run a job.

**YAML File Example**:
```yaml
deploy_prod:
  stage: deploy
  script:
    - echo "Deploy to prod server."
  when: manual   #<-- A human has to click a button (play button in Gitlab) for this task to execute.
```

**Explanation**:
- The `when: manual` tag requires human approval before the job runs.

## Summary

- All methods (native installation, package manager, Docker) are suitable for CI/CD integration.
- **Docker**: Recommended for CI/CD as it operates smoothly without dependencies.
- **Binary File**: Efficient, avoiding additional dependencies.

**Check Results**:
- Visit [Pipeline Results](https://gitlab-ce-oiahxczk.lab.practical-devsecops.training/root/django-nv/pipelines).
- Click on the appropriate job name to see the pipeline output.

# Challenges 

## Create four stages, namely build, test, integration, and deploy

```yaml
stages: 
  - build
  - test
  - integration
  - deploy
```

## Create a file using the echo "this is an output" > output.txt command in the integration stage and upload output.txt using the artifacts keyword

```yaml
integration:
  stage: integration
  script:
    echo "this is an output" > output.txt
    exit 1
  artifacts:
    paths: [output.txt]
  when: always
  allow_failure: true
```

## In the deploy job, add a term for giving a person’s approval (via a button such as a play button) in order to execute the job

```yaml
deploy:
  stage: deploy
  script:
    echo "Deploying an app"
  when: manual 
```
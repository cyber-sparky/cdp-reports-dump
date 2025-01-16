
# Introduction to Artifact

## What Is An Artifact?

**Artifact**: An output generated during the CI/CD pipeline process. This could be a result file from the application or any item necessary for building an application.

### Why Are Artifacts Important In CI/CD?

Artifacts are crucial in the CI/CD pipeline because:

- They represent the state of a project at a given point in time.
- They can be preserved and shared across different stages of the pipeline.
- They can be used to rollback a release to a previous version if something goes wrong.

Each step of the CI/CD pipeline produces an artifact that the next step can use.

## How Artifacts Work In Pipeline?

The process of handling artifacts in a pipeline:

1. **Triggering the Pipeline**: 
   - The user triggers the pipeline using a pipeline file (e.g., `.gitlab-ci.yml`, `Jenkinsfile`).
   - The pipeline runs each job sequentially based on defined rules.
   - Jobs include rules to generate artifacts upon completion.

2. **Job Execution**:
   - Jobs run in the pipeline.
   - If the job completes successfully, it generates an artifact based on the rules defined in the pipeline file.

3. **Deployment**:
   - Once all jobs are completed, the deployment process is triggered.

**Conclusion**: Artifacts are integral to the CI/CD pipeline, impacting the speed, safety, and reliability of software delivery.

## Saving Job Result As Artifact In GitLab

To store scan results in a file and save them on the CI system for further processing, you can use the `artifacts` tag.

### Example Pipeline Configuration

**Job to Store Scan Results as Artifacts**

```yaml
someScan:
  script: 
    - ./security-tool.sh  # Example script generating an output file
  artifacts:              # Use artifacts tag to save results
    paths:                # Specify the path of the scan result files to store
      - vulnerabilities.json  # Filename
    when: always
    expire_in: 1 week     # Store only for 1 week to save disk space
```

**Explanation:**

- **Artifacts Tag**: Specifies files to store for further processing.
- **Path**: The path to the output file.
- **Expiration**: Duration to keep the file.

### Simplified Pipeline Example

To illustrate, we will echo a JSON string into a file instead of running a security tool.

**Pipeline Stages and Jobs**

```yaml
stages: 
  - build
  - test
  - integration
  - prod

build:   
  stage: build
  script:
    - echo "This is a build step"
    - echo "{\"vulnerability\":\"SQL Injection\"}" > vulnerabilities.json
  artifacts:
    paths: [vulnerabilities.json]
    when: always
    expire_in: 1 week

test:
  stage: test
  script:
    - echo "This is a test step."
    - exit 1         # Non-zero exit code to fail the job
  allow_failure: true  # Allow the build to fail without marking it as such

integration:  
  stage: integration
  script:
    - echo "This is an integration step."

prod:
  stage: prod
  script:
    - echo "This is a deploy step."
```

### Running and Viewing the Pipeline

1. **Run the Pipeline**: Click the commit changes button to run the pipeline.
2. **View Results**: Visit the pipeline page and check the build job for artifacts.

### Producing Artifact from Docker Output

To produce artifacts from Docker output:

**Pipeline Example with Docker**

```yaml
stages: 
  - build
  - test
  - integration
  - prod

build:   
  stage: build
  script:
    - echo "This is a build step"

test:
  stage: test
  script:
    - echo "This is a test step."
    - docker run -i alpine sh -c "echo '{\"vulnerability\":\"XSS Injection\"}' > vulnerabilities.json"
  artifacts:
    paths: [vulnerabilities.json]
  allow_failure: true

integration:  
  stage: integration
  script:
    - echo "This is an integration step."

prod:
  stage: prod
  script:
    - echo "This is a deploy step."
```

**Handling Errors with Docker**

If the artifact is not generated due to an error, bind the local CI/CD machine path to the Docker container path.

**Modified Docker Command**

```yaml
test:
  stage: test
  script:
    - echo "This is a test step."
    - docker run -i -v $(pwd):/app alpine sh -c "echo '{\"vulnerability\":\"XSS Injection\"}' > /app/vulnerabilities.json"
  artifacts:
    paths: [vulnerabilities.json]
  allow_failure: true
```

### Viewing the Results

1. **Run the Pipeline**: Click commit changes.
2. **Check for Success**: Visit the pipeline page and ensure the job succeeded.
3. **Download Artifact**: Find and download the `vulnerabilities.json` file from the artifacts section on the right-hand side.

# Additional Resources 

- **[GitLab CI/CD Artifact](https://docs.gitlab.com/ee/ci/jobs/job_artifacts.html)**: Learn how to use artifacts in GitLab CI/CD.
- **[Jenkins Artifact](https://www.jenkins.io/doc/pipeline/tour/tests-and-artifacts/)**: Explore how to manage artifacts in Jenkins.
- **[GitHub Action Artifact](https://docs.github.com/en/actions/using-workflows/storing-workflow-data-as-artifacts)**: Understand artifact storage in GitHub Actions.
- **[Artifact DevOps](https://medium.com/@balaug3/artifact-devops-69335d054e20)**: Read about artifacts in DevOps on Medium.
### Setting Up a CI/CD Pipeline with GitHub Actions

#### 1. **Create a New Repository**
   - Sign up for a GitHub account if you haven't already.
   - Go to [GitHub New Repository](https://github.com/new).
   - Create a repository named `django.nv`.
   - Choose either **Public** or **Private**.
   - Ignore the **Initialize this repository with** section for now.
   - Click **Create repository**.

   **Note**: Since `django.nv` is intentionally vulnerable and contains hardcoded secrets, consider disabling GitHub's secret scanning policy if used publicly. 

   To disable Push protection:
   - Visit [GitHub Settings](https://github.com/username/projectname/settings/security_analysis).
   - Replace `username` and `projectname` with your GitHub username and repository name.
   - Scroll down and disable the **Push protection** setting.

#### 2. **Create a Personal Access Token (PAT)**
   - Go to [GitHub Personal Access Tokens](https://github.com/settings/personal-access-tokens/new).
   - Give your token a name, e.g., `django`.
   - Select **All repositories** in the Repository access section.
   - Set Repository permissions to **Read & Write** for:
     - Actions
     - Contents
     - Workflows
   - Click **Generate token**.
   - Copy the token and save it in your DevSecOps Box.

#### 3. **Initial Git Setup**
   - Configure Git with your email and username:
     ```bash
     git config --global user.email "your_email@gmail.com"
     git config --global user.name "your_username"
     ```

#### 4. **Download the Repository**
   - Clone the repository:
     ```bash
     git clone https://gitlab.practical-devsecops.training/pdso/django.nv.git
     ```
   - Navigate into the repository:
     ```bash
     cd django.nv
     ```
   - Rename the Git remote to point to GitHub:
     ```bash
     git remote rename origin old-origin
     git remote add origin https://github.com/username/django.nv.git
     ```
   - Push the code to GitHub:
     ```bash
     git push -u origin --all
     ```

   **Note**: If you have previously set up a GitHub repository, use:
   ```bash
   git push -u origin main --force
   ```
   To remove an incorrect remote origin, use:
   ```bash
   git remote remove origin
   ```

#### 5. **Add a Workflow File**
   - Create the necessary directories and file:
     ```bash
     mkdir -p .github/workflows
     ```
   - Add a simple workflow to `.github/workflows/demo.yaml`:
     ```yaml
     name: Django  # Workflow name

     on:
       push:  # Trigger on push to the main branch
         branches:
           - main

     jobs:
       build:
         runs-on: ubuntu-20.04  # Environment for the job
         steps:
           - run: echo "This is a build step"  # Command to execute

       test:
         runs-on: ubuntu-20.04
         steps:
           - run: echo "This is a test step"

       integration:
         runs-on: ubuntu-20.04
         steps:
           - run: echo "This is an integration step"
           - run: exit 1  # This will cause the job to fail

       prod:
         runs-on: ubuntu-20.04
         steps:
           - run: echo "This is a deploy step"
     ```
   - Add and commit the workflow file:
     ```bash
     git add .github/workflows/demo.yaml
     git commit -m "Add GitHub Actions workflow"
     ```

#### 6. **Push the Changes**
   - Push the changes to GitHub:
     ```bash
     git push origin main
     ```

   **Note**: Changes pushed to the repository will trigger the pipeline defined in GitHub Actions.

### Observing the Pipeline Results

- Go to your GitHub repository.
- Click on the **Actions** tab to view the pipeline results.
- You’ll notice that the `integration` job fails due to `exit 1`, while other jobs run successfully.

#### Why Does the Integration Job Fail?
- In GitHub Actions, jobs can run sequentially or in parallel. A failure in one job does not necessarily stop other jobs unless dependencies are explicitly defined.
- By default, jobs in GitHub Actions run independently unless specified otherwise. The `integration` job failing doesn’t prevent other jobs from completing unless they depend on the integration job’s success.

### Additional Resources
- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Introduction to GitHub Actions](https://docs.github.com/en/actions/learn-github-actions/introduction-to-github-actions)

# Challenges

### Understand the demo.yaml provided in the previous step and figure out why prod job didn’t fail even though integration job failed?

In GitHub Actions, jobs run independently by default. In the `demo.yaml` file, the `prod` job runs after the `integration` job, but it doesn’t automatically fail if the `integration` job fails. This is because jobs do not fail other jobs by default.

### How to Make `prod` Fail When `integration` Fails

To ensure that the `prod` job only runs if all previous jobs succeed, you need to specify job dependencies using the `needs` keyword. This way, if a job that `prod` depends on fails, `prod` will not run.

Here's how you can set it up:

```yaml
jobs:
  build:
    runs-on: ubuntu-20.04
    steps:
      - run: echo "This is a build step"

  test:
    runs-on: ubuntu-20.04
    needs: build  # Runs after build job
    steps:
      - run: echo "This is a test step"

  integration:
    runs-on: ubuntu-20.04
    needs: test  # Runs after test job
    steps:
      - run: echo "This is an integration step"
      - run: exit 1  # Causes this job to fail

  prod:
    runs-on: ubuntu-20.04
    needs: [build, test, integration]  # Runs after all previous jobs
    steps:
      - run: echo "This is a deploy step"
```

### Key Points

- **`needs` Keyword**: Specifies which jobs must complete before the current job runs.
- **Job Failure**: If a job listed in `needs` fails, the dependent job (e.g., `prod`) will not run. 

By using `needs`, you ensure that the `prod` job will only run if all previous jobs (`build`, `test`, `integration`) succeed.

# Ensure jobs are run sequentially, and prevent the integration job from failing by adding syntax similar to allow_failure: true

To ensure that jobs run sequentially and to prevent the `integration` job from causing the pipeline to fail (similar to `allow_failure: true` in GitLab CI), you can use GitHub Actions' `continue-on-error` syntax. Here's how to achieve this in your `demo.yaml` file:

### Updated `demo.yaml`

```yaml
name: Django

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-20.04
    steps:
      - run: echo "This is a build step"

  test:
    runs-on: ubuntu-20.04
    needs: build  # Runs after build job
    steps:
      - run: echo "This is a test step"

  integration:
    runs-on: ubuntu-20.04
    needs: test  # Runs after test job
    continue-on-error: true  # Prevents this job from failing the pipeline
    steps:
      - run: echo "This is an integration step"
      - run: exit 1  # This will not fail the pipeline

  prod:
    runs-on: ubuntu-20.04
    needs: [build, test, integration]  # Runs after all previous jobs
    steps:
      - run: echo "This is a deploy step"
```

### Explanation

- **Sequential Execution**: By using the `needs` keyword, you ensure that jobs run in sequence. For example, `test` will run only after `build`, and `integration` will run after `test`.

- **`continue-on-error`**: This option allows the `integration` job to fail without affecting the overall pipeline status. Even if `integration` fails, the `prod` job will still run because `continue-on-error: true` prevents the job failure from stopping the pipeline.

### Key Points

- **`needs`**: Ensures jobs run in a specific order.
- **`continue-on-error`**: Allows a job to fail without failing the entire pipeline, similar to `allow_failure: true` in GitLab CI.

# Add another job called artifact, then create a file using a simple echo command (e.g. echo “hello” > hello.txt) and save it as an artifact

To add a job called `artifact` that creates a file and saves it as an artifact, you can modify your `demo.yaml` workflow as follows:

### Updated `demo.yaml`

```yaml
name: Django

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-20.04
    steps:
      - run: echo "This is a build step"

  test:
    runs-on: ubuntu-20.04
    needs: build  # Runs after build job
    steps:
      - run: echo "This is a test step"

  integration:
    runs-on: ubuntu-20.04
    needs: test  # Runs after test job
    continue-on-error: true  # Prevents this job from failing the pipeline
    steps:
      - run: echo "This is an integration step"
      - run: exit 1  # This will not fail the pipeline

  artifact:
    runs-on: ubuntu-20.04
    needs: [build, test, integration]  # Runs after all previous jobs
    steps:
      - run: echo "hello" > hello.txt  # Create a file named hello.txt
      - uses: actions/upload-artifact@v3  # Use the GitHub Actions artifact upload action
        with:
          name: hello-file  # Name of the artifact
          path: hello.txt   # Path to the file to be uploaded

  prod:
    runs-on: ubuntu-20.04
    needs: [build, test, integration, artifact]  # Runs after all previous jobs, including artifact
    steps:
      - run: echo "This is a deploy step"
```

### Explanation

- **`artifact` Job**:
  - **`runs-on`**: Specifies the environment to run the job.
  - **`needs`**: Ensures that this job runs only after `build`, `test`, and `integration` jobs have completed.
  - **`steps`**:
    - **`run: echo "hello" > hello.txt`**: Creates a file named `hello.txt` with the content "hello".
    - **`uses: actions/upload-artifact@v3`**: Uploads the created file as an artifact. 
      - **`name`**: The name assigned to the artifact.
      - **`path`**: The path to the file that will be uploaded.

- **`prod` Job**:
  - **`needs`**: Specifies that `prod` will run after the `artifact` job has completed.

### Key Points

- **Artifact Upload**: The `actions/upload-artifact` action is used to upload files from your workflow as artifacts, which can be accessed later.
- **Sequential Execution**: The `needs` keyword ensures that jobs run in the specified order.

This setup will create an `artifact` job that generates a file and makes it available as an artifact, and `prod` will run only after all other jobs have completed.
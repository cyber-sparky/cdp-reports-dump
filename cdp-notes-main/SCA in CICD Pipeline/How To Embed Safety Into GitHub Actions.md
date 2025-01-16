
## Simple Github action that runs safety 

```yml
name: Django                                  # workflow name

on:
  push:                                       
    branches:                                 # similar to "only" in GitLab
      - main

jobs:
  build:
    runs-on: ubuntu-20.04                    # similar to "image" in GitLab
    steps:
      - uses: actions/checkout@v2

      - name: Setup python
        uses: actions/setup-python@v2
        with:
          python-version: '3.6'

      - run: |
          pip3 install --upgrade virtualenv
          virtualenv env
          source env/bin/activate
          pip install -r requirements.txt
          python manage.py check

  test:
    runs-on: ubuntu-20.04
    needs: build
    steps:
      - uses: actions/checkout@v2

      - name: Setup python
        uses: actions/setup-python@v2
        with:
          python-version: '3.6'

      - run: |
          pip3 install --upgrade virtualenv
          virtualenv env
          source env/bin/activate
          pip install -r requirements.txt
          python manage.py test taskManager

  integration:
    runs-on: ubuntu-20.04
    needs: test
    steps:
      - run: echo "This is an integration step"
      - run: exit 1
        continue-on-error: true

  prod:
    runs-on: ubuntu-20.04
    needs: integration
    steps:
      - run: echo "This is a deploy step."
```

## Some points to remember 

1. **Embedding Safety Tool in CI/CD**:
   - Use the Safety tool for SCA in your CI/CD pipeline to handle open-source and third-party components effectively.

2. **Manual Command Testing**:
   - Always run and test commands on a local system like DevSecOps Box before automating them in the CI/CD pipeline for easier troubleshooting.

3. **SCA Scan Placement**:
   - Perform SCA scans before static analysis to tackle vulnerabilities early, given the high proportion of non-original code in projects.

4. **GitHub Actions Configuration**:
   - Modify the `.github/workflows/main.yaml` file to integrate the Safety tool:
     ```yaml
     oast:
       runs-on: ubuntu-20.04
       needs: test
       steps:
         - uses: actions/checkout@v2
         - run: docker run --rm -v $(pwd):/src hysnsec/safety check -r requirements.txt --json > oast-results.json
         - uses: actions/upload-artifact@v2
           with:
             name: Safety
             path: oast-results.json
           if: always()  # Ensure this step always runs regardless of previous failures
     ```

5. **Workflow Activation and Monitoring**:
   - Any changes to the repository activate the pipeline.
   - View results by accessing the Actions tab of your repository, such as `django.nv`, and selecting the appropriate workflow.

## Allow Failures 

- **Purpose**: Allows specific jobs or steps within a job to fail without stopping the entire pipeline. This is useful in early stages of development or in environments where failure is acceptable, like with potential false positives in security scans.
- **Usage**: Add `continue-on-error: true` to any step within your GitHub Actions workflow to prevent that step's failure from affecting the rest of the job or workflow.

Here's an example of how to implement this in a job:

```yaml
jobs:
  oast:
    runs-on: ubuntu-20.04
    needs: test
    steps:
      - uses: actions/checkout@v2
      - run: docker run --rm -v $(pwd):/src hysnsec/safety check -r requirements.txt --json > oast-results.json
        continue-on-error: true
      - uses: actions/upload-artifact@v2
        with:
          name: Safety
          path: oast-results.json
        if: always()
```


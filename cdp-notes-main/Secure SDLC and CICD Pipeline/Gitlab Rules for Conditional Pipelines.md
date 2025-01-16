
# Rules 

- **Rules Attributes**: `rules` is a YAML syntax in GitLab CI used to include or exclude jobs in pipelines, replacing the older `only` and `except` clauses. This syntax allows you to control when a job is triggered based on conditions defined in the `.gitlab-ci.yml` file.

- **Usage Examples**:
  - **If Statements**:
    - For instance, you can specify a job to trigger only if changes occur on the `main` branch: 
      ```yaml
      rules:
        - if: '$CI_COMMIT_BRANCH == "main"'
      ```
    - Jobs can also be conditioned on the source of the pipeline, such as a merge request event:
      ```yaml
      rules:
        - if: '$CI_PIPELINE_SOURCE == "merge_request_event"'
      ```

  - **Changes Clause**:
    - Jobs can be set to run only when specific files, like a `Dockerfile`, are modified:
      ```yaml
      rules:
        - changes:
            - Dockerfile
      ```

  - **Exists Clause**:
    - A job might be configured to run only if certain files exist:
      ```yaml
      rules:
        - exists:
            - Dockerfile
      ```

  - **Allow Failure**:
    - You can allow a job to fail without impacting the overall pipeline status:
      ```yaml
      rules:
        - if: '$CI_COMMIT_BRANCH == "main"'
          allow_failure: true
      ```

- **Default Behavior**:
  - If no attributes are defined under `rules`, the defaults are `when: on_success` and `allow_failure: false`. This means the job executes only when previous jobs in earlier stages succeed, and it will cause the entire build to fail if this job fails.

- **Conditional Execution**:
  - By combining conditions, you can create complex rules, such as a job that only runs on a specific branch when a particular file changes.
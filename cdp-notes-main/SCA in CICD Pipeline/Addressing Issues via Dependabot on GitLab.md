
## Configuring Dependabot on gitlab 

```sh
mkdir /django-nv/.gitlab
cat > /django-nv/.gitlab/dependabot.yml <<EOF
version: 2
updates:
  - package-ecosystem: npm
    directory: /
EOF
```
## Pushing it to main branch 

```sh
git add .gitlab/
git commit -m "add dependabot configuration file"
git push origin main
```

## Gitlab workflow for running dependabot 

```yml
image: docker:20.10

services:
  - docker:dind

stages:
  - build

.npm:
  stage: build
  image:
    name: docker.io/andrcuns/dependabot-gitlab:0.11.0
    entrypoint: [""]
  variables:
    GIT_STRATEGY: none
    PACKAGE_MANAGER: npm
    RAILS_ENV: production
    SETTINGS__GITLAB_URL: $CI_SERVER_URL
    SETTINGS__STANDALONE: "true"
  before_script:
    - cd /home/dependabot/app
  script:
    - bundle exec rake "dependabot:update[$CI_PROJECT_NAMESPACE/$CI_PROJECT_NAME,$PACKAGE_MANAGER,/]"

dependabot:
  extends: .npm
  when: manual
```

## References

- **Dependabot-GitLab Repository:** [https://gitlab.com/dependabot-gitlab/dependabot](https://gitlab.com/dependabot-gitlab/dependabot)
- **Running Dependabot on GitLab:** [https://dependabot-gitlab.gitlab.io/dependabot/](https://dependabot-gitlab.gitlab.io/dependabot/)
- **Setting Dependabot-GitLab Environment:** [https://dependabot-gitlab.gitlab.io/dependabot/config/environment.html](https://dependabot-gitlab.gitlab.io/dependabot/config/environment.html)


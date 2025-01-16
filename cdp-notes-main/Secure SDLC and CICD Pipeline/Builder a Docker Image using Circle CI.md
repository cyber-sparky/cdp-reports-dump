### Build a Docker Image in CircleCI

**1. Create `.circleci` Directory and `config.yml` File**

   ```bash
   mkdir -p .circleci
   ```

   Add the following CI script to `.circleci/config.yml`:

   ```yaml
   jobs:
     build:
       docker:
         - image: python:3.6                   # similar to "image" in GitLab
       steps:
         - checkout
         - run: |                              # similar to "script" in GitLab
             pip install -r requirements.txt
             python manage.py check

     test:
       docker:
         - image: python:3.6
       steps:
         - checkout
         - run: |
             pip install -r requirements.txt
             python manage.py test taskManager

     integration:
       docker:
         - image: python:3.6
       steps:
         - checkout
         - run:
             command: |
               echo "This is an integration step"
               exit 1
             when: always                    # Even if the job fails, continue to the next stages

     prod:
       docker:
         - image: python:3.6
       steps:
         - checkout
         - run: echo "This is a deploy step."

   workflows:
     version: 2
     django:
       jobs:
         - build
         - test:
             requires:
               - build 
         - integration:
             requires:
               - test
         - prod:
             type: approval
             requires:
               - integration
   ```

   For more details on CircleCI syntax, refer to [CircleCI Configuration Reference](https://circleci.com/docs/2.0/configuration-reference/).

**2. Add and Commit the Configuration File**

   ```bash
   git add .circleci/config.yml
   git commit -m "Add CircleCI config"
   ```

**3. Push Changes to the Remote Repository**

   ```bash
   git push origin main
   ```

   Example output:
   ```text
   Counting objects: 5, done.
   Delta compression using up to 16 threads.
   Compressing objects: 100% (3/3), done.
   Writing objects: 100% (5/5), 577 bytes | 577.00 KiB/s, done.
   Total 5 (delta 1), reused 0 (delta 0)
   remote: Resolving deltas: 100% (1/1), completed with 1 local object.
   To https://github.com/username/django.nv.git
      df066a2..98e754f  main -> main
   ```

**4. View Pipeline Results**

   - Visit your CircleCI account.
   - Click "Projects," select your `django.nv` repository, and view the pipeline results.

Here are the notes on adding a `release` job to your CircleCI configuration for building and pushing a Docker image:

---

### Adding a `release` Job in CircleCI

**1. Configure Environment Variables**

   - Visit [CircleCI Project Settings](https://app.circleci.com/pipelines/github/username/django.nv).
   - Click on "Project Settings" in the top right.
   - Select "Environment Variables."
   - Add the following environment variables:

     | Name           | Value                   |
     |----------------|-------------------------|
     | `DOCKER_USERNAME` | Your DockerHub username |
     | `DOCKER_PASSWORD` | Your DockerHub password |

**2. Update `.circleci/config.yml`**

   - Edit the `.circleci/config.yml` file on the DevSecOps Box machine using a text editor like `nano` or `vim`.
   - Add the following `release` job after the `test` job:

     ```yaml
     release:
       machine: true                                        # Provides a full VM with Docker
       steps:
         - checkout

         - run: |
             echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin
             docker build -t $DOCKER_USERNAME/django.nv .   # Build the Docker image
             docker push $DOCKER_USERNAME/django.nv         # Push the image to DockerHub
     ```

   - Save changes to the file.

   - **Note:** Do not hardcode credentials directly into `.circleci/config.yml`. Use environment variables instead.

**3. Add, Commit, and Push the Configuration File**

   ```bash
   git add .circleci/config.yml
   git commit -m "Update CI script"
   git push origin main
   ```

   Example output:
   ```bash
   Counting objects: 5, done.
   Delta compression using up to 16 threads.
   Compressing objects: 100% (3/3), done.
   Writing objects: 100% (5/5), 577 bytes | 577.00 KiB/s, done.
   Total 5 (delta 1), reused 0 (delta 0)
   remote: Resolving deltas: 100% (1/1), completed with 1 local object.
   To https://github.com/username/django.nv.git
      df066a2..98e754f  main -> main
   ```

**4. View Pipeline Results**

   - Visit your CircleCI account.
   - Click "Projects," select your `django.nv` repository, and view the pipeline results.

---
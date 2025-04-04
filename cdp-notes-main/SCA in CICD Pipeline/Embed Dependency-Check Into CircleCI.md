
## Bash script for Automating depedency check 

```sh
#!/bin/sh

DATA_DIRECTORY="$PWD/data"
REPORT_DIRECTORY="$PWD/reports"

if [ ! -d "$DATA_DIRECTORY" ]; then
  echo "Initially creating persistent directories"
  mkdir -p "$DATA_DIRECTORY"
  chmod -R 777 "$DATA_DIRECTORY"

  mkdir -p "$REPORT_DIRECTORY"
  chmod -R 777 "$REPORT_DIRECTORY"
fi

cd webgoat-container

# Make sure we are using the latest version
docker pull owasp/dependency-check

docker run --rm \
  --volume $(pwd):/src \
  --volume "$DATA_DIRECTORY":/usr/share/dependency-check/data \
  --volume "$REPORT_DIRECTORY":/reports \
  owasp/dependency-check \
  --scan /src \
  --format "JSON" \
  --project "Webgoat" \
  --failOnCVSS 8 \
  --out /reports
```

## CircleCI Job for running bash script  

```yml
  odc-backend:
    machine: true
    steps:
      - checkout

      - run: chmod +x ./run-depcheck.sh && ./run-depcheck.sh

      - store_artifacts:
          path: reports/dependency-check-report.json
          destination: depcheck-artifact
```

## Workflow for CircleCI

```yml
workflows:
  version: 2
  webgoat:
    jobs:
      - build
      - test:
          requires:
            - build
      - odc-backend:
          requires:
            - test
```

## Installing dastardly 

```sh
docker pull public.ecr.aws/portswigger/dastardly
```

## Running a scan and getting an output in XML format 

```sh
docker run --user $(id -u) --rm -v $(pwd):/dastardly -e BURP_START_URL=https://casp-pm-oiahxczk.lab.practical-devsecops.training/apispec_1.json -e BURP_REPORT_FILE_PATH=/dastardly/dastardly-output.xml public.ecr.aws/portswigger/dastardly
```

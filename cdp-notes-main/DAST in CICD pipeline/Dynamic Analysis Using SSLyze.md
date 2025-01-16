
SSLyze is a fast and powerful SSL/TLS scanning library.

It allows you to analyze the SSL/TLS configuration of a server by connecting to it, in order to detect various issues (bad certificate, weak cipher suites, Heartbleed, ROBOT, TLS 1.3 support, etc.).

SSLyze can either be used as command line tool or as a Python library.

## Installing SSLyze 

```sh
pip3 install sslyze==5.0.3
```

## Running a scan and output in a JSON format 

```sh
sslyze --json_out sslyze-output.json prod-oiahxczk.lab.practical-devsecops.training:443
```
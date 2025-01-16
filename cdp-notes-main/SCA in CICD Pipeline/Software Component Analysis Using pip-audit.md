
## Cloning a Project 

```bash
git clone https://gitlab.practical-devsecops.training/pdso/django.nv webapp
cd webapp
```

## Installing a tool 

```bash
pip3 install pip-audit==1.1.2
```

## Scanning a project 

```bash
/webapp# pip-audit -r ./requirements.txt -f json | tee pip-audit-output.json
WARNING:pip_audit._cache:Warning: pip 20.0.2 doesn't support the `cache dir` subcommand, using /root/.pip-audit-cache instead
Found 25 known vulnerabilities in 1 packages
[{"name": "django", "version": "3.0", "vulns": [{"id": "PYSEC-2021-9", "fix_versions": ["2.2.18", "3.0.12", "3.1.6"]}, {"id": "PYSEC-2020-33", "fix_versions": ["2.2.16", "3.0.10", "3.1.1"]}, {"id": "PYSEC-2020-31", "fix_versions": ["2.2.13", "3.0.7"]}, {"id": "PYSEC-2020-36", "fix_versions": ["1.11.29", "2.2.11", "3.0.4"]}, {"id": "PYSEC-2021-98", "fix_versions": ["2.2.24", "3.1.12", "3.2.4"]}, {"id": "PYSEC-2021-6", "fix_versions": ["2.2.20", "3.0.14", "3.1.8"]}, {"id": "PYSEC-2021-99", "fix_versions": ["2.2.24", "3.1.12", "3.2.4"]}, {"id": "PYSEC-2020-35", "fix_versions": ["1.11.28", "2.2.10", "3.0.3"]}, {"id": "PYSEC-2020-34", "fix_versions": ["2.2.16", "3.0.10", "3.1.1"]}, {"id": "PYSEC-2020-32", "fix_versions": ["2.2.13", "3.0.7"]}, {"id": "GHSA-wpjr-j57x-wxfw", "fix_versions": ["2.2.13", "3.0.7"]}, {"id": "GHSA-68w8-qjq3-2gfm", "fix_versions": ["2.2.24", "3.1.12", "3.2.4"]}, {"id": "GHSA-xgxc-v2qg-chmh", "fix_versions": ["2.2.20", "3.0.14", "3.1.8"]}, {"id": "GHSA-vfq6-hq5r-27r6", "fix_versions": ["1.11.27", "2.2.9", "3.0.1"]}, {"id": "GHSA-fr28-569j-53c4", "fix_versions": ["2.2.16", "3.0.10", "3.1.1"]}, {"id": "GHSA-3gh2-xw74-jmcw", "fix_versions": ["1.11.29", "2.2.11", "3.0.4"]}, {"id": "GHSA-v6rh-hp5x-86rv", "fix_versions": ["2.2.25", "3.1.14", "3.2.10"]}, {"id": "GHSA-rxjp-mfm9-w4wr", "fix_versions": ["2.2.21", "3.1.9", "3.2.1"]}, {"id": "GHSA-fvgf-6h6h-3322", "fix_versions": ["2.2.18", "3.0.12", "3.1.6"]}, {"id": "GHSA-hmr4-m2h5-33qx", "fix_versions": ["1.11.28", "2.2.10", "3.0.3"]}, {"id": "GHSA-xpfp-f569-q3p2", "fix_versions": ["3.1.13", "3.2.5"]}, {"id": "GHSA-m6gj-h9gm-gw44", "fix_versions": ["2.2.16", "3.0.10", "3.1.1"]}, {"id": "GHSA-p99v-5w3c-jqq9", "fix_versions": ["2.2.24", "3.1.12", "3.2.4"]}, {"id": "GHSA-2m34-jcjv-45xf", "fix_versions": ["2.2.13", "3.0.7"]}, {"id": "GHSA-xxj9-f6rv-m3x4", "fix_versions": ["3.2.24", "4.2.10", "5.0.2"]}]}, {"name": "sqlparse", "version": "0.5.1", "vulns": []}, {"name": "asgiref", "version": "3.8.1", "vulns": []}, {"name": "typing-extensions", "version": "4.12.2", "vulns": []}, {"name": "pytz", "version": "2024.1", "vulns": []}]
```
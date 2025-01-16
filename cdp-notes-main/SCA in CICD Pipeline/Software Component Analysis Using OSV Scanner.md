
## Install OSV scanner 

```sh
wget -O /usr/bin/osv-scanner https://github.com/google/osv-scanner/releases/download/v1.4.0/osv-scanner_1.4.0_linux_amd64 && sudo chmod +x /usr/bin/osv-scanner
osv-scanner --help
```

## Running the scanner 

```sh
osv-scanner .
```

## Output format 

| **Term**        | **Description** |
|-----------------|-----------------|
| **OSV URL**     | These links provide comprehensive descriptions of each detected vulnerability. Following these links imparts further understanding over the threat and any available fixes or mitigations. |
| **CVSS**        | An acronym for Common Vulnerability Scoring System. It’s an industry-standard method for rating the severity of security vulnerabilities within software, with scores ranging from 0 to 10. More elevated scores imply more critical vulnerabilities. |
| **ECOSYSTEM**   | This indicates the software or runtime environment in which the package finds typical use, for instance, npm or PyPI. |
| **PACKAGE**     | This signifies the distinctive software package within that ecosystem that has a known vulnerability. Packages such as adm-zip, bl, brace-expansion in the npm ecosystem, or django in the PyPI ecosystem, feature in the table. |
| **VERSION**     | This denotes the exact version of the package comprising the vulnerability. It’s possible that only certain versions of a package are affected. |
| **SOURCE**      | This column informs of the file location of the vulnerable package. In your case, vulnerabilities were detected in the package-lock.json file for npm packages and requirements.txt file for PyPI packages. |


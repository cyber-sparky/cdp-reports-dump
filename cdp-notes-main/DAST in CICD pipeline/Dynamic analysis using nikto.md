
## Install nikto 

```sh
apt install -y libnet-ssleay-perl
git clone https://github.com/sullo/nikto
cd nikto/program
git checkout tags/2.1.6
./nikto.pl -Help
```

## Running a scan and output into xml

```sh
./nikto.pl -output nikto_output.xml -h prod-oiahxczk
```

## Parameters

Before diving into the challenges, let’s explore some of the important flags and options in the `nikto.conf` file, located in the `/nikto/program/` directory. Understanding these options will be invaluable for running Nikto more efficiently.

| **Parameter**     | **Description and Usage**                                                                                                                                          |
|-------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **SKIPIDS**       | **Function:** Allows skipping certain false-positive checks during the scan. <br> **Usage:** Specify a comma-separated list of test ID numbers to skip.             |
| **SKIPPORTS**     | **Function:** Specifies ports that Nikto will not scan, even if they are open. <br> **Usage:** Provide a comma-separated list of ports to skip during the scan.     |
| **CLIOPTS**       | **Function:** Defines the command-line options to be used with Nikto. <br> **Usage:** Customize Nikto’s behavior by specifying desired command-line options.         |
| **STATIC-COOKIE** | **Function:** Allows setting a static string as the Cookie: header in HTTP requests sent by Nikto. <br> **Usage:** Set a static cookie value if required.            |
| **TIMEOUT**       | **Function:** Sets the timeout value (in seconds) for HTTP requests made by Nikto. <br> **Usage:** Adjust the timeout based on network conditions and responsiveness.|
| **EXTRAREQS**     | **Function:** Determines whether Nikto should use extra, non-normative HTTP methods to attempt to reveal additional information or vulnerabilities. <br> **Usage:** Set to 0 (disable) or 1 (enable).|

## Challenges 

### List Available Plugins in Nikto

```sh
./nikto.pl -list-plugins 
```

### Perform Scan Using the ‘headers’ Plugin scan against prod-oiahxczk in Nikto

```sh
./nikto.pl -h prod-oiahxczk -Plugins headers
```

### Configure Nikto (nikto.conf) in the /nikto/program/ directory to exclude ports 21, 22, and 111 from scanning, and then save the scan results in CSV format with the output file named result.csv

```sh
cat >/nikto/program/nikto.conf<<EOF
SKIPPORTS=21 22 111
CLIOPTS=-output result.csv -Format csv
EOF
./nikto.pl -h prod-oiahxczk -config nikto.conf
```
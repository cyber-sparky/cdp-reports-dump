## Cloning a Application 

We can clone the following repo `https://gitlab.practical-devsecops.training/pdso/django.nv` using github 

```bash 
/# git clone https://gitlab.practical-devsecops.training/pdso/django.nv webapp
Cloning into 'webapp'...
warning: redirecting to https://gitlab.practical-devsecops.training/pdso/django.nv.git/
remote: Enumerating objects: 291, done.
remote: Counting objects: 100% (3/3), done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 291 (delta 0), reused 0 (delta 0), pack-reused 288
Receiving objects: 100% (291/291), 1.05 MiB | 29.17 MiB/s, done.
Resolving deltas: 100% (113/113), done.
```

## Install safety using pip

Safety is a tool which helps in scanning components available on python based applications 

```bash
pip3 install safety==2.3.5
```

## Scan using safety 

Once after cloning, go into the directory and run the following command:

```bash 
/webapp# safety check -r requirements.txt --json | tee safety-output.json
{
    "report_meta": {
        "scan_target": "files",
        "scanned": [
            "requirements.txt"
        ],
        "policy_file": null,
        "policy_file_source": "local",
        "api_key": false,
        "local_database_path": null,
        "safety_version": "2.3.5",
        "timestamp": "2024-07-22 10:47:41",
        "packages_found": 1,
        "vulnerabilities_found": 32,
        "vulnerabilities_ignored": 0,
        "remediations_recommended": 0,
        "telemetry": {
            "os_type": "Linux",
            "os_release": "5.15.0-116-generic",
            "os_description": "Linux-5.15.0-116-generic-x86_64-with-glibc2.31",
            "python_version": "3.9.5",
            "safety_command": "check",
            "safety_options": {
                "files": {
                    "-r": 1
                },
                "json": {
                    "--json": 1
                }
            },
            "safety_version": "2.3.5",
            "safety_source": "cli"
        },
        "git": {
            "branch": "main",
            "tag": "",
            "commit": "b5d54733a9364875a59fbc8252d7980b4c2bbc99",
            "dirty": false,
            "origin": "https://gitlab.practical-devsecops.training/pdso/django.nv"
        },
        "project": null,
        "json_version": 1
    },
    "scanned_packages": {
        "Django": {
            "name": "Django",
            "version": "3.0"
        }
    },
    "affected_packages": {
        "Django": {
            "name": "Django",
            "version": "3.0",
            "found": "requirements.txt",
            "insecure_versions": [],
            "secure_versions": [],
            "latest_version_without_known_vulnerabilities": null,
            "latest_version": null,
            "more_info_url": "https://data.safetycli.com"
        }
    },
    "announcements": [],
    "vulnerabilities": [
        {
            "vulnerability_id": "64976",
            "package_name": "django",
            "ignored": {},
            "ignored_reason": null,
            "ignored_expires": null,
            "vulnerable_spec": "<3.2.24",
            "all_vulnerable_specs": [
                "<3.2.24",
                ">=4.0a1,<4.2.10",
                ">=5.0a1,<5.0.2"
            ],
            "analyzed_version": "3.0",
            "advisory": "Affected versions of Django are vulnerable to potential denial-of-service in intcomma template filter when used with very long strings.",
            "is_transitive": false,
            "published_date": null,
            "fixed_versions": [],
            "closest_versions_without_known_vulnerabilities": [],
            "resources": [],
            "CVE": "CVE-2024-24680",
            "severity": null,
            "affected_versions": [],
            "more_info_url": "https://data.safetycli.com/v/64976/f17"
        },
        {
            "vulnerability_id": "48040",
            "package_name": "django",
            "ignored": {},
            "ignored_reason": null,
            "ignored_expires": null,
            "vulnerable_spec": ">=3.0a1,<3.2.13",
            "all_vulnerable_specs": [
                "<2.2.28",
                ">=3.0a1,<3.2.13",
                ">=4.0a1,<4.0.4"
            ],
            "analyzed_version": "3.0",
            "advisory": "Django 2.2.28, 3.2.13 and 4.0.4 include a fix for CVE-2022-28347: A SQL injection issue was discovered in QuerySet.explain() in Django 2.2 before 2.2.28, 3.2 before 3.2.13, and 4.0 before 4.0.4. This occurs by passing a crafted dictionary (with dictionary expansion) as the **options argument, and placing the injection payload in an option name.\r\nhttps://www.djangoproject.com/weblog/2022/apr/11/security-releases",
            "is_transitive": false,
            "published_date": null,
            "fixed_versions": [],
            "closest_versions_without_known_vulnerabilities": [],
            "resources": [],
            "CVE": "CVE-2022-28347",
            "severity": null,
            "affected_versions": [],
            "more_info_url": "https://data.safetycli.com/v/48040/f17"
        },
        {
            "vulnerability_id": "48041",
            "package_name": "django",
            "ignored": {},
            "ignored_reason": null,
            "ignored_expires": null,
            "vulnerable_spec": ">=3.0a1,<3.2.13",
            "all_vulnerable_specs": [
                "<2.2.28",
                ">=3.0a1,<3.2.13",
                ">=4.0a1,<4.0.4"
            ],
            "analyzed_version": "3.0",
            "advisory": "Django 2.2.28, 3.2.13 and 4.0.4 include a fix for CVE-2022-28346: An issue was discovered in Django 2.2 before 2.2.28, 3.2 before 3.2.13, and 4.0 before 4.0.4. QuerySet.annotate(), aggregate(), and extra() methods are subject to SQL injection in column aliases via a crafted dictionary (with dictionary expansion) as the passed **kwargs.\r\nhttps://www.djangoproject.com/weblog/2022/apr/11/security-releases",
            "is_transitive": false,
            "published_date": null,
            "fixed_versions": [],
            "closest_versions_without_known_vulnerabilities": [],
            "resources": [],
            "CVE": "CVE-2022-28346",
            "severity": null,
            "affected_versions": [],
            "more_info_url": "https://data.safetycli.com/v/48041/f17"
        },
        {
            "vulnerability_id": "55264",
            "package_name": "django",
            "ignored": {},
            "ignored_reason": null,
            "ignored_expires": null,
            "vulnerable_spec": "<3.2.19",
            "all_vulnerable_specs": [
                "<3.2.19",
                ">=4.0a1,<4.1.9",
                ">=4.2a1,<4.2.1"
            ],
            "analyzed_version": "3.0",
            "advisory": "Django 4.2.1, 4.1.9 and 3.2.19 include a fix for CVE-2023-31047: In Django 3.2 before 3.2.19, 4.x before 4.1.9, and 4.2 before 4.2.1, it was possible to bypass validation when using one form field to upload multiple files. This multiple upload has never been supported by forms.FileField or forms.ImageField (only the last uploaded file was validated). However, Django's \"Uploading multiple files\" documentation suggested otherwise.\r\nhttps://www.djangoproject.com/weblog/2023/may/03/security-releases",
            "is_transitive": false,
            "published_date": null,
            "fixed_versions": [],
            "closest_versions_without_known_vulnerabilities": [],
            "resources": [],
            "CVE": "CVE-2023-31047",
            "severity": null,
            "affected_versions": [],
            "more_info_url": "https://data.safetycli.com/v/55264/f17"
        },
        {
            "vulnerability_id": "62126",
            "package_name": "django",
            "ignored": {},
            "ignored_reason": null,
            "ignored_expires": null,
            "vulnerable_spec": "<3.2.23",
            "all_vulnerable_specs": [
                "<3.2.23",
                ">=4.0a1,<4.1.13",
                ">=4.2a1,<4.2.7"
            ],
            "analyzed_version": "3.0",
            "advisory": "Django 4.2.7, 4.1.13 and 3.2.23 include a fix for CVE-2023-46695: Potential denial of service vulnerability in UsernameField on Windows.\r\nhttps://www.djangoproject.com/weblog/2023/nov/01/security-releases",
            "is_transitive": false,
            "published_date": null,
            "fixed_versions": [],
            "closest_versions_without_known_vulnerabilities": [],
            "resources": [],
            "CVE": "CVE-2023-46695",
            "severity": null,
            "affected_versions": [],
            "more_info_url": "https://data.safetycli.com/v/62126/f17"
        },
        {
            "vulnerability_id": "38749",
            "package_name": "django",
            "ignored": {},
            "ignored_reason": null,
            "ignored_expires": null,
            "vulnerable_spec": ">=3.0a1,<3.0.10",
            "all_vulnerable_specs": [
                "<2.2.16",
                ">=3.0a1,<3.0.10",
                ">=3.1a1,<3.1.1"
            ],
            "analyzed_version": "3.0",
            "advisory": "Django 2.2.16, 3.0.10 and 3.1.1 include a fix for CVE-2020-24583: An issue was discovered in Django 2.2 before 2.2.16, 3.0 before 3.0.10, and 3.1 before 3.1.1 (when Python 3.7+ is used). FILE_UPLOAD_DIRECTORY_PERMISSIONS mode was not applied to intermediate-level directories created in the process of uploading files. It was also not applied to intermediate-level collected static directories when using the collectstatic management command.\r\n#NOTE: This vulnerability affects only users of Python versions above 3.7.\r\nhttps://www.djangoproject.com/weblog/2020/sep/01/security-releases",
            "is_transitive": false,
            "published_date": null,
            "fixed_versions": [],
            "closest_versions_without_known_vulnerabilities": [],
            "resources": [],
            "CVE": "CVE-2020-24583",
            "severity": null,
            "affected_versions": [],
            "more_info_url": "https://data.safetycli.com/v/38749/f17"
        },
        {
            "vulnerability_id": "38752",
            "package_name": "django",
            "ignored": {},
            "ignored_reason": null,
            "ignored_expires": null,
            "vulnerable_spec": ">=3.0a1,<3.0.10",
            "all_vulnerable_specs": [
                "<2.2.16",
                ">=3.0a1,<3.0.10",
                ">=3.1a1,<3.1.1"
            ],
            "analyzed_version": "3.0",
            "advisory": "An issue was discovered in Django 2.2 before 2.2.16, 3.0 before 3.0.10, and 3.1 before 3.1.1 (when Python 3.7+ is used). The intermediate-level directories of the filesystem cache had the system's standard umask rather than 0o077.",
            "is_transitive": false,
            "published_date": null,
            "fixed_versions": [],
            "closest_versions_without_known_vulnerabilities": [],
            "resources": [],
            "CVE": "CVE-2020-24584",
            "severity": null,
            "affected_versions": [],
            "more_info_url": "https://data.safetycli.com/v/38752/f17"
        },
        {
            "vulnerability_id": "60956",
            "package_name": "django",
            "ignored": {},
            "ignored_reason": null,
            "ignored_expires": null,
            "vulnerable_spec": "<3.2.21",
            "all_vulnerable_specs": [
                "<3.2.21",
                ">=4.0a1,<4.1.11",
                ">=4.2a1,<4.2.5"
            ],
            "analyzed_version": "3.0",
            "advisory": "Affected versions of Django are vulnerable to potential Denial of Service via certain inputs with a very large number of Unicode characters in django.utils.encoding.uri_to_iri().",
            "is_transitive": false,
            "published_date": null,
            "fixed_versions": [],
            "closest_versions_without_known_vulnerabilities": [],
            "resources": [],
            "CVE": "CVE-2023-41164",
            "severity": null,
            "affected_versions": [],
            "more_info_url": "https://data.safetycli.com/v/60956/f17"
        },
        {
            "vulnerability_id": "53315",
            "package_name": "django",
            "ignored": {},
            "ignored_reason": null,
            "ignored_expires": null,
            "vulnerable_spec": "<3.2.18",
            "all_vulnerable_specs": [
                "<3.2.18",
                ">=4.0a1,<4.0.10",
                ">=4.1a1,<4.1.7"
            ],
            "analyzed_version": "3.0",
            "advisory": "Django 4.1.7, 4.0.10 and 3.2.18 include a fix for CVE-2023-24580: Potential denial-of-service vulnerability in file uploads.\r\nhttps://www.djangoproject.com/weblog/2023/feb/14/security-releases",
            "is_transitive": false,
            "published_date": null,
            "fixed_versions": [],
            "closest_versions_without_known_vulnerabilities": [],
            "resources": [],
            "CVE": "CVE-2023-24580",
            "severity": null,
            "affected_versions": [],
            "more_info_url": "https://data.safetycli.com/v/53315/f17"
        },
        {
            "vulnerability_id": "44427",
            "package_name": "django",
            "ignored": {},
            "ignored_reason": null,
            "ignored_expires": null,
            "vulnerable_spec": ">=3.0a1,<3.2.11",
            "all_vulnerable_specs": [
                "<2.2.26",
                ">=3.0a1,<3.2.11",
                ">=4.0a1,<4.0.1"
            ],
            "analyzed_version": "3.0",
            "advisory": "Django 2.2.26, 3.2.11 and 4.0.1 include a fix for CVE-2021-45116: An issue was discovered in Django 2.2 before 2.2.26, 3.2 before 3.2.11, and 4.0 before 4.0.1. Due to leveraging the Django Template Language's variable resolution logic, the dictsort template filter was potentially vulnerable to information disclosure, or an unintended method call, if passed a suitably crafted key.\r\nhttps://www.djangoproject.com/weblog/2022/jan/04/security-releases",
            "is_transitive": false,
            "published_date": null,
            "fixed_versions": [],
            "closest_versions_without_known_vulnerabilities": [],
            "resources": [],
            "CVE": "CVE-2021-45116",
            "severity": null,
            "affected_versions": [],
            "more_info_url": "https://data.safetycli.com/v/44427/f17"
        },
        {
            "vulnerability_id": "44426",
            "package_name": "django",
            "ignored": {},
            "ignored_reason": null,
            "ignored_expires": null,
            "vulnerable_spec": ">=3.0a1,<3.2.11",
            "all_vulnerable_specs": [
                "<2.2.26",
                ">=3.0a1,<3.2.11",
                ">=4.0a1,<4.0.1"
            ],
            "analyzed_version": "3.0",
            "advisory": "Django 2.2.26, 3.2.11 and 4.0.1 include a fix for CVE-2021-45452: Storage.save in Django 2.2 before 2.2.26, 3.2 before 3.2.11, and 4.0 before 4.0.1 allows directory traversal if crafted filenames are directly passed to it.\r\nhttps://www.djangoproject.com/weblog/2022/jan/04/security-releases/",
            "is_transitive": false,
            "published_date": null,
            "fixed_versions": [],
            "closest_versions_without_known_vulnerabilities": [],
            "resources": [],
            "CVE": "CVE-2021-45452",
            "severity": null,
            "affected_versions": [],
            "more_info_url": "https://data.safetycli.com/v/44426/f17"
        },
        {
            "vulnerability_id": "44423",
            "package_name": "django",
            "ignored": {},
            "ignored_reason": null,
            "ignored_expires": null,
            "vulnerable_spec": ">=3.0a1,<3.2.11",
            "all_vulnerable_specs": [
                "<2.2.26",
                ">=3.0a1,<3.2.11",
                ">=4.0a1,<4.0.1"
            ],
            "analyzed_version": "3.0",
            "advisory": "Django 2.2.26, 3.2.11 and 4.0.1 include a fix for CVE-2021-45115: UserAttributeSimilarityValidator incurred significant overhead in evaluating a submitted password that was artificially large in relation to the comparison values. In a situation where access to user registration was unrestricted, this provided a potential vector for a denial-of-service attack.\r\nhttps://www.djangoproject.com/weblog/2022/jan/04/security-releases/",
            "is_transitive": false,
            "published_date": null,
            "fixed_versions": [],
            "closest_versions_without_known_vulnerabilities": [],
            "resources": [],
            "CVE": "CVE-2021-45115",
            "severity": null,
            "affected_versions": [],
            "more_info_url": "https://data.safetycli.com/v/44423/f17"
        },
        {
            "vulnerability_id": "49733",
            "package_name": "django",
            "ignored": {},
            "ignored_reason": null,
            "ignored_expires": null,
            "vulnerable_spec": "<3.2.14",
            "all_vulnerable_specs": [
                "<3.2.14",
                ">=4.0a1,<4.0.6"
            ],
            "analyzed_version": "3.0",
            "advisory": "Django 3.2.14 and 4.0.6 include a fix for CVE-2022-34265: Potential SQL injection via Trunc(kind) and Extract(lookup_name) arguments.\r\nhttps://www.djangoproject.com/weblog/2022/jul/04/security-releases",
            "is_transitive": false,
            "published_date": null,
            "fixed_versions": [],
            "closest_versions_without_known_vulnerabilities": [],
            "resources": [],
            "CVE": "CVE-2022-34265",
            "severity": null,
            "affected_versions": [],
            "more_info_url": "https://data.safetycli.com/v/49733/f17"
        },
        {
            "vulnerability_id": "40404",
            "package_name": "django",
            "ignored": {},
            "ignored_reason": null,
            "ignored_expires": null,
            "vulnerable_spec": ">=3.0a1,<3.1.9",
            "all_vulnerable_specs": [
                ">=3.2a1,<3.2.1",
                "<2.2.21",
                ">=3.0a1,<3.1.9"
            ],
            "analyzed_version": "3.0",
            "advisory": "Django 2.2.21, 3.1.9 and 3.2.1 include a fix for CVE-2021-31542: MultiPartParser, UploadedFile, and FieldFile allowed directory traversal via uploaded files with suitably crafted file names.\r\nhttps://www.djangoproject.com/weblog/2021/may/04/security-releases",
            "is_transitive": false,
            "published_date": null,
            "fixed_versions": [],
            "closest_versions_without_known_vulnerabilities": [],
            "resources": [],
            "CVE": "CVE-2021-31542",
            "severity": null,
            "affected_versions": [],
            "more_info_url": "https://data.safetycli.com/v/40404/f17"
        },
        {
            "vulnerability_id": "61586",
            "package_name": "django",
            "ignored": {},
            "ignored_reason": null,
            "ignored_expires": null,
            "vulnerable_spec": "<3.2.22",
            "all_vulnerable_specs": [
                "<3.2.22",
                ">=4.0a1,<4.1.12",
                ">=4.2a1,<4.2.6"
            ],
            "analyzed_version": "3.0",
            "advisory": "Affected versions of Django are vulnerable to Denial-of-Service via django.utils.text.Truncator. The django.utils.text.Truncator chars() and words() methods (when used with html=True) are subject to a potential DoS (denial of service) attack via certain inputs with very long, potentially malformed HTML text. The chars() and words() methods are used to implement the truncatechars_html and truncatewords_html template filters, which are thus also vulnerable. NOTE: this issue exists because of an incomplete fix for CVE-2019-14232.",
            "is_transitive": false,
            "published_date": null,
            "fixed_versions": [],
            "closest_versions_without_known_vulnerabilities": [],
            "resources": [],
            "CVE": "CVE-2023-43665",
            "severity": null,
            "affected_versions": [],
            "more_info_url": "https://data.safetycli.com/v/61586/f17"
        },
        {
            "vulnerability_id": "50454",
            "package_name": "django",
            "ignored": {},
            "ignored_reason": null,
            "ignored_expires": null,
            "vulnerable_spec": "<3.2.15",
            "all_vulnerable_specs": [
                "<3.2.15",
                ">=4.0a1,<4.0.7"
            ],
            "analyzed_version": "3.0",
            "advisory": "Django 3.2.15 and 4.0.7 include a fix for CVE-2022-36359: An issue was discovered in the HTTP FileResponse class in Django 3.2 before 3.2.15 and 4.0 before 4.0.7. An application is vulnerable to a reflected file download (RFD) attack that sets the Content-Disposition header of a FileResponse when the filename is derived from user-supplied input.\r\nhttps://www.djangoproject.com/weblog/2022/aug/03/security-releases",
            "is_transitive": false,
            "published_date": null,
            "fixed_versions": [],
            "closest_versions_without_known_vulnerabilities": [],
            "resources": [],
            "CVE": "CVE-2022-36359",
            "severity": null,
            "affected_versions": [],
            "more_info_url": "https://data.safetycli.com/v/50454/f17"
        },
        {
            "vulnerability_id": "38010",
            "package_name": "django",
            "ignored": {},
            "ignored_reason": null,
            "ignored_expires": null,
            "vulnerable_spec": ">=3.0a1,<3.0.4",
            "all_vulnerable_specs": [
                ">=1.11a1,<1.1.29",
                ">=2.2a1,<2.2.11",
                ">=3.0a1,<3.0.4"
            ],
            "analyzed_version": "3.0",
            "advisory": "Django 1.11.29, 2.2.11 and 3.0.4 includes a fix for CVE-2020-9402: Django 1.11 before 1.11.29, 2.2 before 2.2.11, and 3.0 before 3.0.4 allows SQL Injection if untrusted data is used as a tolerance parameter in GIS functions and aggregates on Oracle. By passing a suitably crafted tolerance to GIS functions and aggregates on Oracle, it was possible to break escaping and inject malicious SQL.\r\nhttps://www.djangoproject.com/weblog/2020/mar/04/security-releases",
            "is_transitive": false,
            "published_date": null,
            "fixed_versions": [],
            "closest_versions_without_known_vulnerabilities": [],
            "resources": [],
            "CVE": "CVE-2020-9402",
            "severity": null,
            "affected_versions": [],
            "more_info_url": "https://data.safetycli.com/v/38010/f17"
        },
        {
            "vulnerability_id": "39646",
            "package_name": "django",
            "ignored": {},
            "ignored_reason": null,
            "ignored_expires": null,
            "vulnerable_spec": ">=3.0a1,<3.0.13",
            "all_vulnerable_specs": [
                ">=3.0a1,<3.0.13",
                ">=3.1a1,<3.1.7",
                "<2.2.19"
            ],
            "analyzed_version": "3.0",
            "advisory": "Django versions 2.2.19, 3.0.13 and 3.1.7 include a fix for CVE-2021-23336: Web cache poisoning via 'django.utils.http.limited_parse_qsl()'. Django contains a copy of 'urllib.parse.parse_qsl' which was added to backport some security fixes. A further security fix has been issued recently such that 'parse_qsl(' no longer allows using ';' as a query parameter separator by default.",
            "is_transitive": true,
            "published_date": null,
            "fixed_versions": [],
            "closest_versions_without_known_vulnerabilities": [],
            "resources": [],
            "CVE": "CVE-2021-23336",
            "severity": null,
            "affected_versions": [],
            "more_info_url": "https://data.safetycli.com/v/39646/f17"
        },
        {
            "vulnerability_id": "40163",
            "package_name": "django",
            "ignored": {},
            "ignored_reason": null,
            "ignored_expires": null,
            "vulnerable_spec": ">=3.0a1,<3.0.14",
            "all_vulnerable_specs": [
                ">=2.2a1,<2.2.20",
                ">=3.0a1,<3.0.14",
                ">=3.1a1,<3.1.8"
            ],
            "analyzed_version": "3.0",
            "advisory": "In Django 2.2 before 2.2.20, 3.0 before 3.0.14, and 3.1 before 3.1.8, MultiPartParser allowed directory traversal via uploaded files with suitably crafted file names. Built-in upload handlers were not affected by this vulnerability.",
            "is_transitive": false,
            "published_date": null,
            "fixed_versions": [],
            "closest_versions_without_known_vulnerabilities": [],
            "resources": [],
            "CVE": "CVE-2021-28658",
            "severity": null,
            "affected_versions": [],
            "more_info_url": "https://data.safetycli.com/v/40163/f17"
        },
        {
            "vulnerability_id": "40638",
            "package_name": "django",
            "ignored": {},
            "ignored_reason": null,
            "ignored_expires": null,
            "vulnerable_spec": ">=3.0.0a1,<3.1.12",
            "all_vulnerable_specs": [
                ">=3.0.0a1,<3.1.12",
                ">=3.2.0a1,<3.2.4",
                "<2.2.24"
            ],
            "analyzed_version": "3.0",
            "advisory": "Django 2.2.24, 3.1.12, and 3.2.4 include a fix for CVE-2021-33571: In Django 2.2 before 2.2.24, 3.x before 3.1.12, and 3.2 before 3.2.4, URLValidator, validate_ipv4_address, and validate_ipv46_address do not prohibit leading zero characters in octal literals. This may allow a bypass of access control that is based on IP addresses. (validate_ipv4_address and validate_ipv46_address are unaffected with Python 3.9.5+).\r\nhttps://www.djangoproject.com/weblog/2021/jun/02/security-releases",
            "is_transitive": false,
            "published_date": null,
            "fixed_versions": [],
            "closest_versions_without_known_vulnerabilities": [],
            "resources": [],
            "CVE": "CVE-2021-33571",
            "severity": null,
            "affected_versions": [],
            "more_info_url": "https://data.safetycli.com/v/40638/f17"
        },
        {
            "vulnerability_id": "38373",
            "package_name": "django",
            "ignored": {},
            "ignored_reason": null,
            "ignored_expires": null,
            "vulnerable_spec": ">=3.0a1,<3.0.7",
            "all_vulnerable_specs": [
                ">=3.0a1,<3.0.7",
                ">=2.2a1,<2.2.13"
            ],
            "analyzed_version": "3.0",
            "advisory": "An issue was discovered in Django 2.2 before 2.2.13 and 3.0 before 3.0.7. In cases where a memcached backend does not perform key validation, passing malformed cache keys could result in a key collision, and potential data leakage.",
            "is_transitive": false,
            "published_date": null,
            "fixed_versions": [],
            "closest_versions_without_known_vulnerabilities": [],
            "resources": [],
            "CVE": "CVE-2020-13254",
            "severity": null,
            "affected_versions": [],
            "more_info_url": "https://data.safetycli.com/v/38373/f17"
        },
        {
            "vulnerability_id": "38372",
            "package_name": "django",
            "ignored": {},
            "ignored_reason": null,
            "ignored_expires": null,
            "vulnerable_spec": ">=3.0a1,<3.0.7",
            "all_vulnerable_specs": [
                ">=3.0a1,<3.0.7",
                ">=2.2a1,<2.2.13"
            ],
            "analyzed_version": "3.0",
            "advisory": "An issue was discovered in Django 2.2 before 2.2.13 and 3.0 before 3.0.7. Query parameters generated by the Django admin ForeignKeyRawIdWidget were not properly URL encoded, leading to a possibility of an XSS attack.",
            "is_transitive": false,
            "published_date": null,
            "fixed_versions": [],
            "closest_versions_without_known_vulnerabilities": [],
            "resources": [],
            "CVE": "CVE-2020-13596",
            "severity": null,
            "affected_versions": [],
            "more_info_url": "https://data.safetycli.com/v/38372/f17"
        },
        {
            "vulnerability_id": "37815",
            "package_name": "django",
            "ignored": {},
            "ignored_reason": null,
            "ignored_expires": null,
            "vulnerable_spec": ">=3.0a1,<3.0.3",
            "all_vulnerable_specs": [
                ">=1.11a1,<1.11.28",
                ">=2.0a1,<2.2.10",
                ">=3.0a1,<3.0.3"
            ],
            "analyzed_version": "3.0",
            "advisory": "Django 1.11.28, 2.2.10 and 3.0.3 include a fix for CVE-2020-7471: SQL Injection if untrusted data is used as a StringAgg delimiter (e.g., in Django applications that offer downloads of data as a series of rows with a user-specified column delimiter). By passing a suitably crafted delimiter to a contrib.postgres.aggregates.StringAgg instance, it was possible to break escaping and inject malicious SQL.",
            "is_transitive": false,
            "published_date": null,
            "fixed_versions": [],
            "closest_versions_without_known_vulnerabilities": [],
            "resources": [],
            "CVE": "CVE-2020-7471",
            "severity": null,
            "affected_versions": [],
            "more_info_url": "https://data.safetycli.com/v/37815/f17"
        },
        {
            "vulnerability_id": "39521",
            "package_name": "django",
            "ignored": {},
            "ignored_reason": null,
            "ignored_expires": null,
            "vulnerable_spec": ">=3.0a1,<3.0.12",
            "all_vulnerable_specs": [
                ">=2.0a1,<2.2.18",
                ">=3.0a1,<3.0.12",
                ">=3.1a1,<3.1.6"
            ],
            "analyzed_version": "3.0",
            "advisory": "Django 2.2.18, 3.0.12 and 3.1.6 include a fix for CVE-2021-3281: The django.utils.archive.extract method (used by \"startapp --template\" and \"startproject --template\") allows directory traversal via an archive with absolute paths or relative paths with dot segments.",
            "is_transitive": false,
            "published_date": null,
            "fixed_versions": [],
            "closest_versions_without_known_vulnerabilities": [],
            "resources": [],
            "CVE": "CVE-2021-3281",
            "severity": null,
            "affected_versions": [],
            "more_info_url": "https://data.safetycli.com/v/39521/f17"
        },
        {
            "vulnerability_id": "52945",
            "package_name": "django",
            "ignored": {},
            "ignored_reason": null,
            "ignored_expires": null,
            "vulnerable_spec": "<3.2.17",
            "all_vulnerable_specs": [
                "<3.2.17",
                ">=4.0a1,<4.0.9",
                ">=4.1a1,<4.1.6"
            ],
            "analyzed_version": "3.0",
            "advisory": "Django 3.2.17, 4.0.9 and 4.1.6 includes a fix for CVE-2023-23969: In Django 3.2 before 3.2.17, 4.0 before 4.0.9, and 4.1 before 4.1.6, the parsed values of Accept-Language headers are cached in order to avoid repetitive parsing. This leads to a potential denial-of-service vector via excessive memory usage if the raw value of Accept-Language headers is very large.\r\nhttps://www.djangoproject.com/weblog/2023/feb/01/security-releases",
            "is_transitive": false,
            "published_date": null,
            "fixed_versions": [],
            "closest_versions_without_known_vulnerabilities": [],
            "resources": [],
            "CVE": "CVE-2023-23969",
            "severity": null,
            "affected_versions": [],
            "more_info_url": "https://data.safetycli.com/v/52945/f17"
        },
        {
            "vulnerability_id": "40637",
            "package_name": "django",
            "ignored": {},
            "ignored_reason": null,
            "ignored_expires": null,
            "vulnerable_spec": ">=3.0a1,<3.1.12",
            "all_vulnerable_specs": [
                "<2.2.24",
                ">=3.0a1,<3.1.12",
                ">=3.2a1,<3.2.4"
            ],
            "analyzed_version": "3.0",
            "advisory": "Django before 2.2.24, 3.x before 3.1.12, and 3.2.x before 3.2.4 has a potential directory traversal via django.contrib.admindocs. Staff members could use the TemplateDetailView view to check the existence of arbitrary files. Additionally, if (and only if) the default admindocs templates have been customized by application developers to also show file contents, then not only the existence but also the file contents would have been exposed. In other words, there is directory traversal outside of the template root directories.\r\nhttps://www.djangoproject.com/weblog/2021/jun/02/security-releases",
            "is_transitive": false,
            "published_date": null,
            "fixed_versions": [],
            "closest_versions_without_known_vulnerabilities": [],
            "resources": [],
            "CVE": "CVE-2021-33203",
            "severity": null,
            "affected_versions": [],
            "more_info_url": "https://data.safetycli.com/v/40637/f17"
        },
        {
            "vulnerability_id": "37661",
            "package_name": "django",
            "ignored": {},
            "ignored_reason": null,
            "ignored_expires": null,
            "vulnerable_spec": ">=3.0a1,<3.0.1",
            "all_vulnerable_specs": [
                ">=1.11a1,<1.11.27",
                ">=2.0a1,<2.2.9",
                ">=3.0a1,<3.0.1"
            ],
            "analyzed_version": "3.0",
            "advisory": "Django 1.11.27, 2.2.9 and 3.0.1 include a fix for CVE-2019-19844: Account takeover. A suitably crafted email address (that is equal to an existing user's email address after case transformation of Unicode characters) would allow an attacker to be sent a password reset token for the matched user account. One mitigation in the new releases is to send password reset tokens only to the registered user email address.",
            "is_transitive": false,
            "published_date": null,
            "fixed_versions": [],
            "closest_versions_without_known_vulnerabilities": [],
            "resources": [],
            "CVE": "CVE-2019-19844",
            "severity": null,
            "affected_versions": [],
            "more_info_url": "https://data.safetycli.com/v/37661/f17"
        },
        {
            "vulnerability_id": "51340",
            "package_name": "django",
            "ignored": {},
            "ignored_reason": null,
            "ignored_expires": null,
            "vulnerable_spec": "<3.2.16",
            "all_vulnerable_specs": [
                "<3.2.16",
                ">=4.0a1,<4.0.8",
                ">=4.1a1,<4.1.2"
            ],
            "analyzed_version": "3.0",
            "advisory": "In Django 3.2 before 3.2.16, 4.0 before 4.0.8, and 4.1 before 4.1.2, internationalized URLs were subject to a potential denial of service attack via the locale parameter, which is treated as a regular expression.",
            "is_transitive": false,
            "published_date": null,
            "fixed_versions": [],
            "closest_versions_without_known_vulnerabilities": [],
            "resources": [],
            "CVE": "CVE-2022-41323",
            "severity": null,
            "affected_versions": [],
            "more_info_url": "https://data.safetycli.com/v/51340/f17"
        },
        {
            "vulnerability_id": "59293",
            "package_name": "django",
            "ignored": {},
            "ignored_reason": null,
            "ignored_expires": null,
            "vulnerable_spec": "<3.2.20",
            "all_vulnerable_specs": [
                ">=4.0a1,<4.1.10",
                ">=4.2a1,<4.2.3",
                "<3.2.20"
            ],
            "analyzed_version": "3.0",
            "advisory": "Affected versions of Django are vulnerable to a potential ReDoS (regular expression denial of service) in EmailValidator and URLValidator via a very large number of domain name labels of emails and URLs.",
            "is_transitive": false,
            "published_date": null,
            "fixed_versions": [],
            "closest_versions_without_known_vulnerabilities": [],
            "resources": [],
            "CVE": "CVE-2023-36053",
            "severity": null,
            "affected_versions": [],
            "more_info_url": "https://data.safetycli.com/v/59293/f17"
        },
        {
            "vulnerability_id": "65771",
            "package_name": "django",
            "ignored": {},
            "ignored_reason": null,
            "ignored_expires": null,
            "vulnerable_spec": "<3.2.25",
            "all_vulnerable_specs": [
                "<3.2.25",
                ">=4.0a1,<4.2.11",
                ">=5.0a1,<5.0.3"
            ],
            "analyzed_version": "3.0",
            "advisory": "Affected versions of Django are vulnerable to potential regular expression denial-of-service (REDoS). django.utils.text.Truncator.words() method (with html=True) and truncatewords_html template filter were subject to a potential regular expression denial-of-service attack using a suitably crafted string (follow up to CVE-2019-14232 and CVE-2023-43665).",
            "is_transitive": false,
            "published_date": null,
            "fixed_versions": [],
            "closest_versions_without_known_vulnerabilities": [],
            "resources": [],
            "CVE": "CVE-2024-27351",
            "severity": null,
            "affected_versions": [],
            "more_info_url": "https://data.safetycli.com/v/65771/f17"
        },
        {
            "vulnerability_id": "44742",
            "package_name": "django",
            "ignored": {},
            "ignored_reason": null,
            "ignored_expires": null,
            "vulnerable_spec": ">=3.0a1,<3.2.12",
            "all_vulnerable_specs": [
                "<2.2.27",
                ">=3.0a1,<3.2.12",
                ">=4.0a1,<4.0.2"
            ],
            "analyzed_version": "3.0",
            "advisory": "The {% debug %} template tag in Django 2.2 before 2.2.27, 3.2 before 3.2.12, and 4.0 before 4.0.2 does not properly encode the current context. This may lead to XSS.",
            "is_transitive": false,
            "published_date": null,
            "fixed_versions": [],
            "closest_versions_without_known_vulnerabilities": [],
            "resources": [],
            "CVE": "CVE-2022-22818",
            "severity": null,
            "affected_versions": [],
            "more_info_url": "https://data.safetycli.com/v/44742/f17"
        },
        {
            "vulnerability_id": "44741",
            "package_name": "django",
            "ignored": {},
            "ignored_reason": null,
            "ignored_expires": null,
            "vulnerable_spec": ">=3.0a1,<3.2.12",
            "all_vulnerable_specs": [
                "<2.2.27",
                ">=3.0a1,<3.2.12",
                ">=4.0a1,<4.0.2"
            ],
            "analyzed_version": "3.0",
            "advisory": "Django 2.2.27, 3.2.12 and 4.0.2 include a fix for CVE-2022-23833: Denial-of-service possibility in file uploads.\r\nhttps://www.djangoproject.com/weblog/2022/feb/01/security-releases",
            "is_transitive": false,
            "published_date": null,
            "fixed_versions": [],
            "closest_versions_without_known_vulnerabilities": [],
            "resources": [],
            "CVE": "CVE-2022-23833",
            "severity": null,
            "affected_versions": [],
            "more_info_url": "https://data.safetycli.com/v/44741/f17"
        }
    ],
    "ignored_vulnerabilities": [],
    "remediations": {
        "django": {
            "current_version": "3.0",
            "vulnerabilities_found": 32,
            "recommended_version": null,
            "other_recommended_versions": [],
            "more_info_url": "https://data.safetycli.com/?from=3.0"
        }
    }
}
```

Where it will scan target applications components and for this scenario, we have utilised `requirements.txt` where python requirements are mentioned in order to run the application

## Install bundler-audit

```bash
curl -fsSL https://github.com/rbenv/rbenv-installer/raw/HEAD/bin/rbenv-installer | bash
export PATH="~/.rbenv/bin:$PATH"
rbenv install 2.6.5
export PATH="/root/.rbenv/versions/2.6.5/bin:$PATH"
ruby --version
gem install --user-install bundler-audit
export PATH="~/.gem/ruby/2.6.0/bin/:$PATH"
bundle-audit -h
```

## If error persists during rbenv

```sh
apt update
apt-get install build-essential libreadline-dev -y
```

## Running a Scan 

Just move into the particular directory and type the following command `bundle-audit` where it will clone ruby-advisory-db and scans the code and then list vulns associated with the gems 

```sh
/webapp# bundle-audit
Download ruby-advisory-db ...
Cloning into '/root/.local/share/ruby-advisory-db'...
remote: Enumerating objects: 11947, done.
remote: Counting objects: 100% (1690/1690), done.
remote: Compressing objects: 100% (445/445), done.
remote: Total 11947 (delta 1334), reused 1300 (delta 1241), pack-reused 10257
Receiving objects: 100% (11947/11947), 1.95 MiB | 6.00 MiB/s, done.
Resolving deltas: 100% (6777/6777), done.
ruby-advisory-db:
  advisories:   901 advisories
  last updated: 2024-07-17 12:30:44 -0700
  commit:       a3b9e1240633295a4c5582bbe353bf22ec595090
Name: actionpack
Version: 6.0.0
CVE: CVE-2023-22795
GHSA: GHSA-8xww-x3g3-6jcv
Criticality: Unknown
URL: https://github.com/rails/rails/releases/tag/v7.0.4.1
Title: ReDoS based DoS vulnerability in Action Dispatch
Solution: upgrade to '~> 5.2.8, >= 5.2.8.15', '~> 6.1.7, >= 6.1.7.1', '>= 7.0.4.1'

Name: actionpack
Version: 6.0.0
CVE: CVE-2021-22881
GHSA: GHSA-8877-prq4-9xfw
Criticality: Medium
URL: https://groups.google.com/g/rubyonrails-security/c/zN_3qA26l6E
Title: Possible Open Redirect in Host Authorization Middleware
Solution: upgrade to '~> 6.0.3, >= 6.0.3.5', '>= 6.1.2.1'
```
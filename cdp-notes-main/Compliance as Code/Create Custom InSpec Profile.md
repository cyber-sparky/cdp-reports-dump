
## Installing Inspec via script 

```sh
curl https://omnitruck.chef.io/install.sh | sudo bash -s -- -v 5.22.29 -P inspec
```

## Create the custom profile

Here created a custom profile called ubuntu 

```sh
mkdir inspec-profile && cd inspec-profile
inspec init profile ubuntu --chef-license accept
```

## Adding sample ruby code to ubuntu/controls/

```sh
# Basically check whether shadow owned by root
cat > ubuntu/controls/example.rb <<EOL
describe file('/etc/shadow') do
    it { should exist }
    it { should be_file }
    it { should be_owned_by 'root' }
  end
EOL
```

## Validate the profile kind of lint check 

```sh
inspec check ubuntu
```

## Execute the profile locally 

```sh
inspec exec ubuntu
```

## Running aganist our prod server 

```sh
/inspec-profile# echo "StrictHostKeyChecking accept-new" >> ~/.ssh/config
/inspec-profile# inspec exec ubuntu -t ssh://root@prod-oiahxczk -i ~/.ssh/id_rsa --chef-license accept
```

## Challenges 

### Challenges: Create an InSpec profile for PCI/DSS

```sh
inspec init profile challenge --chef-license accept
```

### Edit the newly created InSpec skeleton to include four basic checks: system, password, ssh, and useradd, based on PCI/DSS requirements.

```sh
cat > /inspec-profile/challenge/controls/example.rb <<EOL
describe file('/etc/pam.d/system-auth') do
    its('content') { 
        should match(/^\s*password\s+requisite\s+pam_pwquality\.so\s+(\S+\s+)*try_first_pass/)
    }
    its('content') {
        should match(/^\s*password\s+requisite\s+pam_pwquality\.so\s+(\S+\s+)*retry=[3210]/)
    }
end

describe file('/etc/pam.d/password-auth') do
    its('content') { 
        should match(/^\s*password\s+requisite\s+pam_pwquality\.so\s+(\S+\s+)*try_first_pass/)
    }
    its('content') {
        should match(/^\s*password\s+requisite\s+pam_pwquality\.so\s+(\S+\s+)*retry=[3210]/)
    }
end

describe file('/etc/default/useradd') do
    its('content') {
        should match(/^\s*INACTIVE\s*=\s*(30|[1-2][0-9]|[1-9])\s*(\s+#.*)?$/)
    }
end

describe file('/etc/ssh/sshd_config') do
    it { should exist }
    it { should be_file }
    it { should be_owned_by 'root' }
    its('content') { should match 'PasswordAuthentication no' }
end
EOL
```

### Test the challenge InSpec profile locally before integrating it into the CI pipeline

```sh
/inspec-profile# inspec check challenge
Location :   challenge
Profile :    challenge
Controls :   4
Timestamp :  2024-08-21T06:56:39+00:00
Valid :      true

No errors, warnings, or offenses
/inspec-profile# inspec exec challenge'
> '
Could not fetch inspec profile in "challenge\n".
/inspec-profile# inspec exec challenge

Profile:   InSpec Profile (challenge)
Version:   0.1.0
Target:    local://
Target ID: bac1f99d-5386-5aca-ae5e-115b26743472

  File /etc/pam.d/system-auth
     ×  content is expected to match /^\s*password\s+requisite\s+pam_pwquality\.so\s+(\S+\s+)*try_first_pass/
     expected nil to match /^\s*password\s+requisite\s+pam_pwquality\.so\s+(\S+\s+)*try_first_pass/
     ×  content is expected to match /^\s*password\s+requisite\s+pam_pwquality\.so\s+(\S+\s+)*retry=[3210]/
     expected nil to match /^\s*password\s+requisite\s+pam_pwquality\.so\s+(\S+\s+)*retry=[3210]/
  File /etc/pam.d/password-auth
     ×  content is expected to match /^\s*password\s+requisite\s+pam_pwquality\.so\s+(\S+\s+)*try_first_pass/
     expected nil to match /^\s*password\s+requisite\s+pam_pwquality\.so\s+(\S+\s+)*try_first_pass/
     ×  content is expected to match /^\s*password\s+requisite\s+pam_pwquality\.so\s+(\S+\s+)*retry=[3210]/
     expected nil to match /^\s*password\s+requisite\s+pam_pwquality\.so\s+(\S+\s+)*retry=[3210]/
  File /etc/default/useradd
     ×  content is expected to match /^\s*INACTIVE\s*=\s*(30|[1-2][0-9]|[1-9])\s*(\s+#.*)?$/
     expected "# Default values for useradd(8)\n#\n# The SHELL variable specifies the default login shell on your\n...whether the mail spool should be created while\n# creating the account\n# CREATE_MAIL_SPOOL=yes\n\n" to match /^\s*INACTIVE\s*=\s*(30|[1-2][0-9]|[1-9])\s*(\s+#.*)?$/
     Diff:
     @@ -1,36 +1,71 @@
     -/^\s*INACTIVE\s*=\s*(30|[1-2][0-9]|[1-9])\s*(\s+#.*)?$/
     +# Default values for useradd(8)
     +#
     +# The SHELL variable specifies the default login shell on your
     +# system.
     +# Similar to DSHELL in adduser. However, we use "sh" here because
     +# useradd is a low level utility and should be as general
     +# as possible
     +SHELL=/bin/sh
     +#
     +# The default group for users
     +# 100=users on Debian systems
     +# Same as USERS_GID in adduser
     +# This argument is used when the -n flag is specified.
     +# The default behavior (when -n and -g are not specified) is to create a
     +# primary user group with the same name as the user being added to the
     +# system.
     +# GROUP=100
     +#
     +# The default home directory. Same as DHOME for adduser
     +# HOME=/home
     +#
     +# The number of days after a password expires until the account 
     +# is permanently disabled
     +# INACTIVE=-1
     +#
     +# The default expire date
     +# EXPIRE=
     +#
     +# The SKEL variable specifies the directory containing "skeletal" user
     +# files; in other words, files such as a sample .profile that will be
     +# copied to the new user's home directory when it is created.
     +# SKEL=/etc/skel
     +#
     +# Defines whether the mail spool should be created while
     +# creating the account
     +# CREATE_MAIL_SPOOL=yes

  File /etc/ssh/sshd_config
     ✔  is expected to exist
     ✔  is expected to be file
     ✔  is expected to be owned by "root"
     ✔  content is expected to match "PasswordAuthentication no"

Test Summary: 4 successful, 5 failures, 0 skipped
```

### Commit the challenge InSpec profile to your project’s repository using GitLab UI or Git commands.

```sh
git clone git@gitlab-ce-oiahxczk:root/django-nv.git
cd django-nv
cp -r /inspec-profile/challenge challenge
git add challenge
git config --global user.email "you@example.com"
git config --global user.name "Your Name"
git commit -m "Add custom inspec profile"
git push origin main
```

## In the .gitlab-ci.yml file, add a new job named compliance in the test stage to execute the profile using the InSpec command.

```yml
image: docker:20.10

services:
  - docker:dind

stages:
  - test

compliance:
  stage: test
  script:
    - docker run -i --rm -v $(pwd):/share chef/inspec check challenge --chef-license accept
    - docker run -i --rm -v $(pwd):/share chef/inspec exec challenge --chef-license accept
  allow_failure: true
```


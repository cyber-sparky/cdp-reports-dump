
## Install Renovate 

```sh
mkdir -p /etc/apt/keyrings
curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key | gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg
NODE_MAJOR=20
echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_$NODE_MAJOR.x nodistro main" | tee /etc/apt/sources.list.d/nodesource.list
apt update
apt install nodejs -y
npm install -g renovate
renovate -h
```

## Renovate features

| **Feature**          | **Description** |
|----------------------|-----------------|
| Automation           | Renovate automates the process of identifying and updating outdated dependencies in software projects. |
| Customization        | Renovate offers extensive customization options to suit various project needs and workflows. |
| Scalability          | Renovate can handle projects of any size, from small to large-scale applications. |
| Support              | Renovate provides support for a broad spectrum of languages and package managers, namely JavaScript (npm & Yarn), Python (Pip), Java (Maven), PHP (Composer), Ruby (Bundler), Docker and more. |
| Efficiency           | By automating dependency management, Renovate frees up developers’ time for writing code, leading to improved productivity. |
| Security & Stability | By keeping dependencies updated, Renovate helps integrate the latest security patches and bug fixes promptly, therefore contributing to safer and more stable software. |

## Running the Scanner 

```sh
renovate
```

### If you face issue related to git version 

```sh
echo "deb http://ppa.launchpad.net/git-core/ppa/ubuntu $(lsb_release -cs) main"  >> /etc/apt/sources.list
apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys E1DD270288B4E6030699E45FA1715D88E1DF1F24
apt update && apt install git -y
```


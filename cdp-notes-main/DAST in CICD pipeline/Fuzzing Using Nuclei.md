
## Install Katana 

```sh
wget https://github.com/projectdiscovery/katana/releases/download/v1.0.3/katana_1.0.3_linux_amd64.zip
unzip katana_1.0.3_linux_amd64.zip
mv katana /usr/bin/
```

## Identifying targets using katana 

```sh
katana -u https://public-firing-range.appspot.com/ -f qurl -o endpoints.txt
sed -i '/^$/d' endpoints.txt
```

## Cloning fuzz templates 

```sh
git clone https://github.com/projectdiscovery/fuzzing-templates.git
```

## Scanning with Fuzz templates 

```sh
nuclei -list endpoints.txt -t fuzzing-templates
```
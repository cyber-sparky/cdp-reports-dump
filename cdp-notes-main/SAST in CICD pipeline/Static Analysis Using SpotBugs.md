
SpotBugs is an application that employs static analysis to detect bugs in Java code. SpotBugs is a free software that falls under the confines of the Lesser GNU Public License.

## Install Spotbugs 

```sh
wget https://github.com/spotbugs/spotbugs/releases/download/4.7.3/spotbugs-4.7.3.zip
unzip spotbugs-4.7.3.zip
echo -e "\nexport PATH=\$PATH:/spotbugs-4.7.3/bin" >> ~/.bashrc
source ~/.bashrc
chmod +x /spotbugs-4.7.3/bin/spotbugs
```

## Running a scan with a textui output 

```sh
spotbugs -textui .
```

## Running a scan and generate a HTML output 

```sh
spotbugs -html -output spotbugs-output.html .
```

## Run in a silent way

```sh
spotbugs -textui -quiet .
```

## Shows a verbose and progressive output 

```sh
spotbugs -textui -progress .
```


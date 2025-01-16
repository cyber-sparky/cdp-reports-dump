
njsscan is a semantic aware SAST tool that can find insecure code patterns in your Node.js applications using a simple pattern matcher from libsast and pattern search tool like semgrep (lightweight static analysis for many languages).

## Installing njsscan 

```sh
pip3 install njsscan libsast
```

## Running a scan and output into JSON 

```sh
njsscan --json -o output.json .
```


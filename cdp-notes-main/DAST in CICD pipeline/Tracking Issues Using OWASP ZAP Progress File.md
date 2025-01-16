
## Progress file creation 

You can create a file called `progress-file.json` 

Mention the IDs and status from your output and start progressing with your results

```json
{
    "site": "https://prod-oiahxczk.lab.practical-devsecops.training",
    "issues": [
        {
            "id": "10099",
            "name": "Source Code Disclosure - SQL",
            "state": "inprogress",
            "link": "[FP] INTDENDED VULNERALBILITES, A FALSE POSITIVE [FP]"

        },
        {
            "id": "10063",
            "name": "[EXAMPLE] Insert the issue name here! [EXAMPLE]",
            "state": "inprogress",
            "link": "[EXAMPLE] Insert tracked / on progress issue here! [EXAMPLE]"
        },
        {
            "id": "10036",
            "name": "Server Leaks Version Information via \"Server\" HTTP Response Header Field",
            "state": "inprogress",
            "link": "https://dojo-oiahxczk.lab.practical-devsecops.training/finding/4"
        },
        {
            "id": "10035",
            "name": "Strict-Transport-Security Header Not Set",
            "state": "inprogress",
            "link": "https://dojo-oiahxczk.lab.practical-devsecops.training/finding/3"
        }
    ]
}
```

## Running scan with progress file 

```sh
docker run --user $(id -u):$(id -g) -w /zap -v $(pwd):/zap/wrk:rw --rm  softwaresecurityproject/zap-stable:2.14.0 zap-baseline.py -t https://prod-oiahxczk.lab.practical-devsecops.training -p progress-file.json -J zap-output.json
```
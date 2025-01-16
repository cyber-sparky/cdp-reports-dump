


# Searching in a File:

- **Search Shortcut**: Use `ctrl + f` to search (Note: Default Nano search is `ctrl + w`).

## Steps to Create and Search in a File:

1. Create `security-report.json` with the following content:
    ```json
    {
        "Name":"XSS",
        "Vulnerabilities": "10",
        "Valid": "2",
        "Severity": "High",
        "generated_at": "2022-05-16T17:37:50Z",
        "Name":"SQL injection",
        "Vulnerabilities": "9",
        "Valid": "1",
        "Severity": "High",
        "generated_at": "2022-05-16T17:37:50Z",
        "Name":"IDOR",
        "Vulnerabilities": "6",
        "Valid": "4",
        "Severity": "High",
        "generated_at": "2022-05-16T17:37:50Z"
    }
    ```
2. Open the file in Nano:
    ```sh
    nano security-report.json
    ```
3. Press `ctrl + f` and type `IDOR`, then press `enter`.
    - The cursor will move to the word "IDOR".

## Search in Multiple Occurrences:

- Use `alt + f` for the next occurrence.
- Use `alt + q` for the previous occurrence.
- **Mac Users**: Press `ctrl + f` again and press `enter` to move to the next occurrence.

## Summmary


| Action                        | Steps                                                                                                                                          |
|-------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------|
| Create a File                 | 1. Use `cat` command to create `security-report.json`. <br> ```sh cat > security-report.json <<EOL { "Name":"XSS", "Vulnerabilities": "10", "Valid": "2", "Severity": "High", "generated_at": "2022-05-16T17:37:50Z", "Name":"SQL injection", "Vulnerabilities": "9", "Valid": "1", "Severity": "High", "generated_at": "2022-05-16T17:37:50Z", "Name":"IDOR", "Vulnerabilities": "6", "Valid": "4", "Severity": "High", "generated_at": "2022-05-16T17:37:50Z" } EOL ``` |
| Open File with Nano           | 1. Use `nano` command to open `security-report.json`. <br> ```sh nano security-report.json ```                                                 |
| Search for Text               | 1. Press `ctrl + f` and type `IDOR`, then press `enter`.                                                                                       |
| Search Next Occurrence        | 1. Use `alt + f` for the next occurrence.                                                                                                       |
| Search Previous Occurrence    | 1. Use `alt + q` for the previous occurrence.                                                                                                   |
| Mac Users Search Next         | 1. Press `ctrl + f` again and press `enter` to move to the next occurrence.                                                                    |
| Exit Nano                     | 1. Press `ctrl + x` to exit the editor.                                                                                                        |
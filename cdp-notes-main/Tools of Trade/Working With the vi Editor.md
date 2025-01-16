# Working With vi

The Visual Editor (vi) is a powerful terminal-based text editor that allows you to read, create, and write files. While there are many text editors available, vi is widely used for its robust features. Other popular terminal-based text editors include vim, Nano, Emacs, and Ne.

## Creating and Editing a File with vi

To demonstrate the use of vi, let's create and open a `security-report.json` file.

### Step 1: Create the `security-report.json` File

First, use the `cat` command to create the `security-report.json` file:

```bash
cat > security-report.json <<EOL
{
    "Vulnerabilities": "10",
    "Valid": "2",
    "Severity": "High",
    "generated_at": "2022-05-16T17:37:50Z"
}
EOL
```

This command creates a file named `security-report.json` and adds the following JSON content to it:

```json
{
    "Vulnerabilities": "10",
    "Valid": "2",
    "Severity": "High",
    "generated_at": "2022-05-16T17:37:50Z"
}
```

### Step 2: Open the File with vi

Next, open the `security-report.json` file using the `vi` editor:

```bash
vi security-report.json
```

Upon opening the file with vi, you will see the following content displayed:

```json
{
    "Vulnerabilities": "10",
    "Valid": "2",
    "Severity": "High",
    "generated_at": "2022-05-16T17:37:50Z"
}
```

# Modes of Operation

## vi Editor Modes and Commands

| **Mode**       | **Description**                                                                                                            | **Command**                          | **Usage**                                                                                                                 |
|----------------|----------------------------------------------------------------------------------------------------------------------------|--------------------------------------|---------------------------------------------------------------------------------------------------------------------------|
| **Command Mode** | Default mode when you open vi. Use this mode to navigate, delete, copy, or paste text.                                      | `vi filename`                        | Open the vi editor with a file.                                                                                           |
|                |                                                                                                                            | `:q`                                 | Exit vi.                                                                                                                  |
| **Insert Mode**  | Mode to edit or enter content in a file.                                                                                   | `i`                                  | Enter Insert Mode.                                                                                                        |
|                |                                                                                                                            | `esc`                                | Return to Command Mode.                                                                                                   |
|                |                                                                                                                            | `:wq`                                | Save the file and exit vi.                                                                                                |
|                |                                                                                                                            | `:q!`                                | Exit vi without saving changes.                                                                                           |
| **Line Mode**    | Invoked by typing a colon `:`, while in Command Mode. The cursor moves to the last line, awaiting further commands.          | `:`                                  | Enter Line Mode.                                                                                                          |
|                |                                                                                                                            | `ctrl + v` or `command + v`          | Paste text without entering Insert Mode.                                                                                  |

# Search in a file

## Steps to Create and Edit a File:

1. **Create and Open a File:**
   ```bash
   vi security-report.json
   ```

2. **Enter Insert Mode and Add Content:**
   - Press `i` to enter Insert Mode.
   - Add the following content:
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

3. **Save the File and Exit:**
   - Press `esc` to return to Command Mode.
   - Type `:wq` and press `Enter` to save and exit.

4. **Reopen the File:**
   ```bash
   vi security-report.json
   ```

## Searching for Text:

1. **Enter Command Mode:**
   - Press `esc` to ensure you are in Command Mode.

2. **Search for a Text:**
   - Type `/High` and press `Enter`.
   - The cursor will move to the first occurrence of the word "High".

## Summary Table:

| **Step**                       | **Description**                                    | **Command**                   |
|--------------------------------|----------------------------------------------------|-------------------------------|
| Create and open a file         | Opens vi editor with a specified file              | `vi security-report.json`     |
| Enter Insert Mode              | Allows text entry                                  | `i`                           |
| Add Content                    | Type the JSON content                              | [Content as shown above]      |
| Save and exit                  | Save changes and exit vi                           | `esc` then `:wq`              |
| Reopen the file                | Opens the file again                               | `vi security-report.json`     |
| Enter Command Mode             | Ensure you are in Command Mode                     | `esc`                         |
| Search for text                | Searches for the specified text                    | `/High` then `Enter`          |

# Search in multiple occurences

## Steps to Search and Navigate:

1. **Open the File:**
   ```bash
   vi security-report.json
   ```

2. **Search for Text:**
   - Ensure you are in Command Mode (press `esc` if needed).
   - Type `/High` and press `Enter`.
   - The cursor will move to the first occurrence of "High".

3. **Navigate Between Occurrences:**

| **Action**                          | **Description**                              | **Command**        |
|-------------------------------------|----------------------------------------------|--------------------|
| Move to Next Occurrence              | Move to the next occurrence of "High"         | Press `n`          |
| Move to Previous Occurrence          | Move to the previous occurrence of "High"     | Press `Shift + n`  |

## Summary Table:

| **Step**                       | **Description**                                    | **Command**                   |
|--------------------------------|----------------------------------------------------|-------------------------------|
| Open the file                  | Opens vi editor with a specified file              | `vi security-report.json`     |
| Enter Command Mode             | Ensure you are in Command Mode                     | `esc`                         |
| Search for text                | Searches for the specified text                    | `/High` then `Enter`          |
| Move to Next Occurrence        | Moves to the next occurrence of the text           | Press `n`                     |
| Move to Previous Occurrence    | Moves to the previous occurrence of the text       | Press `Shift + n`             |

By using `n` to move to the next occurrence and `Shift + n` to move to the previous occurrence, you can efficiently navigate through multiple instances of a word or phrase in a file opened with the vi editor.
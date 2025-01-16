# Visual Select

Visual select is useful for identifying chunks of text and performing various operations. 

**Modes:**
1. **Character Mode:** Highlight a sentence or phrase.
2. **Line Mode:** Highlight entire lines.

**Character Mode Steps:**
1. Create `output.json` with:
    ```json
    {
        "Vulnerabilities": "10",
        "Valid": "2",
        "Severity": "High",
        "generated_at": "2022-05-16T17:37:50Z"
    }
    ```
2. Open file in Vim.
3. Press `esc` to enter command mode.
4. Press `v` to enter visual mode.
5. Move cursor to select text.

**Line Mode Steps:**
1. Open `output.json` in Vim.
2. Press `esc` to enter command mode.
3. Press `Shift + v` to enter line mode.
4. Use arrow keys to select lines.

### Summary

| Mode           | Purpose                             | Steps                                                                                                                                          |
|----------------|-------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------|
| Character Mode | Highlight a sentence or phrase      | 1. Create `output.json`. <br> 2. Open in Vim. <br> 3. Press `esc` to enter command mode. <br> 4. Press `v` to enter visual mode. <br> 5. Move cursor. |
| Line Mode      | Highlight entire lines              | 1. Open `output.json`. <br> 2. Press `esc` to enter command mode. <br> 3. Press `Shift + v` to enter line mode. <br> 4. Use arrow keys to select lines. |

# Deleting the text

## Delete Selected Text:

1. Open `output.json` in Vim.
2. Press `esc` to enter command mode.
3. Press `Shift + v` to enter line mode.
4. Place cursor before `"generated_at"`.
5. Press `d` to delete the line.

    **Undo:**
    - Press `u` to undo the delete action.

## Delete Without Selecting:

1. Open `output.json` in Vim.
2. Press `esc` to enter command mode.
3. Place cursor before `"generated_at"`.
4. Press `dd` to delete the line.
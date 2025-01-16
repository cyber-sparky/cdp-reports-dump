# Run the HTTP server


## HTTP Server:

- An HTTP server communicates using URLs and the HTTP protocol.
- It is used for creating web applications and APIs.

## Steps to Create and Host a File:

1. **Create `home_page.html`:**
    ```sh
    cat > home_page.html <<EOL
    <html>
    <body>
    <p>Welcome to the home page!</p>
    </body>
    </html>
    EOL
    ```

2. **Start Python HTTP Server:**
    ```sh
    python3 -m http.server &
    ```
    - **Output:** 
      ```
      Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
      ```
    - **Note:** For Python 2, use:
      ```sh
      python -m SimpleHTTPServer
      ```

3. **Find Hostname:**
    - Open a new terminal.
    - Run:
      ```sh
      hostname
      ```
    - **Output Example:**
      ```
      devsecops-box-oiahxczk
      ```

4. **Access the Server:**
    - Use the link:
      ```
      http://devsecops-box-oiahxczk-8000.lab.practical-devsecops.training
      ```
    - Click `home_page.html` to view the file.
    - **Output:**
      ```
      Welcome to the home page!
      ```
## Saving the File:

- Right-click the page and choose "Save As".
- Name the file with an appropriate extension and save.

### Summary

| Action                       | Steps                                                                                               |
|------------------------------|-----------------------------------------------------------------------------------------------------|
| Create `home_page.html`      | ```sh cat > home_page.html <<EOL <html> <body> <p>Welcome to the home page!</p> </body> </html> EOL ``` |
| Start Python HTTP Server     | ```sh python3 -m http.server & ``` <br> **For Python 2:** ```sh python -m SimpleHTTPServer ```        |
| Find Hostname                | ```sh hostname ```                                                                                   |
| Access Server                | Use the link: <br> ```http://<hostname>-8000.lab.practical-devsecops.training```                     |
| View and Save File           | Right-click and choose "Save As". <br> Name the file and save.                                       |
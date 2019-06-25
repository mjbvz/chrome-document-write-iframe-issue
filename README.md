# Chrome iframe + document.write() issue

When using `document.write()` to replace a local iframe, chrome can end up dropping all content after a certain `<script>` tag. This may be related to the size of the script or its encoding but I am not sure. The issue does not occur in Firefox or Safari.

I cannot reliably reproduce this in a simple example, but hit this every time in VS Code web (which is a much more complex page and uses a service worker to load all the scripts).

### Repo

1. Open this folder
1. Start a local http server on port 8080.
1. Load http://localhost:8080 in chrome
1. If you can't repo, try opening the dev tools and reloading the page a few times.

### Expected
1. The iframe should have the content of `text \n text`
1. In the developer console, the `Writing` and `Wrote` sections should be the same structurally
1. In the developer console, you should see the text:

    ```
    Executing a.js a.js:1:9
    Executing mermaid mermaid.js:1:9
    Executing z.js
    ```

### Actual
1. The iframe is empty.

1. The console shows a difference between the content we think we are writing and the written content:

    ```
    (index):29 == Writing ==
    (index):30 <html><head>
            <script src="http://localhost:8080/scripts/a.js"></script>
            <script src="http://localhost:8080/scripts/mermaid.js"></script> 
            <script src="http://localhost:8080/scripts/z.js"></script>
        </head>
        <body class="vscode-dark">
            text
            text
        </body></html>
    a.js:1 Executing a.js
    (index):37 == Wrote ==
    (index):38 <html><head>
            <script src="http://localhost:8080/scripts/a.js"></script>
            <script src="http://localhost:8080/scripts/mermaid.js"></script></head></html>
    ```

1. Only `Executing a.js` is printed in the console
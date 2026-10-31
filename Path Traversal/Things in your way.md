# Common Obstacles to exploiting path traversal vulnerabilities

> *Many Application that place user input into file paths implement defenses  against path traversal attacks can be bypassed.

> *Enable the images in the proxy history, some image parameters might not show if you dont..

If an application strips or blocks directory traversal sequences from the user-supplied filename it might be able to be bypassed.

> *If you cant use ../ to begin with

    1. Referencing the filesystem root.
        - filename=/etc/passwd
        - This will directly reference the file without using any traversal sequences. (Might work).
        - Only work if the server/backend treats the parameter as being relative to a default working directory.

> *If ../ are being removed by server

    2. Use Nested traversal sequences (....//) OR (....\/)

        - These turn into normal traversal sequences when the inner sequence is stripped.
       - ....// -> ../
       - Reason being it usually removes instances of ../ so doubling them works.

> *if web servers are striping any directory traversal sequences before passing your input to the application.

    3. URL encoding your payload, DOUBLE ENCODING if needed.

        - ../ -> %2e%2e%2f
        - unorthodox encoding may work as well..
            -..%c0%af or ..%ef%bc%8f
    
> * Burp suite offers a predefined payload list that will make it easier to try stuff.
    - File name: Fuzzing - path traversal

> *If an application requires the user-supplied filename to start with the expected base folder, such as `/var/www/images`,`filename=/var/www/images/../../../etc/passwd` would be used.

> * Sometimes an application may require the user-supplied filename to end with an expected file extension, such as .png.
    In this case we can use a null byte to effectively terminate the file path before the required extension.
    ```
    filename=../../../etc/passwd%00.png
    ```

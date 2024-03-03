# DAST
### Link
- https://tryhackme.com/room/dastzap
---
### Task 2

- Is DAST a replacement for SAST or SCA? (Yea/Nay)
    - **Answer**: Nay

- What is the process of mapping an application's surface and parameters usually called?
    - **Answer**: spidering/Crawling

- Does DAST check the code of an application for vulnerabilities? (Yea/Nay)
    - **Answer**: Nay


### Task 3

- ZAP can run an AJAX spider by using browsers without a Graphical User Interface(GUI). What are these browsers called?
    - **Answer**: headless

- Analysing the Sites tab, what HTTP parameters can be passed to login.php using the POST method? (In alphabetical order and separated by commas)
    - **Answer**: pass,user


- What other .php resource, besides nospiders-gallery.php was found by the AJAX spider but not by the regular spider?
    - **Answer**: /view.php


### Task 4

- Will disabling some test categories help speed up the scanning phase? (Yea/Nay)
    - **Answer**: Yea

- There should be two high-risk alerts in your scan results. One is Path Traversal. What's the name of the other one?
    - **Answer**: cross site scripting (reflected)


### Task 5

- Which type of script was used to record the authentication process to our site in ZAP?
    - **Answer**: ZEST scripts

- What additional high-risk vulnerability was found on the site after running the authenticated scan?
    - **Answer**: remote os command injection


### Task 6

- What high-risk vulnerability was found on the /asciiart/generate endpoint?
    - **Answer**: remote os command injection

- Read the details on the Path Traversal vulnerability detected. Based solely on the information presented by the scanner, would you categorise this finding as a false positive? (yea/nay)
    - **Answer**: yea

### Task 7

- Download the ZAP report for the simple-webapp repository. How many medium-risk vulnerabilities were found?
    - **Answer**: 3

- Check the main branch of the simple-api repository on Jenkins. One of the builds failed during the Build the Docker image step. What is the number of the pre-existing failed build?
    - **Answer**: 4

- Download the ZAP report for the simple-api repository. What high-risk vulnerability was found?
    - **Answer**: remote os command injection


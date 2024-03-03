# SAST
### Link
- https://tryhackme.com/room/sast
---
### Task 2

- Are automated code reviews a substitute for manual reviewing? (yea/nay)
    - **Answer** : nay

- What type of code review will run faster? (Manual/Automated)
    - **Answer** : Automated

- What type of code review will be more thorough? (Manual/Automated)
     - **Answer** : Manual

### Task 3

-  Which of the mentioned functions is used in the project? (Include the parenthesis at the end of the function name)
- Command line : grep -r -n 'include('
    - **Answer** : include()


- How many instances of the function found in question 2 exist in your project's code?
-  Use the result of the previous command line to find the answer.
    - **Answer** : 9

- What file contains the vulnerable instance?
- Result from the previous command line : **view.php:22:include('./gallery-files/'.$_GET['img']);**
    - **Answer** : view.php

- What line in the file found on the previous question is vulnerable to LFI?
- Result from the previous command line : **view.php:22:include('./gallery-files/'.$_GET['img']);**
    - **Answer** : 22


### Task 4

- Does SAST require a running instance of the application for analysis? (yea/nay)
    - **Answer**: 

- What kind of analysis would likely flag dead code segments?
    - **Answer**: Structual analysis

- What kind of analysis would likely detect flaws in configuration files?
    - **Answer**: Configuration analysis

- What kind of analysis is similar to grepping the code in search of flaws?
    - **Answer**: Semantic analysis

### Task 5

- What type of error occurs when the tool reports on a vulnerability that isn't present in the code?
    - **Answer**: false positive

- How many errors are reported after annotating the code as instructed in this task and re-running Psalm?
    - **Answer**: 9

### Task 6

- How many problems in total are detected by Semgrep in this project?
    - **Answer**: 27

- How many problems are detected in the showrecipe.inc.php file?
    - **Answer**: 8

- What other problem identifier is reported by Semgrep in this file? (Write the id reported by Semgrep)
    - **Answer**: echoed-request

- What type of vulnerability is associated with the problem identifier on the previous question?
    - **Answer**: cross-site scripting






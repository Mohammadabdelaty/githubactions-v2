# githubactions-v2

[Gitub-repo](https://github.com/sidd-harth/solar-system)

- Workflow > job > steps



# Syntax

- There are some pre-built actions, some of them are verified and some others from community.

    ![alt text](image.png)

- Artifacts:

    ```yaml        
    - uses: actions/upload-artifact@v7
        with:
        name: my-hello-artifact
        path: hello.txt 
    ```

    ![alt text](image-1.png)

    - or to downlaod it from here

        ![alt text](image-2.png)

    - To use in later jobs you need to donwload it and make the second job depends on the first one.

        ![alt text](image-3.png)

    - Usually artifacts stays for 90 days retention

        ![alt text](image-5.png) 

- Variables:

    - You can use variabels in 2 ways:

        ![alt text](image-6.png)
    
    - we can keep env: variable in root level so all jobs can access them

- Secret variables:

    - As in gitlab you can keep the secrets in repo settieng on environment or repository levels:

        ![alt text](image-7.png)
    
    - in this case we use `${{ secret.DOCKER_PASSWORD}}`, this secret is withing the same environment not repo level

        ![alt text](image-8.png)

        ![alt text](image-9.png)

    - Incase of repo veriables, `${{ vars.DOCKER_USERNAME }}`  

        ![alt text](image-10.png)

### Demos
- [variables-repo](https://github.com/Mohammadabdelaty/github-actions-variables)
- [simple](https://github.com/Mohammadabdelaty/github-actions-lab1-q1)
- [artifacts](https://github.com/Mohammadabdelaty/github-actions-artifacts-hands-on)


## Jobs

- Trigger `workflow_dispatch` lets you run it manually

    ![alt text](image-11.png)

- Concurrency, usually should be disabled if not needed, to reduce resources, it can on jos or workflow levels

    ![alt text](image-12.png)

    - so the higher priotiy will get in

        ![alt text](image-13.png)

    - If we set the arg to false it put the second workflow/job in queue

        ![alt text](image-14.png)

- Timeout: incase of mistakenly long runing job will consume alot of time and money, you can set a `timeout-minutes`:

    ![alt text](image-16.png)    

    ![alt text](image-17.png)

- matrix strategy: we can run the job with multiple images on multiple runners in parallel 

    ![alt text](image-18.png)

    ![alt text](image-19.png)

    - Some of it will fail as alpine won't work with amd windows so we execlud it and include some other image for ubuntu, also disable `fail-fast` and allow maximum 2 parallel jobs.

        ![alt text](image-20.png)

- runner-context: it show default variables in the repo which can be used (same as gitlab) in as expression for conditions

    ![alt text](image-21.png)

- expression: we use the conext in condition as follows

    ![alt text](image-22.png)

    - This will skip this job if not in main branch using the expression

        ![alt text](image-23.png)

- event filter: used in cases like trigger the workflow in a specific condition

    ![alt text](image-24.png)

    - It also can be done for pull-requests

        ![alt text](image-25.png)

    - some use cases require to run specific workflow as a check for a feature so it can be done only with pull request, whatever this request is open or closed 

    - Skip the workflow
        - [skip ci], [ci skip], [action skip], [skip action]

## Nodejs Pipeline

    ![alt text](image-26.png)

## Exeprission for code coverage error

    ![alt text](image-27.png)

- We may need to use condition in code coverage use case to keep going with the workflow even i didn't meet the threshold

    ![alt text](image-28.png)

    ![alt text](image-29.png)

- Also we can use `if` condition, usecase is that if there is a failure in one step and the next step uploads artifacts so to shw why eror happens, we need to use context as well for a step

    ![alt text](image-30.png)

    ![alt text](image-31.png)

    - Or, we can use a pre-confiugred expression ready 

        ![alt text](image-32.png)

    - The above `failure()` describes the status of the previous step

    - But the best practice in this case is to use `always()`, we need it to run it whether the previous one worked or not

        ![alt text](image-33.png)


#### Demo to be done

```
Navigate to your GitHub account and use github-actions-solar-system repository within feature/workflow branch
Explore and modify the workflow file named solar-system.yml

Do the following
Append a new job with id as code-coverage,
a. This job should execute on this operating system - ubuntu-latest
b. Add following steps
Step 1 - use an action to checkout the repository
Step 2 - use actions/setup-node action to setup NodeJS of version 18
Step 3 - Install NodeJS dependencies
Step 4 - Run Code-coverage command (npm run code-coverage)
This step may fail, configure the step to prevent the job from failing when this step fails.
Step 5 - Upload code-coverage reports using upload-artifact action with the below
config
- name: Code-Coverage-Result
- path: coverage
- retention-days: 5

Both the unit-testing and code-coverage jobs should run in parallel.
```
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

## Caching dependencies 

- Caching vs artifacts
    - Caching: re-use files that don't change between jobs.
    - Artifacts: Stors files that will be used between jobs.

- So we specify the path that files will be stored in, then a `key` for it.
    - This `key uses hash` for a file that is needed to be cached as if it's re-written, if this hash changes, so we get the new file or update.

    ![alt text](image-34.png)

    - this is in 1st start

    ![alt text](image-35.png)

    - this is when we run the workflow for the next time

    ![alt text](image-36.png)

    ![alt text](image-37.png)

- This example above shows another difference between caching and artifacts,
    - artifacts are not available for the next running workflow, but the

- You need to add the cache step in the 2 jobs in the same workflow with the same key before installation step.

## Doecker, push and test

### Dockerhub

- Only build and test for now

    ![alt text](image-38.png)

- To push, add the build push again but with `true` value for the `push` arg.

    ![alt text](image-39.png)

### GHCR

- Login

    ![alt text](image-40.png)

- Push:
    - 1st one is for doackerhub
    - snd is for ghcr

    ![alt text](image-41.png)

> No need to add `GITHUB_TOKEN` as a secret.

- The github tocken needs to have the read/write permission

    ![alt text](image-42.png)

- So we add the permission: wrtie to the packages

    ![alt text](image-43.png)

    ![alt text](image-44.png)

## Job and service containers

- There was an issue with accessing the mongodb database, slow, so they've decideed to run similar db as a `service container` and the nodejs in `job container` to handl testing and installing dependancies while testing the app to be faster

    ![alt text](image-45.png)

    ![alt text](image-46.png)

    ![alt text](image-47.png)

- We'll run code coverage in a job container as well but at this time it needs to access the mongodb serverce no using the `localhost` as the last job, in needs a name -all containers by default are in a brige network- we name it mongo

    ![alt text](image-48.png)

    - and change the mongodb url env variable

## Demo

Navigate to your GitHub account and use github-actions-solar-system repository within feature/workflow branch
Explore and modify the workflow file named solar-system.yml

Do the following:
Modify the job with id as unit-testing,
a. This job should make use of a container to run all the steps
Container image - node:20
The Setup Node.js Version step should be commented out because Node.js is already available in the container
b. Commit the changes and checkout the workflow execution

## Kubernetes deployment

- We need kubectl installation step by azure
- keep kube config file as a repo secret
- use `kuberenetes set context` by azure.

    ![alt text](image-49.png)

- keep variable inside manifest yaml files as follows `_{_NAMESPACE_}_`

    ![alt text](image-50.png)

- Add repo variable with name `NAMESPACE`
- Then replace it with `cschleiden/replace-token@v1`

    ![alt text](image-51.png)

- Another step for setting the `INGRESS_IP` variable >> `env.GITHUB` and make it available for next steps.

    ![alt text](image-52.png)

    ![alt text](image-53.png)

    ![alt text](image-54.png)

- Then create a secret with mongodb data and deploy

    ![alt text](image-55.png)

- To test it do anoher job but we need to first get the ingress host like ingress ip and but, 
    - As testing will be in another job we will need to expose the ingress host from the previous step as job output.
    - It requires to set id to the 1st job 

        ![alt text](image-56.png)

        ![alt text](image-57.png)

# Environments

- Used to set multi env dev and prod
- Set vars and could be the same var name but one foe dev with dev values like kubeconfig of dev cluster and the othe one for prod like the kubeconfig of the production env.

    ![alt text](image-58.png)

- You can also set rules like:
    - leader approval
    - wait time for making sure all good 

- We can set env vars and secrets like (kubeconfig)

- it takes name and url

    ![alt text](image-61.png)
    
    ![alt text](image-59.png)

## Custom actions

![alt text](image-62.png)

- It's like creating your own module in python
- here you creating your own action in a path likw this under `.github`

    ![alt text](image-63.png)

- Also we can package some steps or repeated ones in one action

- It's like variables and fiilling the required fields with these variable 

    ![alt text](image-64.png)

- So you can use this action by copying the `relative path` as the action, 

    ![alt text](image-65.png)

    To this

    ![alt text](image-66.png)

- Specify the shell dep on runner like `shell: bash`

## Docker action

- A docker image for running a REST API reqeust to call `GIPHY` 3rd party.

- Here we will use the following custom action to use `docker` instead of `composit` as the last action.

    ![alt text](image-68.png)

- The args in the end referes to inputs which will be input to the shell script to run inside this container

    ![alt text](image-69.png)

- We can call this custom action from another repo

    `uses: <repo-owner>/<repo>@<branch>`

## Custom action

- You can create a custom action and release it to marketplace

    ![alt text](image-71.png)

- This action will be used then run the index.js file to do giphy rest api request

    ![alt text](image-72.png)

# Runners

- As every ci tools you can have github/self hosted runners

- It can be on enterprise, or, or repo levels

- All running jobs work will be in `_work` dir in home of self-hosted runner

## Re-usable workflows

- As a funtion in programming, we call another workflow
- This another wf is created and triggered with `on.workflow_call`
- call it as we've called custom action

    ![alt text](image-73.png)

- `Secrets` Some variables and secret may be not be inhireted in the on_call wf, so we will need to inhirt them

    - on the main wf add them

        ![alt text](image-74.png)
    
    - on the on_call add them ass follows

        ![alt text](image-75.png)

- `Inputs` In the same way of inputting the secrets, we need to input another variables not secrets this time
- These vars are Kube version, environmet (prod, dev) as it referes to dir which contains the yaml files used in deployment

    ![alt text](image-76.png)

- Then use it in the same reusable wf

    ![alt text](image-77.png)

    ![alt text](image-78.png)

- In the main wf

    ![alt text](image-80.png)

    ![alt text](image-81.png)

- You'll see mongodb used in run

    ![alt text](image-82.png)

- `Output`. We set output to resuable wf

    ![alt text](image-83.png)

    - USe it in mian wf

        ![alt text](image-84.png)

## Reports

- We can donwload artifact like code converage and test, put them in one dir, upload that dir to S3
- We use `s3 Sync`

    ![alt text](image-86.png)

    ![alt text](image-87.png)

## Slack notify

- Create a channel on slack
- Create an app from docs, which will get you a webhook

    ![alt text](image-88.png)

    ![alt text](image-89.png)

- We made it runs `always()` not to depend on another rules

## Security

- You should check the action code first if it's not verified

    ![alt text](image-90.png)

- Sometimes script injection can be very risky
    - when i trigger the wf with an issue creation

       ![alt text](image-92.png)
    
    - then do this to the title of issue

        ![alt text](image-94.png)

- Or using `httpdump` curl to get aws secret key

    ![alt text](image-97.png)

    ![alt text](image-95.png)
    
    ![alt text](image-96.png)

- So how to avoid this??

    - We keep the title in env in memory

    ![alt text](image-98.png)

- You can keep secret in vault and integrat it with github

[![Solar System Workflow](https://github.com/Mohammadabdelaty/github-actions-solar-system/actions/workflows/solar-system-2.yml/badge.svg)](https://github.com/Mohammadabdelaty/github-actions-solar-system/actions/workflows/solar-system-2.yml) 🥲🥲🥲🥲🥲🥲

> How Script Injection Can Happen in GitHub Actions:
Untrusted Input: Using inputs from pull requests, issue comments, or other sources controlled by users without proper sanitization.

Environment Variables: Injecting untrusted data into environment variables that are later used in a script or command.

Workflow Commands: GitHub Actions supports a set of workflow commands (like ::set-env or ::add-path) that can be used to dynamically set environment variables, paths, etc. If an attacker can control the data that gets interpreted as a workflow command, they can alter the behavior of the workflow.

> Mitigation Strategies:
Validate and Sanitize Inputs: Always validate and sanitize user inputs or data fetched from external sources. Be cautious with data from pull requests, especially from forks.

Use Built-in Token and Permissions: GitHub Actions provides a GITHUB_TOKEN with limited permissions by default. Avoid using high-permission personal access tokens if possible.

Limit Workflow Permissions: Explicitly set the permissions for the GITHUB_TOKEN in your workflow to only what is necessary for the tasks at hand.

Avoid Exposing Sensitive Information: Be careful not to echo sensitive information in your logs. Treat all output as potentially visible.

Use Trusted Actions: Prefer actions that are well-maintained, versioned, and from reputable sources. Pin actions to a full length commit SHA to avoid unexpected changes.

Code Reviews: Implement a code review process for changes to workflows to catch potentially malicious changes.

Regular Audits: Regularly audit your workflows and actions for vulnerabilities and keep them updated.

Limit Runner Exposure: For self-hosted runners, ensure they are used only by trusted repositories as they have more access to your network.

Be Aware of Workflow Command Injection: GitHub has deprecated some workflow commands (like set-env and add-path) due to security issues. Always use the latest and safest methods for setting environment variables and paths.

By following these best practices, you can significantly reduce the risk of script injection attacks in your GitHub Actions workflows. Remember that security is an ongoing process, and keeping up to date with GitHub's recommendations and updates is crucial.
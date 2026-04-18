# githubactions-v2

[Gitub-repo](https://github.com/sidd-harth/solar-system)

- Workflow > job > steps

## Introduction
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

- runner-context


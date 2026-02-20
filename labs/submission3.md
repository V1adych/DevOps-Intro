## Task 1
[Link to successful CI run](https://github.com/V1adych/DevOps-Intro/actions/runs/22234183791/job/64321501372)

Jobs are named entities in `.yaml` file that are being run
steps are consecutive bash / higher-order commands to be executed withing a job

The job trigger is defined with `on` entry. Since `[push]` was used, the job was triggered after the commit was pushed to remote

The workflow execution process was sequential wrt. steps defined in `.yaml`
![image](assets/ci.png)

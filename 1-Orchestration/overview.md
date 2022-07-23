# Orchestration Overview

Services are containers in production. 

Stacks are groups of interrelated services that share dependencies and can be scaled together.


## Troubleshooting a Service

First, get a task ID with `docker service ps <service-name>`.

Next, check metadata with `docker inspect <task-id>`. In particular, Error message before container start is in the status field, and then confirm whether it was started with the intended parameters.

If the task has container ID, it was abnormally exited after starting the container, so check the log of the container with `docker logs <container-id>`.


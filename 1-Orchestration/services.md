
The **orchestrator** keeps the desired state defined in a service.

A service's tasks are initialized in the NEW state.


## Commands for Services

To add a placement preference: `docker service update --placement-pref-add`

To add or update a mount on an existing service: `docker service update --mount-add`

To remove a published port: `docker service update --publish-rm`

To publish container port(s) on an existing service: `--publish-add`

To set minimum and maximum memory: `--memory 4GB --memory-reservation 2GB`

## Troubleshooting a Service

First, get a task ID with `docker service ps <service-name>`.

Next, check metadata with `docker inspect <task-id>`. In particular, Error message before container start is in the status field, and then confirm whether it was started with the intended parameters.

If the task has container ID, it was abnormally exited after starting the container, so check the log of the container with `docker logs <container-id>`.
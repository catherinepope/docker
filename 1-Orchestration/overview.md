# Orchestration Overview

Services are containers in production. 

Stacks are groups of interrelated services that share dependencies and can be scaled together.

Steps when creating a service process in swarm mode: Docker API > orchestrator > allocator > dispatcher > scheduler

A dispatcher determines on which node a task is scheduled.

Swarmkit is a supported orchestrator in DockerEE.

*Placement Preference* is used to place services evenly on appropriate nodes in a swarm cluster.

Use the `--update-delay` flag to introduce a delay between tasks during a rolling restart.
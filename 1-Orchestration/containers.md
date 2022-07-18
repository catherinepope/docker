# Running Containers

Images are *build-time* constructs, whereas containers are *run-time* constructs.

We use `docker container run` and `docker service create` commands to start one or more containers from a single image.

You cannot delete the image until the last container using it has been stopped and destroyed.

Images don't contain a kernel. All containers running on a Docker host share access to the host's kernel.


Pull an image:

```docker image pull ubuntu:latest```

View images:

```docker image ls```

Run container from image in interactive mode:

```docker container run -it ubuntu:latest /bin/bash```

Press `Ctrl-PQ` to exit the container without terminating it.

It should still be visible through this command:

```docker container ls```

You can attach your shell to the terminal of a running container with the `docker container exec command`:

```docker container exec -it inspiring_banzai bash```

To stop the container:

```docker container stop inspiring_banzai```

To remove the container:

```docker container rm inspiring_banzai```

To check the container is removed, run:

```docker container ls -a```

The `-a` flag tells Docker to list *all* containers, even those in stopped state.
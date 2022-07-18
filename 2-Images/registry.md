# Using Registries

The local image repository on a Linux-based Docker host is usually located at `/var/lib/docker/<storage-driver>`.

You can check the contents with:

```docker image ls```

Image registries contain one or more *image repositories*.

## Searching Docker Hub from the CLI

The `docker search` command lets you search Docker Hub from the CLI:

```docker search catherinepope```

You can use a filter to ensure only official repos are displayed:

```docker search alpine --filter "is-official=true"```


## Pulling Images

```docker image pull redis:latest```

If you don't specify an image tag after the repository name, Docker assumes you're referring to the image tagged as `latest`. If the repository doesn't have an image tagged as `latest`, the command fails.

If you want to pull images from third-party registries (not Docker Hub), you need to prepend the repository name with the DNS name of the registry:

```docker image pull gcr.io/google-containers/git-sync:v3.1.5```

### Images with Multiple Tags

Pull all of the images in a repository by adding the `-a` flag:

```docker image pull -a nigelpoulton/tu-demo```

### Pulling Images by Digest

Tags are mutable! It's possible to wrongly tag an image.

All images get a cryptographic *content hash*, also referred to as a digest. It's impossible to change the contents of the image without creating a new unique digest. Digests are immutable.

Every time you pull an image, the `docker image pull` command includes the image's digest.

You can also view the digests of your images:

```docker image ls --digests```

Once we know the digest of an image, we can use it when pulling the image again:

```docker image pull ubuntu@sha256:b6b83d3c331794420340093eb706a6f152d9c1fa51b262d9bf34594887c2c7ac```

### Multi-Architecture Images

A single image, such as `golang:latest`, can contain an image for Linux on x64, Linux on PowerPC, and Windows x64. You can run a simple `docker image pull golang:latest` from any platform and Docker pulls the correct image for your platform and architecture.


## Inspecting Images

```docker image inspect ubuntu:latest```

The `docker history` command is another way of inspecting an image and seeing layer data. However, it shows the *build history* of an image and is not a strict list of layers in the *final image*.

For example, some Dockerfile instructions (`ENV`, `EXPOSE`, `CMD`, `ENTRYPOINT`) add metadata to the image and don't create permanent layers.

## Deleting Images

```docker image rm <image-name>```

If an image layer is shared by more than one image, that layer won't be deleted until all images referencing it have been deleted.

You can delete multiple images this way:

```docker image rm <image1> <image2>```

To delete all images:

```docker image rm -f $(docker image ls -q)```

The `-q` flag returns a list of just the image IDs.
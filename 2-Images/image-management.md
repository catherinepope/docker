# Managing Images

## Inspecting Images

```docker image inspect <image-name>```

## Filtering Images

Use the `--filter` flag to filter the list of images returned by `docker image ls`.

The following command returns only dangling images:

```docker image ls --filter dangling=true```

A dangling image is an image that is no longer tagged. It appears in listings as `<none:none>`. This usually occurs when building an image with an existing tag. When this happens, Docker builds the new image and removes the tag from the existing image.

## Using the --format Flag

The following command returns only the size property of images on a Docker host:

```docker image ls --format "{{.Size}}"```

Use the following command to return all images, but display only the repo, tag, and size:

```docker image ls --format "{{.Repository}}: {{.Tag}}: {{.Size}}"```

## Pruning Images

You can delete all dangling images on a system with the `docker image prune` command. If you add the `-a` flag, Docker also removes all unused images (those not in use by any containers).


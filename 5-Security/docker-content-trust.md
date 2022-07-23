# Docker Content Trust (DCT)

DCT is used to sign and verify the content digitally between senders, a Docker registry with an attached Notary server, and receivers.

Docker uses three keys:

- Root/offline key
- Repository/tagging key
- Timestamp key 

`export DOCKER_CONTENT_TRUST=1`

Once the above environment variable is set, when you push an image, you'll be prompted to create root and repository keys.

With `DOCKER_CONTENT_TRUST` set to 1, all images pulled or pushed must be signed, unless they are official or verified images.

Set it to 0 to disable this.

The root key is crucial! Don't lose it, else you'll need to contact Docker Support to reset it. The root key belongs to one person or organization.

The repository key is associated with an image repository. The last key (the timestamp) denotes image freshness.

You could also use:

`docker image pull --disable-content-trust` to pull images, but you would be unable to run them as containers.

Another alternative:

```
$ docker trust key generate <your name>
$ docker trust signer add --key cert.pem <your name> <registry address>/<username>/<image name>:<tag>
$ export DOCKER_CONTENT_TRUST=1
$ docker push <registry address>/<username>/<image name>:<tag>
```

To remove trust data, use the `docker trust revoke` subcommand.
# Creating Images

To build an image:

```docker image build -t test:latest .```

To check the image is created:

```docker image ls```

To run the image as a container:

```docker container run -d --name web1 --publish 8080:8080 test:latest```

## Containerizing an App

The process of containerizing an app:

1. Start with application code and dependencies.
2. Create a *Dockerfile* that describes your app, its dependencies, and how to run it.
3. Feed the *Dockerfile* into the `docker image build` command.
4. Push the new image to a registry (optional).
5. Run container from the image.

```
FROM alpine

LABEL maintainer="nigelpoulton@hotmail.com"

# Install Node and NPM
RUN apk add --update nodejs npm curl

# Copy app to /src
COPY . /src

WORKDIR /src

# Install dependencies
RUN  npm install

EXPOSE 8080

ENTRYPOINT ["node", "./app.js"]
```

`FROM` is the base layer of the image. If it's a Linux app, this must specify a Linux-based image. The base layer should be small, and preferably from an official source.

`LABEL` is a key-value pair and a way of adding custom metadata to an image.

`RUN` runs a command and creates a new layer above the base layer (typically downloading and installing software, such as Node).

`CMD` there are several difference between CMD and RUN:

- The `CMD` is executed when you run a container from your image. The `RUN` instruction is executed during the build time of the image.
- You can have only one `CMD` instruction in a Dockerflle. If you add more, only the last one takes effect. You can have as many `RUN` instructions as you need in the same Dockerfile.
- You can add a health check to the CMD instruction, for example, `HEALTHCHECK CMD curl --fail http://localhost/health || exit 1`, which tells the Docker engine to kill the container with exit status 1 if the container health fails.
- The `CMD` syntax uses this form [“param”, param”, “param”] when used in conjunction with the ENTRYPOINT instruction. It should be in the following form CMD [“executable”, "param1”, “param2”…] if used by itself.

Example: `CMD "echo" "Hello World!"`

`COPY` creates another new layer and copies the application and dependency files from the build context.

`ADD` is similar to COPY, but there are some significant differences:

- `ADD` supports URL handling, `COPY` doesn't.
- `ADD` supports extra features, such as local-only tar extraction.
- `ADD` supports regular expression handling, `COPY` doesn't.

`WORKDIR` sets the working directory inside the image filesystem for the rest of the instructions in the file. This instruction doesn't create a new image layer.

`RUN` creates a new layer and uses npm (installed in previous instruction) to install application dependencies listed in the `package.json` file in the build context. It runs within the context of the `WORKDIR` set in the previous instruction.

`EXPOSE` exposes a web service on TCP port 8080. This is added as image metadata, not as a layer.

`ENTRYPOINT` sets the main application that the image (container) should run. This is also metadata. `ENTRYPOINT` overrides the `CMD` instruction and `CMD`'s parameters are used as arguments to `ENTRYPOINT`, e.g:

```
CMD "This is my container"
ENTRYPOINT echo
```

`ENV` sets the environment variables in the container, e.g. setting a log path other than the Docker Engine default, e.g. `ENV log_dir /var/log`.

`USER` By default, the Docker engine sets the container’s user to root, which can be harmful. Actually, no one gives root privileges like that. Therefore, you should set a user ID and username for your container, e.g. `USER 75 engy`.

`VOLUME` creates a directory in the image filesystem, which can later be used for mounting volumes from the Docker host or the other containers.

If an instruction is adding *content*, such as files and programs to the image, it creates a new layer. If it is adding *instructions*, it creates metadata.

## Pushing an Image

```docker login```

Docker needs the following information when pushing an image:

- Registry
- Repository
- Tag

You need to retag your image to include your Docker ID, e.g.:

```docker image tag web:latest catherinepope/web:latest```

The format of the command is `docker image tag <current-tag> <new-tag>`. It adds an additional tag, rather than overwriting the original.

Then you can push it:

```docker image push catherinepope/web:latest```

## Inspecting Images

You can view the instructions that were used to build the image:

```docker image history web:latest```

Each line corresponds to an *instruction* in the Dockerfile, starting from the bottom and working up.

Use the `docker image inspect` command to inspect the *layers* that were created:

```docker image inspect web:latest```

## Using Multi-Stage Builds

Every `RUN` instruction adds a new layer. Consequently, it's best practice to include multiple commands as part of a single `RUN` instruction using `&&` and `\` line breaks.

It's even better to remove stuff you don't need, e.g. installation files, once you've finished with them.

```
FROM node:latest AS storefront
WORKDIR /usr/src/atsea/app/react-app
COPY react-app .
RUN npm install
RUN npm run build

FROM maven:latest AS appserver
WORKDIR /usr/src/atsea
COPY pom.xml .
RUN mvn -B -f pom.xml -s /usr/share/maven/ref/settings-docker.xml dependency:resolve
COPY . .
RUN mvn -B -s /usr/share/maven/ref/settings-docker.xml package -DskipTests

FROM java:8-jdk-alpine
RUN adduser -Dh /home/gordon gordon
WORKDIR /static
COPY --from=storefront /usr/src/atsea/app/react-app/build/ .
WORKDIR /app
COPY --from=appserver /usr/src/atsea/target/AtSea-0.0.1-SNAPSHOT.jar .
ENTRYPOINT ["java", "-jar", "/app/AtSea-0.0.1-SNAPSHOT.jar"]
CMD ["--spring.profiles.active=postgres"]

```

The example above includes three `FROM` instructions, each constituting a distinct *build stage*. They're numbered from the top, starting at 0. You can also give each stage a friendly name. 

In this example, stage 0 is called `storefront`. This stage pulls the `node:latest` image and contains lots of build stuff.

Stage 1, called `appserver` pulls the `maven:latest` image and adds lots of build tools. There's not much production code yet.

Stage 2, called `production`, pulls the `java:9-jdk-alpine` image. It copies in some app code produced by the `storefront` and `appserver` stages.

`COPY --from` instructions are used to copy only production related application code from the images built by the previous stage. They don't copy build artifacts that aren't needed for production.

If you run `docker image ls`, you'll see a list of images pulled and created by the build operation. You should see that the final image - `multi:stage` - is significantly smaller than the other images that were pulled. This is because it's based on the much smaller `java:9-jdk-alpine` image and contains only the production-related app files from the previous stages.

The `docker image build` process iterates through a Docker file one line at a time, starting from the top. For each instruction, Docker looks to see whether it already has an image layer for that instruction in its cache. If it does, this is a *cache hit* and it uses that layer; if it doesn't, that's a *cache miss* and it builds a new layer from the instruction. Of course, *cache hits* can greatly accelerate the build process.

As soon as any instruction results in a *cache miss*, the cache is no longer used for the remainder of the build. This is important for how you write your Dockerfiles: place instructions that are *likely to invalidate the cache* towards the end of the file. This means a *cache miss* won't occur until the later stages of the build, allowing the build to benefit as much as possible from the cache.

You can force the build process to ignore the entire cache by passing the `--no-cache=true` flag to the `docker image build` command.

`COPY` and `ADD` instructions include steps to ensure that the content copies to the image hasn't changed since the last build. Docker performs a checksum against each file copies and compares it to a checksum of the same file in the cached layer. If the checksums don't match, the cache is invalidated and a new layer is built. 

If you're building Linux images and using the apt package manager, you should use the `no-install-recommends` flag with the `apt-get install` command - this ensures apt installs only main dependencies and not recommended or suggested packages.
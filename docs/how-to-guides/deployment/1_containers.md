# How to create Containers

Containers are a way to package and distribute software as packages that run very independently of the host operating system.
Containers do not care whether you run them on Windows, macOS, Debian (Linux), in the cloud or in any other way.
This makes them very useful and easy to deploy.

!!! important "Goal"

    At the end of this guide, you will have a container that serves a simple Vue application to your browser.

!!! important "Prerequisites"

    This guide assumes that you have some Vue application in your current working directory.

    Simply running `#!shell npm init vue@latest` is fine and changing into the generated directory is fine though.


## 1) Create a *Dockerfile*

As said above, containers are a way to package your application.
*Dockerfile*s in essence are scripts that tell your computer **how** such a container is built from the surrounding source code.
The syntax reference and list of commands can be found [here](https://docs.docker.com/engine/reference/builder/).

Simply add a file called `Dockerfile` to the root of your application (besides *package.json*) and fill it with the following content:

```dockerfile title="Dockerfile"
# create an intermediate container named 'build' based on nodejs
FROM docker.io/node:18-alpine AS build
# switch the working directory to /app/src
WORKDIR /app/src
# add all files of the current directory to /app/src/
ADD ./ /app/src/
# install all required dependencies from package-lock.json and abort if it is not up-to-date
RUN npm clean-install
# build static files into dist folder (this is standard vue behavior)
RUN npm run build


# create another container named 'http' based on a stable version of the nginx webserver
FROM docker.io/nginx:mainline as http
# add a config file for the nginx webserver
ADD .docker/nginx.conf /etc/nginx/conf.d/default.conf
# add built files from the intermediate 'build' container into /var/www/ so that they can be served by nginx
COPY --from=build /app/src/dist/ /var/www/
# add some additional image metadata
# this is not strictly required but is nice so that the image describes itself
EXPOSE 80/tcp
```

## 2) Create an nginx configuration

In the *Dockerfile* we referenced the `.docker/nginx.conf` file.
This file does not yet exist so docker would complain about that when building the container.
What this file does is tell nginx how to serve our application.
It can include a number of directives[^1] and you can find more information on it in its documentation [here](https://nginx.org/en/docs/).

[^1]: [List of nginx directives](https://nginx.org/en/docs/dirindex.html)

!!! info

    We generally prefer to keep files that are not directly part of the application source code and which are only
    required when deploying via docker in the `.docker` folder.

```nginx title=".docker/nginx.conf"
server {
  # respond to all requests on port 80 with this configuration
  listen 80;
  server_name default_server;

  # configure /var/www/ as the directory from which to serve static files as responses
  root /var/www;
  # fall back to /index.html when a url does not have a corresponding file on the server
  try_files $uri $uri/ /index.html;

  location /assets/ {
    # allow caching for 7 days and tell caches that the resource is immutable
    add_header "Cache-Control" "max-age=604800, immutable";
  }
}
```


## 3) Create a *.dockerignore* file

Inside our `Dockerfile` we clean-install all dependencies and then build the application from scratch.
However, before that we are adding all files of the current directory to the container (`#!dockerfile ADD ./ /app/src/`) including the existing `node_modules` and `dist` folders that a developer might have on their machine.

To prevent this we could either use a more fine-grained *ADD* instruction to only add actually required files, but we can also restrict docker from ever seeing certain files.
This can be done by using a `.dockerignore` file.
Its syntax[^2] is similar to *gitignore*, and it completely hides certain files from docker.

[^2]: [.dockerignore Syntax Reference](https://docs.docker.com/engine/reference/builder/#dockerignore-file)

??? example "More fine-grained *ADD* instruction"

    The part of the *Dockerfile* containing `#!dockerfile ADD ./ /app/src/` could be replaced by the following.
    As you can see this is a lot more verbose and requires updating if more top-level files or folders are added.  

    ```dockerfile title="Dockerfile"
    ADD package.json package-lock.json vite.config.js index.html /app/src/
    ADD src /app/src/src/
    ADD public /app/src/public/
    ```

```gitignore title=".dockerignore"
node_modules
dist
```

## 4) Build and run the containerized app

Now that we have everything in place we want to build the container and test that everything works correctly.

### Building

First we need to build the container.
This can be done using the *docker build*[^3] command.

[^3]: [Docker Command Reference](https://docs.docker.com/engine/reference/commandline/docker/)

```shell title="Build the container" linenums="0"
docker build -t example_app .
```

| Command part     | Purpose                                                                                                                                |
|------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `docker build`   | Call docker in build mode to build a container.                                                                                        |
| `-t example_app` | Gives the built container the tag *example_app*.<br/>This can be almost anything you want and should probably be the name of your app. |
| `.`              | Uses the current directory as build context (meaning references to files from *Dockerfile* are relative to this directory).            |

### Running

After *docker build* completed successfully you can start the container using the tag that was chosen in the build command by calling *docker run*[^3].

```shell title="Run the container" linenums="0"
docker run -p 8000:80 example_app
```

| Command part  | Purpose                                                                                                                          |
|---------------|----------------------------------------------------------------------------------------------------------------------------------|
| `docker run`  | Call docker in run mode to start a container                                                                                     |
| `-p 8000:80`  | Publishes the given container port so that requests to your host machines port *8000* are forwarded to the containers port *80*. |
| `example_app` | The tag of the container that we used in the *docker build* step.                                                                |

### Accessing

Once the container has started up you can access the app at [localhost:8000](http://localhost:8000) since port *8000* is forwarded to the container and we have a webserver running inside it.

### Stopping

You can stop the running container by pressing `CTRL+C` in the terminal.

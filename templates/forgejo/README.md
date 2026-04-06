# Forgejo 

Forgejo is a self-hosted lightweight fork of Gitea. 
It supports adding workflows and actions to it.

## How to setup

There are mainly 3 steps to setup the forgejo actions

### Step 1 - Getting the runner token

First of all you want to activate the Forgejo Server container (labelled `server`).

Log into the server (at `http://your.server.ip:3000`) and follow the usual setup of the user and like with normal Forgejo.

Once you have setup everything you can click on your profile dropdown (top right) and select the `Site administration`.
(Alternatively you can create an Org-wide / User-wide / Repo-only runner, but for simplicity we will use the site-wide runner)

Then on the left side navigate to `Actions` and select from the dropdown the `Runners` option.
You will be taken to a page which diplays the runners you have, you will have to select the `Create new runner` option.

This will give you a `Registration Token`, copy it and store it as you will need it next.

You can then close the website.

### Step 2 - Configuring the runner to use docker-in-docker container

For step 2, you can activate the forgejo runner container (labelled `runner`).
NOTE: you **have** to run the runner container with this option: `command: ["/bin/sh", "-c", "while : ; do sleep 1 ; done ;"]` in the yaml, else the container will automatically go down.

Once you activate the runner container, you can enter the container's shell and use the following command inside of it:
```bash
cd /data
forgejo-runner generate-config > config.yml
```

This will generate the configuration file in the `/data` folder (`/runner` for us since it is binded there) so that we can edit it from the host.

The `config.yaml` file will have a bunch of options, you can read what each of them do if you want, but for simplicity this is the configuration I use:

```yaml
runner:
    file: .runner
    capacity: 1
    envs:
        DOCKER_HOST: tcp://dind_container.docker.internal:2375
    env_file: .env
    timeout: 3h
    shutdown_timeout: 3h
    insecure: false
    fetch_timeout: 5s
    fetch_interval: 2s
    report_interval: 1s
    labels: []

cache:
    enabled: true
    port: 0
    dir: ""
    external_server: ""
    secret: ""
    host: ""
    proxy_port: 0
    actions_cache_url_override: ""

container:
    network: ""
    enable_ipv6: false
    privileged: false
    options: '--add-host=dind_container.docker.internal:host-gateway'
    workdir_parent:
    valid_volumes: []
    docker_host: "-"
    force_pull: false
    force_rebuild: false

host:
  workdir_parent:
```

In short this will tell the runner to use the `docker-in-docker` container to spawn containers to run the forgejo workflows.

Once you have configured the runner you can register your runner.

### Step 3 - Registering the runner

Once again enter the runner container's shell and use this command (in the `/data` folder):
```bash
forgejo-runner register
```

This will begin an interactive guide for registering the runner, you will need to pass the token we copied in step 1 to the runner (for the url I used the public URL that points at the forgejo server with https)

You will be prompted to give a label to the runner, the label is the default image to use for your runner, like `alpine` or `python`

I personally use `alpine` hence why i gave it this label: `docker:docker://data.forgejo.org/oci/alpine:3.20`
It means that it will use the `linux alpine 3.20` image if the label given in the workflow is `docker`.

Do note that even if you register it with this image you can change the image it runs later on by simply adding
```yaml
jobs:
  test:
    runs-on: docker
    container:
      image: node:20-bullseye
```
The image argument to the container, it will pull that image instead of the default image.

Also to note that the label format is this: `<label-name>:<label-type>://<default-image>`
So in our case the `label-type` has to be docker since we are using docker-in-docker.

All of the above will generate a `.runner` file in the `/data` directory that identifies your runner. 

### Post registering

Now that you have registered your runner, you can finally activate the forgejo runner daemon.
You can delete this line from the `docker-compose.yaml` file:

```yaml
command: ["/bin/sh", "-c", "while : ; do sleep 1 ; done ;"]
```

And uncomment this line for it to look like this:
```yaml
command: ["/bin/sh", "-c", "sleep 5; forgejo-runner daemon --config /data/config.yml"]
```

This will activate the daemon, you can now redeploy the entire project and check in the Runners tab under Site administration option to see your runner active!
at 202204041021
Status: #idea
Tags: #docker #programming

# docker
- Has no file system. It's a layer between the host OS and the application you're trying to run. Limitations (for security) on some protected stuff on the host machine.
- A container is kind of like a [[git]] workflow. An image is like master - changes aren't saved unless commanded.
- An image is the blueprint, the container is what is actually run. Containers are built from the image.
- To specify the ubuntu version to use when building a docker image, use the `-p` flag:
```bash
build_docker_image.sh -p ubuntu_jammy_x86_64 -t poe:v14-rc ./
```

### Docker run 
- container starts, a bash terminal is attached to that container/process
- Can attach more than 1 terminal to that container

Changes to an image refer only to installed software, not files.

### General Workflow
- Make changes to a container
- Commit prior to closing: `docker commit [container id] [image name]`
- If `[image_name]` is blank, a new, nameless image will be created.

### Permissions
- Permissions can get wonky, better to make permission/access changes outside the container
- Container can be run in priveledged mode if you need to access serial devices; normally docker containers don't have access to all devices.

Committing a change to an image will create a new image - can be overwritten by not specifying tags. Try:

| REPOSITORY | TAG       |
|------------|-----------|
| POE        | VERSION   |
| POE        | VERSION 2 |

etc...

(VS code has better docker support)

### [[Qt Creator]]
- launch container
- install qt in the container
- save the image
- This requires qt to be in a location mounted by the default for the container - for us it's under `~/`, so my current location will need to be moved to work.

### Containers
Can view the container registry to see what containers exist. 

#### Login
First do this to log into azure:
`az acr login -n acrgss1`

Then:
```bash
az acr repository show-tags --name acrgss1 --repository project_opt_mdas_ubuntu_bionic_x86_64
```
`--repository` will be the name of the container to look for.

#### Pulling Image
```bash
docker pull acrgss1.azurecr.io/project_opt_mdas_ubuntu_jammy_x86_64:grey-seal-rc
```

#### Copy file from container
`docker cp CONTAINER:/var/logs/ /tmp/app_logs`


#### Bionic/Jammy
Containers may exists for both `ubuntu_bionic_x86_64` and `ubuntu_jammy_x86_64`. Jenkins will attempt to build both.

A container can be pulled from the repository. Works via VS Code out of the box apparently.
# References
https://stackoverflowteams.com/c/greensea/questions/292
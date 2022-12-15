202204041021
Status: #idea
Tags: #docker #programming

# docker
- Has no file system. It's a layer between the host OS and the application you're trying to run. Limitations (for security) on some protected stuff on the host machine.
- A container is kind of like a [[git]] workflow. An image is like master - changes aren't saved unless commanded.
- An image is the blueprint, the container is what is actually run. Containers are built from the image.


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

# References




202204041021
Status: #idea
Tags: #docker #programming

# docker
- Has no file system. It's a layer between the host OS and the application you're trying to run. Limitations (for security) on some protected stuff on the host machine.
- A container is kind of like a [[git]] workflow. An image is like master - changes aren't saved unless commanded.

### Docker run 
- container starts, a bash terminal is attached to that container/process
- Can attach more than 1 terminal to that container

Changes to an image refer only to installed software, not files.

### General Workflow
- Make changes to a container
- Commit prior to closing

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

# References




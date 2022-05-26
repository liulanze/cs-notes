# Docker learning note

- <img src="https://raw.githubusercontent.com/liulanze/cs-notes/main/notes/pics/docker1.png">
- <img src="https://raw.githubusercontent.com/liulanze/cs-notes/main/notes/pics/docker2.png">
- Image is a single file with required needs to run one certain program.
- Container is running "that" program, also, container is the instance of the image.
- <img src="https://raw.githubusercontent.com/liulanze/cs-notes/main/notes/pics/docker3.png">
- <img src="https://raw.githubusercontent.com/liulanze/cs-notes/main/notes/pics/docker4.png">
- Firstly, image is composed of two parts: (1) file system snapshot which saves files & file path. (2) startup command which will be run right after the required files are prepared ready. In this example, the image segment a place in HD which dedicate to save Chrome files to run the program.
- <img src="https://raw.githubusercontent.com/liulanze/cs-notes/main/notes/pics/docker5.png">
- ```
  docker run hello-world = docker create hello-world + docker start hello-world
  ```
- ``` docker ps ```
- ``` docker ps --all ```
- ```
  docker create <image_name> -> return a container id
  docker start -a <container_id> -> return program running output of the container
  ```
- ``` docker system prune ```
- ``` docker stop <container_id> ```
- ``` docker kill <container_id> ```
- ```
  sometimes we want container to be boot then to run some certain clis. "-it" is to allow to provide input to the container.
  ->
  docker exec -it <container_id> <command>

  similiar one but enter container terminal, note that press control + d to exit the terminal.
  ->
  docker exec -it <container_id> sh
  ```

## Building custom image through docker server
---

- In order to create a usable image, we need to create a Dockerfile
- <img src="https://raw.githubusercontent.com/liulanze/cs-notes/main/notes/pics/docker6.png">
- ``` docker build <Dockerfile> . ```
- In the process of docker build, every image built from each step will be cached, which means in the future when facing same build, image will be retrieved from cache. Save time.
- ```
  cli for replacing container id to a string name: 
  docker build -t <docker_name/repo_name:version> .
  ->
  that "." at the end is for specifying the directory of files / folders to use for the build.
  ```
- ```
  When creating image, we know that many temporary containers will be created, which will help to create the final required image. So, imagine in the middle process, container is running and will need files below local machine, it can not find this required file because container segment a own place under HD, so it can not reach out to the local hard disk. In this case, we will need to copy local file to the container.
  ->
  COPY ./ ./
  ->
  1) the first "./" is the path of the folder / file to copy from locally relatively to your build context.
  2) the second one is the place to copy stuff to inside the container.
  ```
- <img src="https://raw.githubusercontent.com/liulanze/cs-notes/main/notes/pics/docker7.png">
- ```
  Container can not only segment hard disk, but also can segment network. So we will need to connect local and container.
  ->
  docker run -p 8080:8080 <image_id / image_name>
  ```

## Docker compose with multiple local containers
---

- Docker compose is a kind of docker cli that help to save running multiple lines of docker clis. docker compose can integrate multiple docker clis, and run them at once.
- Docker compose yml file can automatically help to connect multiple containers. Docker compose can automatically setup some networking between different services or these different types of containers that we defined inside the docker-compose.yml file.
- ``` docker-compose ip = docker run image```
- ``` docker-compose up --build = docker build . + docker run image ```
- ``` docker-compose up -d ``` -> run all the containers.
- ``` docker-compose down ```
- ``` docker-compose ps ```

### Docker volumes
---

- When we create an image, then to do some modify to the code, container will not make corresponding change unless we rebuild the image. In dev (not for prod!), we can use docker volumes to sync the code and container.
- <img src="https://raw.githubusercontent.com/liulanze/cs-notes/main/notes/pics/docker8.png">
- ```
  docker run -p 8080:8080 -v /project -v $(pwd):/app <image_id / image_name>
  ```
- ### volumes are not usually used in prod.

## Production level workflow
---

- Dockerfile: defines image that how the containers will look like.
- docker-compose.yml: defines how many containers will be created and what purpose for each container.
- docker attach: Use docker attach to attach your terminal's standard input, output, and error (or any combination of the three) to a running container using the container's ID or name. This allows you to view its ongoing output or to control it interactively, as though the commands were running directly in your terminal.

Reference: https://www.udemy.com/certificate/UC-730cb976-ae30-464c-9275-d9d8bf16a47d/

TKA Modul 1

Question Source: [Cloud Computing Practicum Module 1](https://docs.google.com/document/d/1vnD6l0r7BPj8p0vqhMG14pTPug0mWu37NKeGqQIT6w8/edit?tab=t.0)

# Soal 1

# Soal 2

# Soal 3

## 1. Create Project Directory

We can create the initial project directory easily with this command:

```bash
$ mkdir -r eva-project/app,logs && cd eva-project/ && touch Dockerfile && touch app/index.html
```

## 2. Create the Simple Landing Page

The [index.html](./soal_3/eva-project/app/index.html) can be filled with a simple HTML, such as:

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <p>EVANGELION STATUS SYSTEM</p>
    <p>Unit-01 Ready for Launch</p>
  </body>
</html>
```

## 3. Initial Dockerfile Creation

Fill the [Dockerfile](./soal_3/eva-project/app) with this:

```dockerfile
FROM nginx:latest
COPY app/index.html /usr/share/nginx/html/index.html
EXPOSE 80
```

## 4. Building the Docker Image

Run this command on docker CLI:

```bash
$ docker build -t eva-web-b08:v1 .
```

## 5. Run the Docker Image

Initially, to create and start a new container with a new docker image, you can run this:

```bash
$ docker run --name eva-container-b08 -p 8080:80  -v $(pwd)/app:/usr/share/nginx/html -v $(pwd)/logs:/var/log/nginx eva-web-b08:v1
```

After running that, we can check if it's successfully running by running:

```bash
$ docker ps
```

or

```bash
$ docker container ls
```

## 6. Make Sure the Container is Running

Go visit the [local web](http://localhost:8080) to check if the container is running alright.

Or just use this command:

```bash
$ curl http://localhost:8080
```

## 7. Make Sure the Volume is Mounted Successfully

To check if the volume is mounted successfully, we can test it by changing the content of the [index.html](./soal_3/eva-project/app/index.html) in the host's directory into:

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <p>EVANGELION STATUS SYSTEM</p>
    <p>Unit-01 Launch Successful</p>
  </body>
</html>
```

If the volume was mounted successfully the [web page](http://localhost:8080) should also be updated automatically updated.

Either just access the [web page](http://localhost:8080) again or just use this command again:

```bash
$ curl http://localhost:8080
```

## 8. RCA Docker Internal

Here in the practicum we're needed to:

1. Check the container's directory
2. Check the contents of the index.html through the container
3. Check the container's log in real-time

Below are the commands we can use to access the container's directory list the directory and to read files inside of it, by simulating a terminal that is connected to the container:

```bash
$ docker exec -it eva-container-b08 bash
$ ls -l
$ cd /usr/share/nginx/html/ && cat index.html
```

Usually we can use the command below to output the log of the container, using the command below:

```bash
$ docker logs -f eva-container-b08
```

But since the logs is already binded with the `

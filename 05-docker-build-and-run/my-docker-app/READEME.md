# 🔨 Docker Build & Run

In this hands-on, we will create a simple HTML application, build a Docker Image using a Dockerfile, and run it inside a Docker Container using Nginx.

The complete flow is:

```text
Dockerfile
     ↓
docker build
     ↓
Docker Image
     ↓
docker run
     ↓
Docker Container
     ↓
Running Application
```

---

# 🖥️ Environment

This hands-on was performed on:

- AWS EC2
- Ubuntu
- Docker
- Nginx

---

# Step 1: Check Docker Installation

Check the Docker version:

```bash
docker --version
```

Check the Docker service:

```bash
sudo systemctl status docker
```

Check whether Docker is working:

```bash
docker ps
```

---

# Step 2: Create the Project Directory

Create a directory for our Docker project:

```bash
mkdir my-docker-app
```

Move into the directory:

```bash
cd my-docker-app
```

Check the current location:

```bash
pwd
```

List the files:

```bash
ls
```

At this point, the directory is empty.

---

# Step 3: Create the HTML File

Create the HTML file:

```bash
touch index.html
```

Check the file:

```bash
ls
```

Project structure:

```text
my-docker-app/
└── index.html
```

---

# Step 4: Add HTML Content

Open the HTML file:

```bash
nano index.html
```

Add the following:

```html
<!DOCTYPE html>
<html>
<head>
    <title>My Docker App</title>
</head>

<body>

    <h1>Hello from Docker!</h1>

    <p>This HTML page is running inside a Docker container.</p>

</body>
</html>
```

Save the file.

Check the content:

```bash
cat index.html
```

---

# Step 5: Create the Dockerfile

Create the Dockerfile:

```bash
touch Dockerfile
```

Check the files:

```bash
ls
```

Project structure:

```text
my-docker-app/
│
├── Dockerfile
└── index.html
```

---

# Step 6: Add Instructions to the Dockerfile

Open the Dockerfile:

```bash
nano Dockerfile
```

Add:

```dockerfile
FROM nginx

COPY index.html /usr/share/nginx/html/

EXPOSE 80
```

Save the file.

Check the Dockerfile:

```bash
cat Dockerfile
```

---

# Step 7: Understand the Dockerfile

### FROM

```dockerfile
FROM nginx
```

Uses the Nginx image as the base image.

### COPY

```dockerfile
COPY index.html /usr/share/nginx/html/
```

Copies our HTML file into the Nginx web directory.

### EXPOSE

```dockerfile
EXPOSE 80
```

Documents that Nginx uses port `80` inside the container.

---

# Step 8: Build the Docker Image

Build the Docker Image:

```bash
docker build -t my-docker-app .
```

Explanation:

```text
docker build
    ↓
Build an Image

-t my-docker-app
    ↓
Image Name

.
    ↓
Current Directory / Build Context
```

The `.` tells Docker to use the current directory as the build context.

---

# Step 9: Check the Docker Image

List Docker Images:

```bash
docker images
```

You should see something similar to:

```text
REPOSITORY      TAG       IMAGE ID
my-docker-app   latest    xxxxxxxx
```

The image name is:

```text
my-docker-app:latest
```

---

# Step 10: Build an Image with a Tag

We can also create an image with a specific tag:

```bash
docker build -t my-docker-app:v1 .
```

Check the images:

```bash
docker images
```

You may see:

```text
REPOSITORY      TAG       IMAGE ID
my-docker-app   latest    xxxxxxxx
my-docker-app   v1        xxxxxxxx
```

---

# Step 11: Run the Docker Container

Create and start a container:

```bash
docker run -d --name my-docker-container -p 8080:80 my-docker-app
```

Explanation:

```text
-d
→ Run in detached mode

--name my-docker-container
→ Give the container a name

-p 8080:80
→ Map host port 8080 to container port 80

my-docker-app
→ Docker Image used to create the container
```

---

# Step 12: Understand Port Mapping

We used:

```bash
-p 8080:80
```

This means:

```text
Host / EC2 Port 8080
        ↓
Container Port 80
```

Nginx runs on port `80` inside the container.

Docker maps that port to port `8080` on the EC2 host.

---

# Step 13: Check the Running Container

Run:

```bash
docker ps
```

You should see something similar to:

```text
CONTAINER ID   IMAGE          STATUS       PORTS
xxxxxxxx       my-docker-app  Up ...       0.0.0.0:8080->80/tcp
```

The important part is:

```text
8080 → 80
```

---

# Step 14: Check All Containers

To see both running and stopped containers:

```bash
docker ps -a
```

Remember:

```text
docker ps
    ↓
Running Containers

docker ps -a
    ↓
All Containers
```

---

# Step 15: Check Container Logs

Check the container logs:

```bash
docker logs my-docker-container
```

To continuously follow the logs:

```bash
docker logs -f my-docker-container
```

Press:

```text
Ctrl + C
```

to stop following the logs.

---

# Step 16: Configure AWS EC2 Security Group

Because the Docker container is running on an AWS EC2 instance, allow port `8080` in the EC2 Security Group.

Add an inbound rule:

```text
Type: Custom TCP
Port: 8080
Source: Your IP / Required Source
```

The request flow is:

```text
Internet
   ↓
EC2 Security Group
   ↓
Port 8080
   ↓
Docker Container
   ↓
Nginx Port 80
```

---

# Step 17: Access the Application

Find the Public IPv4 Address of your EC2 instance.

Open the following in your browser:

```text
http://<EC2-PUBLIC-IP>:8080
```

Example:

```text
http://12.34.56.78:8080
```

You should see:

```text
Hello from Docker!

This HTML page is running inside a Docker container.
```

The request flow is:

```text
Browser
   ↓
EC2 Public IP
   ↓
Port 8080
   ↓
Docker Container
   ↓
Nginx Port 80
   ↓
index.html
```

---

# Step 18: Stop the Container

Stop the running container:

```bash
docker stop my-docker-container
```

Check:

```bash
docker ps
```

The container will no longer appear in the running container list.

---

# Step 19: Check the Stopped Container

The container still exists even though it is stopped.

Run:

```bash
docker ps -a
```

You should see:

```text
my-docker-container
```

with a status similar to:

```text
Exited
```

> Stopping a container does not remove it.

---

# Step 20: Start the Container Again

Start the stopped container:

```bash
docker start my-docker-container
```

Check:

```bash
docker ps
```

The container should now be running again.

The application should again be available at:

```text
http://<EC2-PUBLIC-IP>:8080
```

---

# Step 21: Restart the Container

Restart the container:

```bash
docker restart my-docker-container
```

Check:

```bash
docker ps
```

The container should be running again.

---

# Step 22: Stop the Container

Before removing the container, stop it:

```bash
docker stop my-docker-container
```

Check:

```bash
docker ps
```

---

# Step 23: Remove the Container

Remove the stopped container:

```bash
docker rm my-docker-container
```

Check:

```bash
docker ps -a
```

The container should no longer be listed.

---

# Step 24: Remove the Docker Image

Remove the image:

```bash
docker rmi my-docker-app
```

Check:

```bash
docker images
```

The `my-docker-app` image should no longer appear.

If you created the `v1` image separately, remove it using:

```bash
docker rmi my-docker-app:v1
```

---

# 🔄 Complete Workflow

```text
Create Project Directory
          ↓
Create index.html
          ↓
Create Dockerfile
          ↓
Add Dockerfile Instructions
          ↓
docker build
          ↓
Docker Image
          ↓
docker run
          ↓
Docker Container
          ↓
Nginx
          ↓
Port 8080
          ↓
Browser
```

---

# 🧠 Important Commands

| Command | Purpose |
|---|---|
| `docker build` | Build a Docker Image |
| `docker images` | List Docker Images |
| `docker run` | Create and start a Container |
| `docker ps` | List running Containers |
| `docker ps -a` | List all Containers |
| `docker logs` | View Container logs |
| `docker stop` | Stop a Container |
| `docker start` | Start a stopped Container |
| `docker restart` | Restart a Container |
| `docker rm` | Remove a Container |
| `docker rmi` | Remove an Image |

---

# 📁 Final Project Structure

```text
my-docker-app/
│
├── Dockerfile
└── index.html
```

Dockerfile:

```dockerfile
FROM nginx

COPY index.html /usr/share/nginx/html/

EXPOSE 80
```

---

# 🎯 Key Takeaway

The basic Docker workflow is:

```text
Dockerfile
     ↓
docker build
     ↓
Docker Image
     ↓
docker run
     ↓
Docker Container
     ↓
Running Application
```

Remember:

```text
docker build → Image

docker run → Container

docker ps → Running Containers

docker images → Docker Images
```

---

# 📚 Concepts Covered

- Docker Build
- Docker Run
- Dockerfile
- Docker Image
- Docker Container
- Nginx
- Image Tags
- Port Mapping
- Build Context
- Container Logs
- Container Lifecycle
- AWS EC2 Security Group
- Running an application inside a Docker Container
- Accessing a Docker application from a browser
- Stopping a Container
- Starting a Container
- Restarting a Container
- Removing a Container
- Removing a Docker Image
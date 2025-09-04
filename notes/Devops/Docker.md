## What is docker?

Docker is and open platform for developing shipping and running application by separating it from our infrastructure, It provides the ability to package and run and application in a loosely isolated environment called container,

- we can run many containers on single host
- containers are light weight and contains everything to run the application
- containers can be shared and same container runs in the same way
- Docker containers can run on a developer's local laptop, on physical or virtual machines in a data center, on cloud providers, or in a mixture of environments.

## Docker Architecture

Docker uses a client server architecture
![docker architecture diagram](image.png)

The installed docker engine has three 3 main pieces

- **Docker Client (CLI)** → the `docker` command line tool you run in terminal.
- **Docker Daemon** → a background service (`dockerd`) that does all the actual work (building, running, managing containers).
- **REST API** → REST API used by the client to talk to the daemon.

👉 So on your system, when you type `docker run`, you’re **using the client**, which then calls the **daemon** to do the heavy lifting.

### 🔹 Now about Linux

- On **Linux**, you can install the **Docker Engine** directly (which gives you both client + daemon).
- On **Windows/Mac**, you cannot install the daemon natively, so Docker Desktop provides a hidden Linux VM where the daemon runs.

### **What is the Daemon?**

- The **daemon (`dockerd`)** is a process running in the background on the host.
- It:
  - listens for Docker Api request
  - manages Docker objects such as images containers, networks volumes
  - talks to registries to pull/push images.
  - can also communicate with other deamons to manage docker services

Think of it as the **server** inside your machine that the client talks to.

### **What is the Client?**

- The Client is the tool that lets us command the deamon.
  - It sends commands to deamon
  - docker commands uses the docker api
  - docker command can communicate with more than one deamon

---

📍
On a normal developer laptop:

- Both Client + Daemon live on the same machine.
- That machine is called the Docker Host.
- The client talks to the daemon over a REST API
- Client and deamon can be on different system

### 4. **What is the Registry?**

A Docker registry stores Docker images. Docker Hub is a public registry that anyone can use

- The **registry is NOT part of Docker Engine** you install on your system.
- It’s a separate service (like **Docker Hub**, AWS ECR, GCP Artifact Registry, or a private registry you set up).
- The registry is just a **store for images**, like GitHub is for code.
- Your Docker Daemon talks to the registry when you:

  - `docker pull` → download an image.
  - `docker push` → upload an image.

### 4. **Docker Objects?**

#### Image

An image is a read-only template with instructions for creating a docker container

- **An image = a read-only snapshot of a filesystem + metadata** (how to start the app).
- It’s built in **layers**. Each layer is the result of one Dockerfile instruction.
- Images are **content-addressed** (layers are identified by a hash), so identical layers are reused and never re-downloaded twice.

Think “Photoshop layers” but for files: the final image you pull is a _stack_ of layers merged together.
We can create our own images or can use those created by others published in a registry, To build own image we can create docker file as

- Your Dockerfile starts with `FROM <base>`, e.g. `FROM ubuntu:22.04`.
- Every subsequent instruction **adds a new layer** on top of that base.
- Example stack (top = newest):

  1. `CMD ["apache2","-D","FOREGROUND"]`
  2. `EXPOSE 80`
  3. `COPY ./site /var/www/html`
  4. `RUN apt-get update && apt-get install -y apache2`
  5. `FROM ubuntu:22.04`

If you later change `COPY ./site ...`, only layer #3 (and anything after it) needs rebuilding/re-pushing.

#### Container

A container is a runnable instance of an image,

- We can create, start, stop, move, or delete a container using docker API.
- We can connect a container to

# Building a container image

In this section, we’ll walk through how to create a custom container image for a local Mosquitto broker using Podman. This includes setting up authentication and access control via `passwords.txt`, `acl.txt`, and `mosquitto.conf`, and building a cross-platform container image. Wherever you see `podman` instructions, you can also use `docker` instead.

---

## What is a Containerfile?

A `Containerfile` (also known as a `Dockerfile` in Docker contexts) is a script that contains a list of instructions used to build a container image. It defines:

- The **base image** to start from.
- Files and configurations to add or copy.
- System setup steps (e.g., creating directories, setting permissions).
- The **entrypoint or command** to run when the container starts.

Each instruction is executed in sequence, and the result is a lightweight, portable, and reproducible image.

---

## Build the Mosquitto container image

First, prepare your folder structure. It should look like this:

```
mosquitto
├── Containerfile
├── acl.txt
├── generatePasswordFile.py
├── mosquitto.conf
```

## Containerfile explained

Check out the `Containerfile`

```Dockerfile
FROM eclipse-mosquitto:latest

ARG WORKDIR=/home/mosquitto

WORKDIR $WORKDIR

RUN mkdir ${WORKDIR}/config ${WORKDIR}/acl ${WORKDIR}/passwords

RUN chown -R mosquitto:mosquitto ${WORKDIR}

USER mosquitto

EXPOSE 1883
EXPOSE 8000

CMD ["/usr/sbin/mosquitto", "-c", "/home/mosquitto/config/mosquitto.conf"]
```

### Key instructions

- `FROM eclipse-mosquitto:latest`: Uses the official Mosquitto base image.
- `WORKDIR`: Sets the working directory inside the container.
- `RUN mkdir ...`: Creates folders to organize your configs, ACLs, and passwords.
- `USER mosquitto`: Ensures the broker runs as a non-root user.
- `EXPOSE`: Declares which ports are used (1883 for MQTT, 8000 for WebSocket).
- `CMD`: Specifies the command that runs when the container starts, pointing to your custom configuration file.

The [mosquitto.conf](./files/mosquitto.conf) configures how the broker works

### Step 1: Configure access and credentials

- Edit `acl.txt` to define topic access controls.
- Use `generatePasswordFile.py` to generate a `passwords.txt` file for authentication.

Create the missing [Python script](./files/generatePasswords.py) and name it `generatePasswordFile.py`. Feel free to modify the users_and_passwords object to your liking.

Run the script:

```
python generatePasswordFile.py
```

After running the script, your folder structure should now include `passwords.txt`:

```
mosquitto
├── Containerfile
├── acl.txt
├── generatePasswordFile.py
├── mosquitto.conf
├── passwords.txt
```

---

### Step 2: Build the image

Use the following command to build your container image:

```
podman build --jobs 2 --platform linux/amd64,linux/arm64 --manifest mosquitto-custom:1.0 --layers=false /path/to/Containerfile
```

- `--platform linux/amd64,linux/arm64` ensures the image runs on both standard x86 and ARM-based systems.
- This is important because **newer MacBooks use Apple Silicon (ARM64)** and may not be compatible with images built only for AMD64.

Replace `/path/to/Containerfile` with the actual path to your `mosquitto` folder.

---

### OPTIONAL: Test locally

To test your image locally, run:

```
podman run -d -v ./:/home/mosquitto/passwords:ro \
              -v ./:/home/mosquitto/acl:ro \
              -v ./:/home/mosquitto/config:ro \
              -p 1883:1883 --name mosquitto mosquitto-custom:1.0
```

- This command mounts the local directory into the container so it can read `passwords.txt`, `acl.txt`, and `mosquitto.conf`.
- The `-p 1883:1883` flag makes the MQTT broker accessible on your local port 1883.

---

With this setup, you’ll have a fully functional, portable Mosquitto broker container, secured and ready for use in development environments.

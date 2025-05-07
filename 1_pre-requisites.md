# Prerequisites

Before starting the course, please ensure you have the following tools installed on your machine. This guide provides installation instructions for **both macOS and Windows** platforms.

## 1. Podman or Docker

You only need **one** of these container runtimes. Container runtimes allow you to build, run and publish container images locally.

### Podman

Podman is a daemonless and rootless alternative to Docker, which enhances its security. Podman is fully compatible with the docker CLI commands and OCI standards.

#### macOS

```
brew install podman
podman machine init
podman machine start
```

#### Windows

Download and install from the official site: [https://podman.io/getting-started/installation](https://podman.io/getting-started/installation)

### Docker

#### macOS & Windows

Download Docker Desktop from: [https://www.docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop)

> **Note:** Docker Desktop requires a free Docker account.

## 2. `kubectl` (Kubernetes CLI)

To interact with the Kuberentes API, you'll need kubectl.

### macOS

```
brew install kubectl
```

### Windows

Download manually from: [https://kubernetes.io/docs/tasks/tools/](https://kubernetes.io/docs/tasks/tools/)

## 3. IBM Cloud CLI

Download and install from: [https://github.com/IBM-Cloud/ibm-cloud-cli-release/releases/](https://github.com/IBM-Cloud/ibm-cloud-cli-release/releases/)

After installation, verify with:

```
ibmcloud version
```

Optionally, you can also enable the autocompletion:<br />
[https://cloud.ibm.com/docs/cli?topic=cli-shell-autocomplete](https://cloud.ibm.com/docs/cli?topic=cli-shell-autocomplete)

## 4. OPTIONAL: Bruno (API test client)

Bruno is a lightweight API client, ideal for working with REST APIs. In later chapters you'll interact with the IBM Cloudant Database APIs. Bruno will come in handy to test the APIs before implementing them in your code.

Download the latest release for your OS :  
[https://www.usebruno.com/downloads](https://www.usebruno.com/downloads)

Install the version appropriate for your system.

## 5. Python (version 3.8 or higher)

### macOS

```
brew install python
```

### Windows

Download and install from: [https://www.python.org/downloads/windows/](https://www.python.org/downloads/windows/)

> **Note:** During installation, make sure to select **"Add Python to PATH"**.

## 6. Installing `mosquitto_passwd`

The `mosquitto_passwd` utility is used to generate password files for authenticating MQTT clients.

### macOS

Install via Homebrew:

```
brew install mosquitto
```

This will install both the mosquitto broker and the `mosquitto_passwd` tool. You can verify the installation with:

```
mosquitto_passwd -h
```

### Windows

1. Download the mosquitto installer from the official site:  
   [https://mosquitto.org/download/](https://mosquitto.org/download/)

2. During installation, make sure to check the option to install the **utilities**.

3. After installation, add the mosquitto `bin` directory to your system `PATH` (if not done automatically).

You can now use `mosquitto_passwd` from the Command Prompt:

```
mosquitto_passwd -h
```

## 7. Code editor (VS Code or any other)

Recommended is using [Visual Studio Code](https://code.visualstudio.com/) for this course but any other editor will also do the job.

### macOS & Windows

Download from: [https://code.visualstudio.com/Download](https://code.visualstudio.com/Download)

> You can use any other editor you're comfortable with.

## Verification checklist

After installation, you should be able to run the following commands in your terminal or command prompt:

```
podman --version       # or docker --version
kubectl version --client
ibmcloud version
python --version
```

Once everything is installed and working, you're ready to begin!

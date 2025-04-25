# Prerequisites Installation Guide

Before starting the course, please ensure you have the following tools installed on your machine. This guide provides installation instructions for **both macOS and Windows** platforms.

---

## 1. Podman or Docker

You only need **one** of these container runtimes.

### Podman

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

---

## 2. `kubectl` (Kubernetes CLI)

### macOS

```
brew install kubectl
```

### Windows

Download manually from: [https://kubernetes.io/docs/tasks/tools/](https://kubernetes.io/docs/tasks/tools/)

---

## 3. IBM Cloud CLI

Download and install from: [https://github.com/IBM-Cloud/ibm-cloud-cli-release/releases/](https://github.com/IBM-Cloud/ibm-cloud-cli-release/releases/)

After installation, verify with:

```
ibmcloud version
```

---

## 4. Bruno (API Test Client)

Bruno is a lightweight API client, ideal for working with REST APIs.

Download the latest release for your OS :  
[https://www.usebruno.com/downloads](https://www.usebruno.com/downloads)

Install the version appropriate for your system.

---

## 5. Python (Version 3.8 or higher)

### macOS

```
brew install python
```

### Windows

Download and install from: [https://www.python.org/downloads/windows/](https://www.python.org/downloads/windows/)

> **Note:** During installation, make sure to select **"Add Python to PATH"**.

---

## 6. Code Editor (VS Code or any other)

We recommend using [Visual Studio Code](https://code.visualstudio.com/) for this course.

### macOS & Windows

Download from: [https://code.visualstudio.com/Download](https://code.visualstudio.com/Download)

> You can use any other editor you're comfortable with.

---

## Verification Checklist

After installation, you should be able to run the following commands in your terminal or command prompt:

```
podman --version       # or docker --version
kubectl version --client
ibmcloud version
python --version
```

Make sure Bruno is installed and working, and that you can open your code editor.

Once everything is installed and working, you're ready to begin!

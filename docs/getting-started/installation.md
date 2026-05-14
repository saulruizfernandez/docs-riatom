# Installation

## 1. Install Docker

```bash
# Update and install dependencies
sudo apt update
sudo apt install -y ca-certificates curl gnupg

# Add the official Docker's GPG key
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

# Add the repository
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "$VERSION_CODENAME") stable" \
| sudo tee /etc/apt/sources.list.d/docker.list

# Install docker
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin

# Enable user to run docker commands without sudo
sudo usermod -aG docker $USER
newgrp docker
```

### Check the installation

```bash
docker --version
docker compose version
docker run hello-world
```

## 2. Clone the code repositories

```bash
mkdir ria-tom-project
cd ria-tom-project
git clone <repo-url-ria-tom>
git clone <repo-url-scheduler>
```

## 3. Create .env

### Generate a secret key

```bash
python3 -c "import secrets; print(secrets.token_urlsafe(50))"
```

### Create the file .env

```bash
cd ria-tom
nano .env
```

And paste the following code:

```bash
SECRET_KEY=<your-secret-key>
# SECURITY WARNING: don't run with debug turned on in production!
DEBUG=False
ALLOWED_HOSTS=localhost,127.0.0.1,<ip-of-your-machine>
```

!!! note

    For the Canary Institute of Astrophysics, you need to add these extra lines to your .env:
    ```bash
    # KEYCLOAK OIDC CONFIGURATION (IAC-IdP)
    OIDC_RP_CLIENT_ID = ""
    OIDC_RP_CLIENT_SECRET = ""
    OIDC_RP_SIGN_ALGO = ""
    OIDC_OP_AUTHORIZATION_ENDPOINT = ""
    OIDC_OP_TOKEN_ENDPOINT = ""
    OIDC_OP_USER_ENDPOINT = ""
    OIDC_OP_JWKS_ENDPOINT = ""
    ```


## 4. Build the container and run the project

From the `ria-tom/` directory:

```bash
docker compose up --build
```

And finally you will find the project running at <http://localhost:80>

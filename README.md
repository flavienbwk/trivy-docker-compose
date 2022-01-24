# Trivy docker-compose

Deployment-ready docker compose configuration and instructions to use Trivy on your infrastructure and CIs.

## Installation

### Download latest vulnerabilities

As [stated in the official documentation](https://aquasecurity.github.io/trivy/v0.22.0/advanced/air-gap/), download the latest vulnerability list tarball from Trivy :

```bash
wget https://github.com/aquasecurity/trivy-db/releases/latest/download/trivy-offline.db.tgz
tar xvf trivy-offline.db.tgz -C ./trivy-data/
```

### Run Trivy server

```bash
docker-compose up -d
```

## Run scan (local)

```bash
# Install client
wget https://github.com/aquasecurity/trivy/releases/download/v0.22.0/trivy_0.22.0_Linux-64bit.deb
sudo dpkg -i trivy_0.22.0_Linux-64bit.deb

# Run
trivy client --offline-scan --remote http://172.17.0.1:4030 "$FULL_IMAGE_NAME"
```

- Image avec vulnérabilité : `node:10-alpine`
- Image sans vulnérabilité : `alpine:3.12`

## Run scan (CI)

Trivy can be [integrated to a GitLab CI](https://aquasecurity.github.io/trivy/v0.22.0/advanced/integrations/gitlab-ci/).

### GitLab CE

1. Download Trivy client binaries

    ```bash
    wget https://github.com/aquasecurity/trivy/releases/download/v0.22.0/trivy_0.22.0_Linux-64bit.tar.gz
    ```

2. In a `docker:dind` image CI run

    ```bash
    mkdir trivy_bin && tar xf trivy_0.22.0_Linux-64bit.tar.gz -C trivy_bin/
    ./trivy_bin/trivy
    rm -r trivy_bin

    trivy --exit-code 1 --cache-dir .trivycache/ --severity CRITICAL --no-progress "$FULL_IMAGE_NAME"
    ```

    This command will make the container exit 1 on CRITICAL vulnerabillity found.

### GitLab EE

Check the [template from the official documentation](https://aquasecurity.github.io/trivy/v0.22.0/advanced/integrations/gitlab-ci/) so you can benefit of a nice report formating.

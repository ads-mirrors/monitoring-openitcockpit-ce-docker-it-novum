# openITCOCKPIT-CE-docker

This repository contains the necessary files to run openITCOCKPIT Community Edition as a container.

It requires Docker Compose or a compatible alternative.

Please see the official documentation for more information: https://docs.openitcockpit.io/en/installation/docker

## Prerequisites
Before running openITCOCKPIT Community Edition with Docker, make sure you have the following prerequisites installed on your system:
- Docker: [Install Docker](https://docs.docker.com/get-docker/)
- Docker Compose: [Install Docker Compose](https://docs.docker.com/compose/install/)

## Getting Started
To get started with openITCOCKPIT Community Edition using Docker, follow these steps:

1. Clone this repository to your local machine:
   ```bash
   git clone https://github.com/openITCOCKPIT/openITCOCKPIT-ce-docker.git
   ```

2. Navigate to the cloned repository:
   ```bash
   cd openITCOCKPIT-ce-docker
   ```

3. Run the Docker Compose command to start openITCOCKPIT:
   ```bash
   docker compose up -d
   ```

4. Wait for the containers to start and initialize the application.

5. Once the containers are up and running, you can access openITCOCKPIT Community Edition by opening a web browser and entering the following URL:
   ```
   https://localhost
   ```

6. Follow the on-screen instructions to complete the initial setup of openITCOCKPIT.

## Configuration
The configuration for openITCOCKPIT Community Edition can be modified by editing the `compose.yml` file in the repository. You can adjust settings such as database credentials, ports, and volumes according to your requirements.

Most settings can be adjusted through environment variables. Please see the file `openitcockpit.env`, which contains all available environment variables with default values.

By default, openITCOCKPIT Community Edition will expose these ports:

 - 80 (HTTP)
 - 443 (HTTPS)

The default credentials are:

- E-Mail `user@example.org`
- Password: `asdf12`

You can change the default credentials **before the first start of the docker container** through the file `openitcockpit.env`:
```sh
OITC_ADMIN_EMAIL=user@example.org
OITC_ADMIN_PASSWORD=asdf12
OITC_ADMIN_FIRSTNAME=John
OITC_ADMIN_LASTNAME=Doe
```

**If you have not changed the default credentials, you can still change the password or [create a new user](https://docs.openitcockpit.io/en/configuration/usermanagement/) afterwords.**

## Updating openITCOCKPIT
To update openITCOCKPIT Community Edition to the latest version, follow these steps:

1. Pull the latest changes from the repository:
   ```bash
   git pull origin main
   ```

2. Stop and remove the running containers:
   ```bash
   docker compose down
   ```

3. Run the Docker Compose command again to start the updated version:
   ```bash
   docker compose up --pull always -d
   ```

## Setup TLS Certificates
By default, openITCOCKPIT will generate and use a self-signed certificate. It is also possible to use own certificates like Let’s Encrypt for example.

First, you have to change the path to the certificate files through the file `openitcockpit.env`:
```
OITC_NGINX_SSL_CERTIFICATE=/tmp/Lets_Encrypt/cert.crt
OITC_NGINX_SSL_CERTIFICATE_KEY=/tmp/Lets_Encrypt/private.key
```

In the next step you need to mount the certificate files into the container. You can do this by adding a bind-mount for the `openitcockpit` container into the `compose.yml`

Short example:
```yml
    volumes:
      - /host/ssl/Lets_Encrypt:/tmp/Lets_Encrypt:ro                           # Pass Lets Encrypt Certificate
```

Full example:
```yml
  openitcockpit:
    image: openitcockpit/openitcockpit-ce:latest                              # Community Edition of openITCOCKPIT
    init: true
    env_file:
     - openitcockpit.env
    ports:
      - "80:80"       # HTTP
      - "443:443"     # HTTPS
    networks:
      - oitc-backend
    volumes:
      - naemon-config:/opt/openitc/nagios/etc/config                          # Configuration files related to Naemon
      - naemon-var:/opt/openitc/nagios/var                                    # The status.dat is required for the recurring downtimes cronjob
      - oitc-frontend-src:/opt/openitc/src_sharing/frontend                   # Frontend of openITCOCKPIT to be shared with mod_gearman container so that the container can execute the EVC plugin
      - oitc-webroot:/opt/openitc/frontend/webroot                            # Webroot of openITCOCKPIT to keep images and share files with puppeteer
      - oitc-maps:/opt/openitc/frontend/plugins/MapModule/webroot/img         # Images of the MapModule
      - oitc-agent-cert:/opt/openitc/agent                                    # TLS certificats of the openITCOCKPIT Monitoring Agent
      - oitc-agent-etc:/opt/openitc/receiver/etc                              # Configuration for the openITCOCKPIT Monitoring Agent Check Reciver
      - oitc-var:/opt/openitc/var                                             # A safe harbor to store .lock files
      - oitc-backups:/opt/openitc/nagios/backup                               # Automaticaly generated MySQL dumps
      #- oitc-import:/opt/openitc/frontend/plugins/ImportModule/webroot/files # Uplaoded files of the ImportModule
      - oitc-styles:/opt/openitc/frontend/plugins/DesignModule/webroot/css    # Custom styles of the DesignModule
      - checkmk-agents:/opt/openitc/check_mk/agents:ro
      - checkmk-etc:/opt/openitc/check_mk/etc/check_mk
      - checkmk-var:/opt/openitc/check_mk/var/check_mk
      - /host/ssl/Lets_Encrypt:/tmp/Lets_Encrypt:ro                           # Pass Lets Encrypt Certificate
```


## Troubleshooting
If you encounter any issues or have questions regarding openITCOCKPIT Community Edition with Docker, please refer to the [official documentation](https://docs.openitcockpit.io/en/installation/docker).

## Contributing
Contributions are welcome! If you find any bugs or have suggestions for improvements, please open an issue or submit a pull request in this repository.

Bugs or issues related to openITCOCKPIT itself should be created in the corresponding repository: https://github.com/openITCOCKPIT/openITCOCKPIT


## Support
For any further assistance or support, please reach out to the [openITCOCKPIT Community](https://openitcockpit.io/community/).

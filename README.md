
# Moodle Traefik Deployment

Welcome to the **Moodle Traefik Deployment** project. This repository provides a streamlined setup for deploying a Moodle application using Docker Compose with Traefik as a reverse proxy. Follow the instructions below to get your Moodle instance up and running.

## Table of Contents

- [Prerequisites](#prerequisites)
- [Project Structure](#project-structure)
- [Configuration](#configuration)
  - [Environment Variables](#environment-variables)
- [Deployment](#deployment)
  - [Deploying Traefik](#deploying-traefik)
  - [Deploying Moodle](#deploying-moodle)
- [Accessing Moodle](#accessing-moodle)
- [Troubleshooting](#troubleshooting)

## Prerequisites

Before you begin, ensure you have the following installed on your system:

- [Docker](https://docs.docker.com/get-docker/) (version 20.10 or higher)
- [Docker Compose](https://docs.docker.com/compose/install/) (version 1.29 or higher)
- [Git](https://git-scm.com/downloads)

## Project Structure

```
moodle_traefik/
├── traefik/
│   ├── compose.traefik.yaml
│   ├── compose.traefik-ssl.yaml
│   └── traefik.env
├── moodletmplate.yaml
├── moodleenv.example
└── README.md
```

- **traefik/**: Contains Traefik configuration files.
- **moodletmplate.yaml**: Docker Compose template for Moodle and PostgreSQL services.
- **moodleenv.example**: Example environment variables file for Moodle deployment.

## Configuration

## Environment Variables
### Moodle Environment Variables

1. **MOODLE_NETWORK_NAME**
   - **Description**: Name of the Docker network used for Moodle services.
   - **Example**: `moodle_network_customer1`

2. **ROUTER**
   - **Description**: Router name for Traefik to configure routing rules.
   - **Example**: `moodle-customer1`

3. **DB_VOLUME_NAME**
   - **Description**: Name of the Docker volume for PostgreSQL database data storage.
   - **Example**: `db_data_customer1`

4. **MOODLE_DATA_VOLUME_NAME**
   - **Description**: Name of the Docker volume for Moodle data storage.
   - **Example**: `moodle_data_customer1`

5. **MOODLE_SOURCE_VOLUME_NAME**
   - **Description**: Name of the Docker volume for Moodle source code.
   - **Example**: `moodlesourcecode_data_customer1`

6. **POSTGRES_USER**
   - **Description**: Username for the PostgreSQL database.
   - **Example**: `moodleuser1`

7. **POSTGRES_PASSWORD**
   - **Description**: Password for the PostgreSQL database user.
   - **Example**: `customer1password`

8. **POSTGRES_DB**
   - **Description**: Name of the PostgreSQL database.
   - **Example**: `moodle_customer1`

9. **MOODLE_DB_NAME**
   - **Description**: Name of the Moodle database.
   - **Example**: `moodle_customer1`

10. **MOODLE_DB_USER**
    - **Description**: Username for the Moodle database connection.
    - **Example**: `moodleuser1`

11. **MOODLE_DB_PASSWORD**
    - **Description**: Password for the Moodle database connection.
    - **Example**: `customer1password`

12. **MOODLE_URL**
    - **Description**: Public URL for accessing the Moodle instance.
    - **Example**: `moodleinst.mosalam.com`

*Ensure all environment variables are set correctly before deploying the application.*

   Create a copy of the example environment file and customize it with your settings:

   ```bash
   cp moodleenv.example moodle-example.env
   ```

   Open `moodle-example.env` and set the appropriate values, for example:

   ```env
   MOODLE_NETWORK_NAME=moodle_network_customer1
   ROUTER=moodle-customer1
   DB_VOLUME_NAME=db_data_customer1
   MOODLE_DATA_VOLUME_NAME=moodle_data_customer1
   MOODLE_SOURCE_VOLUME_NAME=moodlesourcecode_data_customer1
   POSTGRES_USER=moodleuser1
   POSTGRES_PASSWORD=customer1password
   POSTGRES_DB=moodle_customer1
   MOODLE_DB_NAME=moodle_customer1
   MOODLE_DB_USER=moodleuser1
   MOODLE_DB_PASSWORD=customer1password
   MOODLE_URL=moodleinst.mosalam.com
   ```

---

### Traefik Environment Variables

1. **TRAEFIK_DOMAIN**
   - **Description**: Domain name for accessing the Traefik dashboard.
   - **Example**: `traefik.example.com`

2. **EMAIL**
   - **Description**: Email address for Let's Encrypt SSL certificate notifications.
   - **Example**: `admin@example.com`

3. **HASHED_PASSWORD**
   - **Description**: Hashed password for accessing the Traefik dashboard.
   - **Example**: (Replace `changeit` with the plaintext password and `openssl` will generate it)



   Create and configure the Traefik environment file:

   ```bash
   echo 'TRAEFIK_DOMAIN=traefik.example.com' > ./traefik/traefik.env
   echo 'EMAIL=admin@example.com' >> ./traefik/traefik.env
   echo 'HASHED_PASSWORD='$(openssl passwd -apr1 changeit | sed -e s/\$/\$\$/g) >> ./traefik/traefik.env
   ```


## Deployment

### Deploying Traefik

If Traefik is not already deployed, follow these steps:

1. **Navigate to the Traefik Directory**

   ```bash
   cd traefik
   ```

2. **Create the Traefik Environment File**

   ```bash
   echo 'TRAEFIK_DOMAIN=traefik.example.com' > ./traefik.env
   echo 'EMAIL=admin@example.com' >> ./traefik.env
   echo 'HASHED_PASSWORD='$(openssl passwd -apr1 changeit | sed -e s/\$/\$\$/g) >> ./traefik.env
   ```

   > **Note:** Replace `traefik.example.com` and `admin@example.com` with your actual domain and email.

3. **Deploy Traefik Using Docker Compose**

   ```bash
   docker compose --project-name traefik --env-file ./traefik.env  -f ./compose.traefik.yaml  -f ./compose.traefik-ssl.yaml up -d
   ```

   This command sets up Traefik with SSL support and starts the services in detached mode.

### Deploying Moodle

Once Traefik is up and running, proceed to deploy the Moodle application.

1. **Prepare the Moodle Environment File**

   ```bash
   cp moodleenv.example moodle-example.env
   ```

   Edit `moodle-example.env` with your specific variables as shown in the [Configuration](#configuration) section.

2. **Generate the Moodle Docker Compose Configuration**

   ```bash
   docker compose --project-name moodle-example --env-file moodle-example.env -f moodletmplate.yaml config > moodle-example.yaml
   ```

   This command generates a `moodle-example.yaml` file with your customized settings.

3. **Deploy Moodle Using Docker Compose**

   ```bash
   docker compose -f moodle-example.yaml up -d
   ```

   This will start the Moodle and PostgreSQL services in detached mode.

## Accessing Moodle

Once deployed, access your Moodle instance by navigating to `https://moodleinst.mosalam.com` (replace with your actual `MOODLE_URL`) in your web browser.


## Troubleshooting

- **Traefik Dashboard Access Issues**

  Ensure that Traefik is correctly deployed and accessible via the `TRAEFIK_DOMAIN`. Check Traefik logs for any errors.

- **Moodle Connectivity Issues**

  Verify that the Moodle service can communicate with the PostgreSQL database. Check environment variables and Docker networks.

- **Certificate Issues**

  Ensure that your domain's DNS records point to the server where Traefik is running and that ports `80` and `443` are open.



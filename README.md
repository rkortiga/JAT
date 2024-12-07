# Setup

This repository provides a Docker Compose configuration to run the **Job Application Tracker** application, which consists of three services:
- **Backend**: ASP.NET Core Web API
- **Database**: Microsoft SQL Server
- **Frontend**: Angular

The Web API and Angular frontend are housed in separate repositories and included here as submodules for easy integration and orchestration using Docker.

## Prerequisites

Before setting up the environment, ensure you have the following installed:
- [Docker](https://www.docker.com/get-started)
- [Docker Compose](https://docs.docker.com/compose/install/)

## Cloning the Repository

1. Clone this repository, including its submodules:
   ```bash
   git clone --recurse-submodules https://github.com/rkortiga/JobApplicationTracker-Docker.git
   ```

2. After cloning make sure the JobApplicationTracker-Docker project all its submodules are fully initialized, up-to-date, and consistent with the versions specified in the main repository, run this command.
   ```bash
   git submodule update --init --recursive
   ```

3. Navigate into the project directory:
   ```bash
   cd JobApplicationTracker-Docker
   ```

## Environment Setup

This project requires certain environment variables and settings to be configured. 

### 1. Create the `.env` file

The `.env` file is ignored from source control and must be created manually.

1. Create a `.env` file at the root of the repository.

2. Add the following variables in the `.env` file:

   ```plaintext
   ASPNETCORE_ENVIRONMENT=Development
   SA_PASSWORD=<YOUR_DB_PASSWORD>
   ACCEPT_EULA=Y
   DB_PORT=1433
   ```

   - **ASPNETCORE_ENVIRONMENT**: Specifies the environment for ASP.NET Core.
   - **SA_PASSWORD**: The password for the SQL Server admin user.
   - **ACCEPT_EULA**: Must be set to "Y" to accept the SQL Server End User License Agreement.
   - **DB_PORT**: Port on which SQL Server will be exposed.

### 2. Create the `appsettings.json` file for the Web API

The **appsettings.json** file for the Web API contains sensitive information such as connection strings and JWT signing keys, and is ignored from source control. You must create this file manually in the **JobApplicationTrackerAPI** directory.

#### Steps to create the `appsettings.json` file:

1. Navigate to the **JobApplicationTrackerAPI** directory:
   ```bash
   cd JobApplicationTrackerAPI
   ```

2. Create an `appsettings.json` file in the **JobApplicationTrackerAPI** folder.

3. Use the following template for the file:

   ```json
   {  
     "Logging": {  
       "LogLevel": {  
         "Default": "Information",  
         "Microsoft.AspNetCore": "Warning"  
       }  
     },  
     "AllowedHosts": "*",  
     "ConnectionStrings": {  
       "JATConnectionString": "Server=<DATABASE_SERVER>,<PORT>;Database=<DATABASE_NAME>;Trusted_Connection=false;MultipleActiveResultSets=true;User Id=<DB_USER>;Password=<DB_PASSWORD>;Encrypt=false;"  
     },  
     "Jwt": {  
       "Issuer": "<JWT_ISSUER>",  
       "Audience": "<JWT_AUDIENCE>",  
       "SigningKey": "<JWT_SIGNING_KEY>"  
     }  
   }
   ```

4. Replace the placeholder values (`<DATABASE_SERVER>`, `<DB_USER>`, `<DB_PASSWORD>`, etc.) with the appropriate values for your environment.

## Running the Application

Once the `.env` and `appsettings.json` files are set up, you can bring up the application stack with Docker Compose:

```bash
docker-compose up --build
```

### Services and Ports:

- **Backend (ASP.NET Core Web API)**:  
  Accessible at `http://localhost:8080`  
  Swagger UI at `http://localhost:8081/swagger`

- **Frontend (Angular)**:  
  Accessible at `http://localhost:4200`

- **Database (MS SQL Server)**:  
  Accessible on `localhost:${DB_PORT}` (default: 1433)

## Stopping the Application

To stop and remove the containers, run:

```bash
docker-compose down
```

## Troubleshooting

- Ensure Docker and Docker Compose are installed and running properly.
- Make sure the ports (8080, 8081, 4200, and 1433) are not being used by other services on your machine.
- If changes are made to the codebase, rebuild the containers with:
  ```bash
  docker-compose up --build
  ```


## NOTE  

  - Before running Docker Compose, ensure that your local SQL Server instance is stopped. This prevents port conflicts with the SQL Server container that Docker Compose will run.  

    This can be done in the SQL Server Configuration Manager in your Windows host machine.  

  - Internal services (e.g., the API) will connect to the database using `jobapplicationtracker_db` as the hostname. Docker Compose sets up an internal network where containers can communicate using their service names as DNS hostnames. This is defined in the Docker Compose YAML file.    

    So the value of `<DATABASE_SERVER>` in the connection string should be `jobapplicationtracker_db` unless otherwise defined in the YAML file.  

  - Ensure the `apiBaseUrl` value in the `environment.ts` is set to `http://localhost:8080/api`. Docker Compose will expose the API on port 8080 for external access.

  - Use `sa` as the value for `User Id` and use the value from `<SA_PASSWORD from env file>` for `Password` in the connection string

  - After running Docker Compose successfully, you can access the SQL server in SSMS using the credentials

    ```bash
      Server Name: localhost
      Authentication: SQL Server Authentication
      Login: sa
      Password: <SA_PASSWORD from env file>
    ```
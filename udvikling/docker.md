# Docker

Det endte med at jeg gav op på at Containerize min applikation. Det var lykkedes for mig med en prototype,
men der findes ingen muligheder for at komme nemt fra start, eller noget dokumentation for hvordan at man gør.
Endnu en årsag til at jeg vil vælge en anden form for serverless frontend, hvis det skulle være et krav.

## Scaffold docker i .NET Web API

- Højreklik på server projektet

- klik på Docker support..

- Vælg dette

  - Container OS: Linux
  - Container Build Type: Dockerfile
  - Container Image Distro: Default (8)
  - Docker Build Context: ...

- Det scaffolder 'Dockerfile' i roden af folderen og ser sådan her ud:

```csharp
# See https://aka.ms/customizecontainer to learn how to customize your debug container and how Visual Studio uses this Dockerfile to build your images for faster debugging.

# This stage is used when running from VS in fast mode (Default for Debug configuration)
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
USER $APP_UID
WORKDIR /app
EXPOSE 8080
EXPOSE 8081

# This stage is used to build the service project
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src
COPY ["DogObedience.Server/DogObedience.Server.csproj", "DogObedience.Server/"]
COPY ["DogObedience.Client/DogObedience.Client.csproj", "DogObedience.Client/"]
COPY ["DogObedience.Shared/DogObedience.Shared.csproj", "DogObedience.Shared/"]
RUN dotnet restore "./DogObedience.Server/DogObedience.Server.csproj"
COPY . .
WORKDIR "/src/DogObedience.Server"
RUN dotnet build "./DogObedience.Server.csproj" -c $BUILD_CONFIGURATION -o /app/build

# This stage is used to publish the service project to be copied to the final stage
FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "./DogObedience.Server.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

# This stage is used in production or when running from VS in regular mode (Default when not using the Debug configuration)
FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "DogObedience.Server.dll"]
```

Ovenstående fil er ikke helt korrekt, da jeg gerne vil ***hoste*** mit Server og Client projekt hver for sig. Derfor skal de selfølgelig have hver deres container.
Client projektet fjernes og jeg saniterer det en smule, så det er nemmere at se hvad der sker.
Server og Shared projekterne beholdes.
Server, fordi at det er det vi gerne vil containorize, og Shared fordi at det bliver brugt i Server projektet og gerne skulle hostes sammen:

```csharp
# Use the ASP.NET Core runtime as the base image
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
WORKDIR /app
EXPOSE 8080

# Use the .NET SDK to build the project
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src

# Copy and restore dependencies
COPY ./DogObedience.Shared/ /src/DogObedience.Shared/
COPY ./DogObedience.Server/DogObedience.Server.csproj ./DogObedience.Server/
WORKDIR /src/DogObedience.Server
RUN dotnet restore "./DogObedience.Server.csproj"

# Copy the entire source and build
COPY ./DogObedience.Server/. ./
RUN dotnet build "./DogObedience.Server.csproj" -c $BUILD_CONFIGURATION -o /app/build

# Publish the app in a separate stage
FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "./DogObedience.Server.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

# Final stage: Run the application
FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "DogObedience.Server.dll"]
```

Nu er det tid til at containorize Client projeket:

1. Tilføj en fil i roden af klient projektet. Lav en .txt fil, giv det navnet ***Dockerfile*** og slet ***.txt*** efterfølgende
2. Tilføj en fil i roden af klient projektet. giv det navnet ***nginx.conf***

[nginx](https://nginx.org/en/) er en web server og den har vi brug for, for at kunne serve HTML, CSS og Java Script, i Standalone.
Hvis jeg ikke har nævnt det noget andet sted i bloggen, så er Blazor Standalone et serverless framework.
Det vil sige at den ikke er afhængig af en .NET server til at 'serve' HTML, CSS og Java Script. Derfor skal der en server til for at kunne 'serve' det.

```HTML
events { }
http {
   include mime.types;
   types {
      application/wasm wasm;
   }
   server {
      listen 80;
      index index.html;
      location / {
         root /usr/share/nginx/html;
         try_files $uri $uri/ /index.html =404;
      }
   }
}
```

Dockerfile i Client projektet:

```csharp
# Build Stage
FROM mcr.microsoft.com/dotnet/sdk:8.0-alpine AS build-env
WORKDIR /app

# Copy the client and shared .csproj files and restore dependencies
COPY ./DogObedience.Client/DogObedience.Client.csproj ./DogObedience.Client/
COPY ./DogObedience.Shared/DogObedience.Shared.csproj ./DogObedience.Shared/
RUN dotnet restore "./DogObedience.Client/DogObedience.Client.csproj"

# Copy the rest of the client and shared project files
COPY ./DogObedience.Client/ ./DogObedience.Client/
COPY ./DogObedience.Shared/ ./DogObedience.Shared/

# Publish the client project
WORKDIR /app/DogObedience.Client
RUN dotnet publish -c Release -o output

# Nginx Stage	
FROM nginx:alpine
WORKDIR /usr/share/nginx/html

# Copy the published output to the Nginx HTML folder
COPY --from=build-env /app/DogObedience.Client/output/wwwroot .

# Specify the path for nginx.conf from the context directory
COPY ./DogObedience.Client/nginx.conf /etc/nginx/nginx.conf

# Expose port 80 for the client
EXPOSE 80
```

Så er det tid til Docker-compose, så jeg kan orkestrerer containerne.

Højre klik på Server projektet => ***Add*** => ***Container Orchestrator Support*** => ***OK*** => ***OK***

Det giver 3 filer:

**.dockerignore**  
Den lader vi stå som den er

**launchSettings.json**
Så jeg kan trykke på den grønne pil i VS 2022 og spinne containerne op, uden at bruge CLI'en.

```csharp
{
  "profiles": {
    "Docker Compose": {
      "commandName": "DockerCompose",
      "commandVersion": "1.0",
      "serviceActions": {
        "fastrallyobedience.server": "StartDebugging"
      }
    }
  }
}
```

**Docker-compose**
Den fil der får det hele til at spille sammen, så man kun behøver at køre 1 fil for at få containerne op at spinde.

> [!IMPORTANT]
> **context .**  
> Servicene tager udgangspunkt i **root** af solution.
> Det tog mig pinligt lang tid at finde ud af hvorfor at jeg ikke kunne få det til at virke.
> Hvis ikke at man sætter udgangspunktet til **root* så skal man huske på at docker-compose.yml filen,
> i dette tilfælde er placeret inde i **docker-compose** folderen.

```csharp
services:
  DogObedience.server:
    build:
      context: .
      dockerfile: ./DogObedience.Server/Dockerfile
    ports:
      - "8080:8080" # HTTP
    depends_on:
      - sqlserver

  sqlserver:
    image: mcr.microsoft.com/mssql/server:2022-latest
    environment:
      MSSQL_SA_PASSWORD: "myStrong_Password123#"
      ACCEPT_EULA: "Y"
    ports:
      - "1433:1433"

  DogObedience.client:
    build:
      context: .
      dockerfile: ./DogObedience.Client/Dockerfile
    ports:
      - "80:80"
```

Client, Server og en SQLServer er de containere jeg gerne skal have op at køre.

Når jeg bruger kommandoen ***docker-compose up --build*** ser det umiddelbart ud til at fungere, men jeg har glemt en ting:

*Microsoft.Data.SqlClient.SqlException (0x80131904): A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible.*


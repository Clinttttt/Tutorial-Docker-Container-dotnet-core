

---

````markdown
# 🚀 Docker Setup for Tutorial1DockerSetup_dotnet (Blazor Server)

## 1. Go to your project folder
Open **PowerShell** and change into your Blazor Server project root:

```powershell
cd "C:\Users\Clint Villanueva\source\repos\Tutorial1DockerSetup_dotnet"
````

---

## 2. Create a Dockerfile

Inside `Tutorial1DockerSetup_dotnet`, create a file named **`Dockerfile`**:

```dockerfile
# ---- build stage ----
FROM mcr.microsoft.com/dotnet/sdk:7.0 AS build
WORKDIR /src

COPY ["Tutorial1DockerSetup_dotnet/Tutorial1DockerSetup_dotnet.csproj", "Tutorial1DockerSetup_dotnet/"]
RUN dotnet restore "Tutorial1DockerSetup_dotnet/Tutorial1DockerSetup_dotnet.csproj"

COPY . .
WORKDIR /src/Tutorial1DockerSetup_dotnet
RUN dotnet publish -c Release -o /app/publish

# ---- runtime stage ----
FROM mcr.microsoft.com/dotnet/aspnet:7.0 AS runtime
WORKDIR /app
COPY --from=build /app/publish .
ENTRYPOINT ["dotnet", "Tutorial1DockerSetup_dotnet.dll"]
```

> ⚠️ Replace `.NET 7.0` with your version if using .NET 6 or .NET 8.
> ⚠️ Ensure the `.csproj` filename matches your project.

---

## 3. Build the Docker image

```powershell
docker build -t Tutorial1DockerSetup_dotnet .
```

---

## 4. Run the container

Run the app and expose port **7094**:

```powershell
docker run -d -p 7094:7094 --name Tutorial1DockerSetup_dotnet Tutorial1DockerSetup_dotnet
```

---

## 5. Multi-port example

```powershell
docker build -t Tutorial1DockerSetup_dotnet .
docker images
docker run -p 7094:8081 -p 5255:8080 Tutorial1DockerSetup_dotnet
```

---

## 6. Clean up unused Docker resources

⚠️ This deletes **all unused containers, images, networks, and volumes**:

```powershell
docker system prune -a --volumes
```

---

## 7. Build with explicit Dockerfile path

If the Dockerfile is inside the project folder:

```powershell
docker build -f Tutorial1DockerSetup_dotnet\Dockerfile -t Tutorial1DockerSetup_dotnet .
```

---

## 8. Run with HTTPS certificate

### A. Create and trust a dev certificate

```powershell
dotnet dev-certs https -ep "$env:USERPROFILE\.aspnet\https\Tutorial1DockerSetup_dotnet.pfx" -p "MySecret123"
dotnet dev-certs https --trust
```

### B. Run with HTTPS enabled

```powershell
docker run -it --rm -p 5000:8080 -p 5001:8081 `
  -e ASPNETCORE_ENVIRONMENT=Development `
  -e ASPNETCORE_Kestrel__Certificates__Default__Password="MySecret123" `
  -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/Tutorial1DockerSetup_dotnet.pfx `
  -v "C:\Users\Clint Villanueva\.aspnet\https:/https:ro" `
  Tutorial1DockerSetup_dotnet
```

---



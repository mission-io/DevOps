# Dotnet-Core



## Dotnet Core Smaller and Secure Docker Image Sample Dockerfile

```dockerfile

FROM mcr.microsoft.com/dotnet/sdk:5.0-alpine AS publish
WORKDIR /src
COPY ContainerSample.csproj ./

RUN dotnet restore "./ContainerSample.csproj" --runtime alpine-x64
COPY . .
RUN dotnet publish "ContainerSample.csproj" -c Release -o /app/publish \
  --no-restore \
  --runtime alpine-x64 \
  --self-contained true \
  /p:PublishTrimmed=true \
  /p:PublishSingleFile=true

FROM mcr.microsoft.com/dotnet/runtime-deps:5.0-alpine AS final

RUN adduser --disabled-password \
  --home /app \
  --gecos '' dotnetuser && chown -R dotnetuser /app

# upgrade musl to remove potential vulnerability
RUN apk upgrade musl

USER dotnetuser
WORKDIR /app
EXPOSE 5000
COPY --from=publish /app/publish .

ENTRYPOINT ["./ContainerSample", "--urls", "http://localhost:5000"]

```

# Reference

1. [Build Smaller and Secure Docker Image`](https://thorsten-hans.com/how-to-build-smaller-and-secure-docker-images-for-net5) 
2. [Instrumenting ASP.NET Core Application For Exporting Metrics to Prometheus](https://www.c-sharpcorner.com/article/instrumenting-asp-net-core-application-for-exporting-metrics-to-prometheus/)
3. [Dockerfile Sample](https://medium.com/01001101/containerize-your-net-core-app-the-right-way-35c267224a8d)

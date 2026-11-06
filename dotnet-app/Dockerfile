# syntax=docker/dockerfile:1

ARG DOTNET_VERSION=10.0

############ Stage 1: build ############
FROM mcr.microsoft.com/dotnet/sdk:${DOTNET_VERSION} AS build
ARG APP_VERSION=0.0.0
WORKDIR /src

COPY Directory.Build.props ./
COPY src/DemoApi/DemoApi.csproj src/DemoApi/
RUN dotnet restore src/DemoApi/DemoApi.csproj

COPY src/DemoApi/ src/DemoApi/
RUN dotnet publish src/DemoApi/DemoApi.csproj \
        -c Release -o /app/publish --no-restore -p:Version=${APP_VERSION}

############ Stage 2: test ############
FROM build AS test
COPY tests/DemoApi.Tests/ tests/DemoApi.Tests/
RUN dotnet test tests/DemoApi.Tests/DemoApi.Tests.csproj -c Release

############ Stage 3: runtime ############
FROM mcr.microsoft.com/dotnet/aspnet:${DOTNET_VERSION} AS final
ARG APP_VERSION=0.0.0
WORKDIR /app

LABEL org.opencontainers.image.source="https://github.com/jennywitzke/docker-build-demo" \
      org.opencontainers.image.description="Demo API" \
      org.opencontainers.image.licenses="MIT" \
      org.opencontainers.image.version=${APP_VERSION}

COPY --from=build /app/publish .

EXPOSE 8080
USER $APP_UID
ENTRYPOINT ["dotnet", "DemoApi.dll"]

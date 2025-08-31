FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base

ARG APP_UID=1000
ARG APP_GID=1000

RUN groupadd -g ${APP_GID} appgroup && \
    useradd -u ${APP_UID} -g ${APP_GID} -m appuser

USER appuser
WORKDIR /app

EXPOSE 8080
EXPOSE 8081

ARG ASPNETCORE_URLS
ARG DOTNET_RUNNING_IN_CONTAINER
ARG DOTNET_SYSTEM_GLOBALIZATION_INVARIANT
ARG NUGET_XMLDOC_MODE
ARG ASPNETCORE_ENVIRONMENT

ENV ASPNETCORE_URLS=$ASPNETCORE_URLS
ENV DOTNET_RUNNING_IN_CONTAINER=$DOTNET_RUNNING_IN_CONTAINER
ENV DOTNET_SYSTEM_GLOBALIZATION_INVARIANT=$DOTNET_SYSTEM_GLOBALIZATION_INVARIANT
ENV NUGET_XMLDOC_MODE=$NUGET_XMLDOC_MODE
ENV ASPNETCORE_ENVIRONMENT=$ASPNETCORE_ENVIRONMENT

FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src
COPY ["src/backend/EStudy.Api/EStudy.Api.csproj", "src/backend/EStudy.Api/"]
COPY ["src/backend/EStudy.Application/EStudy.Application.csproj", "src/backend/EStudy.Application/"]
COPY ["src/backend/EStudy.Communication/EStudy.Communication.csproj", "src/backend/EStudy.Communication/"]
COPY ["src/backend/EStudy.Domain/EStudy.Domain.csproj", "src/backend/EStudy.Domain/"]
COPY ["src/backend/EStudy.Exception/EStudy.Exception.csproj", "src/backend/EStudy.Exception/"]
RUN dotnet restore "src/backend/EStudy.Api/EStudy.Api.csproj"
COPY . .
WORKDIR "/src/src/backend/EStudy.Api"
RUN dotnet build "EStudy.Api.csproj" -c $BUILD_CONFIGURATION -o /app/build

FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "EStudy.Api.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "EStudy.Api.dll"]

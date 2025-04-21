# See https://aka.ms/customizecontainer to learn how to customize your debug container and how Visual Studio uses this Dockerfile to build your images for faster debugging.

# This stage is used when running from VS in fast mode (Default for Debug configuration)
FROM mcr.microsoft.com/dotnet/aspnet:9.0 AS base
USER $APP_UID
WORKDIR /app
EXPOSE 8080
EXPOSE 8081


# This stage is used to build the service project
FROM mcr.microsoft.com/dotnet/sdk:9.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src
COPY ["Microsoft.SCIM.WebHostSample/Microsoft.SCIM.WebHostSample.csproj", "Microsoft.SCIM.WebHostSample/"]
COPY ["Microsoft.SystemForCrossDomainIdentityManagement/Microsoft.SCIM.csproj", "Microsoft.SystemForCrossDomainIdentityManagement/"]
RUN dotnet restore "./Microsoft.SCIM.WebHostSample/Microsoft.SCIM.WebHostSample.csproj"
COPY . .
WORKDIR "/src/Microsoft.SCIM.WebHostSample"
RUN dotnet build "./Microsoft.SCIM.WebHostSample.csproj" -c $BUILD_CONFIGURATION -o /app/build

# This stage is used to publish the service project to be copied to the final stage
FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "./Microsoft.SCIM.WebHostSample.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

# This stage is used in production or when running from VS in regular mode (Default when not using the Debug configuration)
FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
# ENTRYPOINT ["dotnet", "Microsoft.SCIM.WebHostSample.dll", "--Token:IssuerSigningKey=A1B2C3D4E5F6A1B2C3D4E5F6A1B2C3D4"]
ENTRYPOINT ["dotnet", "Microsoft.SCIM.WebHostSample.dll"]

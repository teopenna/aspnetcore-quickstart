#See https://aka.ms/containerfastmode to understand how Visual Studio uses this Dockerfile to build your images for faster debugging.

FROM mcr.microsoft.com/dotnet/core/runtime:3.1-alpine3.12 AS base

# Install ASP.NET Core
RUN aspnetcore_version=3.1.6 \
    && wget -O aspnetcore.tar.gz https://dotnetcli.azureedge.net/dotnet/aspnetcore/Runtime/$aspnetcore_version/aspnetcore-runtime-$aspnetcore_version-linux-musl-x64.tar.gz \
    && aspnetcore_sha512='fc0b6acc3031673a0f77b53a028c86c1dd55da08d4c45792d46fd040792cc5668cba3e0b29c5642897df04e66a095eff66d0eee4b92b04bdabeea2442c264166' \
    && echo "$aspnetcore_sha512  aspnetcore.tar.gz" | sha512sum -c - \
    && tar -ozxf aspnetcore.tar.gz -C /usr/share/dotnet ./shared/Microsoft.AspNetCore.App \
    && rm aspnetcore.tar.gz

WORKDIR /app
EXPOSE 80
EXPOSE 443

FROM mcr.microsoft.com/dotnet/core/sdk:3.1-alpine3.12 AS build
WORKDIR /src
COPY ["QuickStart.csproj", "QuickStart/"]
RUN dotnet restore "QuickStart/QuickStart.csproj"
WORKDIR "/src/QuickStart"
COPY . .
RUN dotnet build "QuickStart.csproj" -c Release -o /app/build

FROM build AS publish
RUN dotnet publish "QuickStart.csproj" -c Release -o /app/publish

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "QuickStart.dll"]
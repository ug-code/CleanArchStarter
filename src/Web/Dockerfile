# Bu aşama, VS'deki Docker ile hata ayıklamak için kullanılır
FROM mcr.microsoft.com/dotnet/aspnet:9.0 AS base
ENV ASPNETCORE_URLS=https://+:8080;http://+:8081
WORKDIR /app
EXPOSE 8080
EXPOSE 8081

FROM mcr.microsoft.com/dotnet/sdk:9.0 AS build
RUN curl -sL https://deb.nodesource.com/setup_16.x | bash -  # Node.js için güncel sürümü kullanıyoruz
RUN apt install -y nodejs
WORKDIR /src
COPY ["src/WebUI/WebUI.csproj", "src/WebUI/"]
COPY ["src/Application/Application.csproj", "src/Application/"]
COPY ["src/Domain/Domain.csproj", "src/Domain/"]
COPY ["src/Infrastructure/Infrastructure.csproj", "src/Infrastructure/"]
RUN dotnet restore "src/WebUI/WebUI.csproj"
COPY . . 
WORKDIR "/src/src/WebUI"
RUN dotnet build "WebUI.csproj" -c Release -o /app/build

FROM build AS publish
RUN dotnet publish "WebUI.csproj" -c Release -o /app/publish

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish . 
ENTRYPOINT ["dotnet", "CleanArchitecture.WebUI.dll"]
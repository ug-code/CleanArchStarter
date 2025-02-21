# .NET 8.0 Tabanlı Dockerfile
# İlk aşama: Çalışma zamanı ortamı
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
ENV ASPNETCORE_URLS=https://+:8080;http://+:8081
WORKDIR /app
EXPOSE 8080
EXPOSE 8081

# İkinci aşama: Derleme ve bağımlılıkların yüklenmesi
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
RUN apt-get update && apt-get install -y curl && rm -rf /var/lib/apt/lists/*
RUN curl -sL https://deb.nodesource.com/setup_16.x | bash -  # Node.js için güncel sürüm
RUN apt-get install -y nodejs

WORKDIR /src

# Bağımlı projeleri sırayla kopyalıyoruz
COPY ["src/Application/Application.csproj", "src/Application/"]
COPY ["src/Domain/Domain.csproj", "src/Domain/"]
COPY ["src/Infrastructure/Infrastructure.csproj", "src/Infrastructure/"]
COPY ["src/Web/Web.csproj", "src/Web/"]

# Projeleri restore ediyoruz
RUN dotnet restore "src/Web/Web.csproj"

# Tüm kaynak kodlarını kopyalıyoruz
COPY . .
WORKDIR "/src/src/Web"

# Projeyi derliyoruz
RUN dotnet build "Web.csproj" -c Release -o /app/build

# Yayınlama aşaması
FROM build AS publish
RUN dotnet publish "Web.csproj" -c Release -o /app/publish

# Son aşama: Çalıştırma ortamı
FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "CleanArchitecture.Web.dll"]

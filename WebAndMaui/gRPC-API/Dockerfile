FROM mcr.microsoft.com/dotnet/sdk:10.0 AS build
WORKDIR /src
# copy csproj and restore (adjust path if your .csproj filename differs)
COPY *.csproj ./
RUN dotnet restore

# copy everything and publish
COPY . ./
RUN dotnet publish -c Release -o /app/publish

FROM mcr.microsoft.com/dotnet/aspnet:10.0 AS runtime
WORKDIR /app
COPY --from=build /app/publish .
ENV ASPNETCORE_URLS=http://+:80
EXPOSE 80
ENTRYPOINT ["dotnet", "gRPC-API.dll"]
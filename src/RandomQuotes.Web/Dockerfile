FROM --platform=$BUILDPLATFORM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
USER app
WORKDIR /app
EXPOSE 8080

FROM --platform=$BUILDPLATFORM mcr.microsoft.com/dotnet/sdk:8.0 AS build
ARG TARGETARCH

# Map Docker arch to .NET runtime identifier
ENV RUNTIME_ID=linux-x64
RUN if [ "$TARGETARCH" = "arm64" ]; then RUNTIME_ID=linux-arm64; \
    elif [ "$TARGETARCH" = "arm/v7" ]; then RUNTIME_ID=linux-arm; fi && \
    echo "Target runtime: $RUNTIME_ID"

WORKDIR /src
COPY ["RandomQuotes.Web/RandomQuotes.Web.csproj", "RandomQuotes.Web/"]
RUN dotnet restore "RandomQuotes.Web/RandomQuotes.Web.csproj"

COPY . .
WORKDIR "/src/RandomQuotes.Web"
ARG RUNTIME_ID
RUN dotnet publish "RandomQuotes.Web.csproj" -c Release \
    --runtime $RUNTIME_ID \
    --self-contained false \
    -o /app/publish /p:UseAppHost=false

FROM base AS final
WORKDIR /app
COPY --from=build /app/publish .

ENV RANDOM_PHRASE="Default Phrase"
ENV ASPNETCORE_URLS="http://+:8080"
ENV ASPNETCORE_ENVIRONMENT="Production"
ENTRYPOINT ["dotnet", "RandomQuotes.Web.dll"]

# Spotify Search CLI

A command-line application built with **Rust** that interacts with the Spotify Web API to search for music tracks and retrieve track metadata. It uses `reqwest` to send HTTP requests and `serde` to deserialize JSON responses into Rust structs.

## Features

- Search Spotify for tracks using a command-line query.
- Authenticate API requests using a Spotify access token.
- Retrieve track information, including:
  - Track name
  - Album name
  - Artist names
  - Spotify URLs
  - API-provided track metadata
- Parse JSON API responses into strongly typed Rust structs using Serde.
- Handle HTTP requests asynchronously with `reqwest`.

## Tech Stack

- **Rust** — Core programming language
- **Reqwest** — HTTP client for API requests
- **Serde** — Serialization and deserialization
- **Spotify Web API** — Music search and track metadata

## Prerequisites

Before running the project, ensure you have:

- [Rust and Cargo](https://www.rust-lang.org/tools/install) installed.
- A Spotify account with the required access to the Spotify Developer platform.
- A Spotify Developer application and a valid API access token.

## Getting Started

### 1. Clone the repository

```bash
git clone https://github.com/dewdropx/spotify-api-search.git
cd spotify-api-search
```

### 2. Install dependencies

Cargo downloads the dependencies defined in `Cargo.toml` when you build or run the project.

```bash
cargo build
```

### 3. Generate a Spotify access token

1. Open the [Spotify Developer Dashboard](https://developer.spotify.com/dashboard).
2. Create or select your Spotify application.
3. Obtain your Client ID and Client Secret.
4. Request an access token using the **Client Credentials Flow**.

Example using PowerShell:

```powershell
$clientId = "YOUR_CLIENT_ID"
$clientSecret = "YOUR_CLIENT_SECRET"

$credentials = [Convert]::ToBase64String(
    [Text.Encoding]::ASCII.GetBytes("${clientId}:${clientSecret}")
)

$response = Invoke-RestMethod `
    -Uri "https://accounts.spotify.com/api/token" `
    -Method Post `
    -Headers @{ Authorization = "Basic $credentials" } `
    -ContentType "application/x-www-form-urlencoded" `
    -Body @{ grant_type = "client_credentials" }

$token = $response.access_token
$token
```

The access token can then be supplied to the application. Client Credentials Flow tokens typically expire after one hour, so generate a new token when necessary.

**Note:** Spotify API access and development-mode requirements may depend on your application's eligibility and Spotify's current developer policies.

### 4. Run the application

Pass the search query and access token as command-line arguments:

```bash
cargo run "SEARCH_QUERY" "YOUR_ACCESS_TOKEN"
```

## How It Works

1. **Read CLI arguments:** The application receives the search query and access token.
2. **Construct the API request:** It builds a request to Spotify's Search endpoint.
3. **Authenticate:** It sends the access token in the HTTP `Authorization` header using the Bearer scheme.
4. **Send the request:** `reqwest` performs an asynchronous GET request.
5. **Deserialize the response:** Serde maps the JSON response into Rust structs representing tracks, albums, artists, and external Spotify URLs.
6. **Process the results:** The application accesses the returned track collection and displays or processes the matching results.

### API Endpoint

```http
GET https://api.spotify.com/v1/search
```

The request uses the following query parameters:

| Parameter | Description |
|---|---|
| `q` | The search query |
| `type` | The Spotify resource types to search, such as `track` and `artist` |

## Error Handling and Debugging

During development, inspecting the raw API response can help diagnose issues such as:

- Invalid or expired access tokens
- HTTP errors returned by Spotify
- JSON fields missing from the expected Rust structs
- Differences between the API response and the application's data model

When deserializing JSON with Serde, the fields in a Rust struct should match the response schema. Fields that may be absent should be modeled appropriately, for example with `Option<T>`.

## Security

- Never commit your Spotify Client Secret or access token to GitHub.
- Avoid hardcoding credentials in your source code.
- Do not publish terminal logs containing live access tokens.
- Consider using environment variables or a local `.env` file excluded through `.gitignore` for sensitive configuration.

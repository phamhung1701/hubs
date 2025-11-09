# Running Hubs against a local Reticulum

The development client (`npm run local`) expects a Reticulum server to be available on `https://hubs.local:4000`. Reticulum is the Phoenix backend that manages room state, avatars, and permissions. If you only run `npm run dev` the client will continue to talk to the shared development Reticulum cluster, but for fully offline testing you need to host Reticulum yourself.

## Prerequisites

Install the dependencies required by Reticulum:

- [Elixir and Erlang](https://elixir-lang.org/install.html)
- [PostgreSQL](https://www.postgresql.org/download/)
- [Redis](https://redis.io/download)
- Node.js 16.x (needed for Reticulum's asset pipeline)

> **Tip:** The [asdf version manager](https://asdf-vm.com/) works well for installing Erlang, Elixir, and Node with the versions defined in Reticulum's `.tool-versions` file.

## Clone the repository

Clone Reticulum next to your Hubs checkout so the two projects share the same parent directory:

```bash
cd /path/to/workspace
git clone https://github.com/Hubs-Foundation/reticulum.git
```

## Configure Reticulum

1. Change into the new folder: `cd reticulum`
2. Copy the default secrets file: `cp config/dev.secret.exs.example config/dev.secret.exs`
3. Install dependencies and set up the database:
   ```bash
   mix deps.get
   mix ecto.setup
   (cd assets && npm install)
   ```
4. Start the Phoenix server with HTTPS enabled so Hubs can connect through `https://hubs.local:4000`:
   ```bash
   PHX_SERVER=true MIX_ENV=dev mix phx.server
   ```

The first launch will generate a self-signed certificate under `priv/cert/`. When prompted by your browser, accept the warning for the self-signed certificate on `https://hubs.local:4000`.

## Point Hubs at the local server

Back in the Hubs repository, install dependencies if you have not already:

```bash
npm ci
```

Then start the client so it targets the local Reticulum instance and proxies asset requests through `hubs.local`:

```bash
npm run local
```

Make sure `hubs.local` and `hubs-proxy.local` both resolve to `127.0.0.1` in your `/etc/hosts` (or the Windows/macOS equivalent). When both servers are running you can visit:

- `https://hubs.local:4000` – Reticulum (Phoenix backend)
- `https://hubs.local:8080` – Hubs client pointing at the local backend

With Reticulum running locally you can sign in with the default admin credentials shown in the terminal output and create rooms without relying on the shared development infrastructure.

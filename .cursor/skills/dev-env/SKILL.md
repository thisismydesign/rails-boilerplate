---
name: dev-env
description: Read this when working locally or in a Cloud VM.
---

# Development Environment

## Setup

Needs:
- Mise installed and activated
- Docker installed and service running

```bash
mise install
cp .env.native .env.development.local
cp .env.native .env.test.local
docker compose up -d db
bin/setup --skip-server
```

## Starting the app

```bash
docker compose up -d db
bin/setup
```

Development URL: http://localhost:3000

## Testing

```bash
bundle exec rspec path/to/spec_file.rb      # Run single test file
bundle exec rspec path/to/spec_file.rb:42   # Run single test at line
```

## Lint

```bash
bundle exec rubocop -A path/to/file.rb      # Lint with auto-fix
```

## Devcontainer

There's also a devcontainer setup. Make sure `.env.development.local` from native setup is removed.

Inside to start the upp:

```bash
bin/setup
```

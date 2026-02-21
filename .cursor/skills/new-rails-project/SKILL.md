---
name: new-rails-project
description: Kickstart a new Rails project with opinionated defaults. Use when user wants to set up a new project.
---

# Steps

## Section 1 - Orientation

Check where you ar in the process. If Section 2 setup is not yet complete you need to do that first. Otherwise, if Section 2 is done and you're in a devcontainer you can continue with Section 3. When you're done with a step, commit your changes. Any time you can re-start by running the command(s) from the README.

## Section 2 - Initial setup & codegen on host

- Ask the user aboutproject preferences, edit .railsrc config accordingly.
- Edit the image name in compose and Dockerfile based on the new project name.
- Run the command(s) from the README to init the new app. Overwrite files if asked.
- Discard README.md changes.
- Update devcontainer setup:
  - Use compose from root.
  - Set name to project name.
  - Set service to match service from compose.
  - Add git and sshd features.
  - Delete devcontainer dockerfile and compose.
  - Update DB_HOST container env to match compose.
- `cp AGENTS.md.example AGENTS.md`, fill in project name and description.
- Ask user to rebuild and reopen in container

## Section 3 - Further setup in devcontainer

- Add RUBYOPT="--enable-frozen-string-literal" to Dockerfile ENV setting
- Rearrange Gemfile:
  - 1 line per gem, sections with headers (# Defaults, # App, # Observability).
  - Remove unused gems.
  - Move comments from top to after the gem definition, do not change comments.
- Add & configure new gems (dev & test) (use latest versions): rspec-rails, faker, factory_bot_rails
- Add & configure new gems (use latest versions): rubocop, rubocop-factory_bot, rubocop-performance, rubocop-rails, rubocop-rspec, rubocop-rspec_rails
- Auto-fix & fix issues
- Add more steps to .github/workflows/ci.yml:
  - test: checkout, pull images, start db via compose, setup ruby with bundler cache, db:prepare, rspec
  - lint: checkout, setup ruby with bundler cache, rubocop

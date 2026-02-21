# Rails Docker boilerplate

#### A boilerplate that sets the ground for Rails development with Docker and Agents.

## Agent usage

`/new-rails-project`

## Manual usage

### Init app

```sh
docker compose build
# Edit .railsrc to customize the rails app
# Create new rails app
docker compose run --rm web bash -c "bundle && rails new . --force --rc=.railsrc"
```

### Run app

```sh
# Run app
docker compose up -d
docker compose exec web bin/setup --skip-server
docker compose exec web bin/dev
```

### Start over

```sh
git reset --hard HEAD
git clean -fdx
```

## License

The project is available as open source under the terms of the [MIT License](http://opensource.org/licenses/MIT).

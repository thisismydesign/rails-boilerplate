# Rails Docker boilerplate

#### A boilerplate that sets the ground for Rails development with Docker

## Usage

```sh
docker compose build
# Edit .railsrc to customize the rails app
# Create new rails app
docker compose run --rm web bash -c "bundle && rails new . --force --rc=.railsrc"

# Run app
docker compose up -d
docker compose exec web bin/setup --skip-server
docker compose exec web bin/dev -b 0.0.0.0
```

Reset (start over) via
```sh
git reset --hard HEAD
git clean -fdx
```

## License

The project is available as open source under the terms of the [MIT License](http://opensource.org/licenses/MIT).

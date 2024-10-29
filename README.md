# Hasura 2 with PostgreSQL playground

Here's my Hasura playground repository.

## Prerequisites

- Install docker and docker compose
- PostgreSQL clients: `psql`
- Install [Mise](https://mise.jdx.dev/)
- Install [httpie](https://httpie.io/docs/cli/installation)
- direnv

## Getting started

Configure Mise and install tools:

```sh
$ mise plugin add hasura-cli https://github.com/gurukulkarni/asdf-hasura.git
$ mise install
```

Load variable env:

```sh
$ direnv allow
```

Start PostgreSQL database and Hasura graphql-engine service:

```sh
$ docker compose up -d
$ ./scripts/inject-seed-in-local-pg.sh
```

```sh
$ hasura metadata apply
```

Go to http://localhost:8080/console (password: `password`).


```sh
$ http --body POST http://localhost:8080/v1/graphql \
  Content-Type:application/json \
  x-hasura-admin-secret:password \
  query='{ users { id username } }'
{
    "data": {
        "users": [
            {
                "id": 1,
                "username": "user1"
            },
            {
                "id": 2,
                "username": "user2"
            },
            {
                "id": 3,
                "username": "user3"
            }
        ]
    }
}
```

```sh
$ http --body POST http://localhost:8080/v1/graphql \
  Content-Type:application/json \
  x-hasura-admin-secret:password \
  query='{ search_users(args: {search: "user1"}) { id username } }'
{
    "data": {
        "search_users": [
            {
                "id": 1,
                "username": "user1"
            }
        ]
    }
}
```

## Miscellaneous notes

I'm not using Hasura version 2.44 because of this issue: https://github.com/hasura/graphql-engine/issues/10580

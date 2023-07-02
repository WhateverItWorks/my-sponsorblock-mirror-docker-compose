# sponsorblock-mirror

This is a Rust-based mirror of the [SponsorBlock](https://sponsor.ajay.app) API.

It also uses [sb-mirror](https://github.com/mchangrh/sb-mirror) for mirroring the CSV dumps via rsync.

## Instances

-   [sponsorblock.kavin.rocks](https://sponsorblock.kavin.rocks) - 🇩🇪 (main instance)
-   [sponsorblock.gleesh.net](https://sponsorblock.gleesh.net) - 🇩🇪
-   [sponsorblock.2255.me](https://sponsorblock.2255.me) - 🇵🇱
-   [sponsorblock.hostux.net](https://sponsorblock.hostux.net) - :fr:

Feel free to add your instance to this list by making a pull request.

You can also configure Piped-Backend to use your mirror by changing the `SPONSORBLOCK_SERVERS` configuration value.

## Compatibility

This implementation does not implement the full SponsorBlock server API. It supports hash-based queries to `/api/skipSegments/<hash>`, with optional `categories` parameter, and queries to `/api/skipSegments` with required `videoID` and optional `categories` parameters.

The browser extension works with only the hash-based query endpoint, but other clients, such as the one in ReVanced, require the video ID endpoint, and additionally query `/api/userInfo` and `/api/isUserVip`. Right now there are stub implementations for these. ReVanced had not yet been verified as compatible.

### Deploy with Docker
```
docker-compose up -d
```

### Deploy with Dockerfile (Build with Source)
```
docker-compose up -d --build
```

This starts the API server, a database, and a mirroring service to download the SponsorBlock data from the `sponsorblock.kavin.rocks` mirror and keep it up to date.

The API will be available on `http://localhost:8000`. For example, you can try `http://localhost:8000/api/skipSegments/aabf` or `http://localhost:8000/api/skipSegments?videoID=eQ_8F4nzyiw`. **It will take a few minutes at least for the database to download and import,** so these will not return data on the first run.



## Troubleshooting

* If the linker complains about a missing `-lpq`, make sure you have the PostgreSQL development libraries, which may be in a `libpq-dev` package or your distribution's equivalent.

* If Docker complains that `the --mount option requires BuildKit`, make sure you are building with `docker buildx build` and not `docker build`.

* To access the PostgreSQL database directly, you can `docker exec -ti postgres-sb-mirror bash -c 'psql $POSTGRES_DB $POSTGRES_USER'`.

* Requests for videos not in the database are forwarded to `https://sponsor.ajay.app/`, which may be down or malfunctioning. A response of the string `Internal Server Error` is likely to be from there, rather than from this application.

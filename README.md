# Homer Dashboard

My simple configuration of the Homer dashboard.

![Homer Dashboard](assets/images/demo.png?v=2)

## Docker

### Build

`docker build . --tag homer:0.0.1`

### Run

`docker run --rm -p 8080:8080 homer:0.0.1`

### Compose

`docker compose -f docker-compose.yaml up`

## Acknowledgements

- [Homer Dashboard](https://github.com/bastienwirtz/homer)
- [Catppuccin Theme](https://github.com/mrpbennett/catppucin-homer)

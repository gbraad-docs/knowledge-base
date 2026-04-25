# mdBook Container

Ubuntu 24.04-based container image with mdBook and the callouts preprocessor.

## Build

```bash
docker build -t mdbook:latest -f Containerfile .
```

## Use

```bash
docker run --rm -v $PWD:/workspace mdbook:latest build
docker run --rm -v $PWD:/workspace -p 3000:3000 mdbook:latest serve --hostname 0.0.0.0
```

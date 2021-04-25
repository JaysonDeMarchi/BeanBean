# BeanBean

One click deploy proof of concept for a BitClout node.

## Development

### Build Phase
The issue we'll face on any hosting platform is they require a published image to deploy with Docker. Our build phase here takes the base BitClout images for their frontend and nginx config, then templates out their URLs to environment variables. In theory we can scale this approach to the core image too if we want their custom settings too, but this is more of a stretch goal.

```bash
docker compose -f ./build/docker-compose.yaml build
```

Note: I left the SSL commented out in the nginx config. Its possible the hosting provider will handle this for us, so more testing in the ideal production environment is necessary.

### Deploy Phase
Deploys our wrapper images with the associated environment variables. This relies on the docker images built from the build phasae. Ideally a client just has to modify the env variables if they wanted to spin up their own instance.

```bash
docker compose -f ./deploy/docker-compose.yaml up -d
```

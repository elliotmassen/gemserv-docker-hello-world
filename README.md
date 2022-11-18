# Gemserv Docker: Hello world

This repository enables a [Gemserv](https://git.sr.ht/~int80h/gemserv) Docker image to be built which can be deployed to a provider, such as Fly.io, to host public Gemini capsules.

- [Local setup](#local)
- [Fly.io setup](#deploy-to-flyio)

## Todo

- [ ] Version the Docker images using in the Dockerfile `FROM` statements.
- [ ] Split the various local and Fly files into separate directories.
- [ ] Attempt to bring the certificate generation step within the Dockerfiles.
- [ ] Docker Compose file for local set up.
- [ ] Investigate Fly.io's `tls handshake eof` error (potentially related to the `tcp_checks` config section).

## Local

1. Install [mkcert](https://mkcert.dev), and run `mkcert example.com`
2. Update hosts file to include `127.0.0.1 example.com`
3. `docker build -f Dockerfile.local -t gemserv-hello-world-local .`
4. `docker run -p 1965:1965 gemserv-hello-world-local`
5. Send a Gemini request via your preferred browser. 
   1. For example with [bombadillo](https://bombadillo.colorfield.space/): `bombadillo gemini://example.com:1965` (if you have used the domain with different certificates in the past, be sure to purge the locally stored certificates from the browser: this can be achieved in bombadillo with the line command: `purge example.com`).

## Deploy to Fly.io

1. Install [`flyctl`](https://fly.io/docs/hands-on/install-flyctl/), sign up to Fly, and sign in via flyctl.
2. Choose a name for your application. This will be used by Fly as the subdomin. In the following steps replace `{name}` with this application name.
3. `fly launch --copy-config --name {name}`
4. Install [mkcert](https://mkcert.dev), and run `mkcert -cert-file fly-cert.pem -key-file fly-key.pem {name}.fly.dev`
5. `docker build -f Dockerfile.fly -t gemserv-hello-world-fly --build-arg APP_NAME={name} .`
6. `fly deploy --image gemserv-hello-world-fly --local-only`
7. Send a Gemini request via your preferred browser.
   1. For example with [bombadillo](https://bombadillo.colorfield.space/): `bombadillo gemini://{name}.fly.dev:1965`
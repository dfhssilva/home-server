# Traefik - Before Deployment

Before composing up the configuration file, create `usersfile` file inside this folder. This file defines the user and passwords for accessing the Traefik dashboard and should have in each line a combination of `user:password-hash`, where `password-hash` is the MD5, SHA1, or BCrypt hash of the original password.

Check this [link](https://doc.traefik.io/traefik/middlewares/http/basicauth/#usersfile) for more information.
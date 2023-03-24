# Fireflyiii Documentation

Before composing up the configuration file, create `.db.env` and `.env` files inside this folder. Check this [link](https://docs.firefly-iii.org/firefly-iii/installation/docker/) for more information and to obtain a template for these files.

Once you get the templates for both files, you need to fill some of the fields.
- Change `DB_PASSWORD` in `.env` to a decent password.
- Change `MYSQL_PASSWORD` in `.db.env` to the **SAME** value as `DB_PASSWORD`.
- Fill `SITE_OWNER`, `APP_KEY`, `TZ` in `.env`.
- Set `TRUSTED_PROXIES` to "**" in `.env` to enable Fireflyiii to work fine with Traefik.
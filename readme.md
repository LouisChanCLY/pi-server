# Pi Home Server

This is a repository that contains a set of containers for my Raspberry Pi 4 hosted home
server.

## Traefik + Cloudflare Reverse Proxy Stack

1. Create user password for logging in

    ```bash
    htpasswd -b -c <path/to/repo>/traefik/shared/.htpasswd admin <your strong password>
    ```

2. Create `acme.json`

    ```bash
    touch traefik/acme.json
    chmod 600 traefik/acme.json
    ```

3. Create log files

    ```bash
    touch traefik/traefik.log
    touch traefik/access.log
    ```

4. Add DNS record to Cloudflare

    ```
    A      your.domain.here     xxx.xxx.xxx.xxx (your public IP)
    CNAME  traefik              your.domain.here
    CNAME  *                    your.domain.here
    ```
    > If you have any `TXT` record named `_acme-challenge`, you may want to remove them.

    > Turning off Proxy for your A record for the initial setup is strongly encouraged.

5. Create Cloudflare API Key

    From [here](https://dash.cloudflare.com/profile/api-tokens), create a new token with
    the following permission:

    * Zone.Zone Settings: Read
    * Zone.Zone: Read
    * Zone.DNS: Edit
    * Zone Resources: Include > Specific zone > choose your domain

6. Add environment variables

    ```
    # TRAEFIK ENV VARIABLES
    PGID=100
    PUID=998
    TZ=<your locale here>
    DOMAINNAME_CLOUD_SERVER=<your domain here>
    CF_API_EMAIL=<your Cloudflare email here>
    CF_API_KEY=<your Cloudflare API key here>
    TRAEFIK_DIR=<path/to/repo>/traefik
    LOCAL_IPS=127.0.0.1/32,10.0.0.0/8,192.168.0.0/16,172.16.0.0/12
    CLOUDFLARE_IPS=173.245.48.0/20,103.21.244.0/22,103.22.200.0/22,103.31.4.0/22,141.101.64.0/18,108.162.192.0/18,190.93.240.0/20,188.114.96.0/20,197.234.240.0/22,198.41.128.0/17,162.158.0.0/15,104.16.0.0/13,104.24.0.0/14,172.64.0.0/13,131.0.72.0/22
    ```

7. Initial Build

    For your initial build, you may want to do the following:

    * uncomment the following line in the docker-compose file:

        `- "traefik.http.routers.traefik-rtr.tls.certresolver=dns-cloudflare"`
    
    * uncomment the following line in `traefik.toml`

        `caServer = https://acme-staging-v02.api.letsencrypt.org/directory`

        > If you have uncommented this line, make sure to comment it out when you have
        > tested the traefik dashboard successfully and rebuild to get the real
        > certificate.

8. Voila! Try it out on `https://traefik.your.domain` with the username and password that you have created.

Maintained by Louis Chan
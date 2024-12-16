![alt text](/assets/jellyfin-logo.png)

HomeLab for hosting private media

JellyFin is an open source media solution that gives you full control of your media. I decided this would be a cool project to implement in my homelab for my whole family to enjoy.  
JellyFin allows me to share my photos, videos, movies with my family like how they would watch Netflix.
Installation

My homelab server is a Debian server that has docker installed and so it made sense to install JellyFin via a docker compose file.

1. Make a new folder on your server to store JellyFin files. 

    ```
    mkdir jellyfin
    ```

2. Make the following  in the jellyfin folder you created.
    ```
    mkdir -p jellyfin/config
    mkdir -p jellyfin/cache
    mkdir -p jellyfin/media/photos
    mkdir -p jellyfin/media/videos
    mkdir -p jellyfin/media2 # optional
    ```

2. Create a docker-compose.yaml file in the jellyfin folder
    ```
    nano docker-compose.yaml
    ```

    ```
    services:
    jellyfin:
        image: jellyfin/jellyfin
        container_name: jellyfin
        user: uid:gid # Change uid:gid to jellyfin user uid:gid check /etc/passwd
        ports:
        - 8096:8096 # Expose jellyfin HTTP on port 8096
        volumes:
        - <PATH TO JELLYFIN FOLDER>/config:/config
        - <PATH TO JELLYFIN FOLDER>/cache:/cache
        - type: bind
            source: <PATH TO JELLYFIN FOLDER>/media
            target: /media
        - type: bind
            source: /<PATH TO JELLYFIN FOLDER>/media2 
            target: /media2 
            read_only: true
        # Optional - extra fonts to be used during transcoding with subtitle burn-in
        - type: bind
            source: <PATH TO JELLYFIN FOLDER>/custom
            target: /usr/local/share/fonts/custom
            read_only: true
        restart: 'unless-stopped'
        # Optional - alternative address used for autodiscovery
        environment:
        - JELLYFIN_PublishedServerUrl=http://localhost:8089 # Expose jellyfin on server
        # Optional - may be necessary for docker healthcheck to pass if running in host network mode
        extra_hosts:
        - 'host.docker.internal:host-gateway'
    ```
    > One useful thing to know about docker YAML files. Dont touch the right side of colon. Edit left side of colon.

4. Run docker compose command
    ```
    docker compose up -d
    ```

5. Access localhost on port 8096 on web client and follow the instructions
    ![alt text](/assets/install.png)

    - When adding a new media library click on the + next to Folders and select the file you want to associate the media library. 
    ![alt text](/assets/setup.png)

6. Upload photos, videos, movies to the folders you created.
    ![alt text](/assets/media.png)

## Exposing webui in LAN
Depending on your network setup you may have to do something different. For my homelab my server and family laptops are all on the same network. Therefore all I needed to do was open firewall ports and port forward my traffic to hit the port.
### Instructions
1. Open server firewall ports for port 8096. TCP and UDP.
2. Port forward traffic from your network to the server on port 8096. Usually done on the router.

## Closing Thoughts
I hope you enjoy setting up JellyFin. I personally use it to host my photos, videos and movies. I think it is a really cool open source project that deserves a lot more love.



# Photoprism

Personal Photo Management powered by Go and Google TensorFlow.

<br>

- [Github repo](https://github.com/photoprism/photoprism)
- [Demo](https://demo.photoprism.org/photos)


## docker-compose.yml
```yml
version: '3.3'

services:
  photoprism:
    image: photoprism/photoprism:latest
    restart: unless-stopped
    ports:
      - 2342:2342
    healthcheck: # Optional
      test: "photoprism status"
      interval: 60s
      timeout: 15s
      retries: 5
    environment: # Run "photoprism help" and "photoprism config" too see all config options and current values
      PHOTOPRISM_DEBUG: "false"                      # Run in debug mode (shows additional log messages)
      PHOTOPRISM_PUBLIC: "false"                     # No authentication required (disables password protection)
      PHOTOPRISM_READONLY: "false"                   # Don't modify originals directory (reduced functionality)
      PHOTOPRISM_UPLOAD_NSFW: "true"                 # Allow uploads that MAY be offensive
      PHOTOPRISM_DETECT_NSFW: "false"                # Flag photos as private that MAY be offensive
      PHOTOPRISM_EXPERIMENTAL: "true"                # Enable experimental features
      PHOTOPRISM_SITE_URL: "http://localhost:2342/"  # Canonical / public site URL
      PHOTOPRISM_SITE_TITLE: "PhotoPrism"
      PHOTOPRISM_SITE_CAPTION: "Browse Your Life"
      PHOTOPRISM_SITE_DESCRIPTION: ""
      PHOTOPRISM_SITE_AUTHOR: ""
      PHOTOPRISM_HTTP_HOST: "0.0.0.0"
      PHOTOPRISM_HTTP_PORT: 2342
      PHOTOPRISM_SETTINGS_HIDDEN: "false"            # Users can not view or change settings
      PHOTOPRISM_ADMIN_PASSWORD: "photoprism"        # Initial admin password (can be changed in settings)
      PHOTOPRISM_DATABASE_DRIVER: "mysql"            # Using MariaDB or MySQL instead of SQLite is optional
      PHOTOPRISM_DATABASE_DSN: "photoprism:photoprism@tcp(photoprism-db:3306)/photoprism?charset=utf8mb4,utf8&parseTime=true"
      # PHOTOPRISM_SIDECAR_JSON: "true"              # Read metadata from JSON sidecar files created by exiftool
      # PHOTOPRISM_SIDECAR_YAML: "true"              # Backup photo metadata to YAML sidecar files
      PHOTOPRISM_THUMB_FILTER: "lanczos"             # Resample filter, best to worst: blackman, lanczos, cubic, linear
      PHOTOPRISM_THUMB_UNCACHED: "false"             # Enable on-demand thumbnail rendering (high memory and cpu usage)
      PHOTOPRISM_THUMB_SIZE: 2048                    # Pre-rendered thumbnail size limit (default 2048, min 720, max 7680)
      # PHOTOPRISM_THUMB_SIZE: 4096                  # Retina 4K, DCI 4K (requires more storage); 7680 for 8K Ultra HD
      PHOTOPRISM_THUMB_SIZE_UNCACHED: 7680           # On-demand rendering size limit (default 7680, min 720, max 7680)
      PHOTOPRISM_JPEG_SIZE: 7680                     # Size limit for converted image files in pixels (720-30000)
      PHOTOPRISM_JPEG_QUALITY: 90                    # Use 95 for high-quality thumbnails (requires more storage)
      PHOTOPRISM_STORAGE_PATH: "/photoprism/storage" # Storage PATH for generated files like cache and index
    volumes:
      - "~/Pictures/Originals:/photoprism/originals" # [local path]:[container path]
      - "~/Pictures/Import:/photoprism/import"       # [local path]:[container path] (optional)
      - "./storage:/photoprism/storage"     # Keep cache, settings and database

  photoprism-db:
    image: mariadb:10.5
    restart: unless-stopped
    command: mysqld --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci --max-connections=1024 --innodb-rollback-on-timeout=OFF --innodb-lock-wait-timeout=50
    volumes:
      - "./db:/var/lib/mysql"
    environment:
      MYSQL_ROOT_PASSWORD: photoprism
      MYSQL_USER: photoprism
      MYSQL_PASSWORD: photoprism
      MYSQL_DATABASE: photoprism
```

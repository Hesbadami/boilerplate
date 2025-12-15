My asynchronous boilerplate for any networked application.

Its highlight features are:

* A sophisticated internal signal system built on NATS that lets you connect different services to create complex
  event-driven interactions
* Built on top of [AnyIO](https://github.com/agronholm/anyio) and [APScheduler](https://github.com/agronholm/apscheduler)
* Designed with `Structured Concurrency` from the ground up


NATS docker-compose:

```yaml
services:
  nats:
    image: nats:latest
    container_name: nats
    restart: always
    ports:
      - "127.0.0.1:4222:4222"
      - "127.0.0.1:8222:8222"
      - "127.0.0.1:6222:6222"
    command:
      - "--jetstream"
      - "--http_port=8222"  # Enable monitoring
    volumes:
      - nats_data:/data

  nats-box:
    image: natsio/nats-box:latest
    container_name: kopilot-nats-box
    depends_on:
      - nats
    command: sleep infinity  # Keep container running for debugging
    restart: unless-stopped

volumes:
  nats_data:
    external: true
```

MySQL docker-compose:

```yaml
services:
  mysql:
    image: mysql:latest
    container_name: mysql
    restart: always
    ports:
      - "127.0.0.1:3306:3306"
    volumes:
      - mysql-data:/var/lib/mysql
    environment:
      - MYSQL_ROOT_PASSWORD=password

  phpmyadmin:
    image: phpmyadmin/phpmyadmin:latest
    container_name: phpmyadmin
    restart: always
    ports:
      - "8080:80"
    environment:
      - PMA_HOST=mysql
      - MYSQL_ROOT_PASSWORD=password
    depends_on:
      - mysql

volumes:
  mysql-data:
    external: true
```
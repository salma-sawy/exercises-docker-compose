# Exercise 2.11 – Your Development Environment

## Project Overview

For this exercise, I created a simple Node.js API called **Book Library API** and containerized its development environment using Docker and Docker Compose.

The API provides two endpoints:

- `GET /`: Returns a welcome message.
- `GET /books`: Returns a list of books.

## Dockerfile

I created a Dockerfile to set up the Node.js development environment.

```dockerfile
FROM node:22

WORKDIR /app

COPY ./backend/package*.json .

RUN npm install

COPY ./backend .

EXPOSE 3000

CMD ["npm", "run", "dev"]
```

### Explanation

- `FROM node:22`: Uses the Node.js 22 image.
- `WORKDIR /app`: Sets the working directory inside the container.
- `COPY`: Copies the package files into the image.
- `RUN npm install`: Installs the project dependencies.
- `COPY ./backend .`: Copies the application source code.
- `EXPOSE 3000`: Documents the port used by the application.
- `CMD`: Starts the application using the development script.

## Docker Compose

I used Docker Compose to configure and run the development environment.

```yaml
services:
  backend:
    build: .
    command: ["npm", "run", "dev"]
    ports:
      - 3000:3000
    volumes:
      - ./backend:/app
      - node-volume:/app/node_modules

volumes:
  node-volume:
```

### Volumes

I used two volumes for different purposes:

**1. Bind Mount**

```yaml
- ./backend:/app
```

This mounts the local backend directory into the container.

It allows me to modify the source code on my host machine and see the changes immediately inside the container without rebuilding the image.

**2. Named Volume**

```yaml
- node-volume:/app/node_modules
```

This keeps the Node.js dependencies in a separate named volume.

It prevents the `node_modules` directory from being hidden by the bind mount.

## Testing the Development Environment

I started the environment using:

```bash
docker compose up
```

The application started successfully inside the container.

I tested the API endpoints using `curl`:

```bash
curl http://localhost:3000/
curl http://localhost:3000/books
```

I also tested the bind mount by modifying the welcome message in the `index.js` file on my host machine.

Nodemon detected the file change and automatically restarted the application inside the container.

After refreshing the request, the updated welcome message appeared successfully without rebuilding the Docker image.

## Conclusion

This exercise helped me understand how containers can be used during development, not only in production.

Using Docker and Docker Compose provides a consistent development environment, while bind mounts and named volumes make the development workflow easier and more efficient.
# Docker Learning Journey 🚀

## Day 1️⃣ - Getting Started with Docker

### 🛠 Install Docker  
- Restart the system after installation.  
- Verify the installation:  
  ```sh
  docker --version
  ```
  **Output:**
  ```sh
  Docker version 24.0.2, build cb74dfc
  ```

### 📦 Pull Images (Default is the latest version)
  ```sh
  docker pull redis
  docker pull mongo
  docker pull postgres:13.11
  ```
  **Output (Example for MongoDB):**
  ```sh
  13.11: Pulling from library/postgres
  Digest: sha256:xxxxxx
  Status: Downloaded newer image for postgres:13.11
  docker.io/library/postgres:13.11
  ```

### ▶️ Create & Run a Container (also pulls if the image is unavailable)
  ```sh
  docker run mongo
  docker run -d mongo                                     # Detached mode
  docker run --name my-mongo -d mongo                     # With a custom name
  docker run --name my-mongo2 -p 1000:27017 -d mongo      # With port mapping (host:container)
  ```
  **Output (Example for running MongoDB):**
  ```sh
  5d24dd50fef45a1b0015c86b9516b1ce5f76e69b832fbea5ff1c56ac4c735f2e
  ```

### 🔍 Check Running Containers  
  ```sh
  docker ps
  docker container ls
  ```
  **Output:**
  ```sh
CONTAINER ID   IMAGE     COMMAND                  CREATED             STATUS          PORTS                      NAMES
b2d25d50948b   mongo     "docker-entrypoint.s…"   About an hour ago   Up 10 seconds   0.0.0.0:27017->27017/tcp   mongodb

  ```

### 🛑 Stop a Running Container  
  ```sh
  docker stop my-mongo
  docker container stop my-mongo
  docker container stop 715d3aa51297  # Using container ID
  ```
  **Output:**
  ```sh
  my-mongo
  ```

### ▶️ Start a Stopped Container (If not deleted)
  ```sh
  docker start my-mongo
  ```
  **Output:**
  ```sh
  my-mongo
  ```

### 📜 Check Logs of a Container  
  ```sh
  docker logs my-mongo
  ```
  **Output (Example):**
  ```sh
{"t":{"$date":"2025-02-05T12:01:22.146+00:00"},"s":"I",  "c":"CONTROL",  "id":4615611, "ctx":"initandlisten","msg":"MongoDB starting","attr":{"pid":1,"port":27017,"dbPath":"/data/db","architecture":"64-bit","host":"b2d25d50948b"}}
  ```

### 🗑 Remove All Containers  
  ```sh
  docker container prune
  ```
  **Output:**
  ```sh
  WARNING! This will remove all stopped containers.
  Are you sure you want to continue? [y/N] y
  ```

### 📷 Check Available Images  
  ```sh
  docker image ls
  ```
  **Output:**
  ```sh
    REPOSITORY      TAG       IMAGE ID       CREATED         SIZE
    redis           latest    844a9bb4b428   4 weeks ago     173MB
    mongo           latest    b5725ac74a0d   2 months ago    1.18GB
    postgres        latest    21d245004053   2 months ago    614MB
    postgres        13.18     594b9b109a87   2 months ago    593MB
    mongo-express   latest    1b23d7976f02   11 months ago   286MB
  ```

### 📂 Check Docker Volumes (Storage for container data)
  ```sh
  docker volume ls
  ```
  **Output:**
  ```sh
    DRIVER    VOLUME NAME
    local     docker_compose_mongo-express-data
  ```

---

## Day 2️⃣ - Networking & Environment Variables

### 🌐 Create a Docker Network  
  ```sh
  docker network create mongo-network
  ```
  **Output:**
  ```sh
  9c8a1d2b3f4e5g6h7i8j9k
  ```

### 🏗 Running MongoDB with Environment Variables  
  ```sh
  docker run --name mongodb -p 27017:27017 -e MONGO_INITDB_ROOT_USERNAME=admin -e MONGO_INITDB_ROOT_PASSWORD=password --net mongo-network -d mongo
  ```
  **Output:**
  ```sh
  7e9f0a1b2c3d4e5f6g7h8i9j
  ```

### 🔍 Check Networks  
  ```sh
  docker network ls
  ```
  **Output:**
  ```sh
  NETWORK ID     NAME            DRIVER    SCOPE
  1a2b3c4d5e6f   mongo-network   bridge    local
  ```

### 🖥️ Connecting Mongo-Express with MongoDB for UI-based DB Control  
  ```sh
  docker run -d -p 8081:8081 -e ME_CONFIG_MONGODB_ADMINUSERNAME=admin -e ME_CONFIG_MONGODB_ADMINPASSWORD=password -e ME_CONFIG_MONGODB_SERVER=mongodb --network mongo-network --name mongo-express mongo-express
  ```
  **Output:**
  ```sh
  8h7g6f5e4d3c2b1a
  ```

---

## Day 3️⃣ - Docker Compose

### 🏗 Docker Compose Automatically Creates a Network  

### ▶️ Run a `docker-compose.yml` File  
  ```sh
  docker-compose -f ./first.yaml up
  ```
  **Output:**
  ```sh
  ✔ Container docker_compose-mongodb-1    Created  
  ✔ Container docker_compose-mongo-express-1  Created
  ......
  mongo-express-1  | Server is open to allow connections from anyone (0.0.0.0)
  mongo-express-1  | basicAuth credentials are "admin:pass", it is recommended you change this in your config.js!
  ```

### 🗑 Remove All Services & Containers from Compose  
  ```sh
  docker-compose -f ./first.yaml down
  ```
  **Output:**
  ```sh
[+] Running 3/3
 ✔ Container docker_compose-mongo-express-1  Removed                                            
 ✔ Container docker_compose-mongodb-1        Removed                                            
 ✔ Network docker_compose_default            Removed 
  ```

start codes ..........
## Day 4️⃣ - Docker Build 🏗

### 🏗 Docker Build creates a Docker image of a project.

### 📜 Create a `Dockerfile` in the project directory (e.g., `/docker_flask/Dockerfile`)
```dockerfile
FROM python:3-alpine3.11  
WORKDIR /
# Copy source code to destination
COPY . /app
RUN pip install --no-cache-dir -r requirements.txt
EXPOSE 3000
CMD ["python", "main.py"]
```

---

### ▶️ Build a Docker Image where the `Dockerfile` exists
```sh
docker build -t mallickboy/flask:0.0.1.RELEASE .
```
**Output:**
```sh
[+] Building 15.2s (10/10) FINISHED 
 => [internal] load build definition from Dockerfile                                   0.0s
 => => transferring dockerfile: 184B                                                   0.0s
......
View build details: docker-desktop://dashboard/build/desktop-linux/desktop-linux/3wg1zmulf0mmi61pzf393cgfd
```

---

### ▶️ Run the Container and Start the Application
```sh
docker container run -d -p 4000:3000 --name mallickboy_flask mallickboy/flask:0.0.1.RELEASE
```

---

### 📤 Push the Image to DockerHub  
```sh
docker container stop mallickboy_flask
docker push mallickboy/flask:0.0.1.RELEASE
```

---

### 📥 Pull the Image from DockerHub (on another system or fresh setup)
```sh
docker pull mallickboy/flask:0.0.1.RELEASE
```
end ..........


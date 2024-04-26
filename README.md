# Travel Memory With Network

## Table of Contents

- [Introduction](#introduction)
- [Environment Setup](#environment-setup)
- [Dockerization](#dockerization)
- [Networking](#networking)
- [Usage](#usage)
- [Screenshots](#screenshots)
- [Docker Networking Commands](#docker-networking-commands)
- [References](#references)

---

## Introduction

Travel Memory is a web application designed to help users document their travel experiences. It allows users to create trips, add trip details, and share their travel stories with others. This README provides instructions on setting up the application environment, Dockerizing the backend and frontend, managing networking, and running the application.

---

## Environment Setup

To set up the application environment, follow these steps:

1. Create a `.env` file in the backend folder and add the following variables:
    ```bash
    MONGO_URI='ENTER_YOUR_URL'
    PORT=3000
    ```

2. Create the `tripdetails` collection in MongoDB and insert the following JSON data:
    ```json
    {
        "tripName": "Incredible India",
        "startDateOfJourney": "19-03-2022",
        "endDateOfJourney": "27-03-2022",
        "nameOfHotels": "Hotel Namaste, Backpackers Club",
        "placesVisited": "Delhi, Kolkata, Chennai, Mumbai",
        "totalCost": 800000,
        "tripType": "leisure",
        "experience": "Lorem Ipsum, Lorem Ipsum, ...",
        "image": "https://t3.ftcdn.net/jpg/03/04/85/26/360_F_304852693_nSOn9KvUgafgvZ6wM0CNaULYUa7xXBkA.jpg",
        "shortDescription": "India is a wonderful country with rich culture and good people.",
        "featured": true
    }
    ```

---

## Dockerization

### Backend Dockerfile

```Dockerfile
# Dockerfile for the backend service
FROM node:18

# Set working directory
WORKDIR /app

# Copy package.json and package-lock.json
COPY package*.json ./

# Install dependencies
RUN npm install

# Copy remaining source code
COPY . .

# Expose port
EXPOSE 3001

# Command to run the application
CMD ["npm", "start"]
```

### To build the backend Docker image, run:

```bash
docker build -t travel_memory_be .
```

### Frontend Dockerfile

```Dockerfile
# Dockerfile for the frontend service
FROM node:18

# Set working directory
WORKDIR /app

# Copy package.json and package-lock.json
COPY package*.json ./

# Install dependencies
RUN npm install

# Copy remaining source code
COPY . .

# Expose port
EXPOSE 3000

# Command to run the application
CMD ["npm", "start"]
```

### To build the frontend Docker image, run:

```bash
docker build -t travel_memory .
```

---

## Networking

### To create a network for MongoDB, backend, and frontend services, use the following command:

```bash
docker network create travelmemory-network
docker network inspect travelmemory-network
```

---

## Usage

### **Run MongoDB Server Inside Private Network**

```bash
docker run -dp 27018:27017 --network travelmemory-network -e MONGO_INITDB_ROOT_USERNAME=root -e MONGO_INITDB_ROOT_PASSWORD=root -v /data/db --name mongodb mongo:latest
docker exec -it mongodb /bin/bash
mongosh -u root -p root
use travelmemory
db.createUser({
    user: "root",
    pwd: "secret1234",
    roles: [{ role: "readWrite", db: "travelmemory" }]
});
db.createCollection("users")

```

### **Run Backend and Frontend Services**

```bash
docker run -dp 3001:3001 --network travelmemory-network --name travel_memory_be -e "PORT=3001" -e "MONGO_URI=mongodb://root:secret1234@mongodb:27017/travelmemory" travel_memory_be
docker run -dp 3002:3000 --network travelmemory-network --name travel_memory_fe travel_memory

```

### **Docker Compose**

```bash 
docker-compose up -d 
docker exec -it travelmemorydockernetwork-mongodb-1 /bin/bash
mongosh -u root -p root
use travelmemory
db.createUser({
    user: "root",
    pwd: "secret1234",
    roles: [{ role: "readWrite", db: "travelmemory" }]
});
db.createCollection("users")
docker-compose up -d 
docker-compose logs -f
docker-compose down
```

---

## Screenshots

![alt text](./screenshots/image.png)

![alt text](./screenshots/image-1.png)

![alt text](./screenshots/image-2.png)

![alt text](./screenshots/image-3.png)

![alt text](./screenshots/image-4.png)

![alt text](./screenshots/image-5.png)

![alt text](./screenshots/image-6.png)

---

## Docker Networking Commands

### Common Network Drivers

- **bridge:** Default network driver.
- **host:** Remove network isolation between the container and Docker host.
- **none:** Completely isolate a container.
- **overlay:** Connect multiple Docker daemons together.
- **ipvlan:** Provides full control over both IPv4 and IPv6 addressing.
- **macvlan:** Assign a MAC address to a container.

### Docker Networking Commands

- **`docker network ls`**: List available networks.
- **`docker inspect bridge <NETWORK_ID>`**: Inspect details of a specific network.
- **`hostname -I`**: Show the IP address of the host.
- **`docker network create <NETWORK_NAME>`**: Create a new network.
- **`docker network connect <NETWORK_NAME> <CONTAINER_NAME>`**: Connect a container to a network.
- **`docker start <CONTAINER_NAME>`**: Start a stopped container.

---

## References

- [Docker Networking Documentation](https://docs.docker.com/network/)
- [MongoDB Official Documentation](https://docs.mongodb.com/)

## Thank You!

Thank you for checking out our Travel Memory project! We hope you find it useful and enjoyable. If you have any questions, suggestions, or feedback, please don't hesitate to reach out. Your input is invaluable as we continue to improve the project.

Happy travels!
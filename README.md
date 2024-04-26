# Travel Memory

Added .env file in backend folder
`.env` file to work with the backend:

Set this two variable in .env file. URL contain your mongodb url string
```
MONGO_URI='ENTER_YOUR_URL'
PORT=3000
```

Create the tripdetails collection and put below json data insert
Data format to be added: 

```json
{
    "tripName": "Incredible India",
    "startDateOfJourney": "19-03-2022",
    "endDateOfJourney": "27-03-2022",
    "nameOfHotels":"Hotel Namaste, Backpackers Club",
    "placesVisited":"Delhi, Kolkata, Chennai, Mumbai",
    "totalCost": 800000,
    "tripType": "leisure",
    "experience": "Lorem Ipsum, Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum, ",
    "image": "https://t3.ftcdn.net/jpg/03/04/85/26/360_F_304852693_nSOn9KvUgafgvZ6wM0CNaULYUa7xXBkA.jpg",
    "shortDescription":"India is a wonderful country with rich culture and good people.",
    "featured": true
}
```

## Create Dockerfile for both Frontend and backend

- Backend Dockerfile

```bash
docker build -t travel_memory_be .
```

- Frontend Dockerfile

```bash
docker build -t travel_memory .
```

## Create Network for Mongo, backend and frontend

```bash
docker network inspect travelmemory-network
```

## Run Mongo Server Inside Private Network that created earlier

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
docker run -dp 3001:3001 --network travelmemory-network --name travel_memory_be -e "PORT=3001" -e "MONGO_URI=mongodb://root:secret1234@mongodb:27017/travelmemory" travel_memory_be
docker run -dp 3002:3000 --network travelmemory-network --name travel_memory_fe travel_memory
docker network inspect travelmemory-network
```

![alt text](./screenshots/image.png)

![alt text](./screenshots/image-1.png)

![alt text](./screenshots/image-2.png)

![alt text](./screenshots/image-3.png)

![alt text](./screenshots/image-4.png)

![alt text](./screenshots/image-5.png)

![alt text](./screenshots/image-6.png)

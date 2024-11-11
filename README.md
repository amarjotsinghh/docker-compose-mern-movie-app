
---

# Movies App.

## Introduction

This project is a MERN stack (MongoDB, Express.js, React.js, Node.js) application that allows users to manage a collection of movies. It supports full CRUD functionality, including creating, listing, updating, and deleting movie entries. 

This repository was built following the tutorial on how to develop a MERN application. For a step-by-step guide, you can check out the original tutorial [here](https://medium.com/@samarony.barros/how-to-create-your-first-mern-mongodb-express-js-react-js-and-node-js-stack-7e8b20463e66).

![MERN Stack](https://miro.medium.com/max/678/1*dqvlaszRLvoPmARpOlLN9A.png)

## Technologies Used

- **MongoDB** 4.0.4+
- **ExpressJS** 4.16.3+
- **ReactJS** 16.5.0+
- **NodeJS** 10.15.1+ (recommended) or 11.4.0+

## Setup and Installation

### Prerequisites

Ensure you have Docker and Docker Compose installed. If you’re deploying to an EC2 instance, configure your security group to open ports 80 (for the frontend) and 8000 (for the backend).

### Getting Started

1. **Clone the Repository**:
   ```bash
   git clone https://github.com/samaronybarros/movies-app.git
   cd movies-app
   ```

2. **Code Modifications**:

   Update configuration files to enable access on an EC2 instance:

   - **Frontend**: Update `client/src/api/index.js`:
     ```javascript
     const base_url = 'http://<instance_ip_address>:3000';
     ```

   - **Backend**: Update the MongoDB URI in `server/db/index.js` to:
     ```javascript
     mongoose.connect('mongodb://mongo:27017/movies-app', {
       useNewUrlParser: true,
       useUnifiedTopology: true,
     });
     ```

### Docker Deployment

1. **Docker Compose File**

   Ensure the `docker-compose.yml` file is correctly set up as shown below:

   ```yaml
   version: '3.8'

   services:
     frontend:
       build:
         context: ./client
       ports:
         - "80:80"
       networks:
         - movie_app_network
       depends_on:
         - backend

     backend:
       build:
         context: ./server
       ports:
         - "3000:3000"
       networks:
         - movie_app_network
       depends_on:
         - mongo

     mongo:
       image: mongo:latest
       container_name: mongo
       ports:
         - "27017:27017"
       volumes:
         - mongo_data:/data/db
       networks:
         - movie_app_network

   networks:
     movie_app_network:
       driver: bridge

   volumes:
     mongo_data:
   ```

2. **Dockerfiles**

   Ensure there are `Dockerfile` files in both `client` and `server` directories.

   - **Frontend Dockerfile (`movies-app/client/Dockerfile`)**:
     ```dockerfile
     FROM node:14 as build
     WORKDIR /app
     COPY package*.json ./
     RUN npm install
     COPY . .
     RUN npm run build
     FROM nginx:alpine
     COPY --from=build /app/build /usr/share/nginx/html
     EXPOSE 80
     CMD ["nginx", "-g", "daemon off;"]
     ```

   - **Backend Dockerfile (`movies-app/server/Dockerfile`)**:
     ```dockerfile
     FROM node:14
     WORKDIR /app
     COPY package*.json ./
     RUN npm install
     COPY . .
     EXPOSE 3000
     CMD ["npm", "start"]
     ```

3. **Run Docker Compose**:

   Start the application with Docker Compose:

   ```bash
   docker-compose up --build -d
   ```

   This will create containers for the frontend, backend, and MongoDB, and expose the necessary ports for external access.

### Usage

Once the containers are running, you can access the application:

- **Frontend**: [http://<instance_ip_address>](http://<instance_ip_address>)
- **Backend API** (optional for testing): [http://<instance_ip_address>:3000](http://<instance_ip_address>:3000)

### CRUD Operations

The application supports the following operations:

- **Create Movie**: Add a new movie entry.
- **List Movies**: View all movies.
- **Update Movie**: Edit an existing movie.
- **Delete Movie**: Remove a movie entry.

### Credits

This project was created based on a tutorial by [Samarony Barros](https://medium.com/@samarony.barros).

For more information, you can refer to the original [GitHub repository](https://github.com/samaronybarros/movies-app).

---

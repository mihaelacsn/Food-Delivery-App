# Food Delivery Platform
A platform that tends towards providing food directly from restaurants and directly to customer's door. 

<hr>

## Setup and Execution Instruction

After installing docker and cloning the repository, we should have (create) a ```.env``` file in each microservice directory with required environment variables such as database connection strings, ports, and JWT secrets. Build docker images using ```docker-compose build``` and run them with ```docker-compose up``` (or ```docker compose up --build```). We can verify that each of our services is running by accessing the URLs. The project can be stopped via ```docker compose down```.
<br>
Database and service connection credential in file should look like:
```
# User Profile Management Service
USER_PROFILE_DATABASE_URL=postgresql://your_username:your_password@postgres_users:5432/your_db

# Restaurant Catalog Service
RESTAURANT_DATABASE_URL=mongodb://your_username:your_password@mongo_restaurants:27017/your_db

# Order Status & Tracking Service
ORDER_DATABASE_URL=neo4j://your_username:your_password@neo4j_orders:7687/your_db

# RabbitMQ Configuration
RABBITMQ_USER=your_username
RABBITMQ_PASS=your_password

# Other service-specific variables
JWT_SECRET=your_jwt_secret_key
```

<hr>

### Testing and Accessing the Endpoints

_**Endpoint Interaction Rules**_

1. User Creation: A user profile must be created before placing an order.
2. Order Creation: Orders can only be placed if the user is authenticated and the corresponding restaurant exists in the catalog.
3. WebSocket Connections: To connect to order tracking via WebSocket, the order must be created first.

**Order of Access:**

  _Step 1_: Register a user (```POST /register``` in User Profile Management Service). <br>
  _Step 2_: Login to retrieve JWT token (```POST /login``` in User Profile Management Service). <br>
  _Step 3_: Access restaurant catalog (```GET /restaurants``` in Restaurant Catalog Service). <br>
  _Step 4_: Place an order (```POST /orders``` in Order Status & Tracking Service). <br>

**Testing Endpoints:**

  1. (can/should) Use tools like Postman or cURL to test HTTP endpoints. Then,
  2. Ensure the JWT token from ```/login``` is included in the Authorization header for secure endpoints.

**Testing WebSockets:**

  1. Use WebSocket clients like wscat or Postman to test WebSocket connections.
  2. Open connections to ```/restaurants-updates``` and ```/order-tracking/{orderId}``` to verify real-time data.

### Running the Docker Tests
1. In the project root, the docker image is created using ```docker-compose build```.
2. Starting the services/running the containers with ```docker-compose up```.
3. To access the containers (to run tests), run unit and integration tests via: <br> ```docker exec -it <service-container-name> /bin/sh <br> #Inside the container <br> pytest```
4. Once all the tests are complete, the containers can be stopped and removed using ```docker-compose down```.
5. The data volumes should be configured in ```docker-compose.yml``` if needed to persist data across container restarts.

<hr>

## Application Suitability

Reasons why this platform is a good idea to be implemented through distributed systems is represented by a list of advantages in using this computing environment.

1. **Scalability**: Because the platform should handle the increasing number of restaurants and their menus, as much as the growing numbers of users and their orders. This characteristic will allow the platform to scale up efficiently, distributing the load across multiple servers and preventing bottlenecks. <br><br>
2. **Real-time Data Processing**: The platform should give the possibility for data updates about restaurants informations (such as addresses, work program, any menu uodates) and also about delivery status. So, we need this distributed systems benefit - real-time updates by spreading the workload across multiple nodes. <br><br>
3. **High Availability**: Meaning we need to be sure, even if a server fails, others can take over and can provide redundancy and minimizing downtime. For example, if a server handling customer payments goes down, users can still browse restaurant menus and prepare orders because these functions are managed by separate services. Once the payment service is restored, customers can finalize their orders without any interruptions.<br><br>
4. **Performance and Speed**: Since no one likes to wait more than a few seconds, the platform should process requests quickly. A distributed system enables parallel processing, reducing latency and ensuring a seamless user experience. <br><br>
5. **Geographical Distribution**: The food delivery platform should have the goal to ensure that users in different regions experience fast, reliable service by distributing resources and servers across various locations. During peak times in one city, the system may experience heavy loads, slowing down order processing or restaurant browsing. To solve this, you can set up geographically distributed servers and databases to ensure each region has its own local resources. <br><br>
6. **Fault Tolerance and Reliability**: Distributed systems can recover quickly from failures by having backups and redundant components, ensuring that critical services (like delivery payments or user authentication) remain unaffected by partial failures.<br><br>

### Some (already done) examples where Distributed System (and their microservices) make the show:
1. Spotify: Spotify uses microservices to manage its vast library of music, playlists, user preferences, and social interactions. Each function (e.g., recommendation engine, search, playback, playlist management) is handled by independent microservices, allowing them to be developed and updated separately.
   * Scalability: As Spotify grew, microservices allowed it to scale quickly to serve millions of users.
   * Fault Tolerance: If one microservice (e.g., recommendations) goes down, others (e.g., music playback) can continue working.
   * Continuous Deployment: New features and updates can be released independently without affecting the entire platform.<br><br>
2. HBO MAX: They use microservices to support streaming, user authentication, content recommendation, and billing. Each service operates independently, which allows them to update individual features or fix bugs without bringing the entire platform down.
   * High Availability: Streaming continues uninterrupted, even if services like recommendations or payment processing experience issues.
   * Flexibility: HBO Max can roll out updates to its streaming service without affecting the user login or content recommendation services.
<hr>

## Service Boundaries

+ ### Service 1: User Profile Management

The User Profile Management service has the role of administering processes such as: Authentication, LogIn, Registration, or directing other user account details.

1. **Authentication** - for verifying a user's identity by checking credentials like a username and password.
2. **LogIn** - for entering the platform by providing credentials mentioned in Authentication. Successful login grants the user access to their account.
3. **Registration** (for first time users) - creating a new account on a platform by providing personal details (as examples might be the First Name, Last Name, phone number, email address, birth date.
   
+ ### Service 2: The Catalogue with Restaurants & (each restaurants') Menu

Provides a list of available restaurants, and for each of them the menu. For each Restaurant it is presented a short description and for the food, the ingridients list, prices. The list of places and menu can also be updated.
 Here, in The Catalog (Restaurant & Menu), we integrate with Tasty API to enhance the menu and restaurant data. The Tasty API provides access to a vast collection of recipes, dishes, and food-related content that can enrich the user experience on our platform.
 When a restaurant lists a particular dish, our system will query the Tasty API to retrieve relevant information, including:
  * Recipe ingredients.
  * Cooking steps (if applicable).
  *  Nutritional breakdown (calories, protein, fat, etc.).
  *  Related videos or media content.
 
+ ### Service 3: Order Status & Tracking

Has the responsability to give live information about the order status, with a feedback-type messagess, such as "The Order was received", "Your food is preparing", "The delivery was picked up", "The delivery is on it's way".

![alt text](https://github.com/mihaelacsn/Food-Delivery-App/blob/main/img/PAD_2ndDiagram.drawio(2).png)
<br><br>
<hr>
Various services connect through an API gateway, which takes care of load balancing and directs requests to the right services. It also handles user authentication and authorization, providing a token ID for ongoing communication. Services like user registration, order management, and payment processing rely on transactional databases. The system will utilize a relational database that can efficiently handle multiple orders and users at the same time. Details about restaurants, menus, prices, and promotions will be stored in ElasticSearch, a JSON document storage solution that offers quick and scalable search capabilities, making it easy for customers to find menus and cuisines. The ordering process involves choosing dishes, calculating costs, and processing payments through various payment gateways. After an order is placed, the details are sent to a central message queue, like RabbitMQ or Redis Queue. The order processing unit picks up the order details, alerts the chosen restaurant, and looks for nearby delivery partners. Customers get push notifications about their orders and can track the status and live location of the delivery person through the order processing and tracking service. The delivery person then collects the order and brings it to the customer, providing real-time updates on the estimated arrival time.<br><br>
The admin can quickly add new restaurants by entering details like the restaurant name, city, address, postal code, type of cuisine, hours of operation, owner info, and payment splits. All this data gets saved in a relational database. <br>

After a restaurant is added, the system creates a unique ID for it, which is then used in Elasticsearch to keep track of things like menus, prices, and prep times. Restaurants have the ability to update, add, or remove their menus, prices, and preparation times whenever they need to.<br>

When customers want to find food options, they can search by dish name, restaurant name, or location, and Elasticsearch handles the query. Elasticsearch is a powerful, scalable open-source search and analytics engine that allows for fast and efficient searching through large amounts of data almost instantly. This makes it super easy for customers to discover the restaurants and dishes they want.<br>

<hr>

The new features to add:
1. _Loyalty and Rewards Program_: a system to reward users for frequent orders, giving points, discounts, or free delivery based on the number of orders or spending level. It could be integrated as a new microservice to handle reward logic and point calculations independently, especially if it requires tracking complex interactions across the platform. Another way it could be integrated within the User Profile Management Service, which can keep track of user points and redeemable rewards in the user profile.

<br>

**Endpoint 1: Check Loyalty Points** - retrieves the loyalty points accumulated by the user.
* Method: GET
* URL: /api/rewards/{userId}/points
* Path Parameters: ``` userId```: ID of the user.
* Response Format: json

   ```
   {
        "userId": "string",
        "points": "integer"
   }
  ```
**Endpoint 2: Redeem Points** - redeems loyalty points to apply a discount on the user’s next order.
* Method: POST
* URL: /api/rewards/redeem
* Request Body: json

```
{

    "userId": "string",
    "pointsToRedeem": "integer"
}
```
* Response Format: json

```
{
    "userId": "string",
    "pointsRedeemed": "integer",
    "newPointsBalance": "integer"
}
```
2. _Review and Rating Service_: service that allows users to review and rate restaurants and menu items, which can be useful for recommendations.

<br>

* Base URL: /api/reviews
  
<br>

**Endpoint 1: Add Review for a Restaurant** - adds a review for a specific restaurant.
* Method: POST
* URL: /api/reviews/restaurant/{restaurantId}
* Path Parameters: ```restaurantId```: ID of the restaurant to be reviewed.
* Request Body: json

```
{
    "userId": "string",
    "rating": "integer", // Range from 1-5
    "review": "string"
}
```
Response Format:

json

    {
        "reviewId": "string",
        "status": "string"
    }

**Endpoint 2: Fetch Restaurant Reviews** - retrieves all reviews for a specific restaurant.
* Method: GET
* URL: /api/reviews/restaurant/{restaurantId}
* Path Parameters: ```restaurantId```: ID of the restaurant.
* Response Format: json

```
[
    {
        "userId": "string",
        "rating": "integer",
        "review": "string",
        "timestamp": "string"
    }
]
```
## Technology Stack and Communication Patterns

![alt text](https://github.com/mihaelacsn/Food-Delivery-App/blob/main/img/export(2).png)
<hr>

## Data Management Design
1. ### **User’s Profile Management Service:** - uses a relational SQL database (PostgreSQL) for structured, normalized data storage(with users' data). <br>
   ->Data Format: Request/Response: JSON. Data Storage: SQL database with a structured schema.
   * **EndPoints:**
     - POST /register:
       * *Request:*
    ```
    {
      "username": "string",
      "email": "string",
      "password": "string",
      "phone": "string"
    }
    ```
      * *Response:*
     ```
     {
      "userId": "string",
      "message": "User successfully registered"
    }
    ```
     - POST /login (authenticates a user and returns an authentication token).
       * *Request:*
    ```
    {
    "email": "string",
    "password": "string"
    }
    ```
      * *Response:*
     ```
     {
      "token": "jwt_token",
      "expiresIn": "3600"
    }
    ```
     - GET /profile/{userId} (fetches the user profile details).
       * No request body.
       * *Response:*
     ```
     {
      "userId": "string",
      "username": "string",
      "email": "string",
      "phone": "string",
      "address": "string"
      }
    ```
2. ### **The Catalog Service (Restaurant & Menu):**<br>
  ->Data Format: Request/Response: JSON for RESTful APIs, WebSocket for real-time updates. Data Storage: NoSQL database (document-based) to store flexible restaurant and menu data.
  * **EndPoints:**
     - GET /restaurants (returns a list of all available restaurants):
       * *Request:* no request body
   
      * *Response:*
     ```
     {
    "restaurantId": "string",
    "name": "string",
    "location": "string",
    "cuisine": "string"
    }
    ```
     - GET /restaurant/{restaurantId}/menu-with-details :
       * *Request:* no request body.
      * *Response:*
     ```
     [
      {
        "menuItemId": "string",
        "name": "string",
        "price": "number",
        "description": "string",
        "tastyDetails": {
          "recipeId": "string",
          "ingredients": ["ingredient1", "ingredient2"],
          "cookingSteps": ["step1", "step2"],
          "nutrition": {
            "calories": "200",
            "protein": "10g",
            "fat": "5g"
          },
          "media": {
            "imageUrl": "url",
            "videoUrl": "url"
          }
        }
      }
    ]
    ```
     - WebSocket /restaurants-updates (subscribes clients to real-time updates for restaurant or menu changes):
       * *Request:* WebSocket connection, no payload required.
       * *Response:*
     ```
     {
      "eventType": "menuUpdate",
      "restaurantId": "string",
      "menuItems": [
        {
          "menuItemId": "string",
          "name": "string",
          "price": "number",
          "description": "string"
       }
       ]
      }
    ```
3. ### **Order Status + Tracking Service:**
   ->Data Format: Request/Response: JSON for RESTful APIs, WebSocket for real-time tracking, and Messaging Queue for asynchronous updates. Data Storage: Graph NoSQL database (e.g., Neo4j) to model complex relationships like user-to-restaurant-to-order-to-delivery.
   * **EndPoints:**
     - POST /orders (creates new order):
       * *Request:*
    ```
    {
      "userId": "string",
      "restaurantId": "string",
      "items": [
        {
          "menuItemId": "string",
          "quantity": "number"
        }
      ],
      "deliveryAddress": "string"
    }
    ```
      * *Response:*
     ```
     {
      "orderId": "string",
      "status": "Received",
      "estimatedDeliveryTime": "30 mins"
     }
    ```
     - GET /order/{orderId}/status (fetches the current status of the order):
       * *Request:* No request body.
      * *Response:*
     ```
     {
      "orderId": "string",
      "status": "Preparing",
      "estimatedDeliveryTime": "30 mins",
      "deliveryDriver": {
        "driverId": "string",
        "name": "string",
        "location": {
          "latitude": "number",
          "longitude": "number"
        }
      }
    }
    ```
     - WebSocket /order-tracking/{orderId}
       * Request: WebSocket connection.
       * *Response:*
     ```
     {
       "orderId": "string",
       "status": "On the Way",
       "location": {
       "latitude": "number",
       "longitude": "number"
        }
      }
    ```
     - Messaging Queue (RabbitMQ, push a message to the queue when the order status changes):
   ```
   {
      "orderId": "string",
      "status": "Delivered",
      "deliveryTime": "20:45"
    }
   ```
<hr>

## Deployment and Scaling Set Up
  1. ***Containerization (Docker)***
    * Dockerize Each Microservice: Each microservice will have its own Dockerfile that defines the environment, dependencies, and code required to run. This ensures consistency across different environments.
    * For User Profile Management, we will include security measures for user authentication.
    * For Catalogue Service, we will ensure efficient data querying, especially with a graph database.
    * For Order Status & Tracking, we will make sure the service can handle real-time updates.
  2. ***Service Discovery & Networking***
    * API Gateway: e.g., NGINX, Kong, or AWS API Gateway, for routing requests between the client and microservices. It can also handle load balancing, caching, and security.
    * Service Mesh: Istio or Linkerd, to manage microservice communication, provide observability, and enforce security.
  3. ***Load Balancing & Scaling***
      * Horizontal Scaling: Each microservice can scale independently.We will use Kubernetes’ Horizontal Pod Autoscaler to scale based on traffic or resource utilization. This ensures that during high traffic (like meal times for food delivery), your system can handle more requests.
      * Vertical Scaling: Adjusting the resources (CPU, memory) for individual containers if they need more processing power.
  4. ***Database Scaling & Management***
        * For User Profile Management, we will ensure our authentication and registration process can handle scaling with a high number of users.
        * The Catalogue Service will be used with a Document type database, therefore each restaurant being stored as a separate document with nested arrays for the menu. As the list of the restaurants gets larger, sharding would be considered to horizontally scale the database.
        * The Order Status & Tracking Service should be optimized for real-time updates. Using a message broker (like RabbitMQ or Kafka) between microservices could handle asynchronous communication and data streams for live order updates.

<hr>

## Sources for Documentation

1. https://www.splunk.com/en_us/blog/learn/distributed-systems.html
2. https://www.linkedin.com/pulse/architecture-design-principle-online-food-delivery-system-sharma

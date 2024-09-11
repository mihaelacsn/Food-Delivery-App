# Food Delivery Platform
A platform that tends towards providing food directly from restaurants and directly to customer's door. 

<hr>

## Application Suitability

Reasons why this platform is a good idea to be implemented through distributed systems is represented by a list of advantages in using this computing environment.

1. Scalability: Because the platform should handle the increasing number of restaurants and their menus, as much as the growing numbers of users and their orders. This characteristic will allow the platform to scale up efficiently, distributing the load across multiple servers and preventing bottlenecks. <br>
2. Real-time Data Processing: The platform should give the possibility for data updates about restaurants informations (such as addresses, work program, any menu uodates) and also about delivery status. So, we need this distributed systems benefit - real-time updates by spreading the workload across multiple nodes. <br>
3. High Availability: Meaning we need to be sure, even if a server fails, others can take over and can provide redundancy and minimizing downtime. For example, if a server handling customer payments goes down, users can still browse restaurant menus and prepare orders because these functions are managed by separate services. Once the payment service is restored, customers can finalize their orders without any interruptions.<br>
4. Performance and Speed: Since no one likes to wait more than a few seconds, the platform should process requests quickly. A distributed system enables parallel processing, reducing latency and ensuring a seamless user experience. <br>
5. Geographical Distribution: The food delivery platform should have the goal to ensure that users in different regions experience fast, reliable service by distributing resources and servers across various locations. During peak times in one city, the system may experience heavy loads, slowing down order processing or restaurant browsing. To solve this, you can set up geographically distributed servers and databases to ensure each region has its own local resources. <br>
6. Fault Tolerance and Reliability: Distributed systems can recover quickly from failures by having backups and redundant components, ensuring that critical services (like delivery payments or user authentication) remain unaffected by partial failures.<br>

### Some (already done) examples where Distributed System (and their microservices) make the show:
1. Spotify: Spotify uses microservices to manage its vast library of music, playlists, user preferences, and social interactions. Each function (e.g., recommendation engine, search, playback, playlist management) is handled by independent microservices, allowing them to be developed and updated separately.
   * Scalability: As Spotify grew, microservices allowed it to scale quickly to serve millions of users.
   * Fault Tolerance: If one microservice (e.g., recommendations) goes down, others (e.g., music playback) can continue working.
   * Continuous Deployment: New features and updates can be released independently without affecting the entire platform.
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
 
+ ### Service 3: Order Status & Tracking

Has the responsability to give live information about the order status, with a feedback-type messagess, such as "The Order was received", "Your food is preparing", "The delivery was picked up", "The delivery is on it's way".

<hr>

## Technology Stack and Communication Patterns

<hr>

## Data Management Design

<hr>

## Deployment and Scaling Set Up

<hr>

## Sources for Documentation

1. https://www.splunk.com/en_us/blog/learn/distributed-systems.html
2. 

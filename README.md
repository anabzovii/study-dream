# Dream and Study

I want to create a platform were students or people can study video courses of different content.

## Microservice Architecture Suitability

I think that video courses platform is a good fit for microservice architecture because of some reasons:

I. Extensibility: The platform consists of several components, each with unique roles. This complexity suits a microservices architecture, allowing individual services to be developed, tested, and maintained separately, promoting better organization and clear separation of responsibilities.

II. Speed and Flexibility in Development:: Microservices support independent development, testing, and deployment for each service, enhancing modularity. This approach shortens development cycles and increases flexibility in responding to changes or introducing new features, improving overall agility.

III. Scaling: Various components of the application may have different scaling requirements. For example, the Courses Service might need to handle large data volumes, while the Students Service could face less demand. Microservices architecture enables each service to scale independently according to its specific needs, leading to more efficient resource allocation.

Similar approach we can see at:
1. Udemy: One of the largest platforms for online courses, offering thousands of video courses on a wide range of subjects. Udemy uses microservices to handle different features like video streaming, course management, user management, and payment services. This allows them to scale specific services independently based on user demand. Microservices enable Udemy to deploy updates and new features for one part of the platform without affecting other parts.
2. Coursera: A popular online learning platform offering courses, specializations, and degrees from universities and institutions. Coursera's architecture is broken into multiple microservices like authentication, video content delivery, grading, and discussion forums. Microservice-based architecture allows Coursera to isolate issues within specific services (e.g., if the video service has an issue, other features like user accounts remain unaffected).
3. Pluralsight: A tech-focused video learning platform offering training for developers, IT professionals, and creative experts. Pluralsight utilizes microservices to handle course delivery, learning analytics, assessments, and personalized recommendations. The microservice architecture enables Pluralsight to perform continuous integration and deployment, releasing new features or updates without downtimes.
## Service Boundaries:
* Login Students service will contain student authentication, session management, it will be based JWT Bearer.
* Courses Service there will be published, updated, saw and separated the courses.
![image](https://github.com/user-attachments/assets/bf33e404-e027-4de3-a31f-07639f817955)



## Technology Stack and Communication Patterns:
* Asynchronous WebSockets enable real-time, two-way communication for events such as notifications.
* Synchronous: gRPC provides efficient, low-latency communication between services, while HTTP/REST is used for client-facing APIs, following a synchronous request-response model over HTTP.
* Java/Spring Framework: develop 2 microservices that will use REST.
* Python: develop the API gateway and discovery service.
* Docker: containerize services, providing scalability, isolation, and simplified deployment.
* Oracle DB: database management and storage.
* Redis for cache storage.
* Spring Security/JWT login and authorization.

## Explanation
* Gateway: The API Gateway acts as a single entry point for all the client requests. In your case, it will handle requests from students or course owners and route them to the appropriate microservice (e.g., Login Service, Courses Service).
* Cache: will store frequently accessed data like course details and user profiles, reducing the need for repeated database queries. This will significantly improve response times and enhance the user experience, especially for static or infrequently updated content.
* Service Discovery: Service Discovery will allow microservices to dynamically discover the addresses of other services, enabling communication between services without hardcoding endpoints. For example, the Login Service can discover the Course Service without needing to know its exact IP address. This is useful for scaling, as services can be added or removed without manual updates.
* Status Endpoints: provide health checks for the Gateway and Service Discovery systems, allowing you to monitor the health and availability of services. These endpoints typically return status codes like HTTP 200 for healthy services and HTTP 500 when a service is down, helping ensure system reliability.
* Load Balancing: In Round Robin Load Balancing, requests are distributed evenly across multiple replicas of a service. This would mean if you have 4 instances of the Course Service, the Gateway will forward each request to the next instance in line. This ensures that no single instance is overwhelmed with too many requests.
* Circuit Breaker: If a certain number of consecutive failures (like 3 errors) occur within a given timeout limit, the circuit breaker will "trip" and prevent further calls to that service. This could mean that if the Course Service is down, the Login Service will stop sending requests to it until it recovers.
* Unit Testing: For example, write tests for the Login Service to verify that user authentication, JWT generation, and role-based access work properly. Tests would simulate different scenarios, ensuring that each microservice behaves correctly in isolation.
* gRPC: microservices will need to communicate with each other. Instead of using REST over HTTP, which can introduce additional overhead, gRPC offers a more efficient, low-latency alternative for service-to-service communication.
* Service Load: will allow me to efficiently distribute requests to the various microservices, particularly the ones handling high-traffic operations like the Courses Service and Student Service.
* Circuit Breaker, Remove Service: will automatically remove a failing service, like the Courses Service, from the available pool after repeated failures, preventing further requests from reaching it. This ensures the system remains stable, and fallback mechanisms can provide alternative responses or notifications until the service recovers.

## Design Data Management:
### User Login Service
- POST /api/user/signup
  Request
      
      {
      "username": "name",
      "password": "password",
      "email": "email",
      "roles": "STUDENT/OWNER"
      }

Response

    {
    "jwt": "123"
    }


- POST /api/users/login

  Request

      {
      "username": "john",
      "password": "password"
      }

  Response

      {
       "jwt": "123"
      }


Owner Requests:

* POST /api/course

Request:

    Header:{"jwt": "1234"}
    {
    "courseName": "Strategy",
    "courseInfo": "text",
    "courseCathegory": "Programming",
    "price": "123"
    }


Response:

    {
    "response": "Course was created successfully."
    }


* DELETE /api/course/{courseId}

  Request:

        Header:{"jwt": "1234"}
Response:

        { 
            "message": "Course qwerty deleted successfully." 
        }


* GET /api/courses
  Request:

        Header:{"jwt": "1234"}
Response:

        [
            { 
                "courseId": "qwerty", 
                "ownerId": "123",
            }
        ]

Lobby Mechanism Endpoints:

* api/student-service/course/subscribe

Client Request:

        Header:{"jwt": "1234"}
        {
            "cathegory": "Programing", 
            "userId": "123" 
        }


Response:

        { 
            "status": "success", 
            "courseId": "123", 
            "message": "Joined course." 
        }

* gRPC /courses/{courseId}/buy

Request:

        { 
            "userId": "123", 
            "courseId": "123" 
        }


Response:

        { 
            "message": "Student bought course using gRPC." 
        }


* Course Service Endpoints:  


## WebSocket 

- courses.com/courses/{ownerId}

Request:

       Header:
       {
       "jwt": "1234"
       }


Response:

       [
       {
       "courseId": "123",
       "name": "how to ...",
       "Description": "description",
       "price" : 123 
       } 
       ]

  
 courses.com/courses
  
 Request:

       Header:
       {
       "jwt": "1234"
       }


Response:

       [
       {
       "courseId": "123",
       "name": "how to ...",
       "Description": "description",
       "price" : 123 
       } 
       ]

  

## Deployment and Scaling:
Services will be deployed in Docker containers, providing isolation for each and enabling independent scaling. Both services will be horizontally scaled to enhance performance and reliability by distributing the load across multiple containers, ensuring the system can manage increased traffic and improve availability. Docker Compose will be used to streamline the deployment process by managing multi-container configurations, simplifying network, volume, and scalability settings for each service.

## New Features
I'll use a strong microservices architecture in my project to guarantee high availability, dependability, and effective data management. I'll utilize load balancers, failover techniques, and a circuit breaker to prevent overloading and promptly identify failing services in order to maintain service high availability. Consistent hashing will guarantee a balanced distribution of data for caching, and cache high availability will offer redundancy to guarantee quick and continuous data access. 

I'll use Long-Running Saga Transactions for intricate workflows and Two-Phase Commits for transactional consistency while managing complicated, dispersed activities. In the event of database problems, the system can smoothly transition to a backup thanks to the redundancy added by database replication with failover. For centralized logging and analysis, I'll utilize the ELK Stack (Elasticsearch, Logstash, and Kibana), which makes it simple to find mistakes or performance bottlenecks. 

Lastly, historical data will be stored in a data warehouse, allowing for in-depth analysis for insights and decision-making. When combined, these technologies produce a scalable, robust infrastructure that can handle demanding applications.


## Running and Testing App

docker-compose docker-compose.yml up --build  This command run docker-compose.yml file that dockerize gateway, service-discovery and my 2 microcervices with their DB.
Run docker-compose docker-compose.yml up --build  This command run docker-compose.yml file that dockerize gateway, service-discovery and my 2 microcervices with their DB.

I. First of all we will run status endpoints to confirm that app is working

II. After that we will create our first element, in our case a video.

III. After creating a video we need to create a user and asign video id to user object. This is our second microservice.

IV. For confirmation that both microservices work we will request video information using new user id.
## New Diagram
![PAD Lab_1](https://github.com/user-attachments/assets/510ae890-5f86-4a35-ba40-0131c8eb884e)

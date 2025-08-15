# 🏗️ System Design Interview Questions

A comprehensive list of commonly asked system design questions in technical interviews for backend, full-stack, and software engineering roles.

---

## 🔧 Fundamentals & Concepts

1. What is system design? What are the key goals when designing a scalable system?

2. What is the difference between vertical and horizontal scaling?

3. What is load balancing? What are common algorithms (e.g., round-robin, least connections)?

4. What is a reverse proxy? How does it differ from a forward proxy?

5. What is caching? Where can you implement it (client, CDN, server, database)?

6. What is a CDN? How does it improve performance?

7. What is database replication? What are master-slave and master-master setups?

8. What is database sharding? How do you choose a shard key?

9. What is the CAP theorem? Explain consistency, availability, and partition tolerance.

10. What is eventual consistency? When is it acceptable?

11. What is the difference between SQL and NoSQL in system design?

12. What is message queuing? When would you use it?

13. What are common message brokers (e.g., Kafka, RabbitMQ)?

14. What is rate limiting? Why is it important?

15. What are common rate-limiting algorithms (e.g., token bucket, sliding window)?

---

## 🌐 Real-World Design Scenarios

16. Design a URL shortener like bit.ly.

17. Design a social media feed like Twitter or Facebook.

18. Design a chat application like WhatsApp or Slack.

19. Design a file-sharing service like Dropbox or Google Drive.

20. Design a ride-sharing app like Uber or Lyft.

21. Design a search engine like Google.

22. Design a streaming platform like Netflix.

23. Design a distributed key-value store.

24. Design a payment processing system.

25. Design a global authentication service.

26. Design an API rate limiter.

27. Design a notification system (email, SMS, push).

28. Design a scalable logging system.

29. Design a job scheduler (like cron at scale).

30. Design a news feed with real-time updates.

---

## 🛠 Architecture & Patterns

31. What is the difference between monolithic and microservices architecture?

32. What are the pros and cons of microservices?

33. What is an API Gateway? What responsibilities does it have?

34. What is service discovery? How does it work?

35. What is circuit breaker pattern? Why use it?

36. What is retry mechanism with exponential backoff?

37. What is idempotency in API design?

38. How do services communicate in microservices (synchronous vs asynchronous)?

39. What is CQRS (Command Query Responsibility Segregation)?

40. What is event sourcing?

---

## 📊 Performance & Scalability

41. How would you handle 1 million concurrent users?

42. How do you scale a database under high read/write load?

43. How do you monitor and debug a distributed system?

44. What is observability? What are its pillars (logging, metrics, tracing)?

45. How do you design for high availability?

46. What is fault tolerance? How do you achieve it?

47. How do you handle data consistency across services?

48. What is a distributed cache? How does Redis help?

49. How do you perform a rolling deployment?

50. What is blue-green deployment and canary release?

---

## 📌 Tip:
Use the **4S Framework** to structure your answers:  
**S**cale (traffic, storage), **S**torage (database, caching), **S**ervices (APIs, components), **S**ync (messaging, events).  
Always clarify requirements first — ask questions!

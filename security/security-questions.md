# 🔐 Security Interview Questions

A curated list of common security-related questions asked in technical interviews for software engineers, backend developers, and DevOps roles.

---

## 🛡️ Web Application Security

1. What is XSS (Cross-Site Scripting)? How can you prevent it?

2. What is CSRF (Cross-Site Request Forgery)? How do you protect against it?

3. What is SQL Injection? How can it be prevented?

4. What is Clickjacking? How can you defend against it?

5. What are security headers? Name some important ones (e.g., `Content-Security-Policy`, `X-Frame-Options`).

6. What is CORS? Can it be a security risk if misconfigured?

7. What is HTTP vs HTTPS? How does TLS/SSL work?

8. What is HSTS and why is it important?

9. What is input validation and why is it critical for security?

10. What is output encoding? When should it be used?

---

## 🔐 Authentication & Authorization

11. What is the difference between authentication and authorization?

12. What is a session? How is it stored and managed on the server?

13. What is a JWT (JSON Web Token)? What are its components?

14. What are the pros and cons of using JWT vs session cookies?

15. Where should you store tokens securely (frontend: localStorage vs cookies)?

16. What is the difference between `HttpOnly`, `Secure`, and `SameSite` cookie attributes?

17. What is OAuth 2.0? What are its main roles (client, resource owner, authorization server, etc.)?

18. What is OpenID Connect (OIDC)? How does it differ from OAuth?

19. What is SSO (Single Sign-On)? How does it work?

20. How do you securely implement password reset functionality?

---

## 🔒 General Security Concepts

21. What is the principle of least privilege?

22. What is rate limiting? Why is it important for security?

23. What is brute force attack protection?

24. What is two-factor authentication (2FA)? How does it work?

25. What is hashing vs encryption vs encoding?

26. What is salting in password hashing? Why is it important?

27. Which hashing algorithms are suitable for passwords (e.g., bcrypt, scrypt, Argon2)?

28. What is symmetric vs asymmetric encryption?

29. What is a digital signature?

30. What is a certificate authority (CA)?

---

## ☁️ Cloud & API Security

31. How do you secure secrets (API keys, DB passwords) in production?

32. What is environment variable injection? Is it secure by itself?

33. What are IAM roles and policies in AWS/Azure/GCP?

34. How do you secure an API (REST or GraphQL)?

35. What is API key vs token-based authentication?

36. What is mutual TLS (mTLS)?

37. How do you audit and monitor security events in the cloud?

38. What is a WAF (Web Application Firewall)? How does it help?

39. What is VPC peering and private endpoints for security?

40. How do you handle data encryption at rest and in transit?

---

## 🧰 Secure Development Practices

41. What is dependency scanning? How do you check for vulnerable packages?

42. What is CSP (Content Security Policy)? How does it mitigate XSS?

43. What is security misconfiguration? Give examples.

44. What is secure error handling? Why should you avoid detailed error messages?

45. What is CORS misconfiguration? How can it lead to security issues?

46. How do you perform security testing in CI/CD?

47. What is penetration testing?

48. What is a zero-day vulnerability?

49. What is the OWASP Top 10? Name a few items.

50. How do you keep third-party libraries up to date securely?

---

## 📌 Tip:
Always think from an attacker’s perspective. In interviews, explain not just *what* but *how* and *why* — e.g., “I’d use HttpOnly cookies to prevent XSS-based session theft.”

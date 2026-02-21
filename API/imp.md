

How to design a scale api

1. API structure: 
     - endpoint details (path and req resp contract)
     - versioning
     - requestBody ->validations
     - repose body to sent
     - exceptional handling
     - proper http methods
     - status codes
     - idempotency

Security : jwt or oAuth (since api's are stateless)

Versioning : backward compailtbilty

Scalability:
- 1️⃣ Password storage     --> Bcrypt + salt hashing
- 2️⃣ Authentication flow  --> Client → Login API → Validate credentials → Generate JWT → Return token
- 3️⃣ Token strategy --> Access Token (Short lived) - 10 min , Refresh Token (Long lived) 
- 4️⃣ Rate limiting  --> [Rate_Limiting_SpringBoot_K8s_Complete_Notes.md](Rate_Limiting_SpringBoot_K8s_Complete_Notes.md)
- 5️⃣ Scalability architecture --> horizontal scaling

Data consistency:
        saga cherography pattern + oubox
# 📱 Microservice-demo-OM-app

A modular microservice-based backend system. This project is designed to support a scalable, maintainable, and secure mobile backend with services like authentication, user management, delivery scheduling, and more.

## 🚀 Technologies Used

- **Java 11**
- **Spring Boot**
- **Spring Cloud (Eureka, Gateway)**
- **MongoDB**
- **Postman**
- **Maven**

## 📱 Mobile Application Backend Project Overview

This section outlines the structure and interactions between the **Mobile-UI (`OM-backoffice-ui`)**, the **mobile application (Android/iOS)**, and the supporting backend **microservices**, as they integrate with the main **OM application**.


### 🖥️ OM-backoffice-ui

- **OM-backoffice-ui** will be a JAR module of the main **OM application**.
- It connects to other microservices via the **API Gateway**.
- Key functionalities:
  - Create mobile application users and assign access (**handled by `user-service` microservice**).
  - View and approve posted **orders** and **payments** coming from the mobile application.
  - After final approval, the data is sent to the main **OM application**.

### 📦 Supporting Microservices

- **User Service (`OM-mobile-userservice`)**  
  Handles all user-related operations, including user creation, access control, and permission management.

- **Delivery Schedule Service (`OM-mobiledelvschd`)**  
  Manages delivery schedule APIs used by the mobile application.

- **Authentication Service (`OM-mobileapp-authservice`)**  
  Handles login/authentication-related APIs and security.

- **Backend Service (`OM-mobileapp-backend`)**  
  Provides APIs related to:
  - Orders  
  - Payments  
  - Distribution  
  - Master Item  
  - Order Transaction Type  
  - Organization (Org)  
  - Inventory Organization (Inv Org)  
  - Bank



## 📁 Project Structure

```bash

OM-Mobile-Backend Projects/
│
├── api-gateway/ # Gateway service to route requests
├── OM-backoffice-ui/ # Backoffice management UI (optional)
├── OM-mobile-userservice/ # User management microservice
├── OM-mobileBase/ # Common/shared utilities or base classes
├── OM-mobileapp-authservice/ # Authentication service
├── OM-mobileapp-backend/ # Core mobile app logic and APIs
├── OM-mobiledelvschd/ # Delivery schedule microservice
├── service-registry/ # Eureka Service Registry
├── OM Mobile Service.postman_collection.json # Postman collection for API testing
├── project structure and flow.xlsx # Project documentation
└── README.md # This file

Go to **project structure and flow.xlsx** file for more details

```

## 🔁 Run Order of Projects (with Ports & Routes)

Start the projects in the following order to ensure proper service discovery and routing:

| Step | Project Name                  | Port  | Description                                 | API Gateway Route Prefix                  |
|------|-------------------------------|-------|---------------------------------------------|-------------------------------------------|
| 1    | **service-registry**          | 8760  | Eureka Server for service registration      | N/A                                       |
| 2    | **OM-authservice**            | 8084  | Authentication and JWT token issuance       | `/msales/api/auth/**`                     |
| 3    | **OM-userservice**            | 8082  | User-related operations                     | `/msales/api/user/**`                     |
| 4    | **OM-backend**                | 8081  | Core business logic for mobile features     | `/msales/api/main/**`                     |
| 5    | **OM-delvschd**               | 8083  | Delivery schedule processing                | `/msales/api/dlv/**`                      |
| 6    | **api-gateway**               | 8080  | Central gateway to route and secure APIs    | Routes requests to all backend services   |
| 7    | **OM-backoffice-ui**          | 9091  | Web frontend (Thymeleaf) interface          | Access via: `http://localhost:9091/OM`       |

> ℹ️ **OM-mobileBase** (common/shared library) is used as a dependency and doesn't need to be run as a standalone application.

---

### ⚠️ Important Note: API Gateway Base URL

All API requests from clients and the frontend UI go through the API Gateway at the base URL:
http://<API_GATEWAY_HOST>:<PORT>/msales/api

For example, if your API Gateway is running on `172.20.1.225` and port `8080`, the base URL would be:
http://172.20.1.225:8080/msales/api


---

### Example API Endpoints

| Service              | Endpoint                       | Full URL                                                       |
|----------------------|--------------------------------|----------------------------------------------------------------|
| Authentication       | `/auth/authenticate`            | `http://172.20.1.225:8080/msales/api/auth/authenticate`        |
| User Service         | `/user/getAllUser`              | `http://172.20.1.225:8080/msales/api/user/getAllUser`          |
| Order Service        | `/main/ord/orderHeaders`        | `http://172.20.1.225:8080/msales/api/main/ord/orderHeaders`    |
| Delivery Schedule    | `/dlv/getAllSchdHeader`         | `http://172.20.1.225:8080/msales/api/dlv/getAllSchdHeader`     |

Requests are routed and filtered by the API Gateway before reaching backend services.

---

## 🛡️ Security

All APIs are secured using JWT (JSON Web Token).

Authentication is handled by OM-mobileapp-authservice.

Clients must first authenticate via /auth/authenticate to obtain a token.

Include the token in all subsequent requests:

---

## 🧪 Testing

Use the included Postman collection to test the APIs across all microservices.

### ✅ Steps to Test

1. **Import the Collection**
   - Open **Postman**.
   - Click on `Import` and select the file `OM Mobile Service.postman_collection.json` from the project root.

2. **Set Environment Variables**
   - Create or configure an environment in Postman with the following:
     - `API_GATEWAY_HOST` = e.g., `172.20.1.225`
     - `PORT` = `8080`
   - This allows you to write requests like:
     ```
     {{API_GATEWAY_HOST}}:{{PORT}}/msales/api/auth/authenticate
     ```

3. **Authenticate and Retrieve Token**
   - Use the `/auth/authenticate` endpoint to log in.
   - **Sample Request Body:**
     ```json
     {
       "username": "hafiz",
       "password": "1234"
     }
     ```
   - A successful response will return a **JWT token**.

4. **Authorize Requests**
   - In Postman, go to the `Authorization` tab for each request.
   - Choose type: `Bearer Token`
   - Paste the JWT token you received in the previous step.

5. **Send Requests**
   - Now test other endpoints like:
     - `/user/getAllUser`
     - `/main/ord/orderHeaders`
     - `/dlv/getAllSchdHeader`
   - All requests will go through the **API Gateway** and be routed to their respective microservices.

### 📌 Notes

- Ensure the required services (auth, user, backend, gateway, etc.) are running before testing.
- If you get a `401 Unauthorized`, re-authenticate and refresh the token.
- Use the **Run Order** in this README to start services in proper sequence.

---

## 🙌 Final Notes

- This project is designed with a **microservice architecture** for modularity, scalability, and maintainability.
- Make sure all services are properly registered in **Eureka** and accessible via the **API Gateway**.
- Refer to the included **Postman collection** and **project structure Excel file** for a better understanding of API usage and inter-service flow.
- Follow the correct **run order** to ensure proper service discovery and data flow between services.

---

> Thank you for using OM Application Backend! 🚀



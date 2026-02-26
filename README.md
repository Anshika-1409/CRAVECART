# CRAVECART — Retail Ordering System

A full-stack retail ordering platform built with **React + Vite** (frontend) and **Spring Boot + JWT** (backend).

---

## Table of Contents

- [Project Overview](#project-overview)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)
- [Frontend](#frontend)
- [Backend](#backend)
- [API Reference](#api-reference)
- [Frontend–Backend Integration](#frontendbackend-integration)
- [Roles & Permissions](#roles--permissions)
- [Mock Mode](#mock-mode)
- [Known Limitations & TODOs](#known-limitations--todos)

---

## Project Overview

CRAVECART is a food retail ordering platform where customers can browse products, manage a cart, place orders with a delivery address, and view their order history. Admins can manage the product menu.

**Key Features:**
- JWT-based authentication (register / login)
- Product menu browsing with category and brand filters
- Menu page grouped by category with collapsible sections
- Frontend cart (localStorage-based, no backend cart)
- Address management — add, select, and delete delivery addresses
- Order placement with confirmation modal showing customer details
- Order history with expandable cards showing items and delivery address
- Admin dashboard — add products, edit stock, delete items
- Fully functional in mock mode when backend is offline
# Tech Stack
### Frontend
| Tool | Version | Purpose |
|---|---|---|
| React | 18.2 | UI framework |
| Vite | 5.1 | Build tool & dev server |
| React Router DOM | 6.22 | Client-side routing |
| Axios | 1.6 | HTTP client |
| Custom CSS | — | Design system (no Tailwind/MUI) |

### Backend
| Tool | Purpose |
|---|---|
| Spring Boot 3 | Application framework |
| Spring Security | Authentication & authorization |
| JWT (jjwt) | Token generation & validation |
| Spring Data JPA | Database ORM |
| BCrypt | Password hashing |
| Lombok | Boilerplate reduction |
| Jakarta Validation | Request validation |

---

## Project Structure

```
cravecrat/
│
├── frontend/  (retail-app/)
│   ├── index.html
│   ├── package.json
│   ├── vite.config.js
│   └── src/
│       ├── main.jsx                  ← App entry point
│       ├── App.jsx                   ← Routes & layout
│       ├── styles.css                ← Global design system
│       │
│       ├── services/
│       │   └── api.js                ← Axios instance + all API calls + mock data
│       │
│       ├── context/
│       │   ├── AuthContext.jsx       ← Login, register, user state, address management
│       │   ├── CartContext.jsx       ← Cart state (localStorage-based)
│       │   └── ToastContext.jsx      ← Global toast notifications
│       │
│       ├── components/
│       │   ├── Navbar.jsx            ← Top navigation with cart badge
│       │   ├── ProductCard.jsx       ← Product card for Home page grid
│       │   └── ProtectedRoute.jsx    ← Auth guard for private routes
│       │
│       └── pages/
│           ├── Login.jsx             ← /login
│           ├── Register.jsx          ← /register
│           ├── Home.jsx              ← / (product grid + filters)
│           ├── Menu.jsx              ← /menu (products grouped by category)
│           ├── Cart.jsx              ← /cart (items + address + order confirmation)
│           ├── Profile.jsx           ← /profile (user info + order history)
│           └── AdminDashboard.jsx    ← /admin (ADMIN role only)
│
└── backend/  (retail_ordering/)
    └── src/main/java/com/hcltech/retail_ordering/
        ├── RetailOrderingApplication.java
        ├── config/
        │   └── SecurityConfig.java
        ├── controller/
        │   ├── AuthController.java
        │   ├── MenuController.java
        │   └── OrderController.java
        ├── dto/
        │   ├── LoginRequest.java
        │   ├── RegisterRequest.java
        │   └── OrderRequest.java
        ├── entity/
        │   ├── User.java
        │   ├── Menu.java
        │   ├── Order.java
        │   └── Role.java  (enum: ADMIN, CUSTOMER)
        ├── repository/
        │   ├── UserRepository.java
        │   ├── MenuRepository.java
        │   └── OrderRepository.java
        ├── security/
        │   ├── JwtUtil.java
        │   └── JwtAuthenticationFilter.java
        ├── services/
        │   ├── AuthService.java
        │   └── OrderService.java
        └── exception/
            └── GlobalExceptionHandler.java


## Getting Started

### Prerequisites
- Node.js 18+
- Java 21
- Maven 3.8+
- MySQL or H2 (for backend)

---

## Frontend

### Install & Run

```bash
cd retail-app
npm install
npm run dev
```

Opens at: `http://localhost:5173`

### Build for Production

```bash
npm run build
```

### Environment

The backend base URL is set in `src/services/api.js`:

```js
const BASE_URL = 'http://localhost:8080/api'
```

Change this if your backend runs on a different port or host.

---

## Backend

### Configure `application.properties`

```properties
# Server
server.port=8080

# Database (MySQL example)
spring.datasource.url=jdbc:mysql://localhost:3306/nexus_db
spring.datasource.username=root
spring.datasource.password=yourpassword
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true

# JWT
jwt.secret=your_very_long_secret_key_minimum_32_chars
jwt.expiration=86400000
```

> For quick testing use H2 in-memory:
> ```properties
> spring.datasource.url=jdbc:h2:mem:nexusdb
> spring.h2.console.enabled=true
> ```

### Run

```bash
cd retail_ordering
mvn spring-boot:run
```

Backend starts at: `http://localhost:8080`

---

## API Reference

### Auth — `/api/auth` (public)

| Method | Endpoint | Body | Response |
|---|---|---|---|
| POST | `/api/auth/register` | `{ username, email, password, contactNumber }` | `"User registered successfully"` |
| POST | `/api/auth/login` | `{ username, password }` | JWT token string |

**Register rules:**
- `contactNumber` must be exactly **10 digits**
- All new users get role `CUSTOMER` automatically

**Login response:** Plain JWT string (not JSON). The frontend decodes it to extract the username.

---

### Menu — `/api/menu` (protected)

| Method | Endpoint | Auth | Body | Response |
|---|---|---|---|---|
| GET | `/api/menu` | Any logged-in user | — | `List<Menu>` |
| POST | `/api/menu` | ADMIN only | `{ name, category, brand, packaging, price, stock }` | `Menu` |

**Menu object:**
```json
{
  "id": 1,
  "name": "Margherita Pizza",
  "category": "Pizza",
  "brand": "Domino's",
  "packaging": "Box",
  "price": 9.99,
  "stock": 50,
  "description": "Classic cheese and tomato pizza with fresh mozzarella",
  "imageUrl": "https://example.com/images/margherita-pizza.jpg",
  "isAvailable": true,
  "createdAt": "2026-02-26T10:30:00Z",
  "updatedAt": "2026-02-26T10:30:00Z"
}
```

---

### Orders — `/api/orders` (protected)

| Method | Endpoint | Auth | Body | Response |
|---|---|---|---|---|
| POST | `/api/orders` | Any logged-in user | `{ menuId, quantity, deliveryAddress }` | `Order` |
| GET | `/api/orders/my` | Any logged-in user | — | `List<Order>` ⚠️ *needs to be added* |

**Order request body:**
```json
{
  "menuId": 1,
  "quantity": 2,
  "deliveryAddress": "123 MG Road, Mumbai, Maharashtra - 400001"
}
```

**Order response:**
```json
{
  "id": 1,
  "user": { ... },
  "menu": { ... },
  "quantity": 2,
  "totalAmount": 25.98,
  "deliveryAddress": "123 MG Road, Mumbai, Maharashtra - 400001",
  "orderDate": "2024-01-15T10:30:00"
}
```

> ⚠️ **Note:** The backend currently has no GET orders endpoint.
> Add this to `OrderController.java` to enable order history:
>
> ```java
> @Autowired
> private UserRepository userRepository;
>
> @GetMapping("/my")
> public List<Order> getMyOrders(Principal principal) {
>     User user = userRepository.findByUsername(principal.getName()).orElseThrow();
>     return orderRepository.findByUser(user);
> }
> ```

---

## Frontend–Backend Integration

### How Auth Works

```
Register:
  Frontend  →  POST /api/auth/register  →  Backend saves user (role: CUSTOMER)
  Frontend  →  POST /api/auth/login     →  Backend returns JWT string
  Frontend decodes JWT → stores token + user object in localStorage

Login:
  Frontend  →  POST /api/auth/login  →  Backend returns JWT string
  Frontend decodes JWT subject (username) → builds user object → stores in localStorage

Every subsequent request:
  Axios interceptor auto-attaches:  Authorization: Bearer <token>
```

### How Orders Work

The backend accepts **one item per order**. When a customer has multiple items in the cart, the frontend loops and places one `POST /api/orders` per item:

```
Cart has 3 items  →  3 × POST /api/orders
                       ├── { menuId: 1, quantity: 2, deliveryAddress: "..." }
                       ├── { menuId: 5, quantity: 1, deliveryAddress: "..." }
                       └── { menuId: 9, quantity: 3, deliveryAddress: "..." }
```

### Address Handling

The backend's `deliveryAddress` is a plain string. The frontend collects a structured address form (street, city, state, pincode) and joins it into a single string before sending:

```js
"123 Main Street, Near Mall, Mumbai, Maharashtra, 400001, India"
```

Addresses are saved in `localStorage` under the user object — they are not stored in the backend.

### CORS

Add this to your Spring Boot backend to allow frontend requests:

```java
// In SecurityConfig.java or a separate CorsConfig.java
@Bean
public CorsConfigurationSource corsConfigurationSource() {
    CorsConfiguration config = new CorsConfiguration();
    config.setAllowedOrigins(List.of("http://localhost:5173"));
    config.setAllowedMethods(List.of("GET", "POST", "PUT", "DELETE", "OPTIONS"));
    config.setAllowedHeaders(List.of("*"));
    config.setAllowCredentials(true);
    UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
    source.registerCorsConfiguration("/**", config);
    return source;
}
```

And update `SecurityConfig.java`:
```java
http.cors(cors -> cors.configurationSource(corsConfigurationSource()))
    .csrf(csrf -> csrf.disable())
    ...
```

---

## Roles & Permissions

| Role | Access |
|---|---|
| `CUSTOMER` | Browse menu, manage cart, place orders, view own order history |
| `ADMIN` | Everything above + add products via Admin Dashboard |

All new registrations get `CUSTOMER` role by default. To create an ADMIN user, manually update the role in the database:

```sql
UPDATE users SET role = 'ADMIN' WHERE username = 'adminuser';
```

---

## Mock Mode

When the backend is **offline or unreachable**, the frontend automatically falls back to mock data — no code changes needed.

| Feature | Mock Behaviour |
|---|---|
| Login | Any username + password works |
| Register | Creates a local user instantly |
| Menu/Products | 12 pre-loaded food items shown |
| Place Order | Saved to in-memory mock list |
| Order History | Shows 2 sample past orders |
| Admin actions | Add/delete/edit updates in-memory only |

Mock data is defined at the top of `src/services/api.js` in the `MOCK_MENU` and `MOCK_ORDERS` arrays. Edit these to match your actual product catalogue.

---

## Known Limitations & TODOs

| # | Issue | Fix |
|---|---|---|
| 1 | No GET `/api/orders/my` endpoint in backend | Add to `OrderController.java` (code above) |
| 2 | No PUT/DELETE for menu items in backend | Add endpoints or keep as frontend-only mock |
| 3 | Addresses stored in localStorage only | Add address table to backend User entity |
| 4 | Cart is frontend-only | Add cart entity and API to backend if persistence needed |
| 5 | ADMIN role must be set manually in DB | Add admin registration or role management endpoint |
| 6 | No order status updates | Add status field management to backend |
| 7 | CORS not configured in backend | Add CORS config (code above) |
| 8 | JWT role not included in token | Add role claim in `JwtUtil.generateToken()` |

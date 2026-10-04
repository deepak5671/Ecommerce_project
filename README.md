# 🌸 SakuraMart — Spring Boot E-Commerce Platform

A full-stack online store built with **Java 17, Spring Boot 3, Spring Security, Spring Data JPA, Thymeleaf, and MySQL**. It provides separate experiences for **customers** (browse, cart, checkout, order tracking) and **administrators** (catalog, users, orders), with role-based security, account lockout, email notifications, and a password-reset flow.

![Java](https://img.shields.io/badge/Java-17-ED8B00?style=flat-square&logo=openjdk&logoColor=white)
![Spring Boot](https://img.shields.io/badge/Spring_Boot-3.2.3-6DB33F?style=flat-square&logo=springboot&logoColor=white)
![Spring Security](https://img.shields.io/badge/Spring_Security-6-6DB33F?style=flat-square&logo=springsecurity&logoColor=white)
![MySQL](https://img.shields.io/badge/MySQL-8-4479A1?style=flat-square&logo=mysql&logoColor=white)
![Thymeleaf](https://img.shields.io/badge/Thymeleaf-005F0F?style=flat-square&logo=thymeleaf&logoColor=white)
![Bootstrap](https://img.shields.io/badge/Bootstrap-5.3-7952B3?style=flat-square&logo=bootstrap&logoColor=white)

> 📄 For architecture, database design, request flows, and the full endpoint reference, see **[PROJECT_REPORT.md](./PROJECT_REPORT.md)**.

---

## ✨ Features

### 🛍️ Customer
- Browse the latest products and categories on the home page
- Filter products by **category** and search by **title or category**, with pagination
- View product details with price, discount, and stock
- **Cart:** add products, increase or decrease quantity, and see a live subtotal
- **Checkout:** enter a delivery address, choose **Cash on Delivery** or **Online**, and see the delivery fee and tax
- **My Orders:** track order status and cancel an order
- Email confirmation when an order is placed and whenever its status changes
- Profile management with a photo upload, plus password change

### 🛠️ Admin
- Dashboard with quick access to every management module
- **Categories:** create, edit, activate or deactivate, and delete, with images and pagination
- **Products:** create, edit, delete, set stock, set a **discount (0–100%)** with the discounted price calculated automatically, toggle active status, and search
- **Orders:** view all orders (paginated), search by order ID, and update status
- **Users:** list customers and admins, and enable or disable accounts
- **Add Admin:** create new administrator accounts

### 🔐 Security
- Form login with **Spring Security** and **BCrypt** password hashing
- Role-based authorization: `/user/**` → `ROLE_USER`, `/admin/**` → `ROLE_ADMIN`
- After login, admins are redirected to `/admin/` and customers to `/`
- **Account lockout:** once **3 failed login attempts** have been recorded, the next failure locks the account, and it unlocks automatically after a cool-down period
- Admins can disable accounts
- **Forgot password:** a UUID reset token is emailed as a one-time link

---

## 🧱 Tech Stack

| Layer | Technology |
|---|---|
| Language | Java 17 |
| Framework | Spring Boot 3.2.3 (Web MVC, DevTools) |
| Security | Spring Security 6, BCrypt |
| Persistence | Spring Data JPA, Hibernate, MySQL 8 |
| View | Thymeleaf, Bootstrap 5.3, Font Awesome 6, jQuery 3.7 + jQuery Validation |
| Mail | Spring Boot Starter Mail (Gmail SMTP) |
| Build | Maven (wrapper included), Lombok |

---

## 📁 Project Structure

```
shopping-cart-spring-boot/
├── pom.xml
└── src/main/
    ├── java/com/ecom/
    │   ├── ShoppingCartApplication.java
    │   ├── config/        # SecurityConfig, CustomUser, UserDetailsServiceImpl,
    │   │                  # AuthSucessHandlerImpl, AuthFailureHandlerImpl
    │   ├── controller/    # HomeController, UserController, AdminController
    │   ├── model/         # UserDtls, Category, Product, Cart, ProductOrder,
    │   │                  # OrderAddress, OrderRequest (DTO)
    │   ├── repository/    # Spring Data JPA repositories
    │   ├── service/       # Service interfaces + impl/
    │   └── util/          # CommonUtil (mail, URLs), OrderStatus, AppConstant
    └── resources/
        ├── application.properties
        ├── static/        # css/, js/script.js, img/{category_img,product_img,profile_img}
        └── templates/     # base, index, product, view_product, login, register,
                           # forgot/reset password, user/*, admin/*
```

---

## 🚀 Getting Started

### Prerequisites
- **JDK 17+**
- **MySQL 8** (local or hosted)
- A Gmail account with an **App Password** (for sending emails)

### 1. Clone
```bash
git clone https://github.com/deepak5671/Ecommerce_project.git
cd Ecommerce_project/shopping-cart-spring-boot
```

### 2. Create the database
```sql
CREATE DATABASE shopping_cart;
```
Hibernate creates the tables automatically (`spring.jpa.hibernate.ddl-auto=update`).

### 3. Configure environment variables
The app reads its database settings from the environment:

```bash
export DB_URL="jdbc:mysql://localhost:3306/shopping_cart"
export DB_USERNAME="root"
export DB_PASSWORD="your_password"
export PORT=8080            # optional, defaults to 8080
```

Set your mail credentials in `src/main/resources/application.properties`, or override them through the environment:

```properties
spring.mail.username=your_email@gmail.com
spring.mail.password=your_gmail_app_password
```

### 4. Run
```bash
./mvnw spring-boot:run        # macOS / Linux
mvnw.cmd spring-boot:run      # Windows
```
Open **http://localhost:8080**.

### 5. Create the first admin
1. Register a normal account at `/register`.
2. Promote it in MySQL:
   ```sql
   UPDATE user_dtls SET role = 'ROLE_ADMIN' WHERE email = 'you@example.com';
   ```
3. Sign in. You'll land on `/admin/`, where you can create more admins from **Add Admin**.

### Build a JAR
```bash
./mvnw clean package
java -jar target/Shopping_Cart-0.0.1-SNAPSHOT.jar
```

---

## 🗺️ Key Routes

| Area | Route | Description |
|---|---|---|
| Public | `/` | Home: latest 6 categories and 8 products |
| Public | `/products?category=&ch=&pageNo=` | Product listing with filter, search, and pagination |
| Public | `/product/{id}` | Product details |
| Public | `/register`, `/signin` | Register / sign in |
| Public | `/forgot-password`, `/reset-password?token=` | Password recovery |
| User | `/user/cart` | Shopping cart |
| User | `/user/orders` → `/user/save-order` | Checkout |
| User | `/user/user-orders` | Order history and cancellation |
| Admin | `/admin/category`, `/admin/products` | Catalog management |
| Admin | `/admin/orders`, `/admin/search-order` | Order management |
| Admin | `/admin/users?type=1\|2` | Customers / admins |

See the [full endpoint reference](./PROJECT_REPORT.md#6-endpoint-reference).

---

## 📦 Order Lifecycle

```
In Progress → Order Received → Product Packed → Out for Delivery → Delivered
      └──────────────→ Cancelled                                    └→ Success
```
The customer gets an email every time the status changes.

---

## 🛣️ Roadmap
- Online payment gateway integration (Razorpay / Stripe)
- Stock decrement on order and out-of-stock handling
- Cloud image storage (S3 / Cloudinary)
- REST API layer and a React/mobile client
- Docker Compose setup and CI pipeline
- Unit and integration tests

---

## 👤 Author

**Deepak** · [@deepak5671](https://github.com/deepak5671)

If you find this project useful, please consider giving it a ⭐.

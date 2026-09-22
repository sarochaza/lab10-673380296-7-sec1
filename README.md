<div align="center">

# 🛍️ Product Management REST API

### Spring WebFlux · Reactive Programming · WebClient

ระบบ REST API สำหรับจัดการสินค้า พัฒนาด้วย Spring WebFlux โดยเน้นการทำงานแบบ **Non-blocking Reactive Programming**

<p>
  <img src="https://img.shields.io/badge/Java-17-orange?style=for-the-badge&logo=openjdk" alt="Java 17">
  <img src="https://img.shields.io/badge/Spring%20Boot-3.3.0-6DB33F?style=for-the-badge&logo=springboot" alt="Spring Boot">
  <img src="https://img.shields.io/badge/Spring%20WebFlux-Reactive-6DB33F?style=for-the-badge&logo=spring" alt="Spring WebFlux">
  <img src="https://img.shields.io/badge/License-Educational-blue?style=for-the-badge" alt="Educational">
</p>

<p>
  <strong>ผู้จัดทำ:</strong> สโรชา เสาทอง · <strong>รหัส:</strong> 673380296-7
</p>

</div>

---

## 📌 ภาพรวมโปรเจกต์

โปรเจกต์นี้เป็น REST API สำหรับระบบจัดการสินค้า พัฒนาด้วย **Spring WebFlux** และ **WebClient** เพื่อฝึกการเขียนโปรแกรมแบบ Reactive โดยระบบสามารถจัดการข้อมูลสินค้าได้ดังนี้

- แสดงสินค้าทั้งหมด
- ค้นหาสินค้าตาม ID
- สร้างสินค้าใหม่
- ลบสินค้า
- ค้นหาสินค้าตาม Category
- คำนวณราคาหลังส่วนลด
- เรียก REST API ผ่าน WebClient

โปรเจกต์นี้ใช้ `ConcurrentHashMap` เป็นพื้นที่เก็บข้อมูลสินค้าในหน่วยความจำ จึงไม่ต้องเชื่อมต่อฐานข้อมูลจริง

## ✨ จุดเด่นของระบบ

| ความสามารถ | รายละเอียด |
|---|---|
| Reactive API | คืนข้อมูลด้วย `Mono` และ `Flux` |
| Non-blocking | ไม่ใช้ `.block()` หรือ `Thread.sleep()` ใน Endpoint |
| Product CRUD | รองรับการดู สร้าง และลบสินค้า |
| Search | ค้นหาตาม ID และ Category |
| Discount | คำนวณราคาหลังส่วนลดอัตโนมัติ |
| WebClient | เรียก API แบบ Reactive |

---

## 🧰 เทคโนโลยีที่ใช้

- Java 17
- Spring Boot 3.3.0
- Spring WebFlux
- Project Reactor
- Reactor Netty
- Maven
- WebClient
- ConcurrentHashMap

---

## 🗂️ โครงสร้างโปรเจกต์

```text
src/main/java/com/example/lab10/
├── Lab10Application.java          # จุดเริ่มต้นของแอปพลิเคชัน
├── AppConfig.java                 # Spring Bean Configuration
├── model/
│   └── Product.java               # โครงสร้างข้อมูลสินค้า
├── repository/
│   └── ProductRepository.java     # จัดเก็บและค้นหาข้อมูล
├── service/
│   └── ProductService.java        # Business Logic
├── controller/
│   └── ProductController.java     # REST API Endpoints
└── client/
    └── ProductWebClient.java      # Reactive HTTP Client
```

### การทำงานของแต่ละ Layer

```text
Client / Browser / Postman
            ↓
       Controller
            ↓
        Service
            ↓
       Repository
            ↓
   ConcurrentHashMap
```

| Layer | หน้าที่ |
|---|---|
| `model` | กำหนดโครงสร้างของ `Product` |
| `repository` | จัดเก็บ ค้นหา บันทึก และลบข้อมูล |
| `service` | จัดการ Business Logic และ Operators |
| `controller` | รับ HTTP Request และส่ง Response |
| `client` | เรียก REST API ด้วย `WebClient` |

---

## ⚡ Reactive Programming

Reactive Programming ทำงานกับข้อมูลแบบ Data Stream โดยไม่ทำให้ Thread หยุดรอผลลัพธ์ ระบบจะส่งข้อมูลเมื่อข้อมูลพร้อมผ่าน `Mono` หรือ `Flux`

### Blocking กับ Reactive

| หัวข้อ | Blocking | Reactive |
|---|---|---|
| Thread | หยุดรอผลลัพธ์ | ไม่หยุดรอ |
| Return Type | `Product`, `List<Product>` | `Mono`, `Flux` |
| ความเหมาะสม | งานทั่วไป | งานที่มี Request จำนวนมาก |
| ตัวอย่าง | `.block()` | `.map()`, `.flatMap()` |

ตัวอย่าง Controller แบบ Non-blocking:

```java
@GetMapping
public Flux<Product> getAll() {
    return service.getAll();
}
```

> ⚠️ Controller ไม่ควรเรียก `.block()` หรือ `subscribe()` เอง เพราะ Spring WebFlux จะจัดการ Subscription ให้โดยอัตโนมัติ

---

## 🔄 Mono และ Flux

| Reactive Type | ความหมาย | ตัวอย่างการใช้งาน |
|---|---|---|
| `Mono<Product>` | ข้อมูล 0 หรือ 1 รายการ | ค้นหาสินค้าตาม ID |
| `Flux<Product>` | ข้อมูล 0 ถึงหลายรายการ | แสดงสินค้าทั้งหมด |
| `Mono<Double>` | ข้อมูลตัวเลข 0 หรือ 1 ค่า | ราคาหลังส่วนลด |
| `Mono<Void>` | ทำงานเสร็จโดยไม่มีข้อมูลตอบกลับ | ลบสินค้า |

---

## 🌐 REST API Endpoints

| Method | Endpoint | Return Type | รายละเอียด |
|:---:|---|---|---|
| `GET` | `/products` | `Flux<Product>` | แสดงสินค้าทั้งหมด |
| `GET` | `/products/{id}` | `Mono<Product>` | ค้นหาสินค้าตาม ID |
| `POST` | `/products` | `Mono<Product>` | สร้างสินค้าใหม่ |
| `DELETE` | `/products/{id}` | `Mono<Void>` | ลบสินค้าตาม ID |
| `GET` | `/products/category/{category}` | `Flux<Product>` | ค้นหาตาม Category |
| `GET` | `/products/{id}/price` | `Mono<Double>` | ดูราคาหลังส่วนลด |

---

## 🚀 วิธีเริ่มต้นใช้งาน

### 1. Clone โปรเจกต์

```bash
git clone <repository-url>
cd LAB10_WebClient
```

### 2. รันโปรเจกต์ด้วย Maven

```bash
mvn spring-boot:run
```

หรือเปิดไฟล์ `Lab10Application.java` แล้วกด **Run** ใน IntelliJ IDEA

เมื่อรันสำเร็จ API จะพร้อมใช้งานที่:

```text
http://localhost:8080
```

---

## 🧪 ตัวอย่างการทดสอบ API

### แสดงสินค้าทั้งหมด

```http
GET http://localhost:8080/products
```

### ค้นหาสินค้าตาม ID

```http
GET http://localhost:8080/products/1
```

### ค้นหาตาม Category

```http
GET http://localhost:8080/products/category/Electronics
```

### ดูราคาหลังส่วนลด

```http
GET http://localhost:8080/products/1/price
```

### สร้างสินค้าใหม่

```http
POST http://localhost:8080/products
Content-Type: application/json
```

```json
{
  "name": "Mechanical Keyboard",
  "category": "Computer",
  "brand": "Logitech",
  "stock": 15,
  "price": 2500.0,
  "discountType": "MEMBER"
}
```

หากไม่ระบุ `id` ระบบจะสร้าง UUID ให้อัตโนมัติ

### ลบสินค้า

```http
DELETE http://localhost:8080/products/1
```

---

## 💰 การคำนวณส่วนลด

| `discountType` | ส่วนลด |
|---|---:|
| `NONE` | 0% |
| `MEMBER` | 10% |
| `SEASONAL` | 20% |

ตัวอย่างสินค้า ID 1:

```text
ราคาปกติ       = 39,900 บาท
ส่วนลด MEMBER  = 10%
ราคาหลังลด     = 39,900 × 0.90 = 35,910 บาท
```

---

## 🧩 Operators ที่ใช้

### `map`

ใช้แปลงข้อมูลจากชนิดหนึ่งเป็นอีกชนิดหนึ่ง

```java
.map(Product::getDiscountedPrice)
```

### `filter`

ใช้กรองข้อมูลตามเงื่อนไข

```java
.filter(product ->
        product.getCategory()
                .equalsIgnoreCase(category))
```

### `flatMap`

ใช้เชื่อมต่อกับ Method ที่คืนค่าเป็น `Mono` หรือ `Flux`

```java
.flatMap(repository::save)
```

### `switchIfEmpty`

ใช้จัดการกรณีไม่พบข้อมูล

```java
.switchIfEmpty(Mono.error(...))
```

---

## 🔌 WebClient

WebClient ใช้เรียก REST API แบบ Reactive และ Non-blocking

```java
public Mono<Product> getProductById(String id) {
    return client.get()
            .uri("/products/{id}", id)
            .retrieve()
            .bodyToMono(Product.class);
}
```

| Method Chain | หน้าที่ |
|---|---|
| `.get()` | กำหนด HTTP Method เป็น GET |
| `.uri()` | กำหนด URL |
| `.retrieve()` | รับข้อมูลจาก Response |
| `.bodyToMono()` | แปลง Response เป็น `Mono` |
| `.bodyToFlux()` | แปลง Response หลายรายการเป็น `Flux` |

ตัวอย่างการ Chain Operators:

```java
getProductById("1")
        .map(Product::getName)
        .defaultIfEmpty("ไม่พบสินค้า")
        .subscribe(System.out::println);
```

---

## ✅ Non-blocking Checklist

- [x] ใช้ `Mono` และ `Flux`
- [x] ใช้ Spring WebFlux
- [x] ใช้ WebClient
- [x] ใช้ `map`, `filter` และ `flatMap`
- [x] ไม่ใช้ `.block()` ใน Endpoint
- [x] ไม่ใช้ `Thread.sleep()`
- [x] Controller คืนค่า Reactive Type โดยตรง

---

## 📸 หลักฐานการทดสอบ

ภาพการทดสอบ API ที่ใช้ประกอบรายงาน:

```text
01_GET_All_Products.png
02_GET_Product_By_Id.png
03_GET_By_Category.png
04_GET_Discounted_Price.png
05_GET_Seasonal_Discount.png
06_POST_Create_Product.png
07_GET_After_Create.png
08_DELETE_Product.png
09_Verify_Delete.png
```

---

## 👩‍💻 ผู้จัดทำ

| รายการ | ข้อมูล |
|---|---|
| ชื่อ | สโรชา เสาทอง |
| รหัสนักศึกษา | 673380296-7 |
| รายวิชา | CP353002 หลักการออกแบบและพัฒนาซอฟต์แวร์ |

<div align="center">

Made for educational purposes · Spring WebFlux Lab 10

</div>

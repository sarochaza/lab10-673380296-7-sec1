Lab 10: Spring WebFlux & WebClient

ข้อมูลนักศึกษา

รหัสนักศึกษา: 673380296-7

ชื่อ: สโรชา เสาทอง

รายวิชา: CP353002 หลักการออกแบบและพัฒนาซอฟต์แวร์

หัวข้อ: Reactive Programming, Spring WebFlux และ WebClient

1. รายละเอียดโปรเจกต์

โปรเจกต์นี้เป็น REST API สำหรับระบบจัดการสินค้า โดยใช้ Spring WebFlux และ WebClient เพื่อให้ระบบทำงานแบบ Non-blocking Reactive Programming

ความสามารถของระบบ ได้แก่

แสดงสินค้าทั้งหมด

ค้นหาสินค้าตาม ID

สร้างสินค้าใหม่

ลบสินค้า

ค้นหาสินค้าตาม Category

คำนวณราคาหลังส่วนลด

เรียก REST API ผ่าน WebClient

โปรเจกต์นี้ใช้ ConcurrentHashMap เก็บข้อมูลสินค้าในหน่วยความจำ จึงไม่ต้องเชื่อมต่อฐานข้อมูลจริง

2. เทคโนโลยีที่ใช้

Java 17

Spring Boot 3.3.0

Spring WebFlux

Project Reactor

Reactor Netty

Maven

WebClient

ConcurrentHashMap

3. โครงสร้างโปรเจกต์

src/main/java/com/example/lab10/
├── Lab10Application.java
├── AppConfig.java
├── model/Product.java
├── repository/ProductRepository.java
├── service/ProductService.java
├── controller/ProductController.java
└── client/ProductWebClient.java

ส่วน

หน้าที่

model

เก็บโครงสร้างข้อมูล Product

repository

จัดเก็บและค้นหาข้อมูลสินค้า

service

จัดการ Business Logic

controller

เปิด REST API

client

เรียก REST API ผ่าน WebClient

4. Reactive Programming และ Blocking Programming

Blocking Programming คือการทำงานที่ Thread ต้องหยุดรอผลลัพธ์ก่อนจึงทำงานต่อได้ หากมี Request จำนวนมาก อาจทำให้ Thread ไม่เพียงพอและระบบตอบสนองช้าลง

Reactive Programming เป็นการทำงานกับข้อมูลแบบ Data Stream และไม่ทำให้ Thread หยุดรอผลลัพธ์ เมื่อข้อมูลพร้อมจึงค่อยส่งข้อมูลกลับมา โดยใช้ Mono หรือ Flux เป็นตัวแทนของข้อมูล

โปรเจกต์นี้จึงไม่ใช้ .block() หรือ Thread.sleep() ใน Endpoint แต่คืนค่า Mono และ Flux ให้ Spring WebFlux จัดการต่อ

@GetMapping
public Flux<Product> getAll() {
    return service.getAll();
}

5. Mono และ Flux

Reactive Type

ความหมาย

ตัวอย่าง

Mono<Product>

ข้อมูล 0 หรือ 1 รายการ

ค้นหาสินค้าตาม ID

Flux<Product>

ข้อมูล 0 ถึงหลายรายการ

ดึงสินค้าทั้งหมด

Mono<Double>

ค่าตัวเลข 0 หรือ 1 ค่า

ราคาหลังส่วนลด

Mono<Void>

ทำงานเสร็จโดยไม่มีข้อมูลตอบกลับ

ลบสินค้า

การเลือกใช้ Mono หรือ Flux ให้ตรงกับจำนวนข้อมูล ทำให้โค้ดมีความชัดเจนและทำงานกับ Reactive Stream ได้ถูกต้อง

6. REST API Endpoints

Method

Endpoint

Return Type

รายละเอียด

GET

/products

Flux<Product>

แสดงสินค้าทั้งหมด

GET

/products/{id}

Mono<Product>

ค้นหาสินค้าตาม ID

POST

/products

Mono<Product>

สร้างสินค้าใหม่

DELETE

/products/{id}

Mono<Void>

ลบสินค้าตาม ID

GET

/products/category/{category}

Flux<Product>

ค้นหาตาม Category

GET

/products/{id}/price

Mono<Double>

ดูราคาหลังส่วนลด

7. วิธีรันโปรเจกต์

เปิด Terminal ในโฟลเดอร์โปรเจกต์ แล้วใช้คำสั่ง:

mvn spring-boot:run

หรือเปิดไฟล์ Lab10Application.java แล้วกด Run ใน IntelliJ IDEA

เซิร์ฟเวอร์จะทำงานที่:

http://localhost:8080

8. ตัวอย่างการทดสอบ API

GET http://localhost:8080/products
GET http://localhost:8080/products/1
GET http://localhost:8080/products/category/Electronics
GET http://localhost:8080/products/1/price
DELETE http://localhost:8080/products/1

สร้างสินค้าใหม่

POST http://localhost:8080/products

Request Body:

{
  "name": "Mechanical Keyboard",
  "category": "Computer",
  "brand": "Logitech",
  "stock": 15,
  "price": 2500.0,
  "discountType": "MEMBER"
}

หากไม่ระบุ id ระบบจะสร้าง UUID ให้อัตโนมัติ

9. Operators ที่ใช้

map

ใช้แปลงข้อมูลจากชนิดหนึ่งเป็นอีกชนิดหนึ่ง เช่น แปลง Product เป็นราคาหลังส่วนลด

.map(Product::getDiscountedPrice)

filter

ใช้กรองข้อมูลตามเงื่อนไข เช่น เลือกสินค้าที่มี Category ตรงกัน

.filter(product ->
        product.getCategory()
                .equalsIgnoreCase(category))

flatMap

ใช้เชื่อมต่อกับ Method ที่คืนค่าเป็น Mono หรือ Flux

.flatMap(repository::save)

switchIfEmpty

ใช้จัดการกรณีไม่พบข้อมูล

.switchIfEmpty(Mono.error(...))

10. WebClient

WebClient ใช้เรียก REST API แบบ Reactive และ Non-blocking

public Mono<Product> getProductById(String id) {
    return client.get()
            .uri("/products/{id}", id)
            .retrieve()
            .bodyToMono(Product.class);
}

ความหมายของ Method Chain:

.get() กำหนด HTTP Method เป็น GET

.uri() กำหนด URL

.retrieve() รับข้อมูลจาก Response

.bodyToMono() แปลง Response เป็น Mono

.bodyToFlux() แปลง Response หลายรายการเป็น Flux

ตัวอย่างการ Chain Operators:

getProductById("1")
        .map(Product::getName)
        .defaultIfEmpty("ไม่พบสินค้า")
        .subscribe(System.out::println);

11. ข้อกำหนด Non-blocking

โปรเจกต์นี้ไม่ใช้คำสั่งต่อไปนี้ใน Endpoint:

.block()
Thread.sleep()

Controller จะคืน Mono หรือ Flux โดยตรง เพื่อให้ Spring WebFlux จัดการการ Subscribe เอง

12. สรุปผลการทำงาน

โปรเจกต์นี้สามารถสร้าง REST API สำหรับระบบจัดการสินค้าได้ครบถ้วน โดยใช้ Spring WebFlux และ WebClient ระบบรองรับการแสดงสินค้า การค้นหาตาม ID การสร้างสินค้า การลบสินค้า การค้นหาตาม Category และการคำนวณราคาหลังส่วนลด

การใช้ Mono, Flux, map, filter, flatMap และ WebClient ทำให้ระบบทำงานแบบ Reactive และ Non-blocking ตามข้อกำหนดของงาน

ผู้จัดทำ

รหัสนักศึกษา: 673380296-7
ชื่อ: สโรชา เสาทอง

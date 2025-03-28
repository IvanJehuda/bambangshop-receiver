# BambangShop Receiver App
Tutorial and Example for Advanced Programming 2024 - Faculty of Computer Science, Universitas Indonesia

---

## About this Project
In this repository, we have provided you a REST (REpresentational State Transfer) API project using Rocket web framework.

This project consists of four modules:
1.  `controller`: this module contains handler functions used to receive request and send responses.
    In Model-View-Controller (MVC) pattern, this is the Controller part.
2.  `model`: this module contains structs that serve as data containers.
    In MVC pattern, this is the Model part.
3.  `service`: this module contains structs with business logic methods.
    In MVC pattern, this is also the Model part.
4.  `repository`: this module contains structs that serve as databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a Rocket web framework skeleton that you can work with.

As this is an Observer Design Pattern tutorial repository, you need to implement a feature: `Notification`.
This feature will receive notifications of creation, promotion, and deletion of a product, when this receiver instance is subscribed to a certain product type.
The notification will be sent using HTTP POST request, so you need to make the receiver endpoint in this project.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Receiver" folder.

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    ROCKET_PORT=8001
    APP_INSTANCE_ROOT_URL=http://localhost:${ROCKET_PORT}
    APP_PUBLISHER_ROOT_URL=http://localhost:8000
    APP_INSTANCE_NAME=Safira Sudrajat
    ```
    Here are the details of each environment variable:
    | variable                | type   | description                                                     |
    |-------------------------|--------|-----------------------------------------------------------------|
    | ROCKET_PORT             | string | Port number that will be listened by this receiver instance.    |
    | APP_INSTANCE_ROOT_URL   | string | URL address where this receiver instance can be accessed.       |
    | APP_PUUBLISHER_ROOT_URL | string | URL address where the publisher instance can be accessed.       |
    | APP_INSTANCE_NAME       | string | Name of this receiver instance, will be shown on notifications. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)
3.  To simulate multiple instances of BambangShop Receiver (as the tutorial mandates you to do so),
    you can open new terminal, then edit `ROCKET_PORT` in `.env` file, then execute another `cargo run`.

    For example, if you want to run 3 (three) instances of BambangShop Receiver at port `8001`, `8002`, and `8003`, you can do these steps:
    -   Edit `ROCKET_PORT` in `.env` to `8001`, then execute `cargo run`.
    -   Open new terminal, edit `ROCKET_PORT` in `.env` to `8002`, then execute `cargo run`.
    -   Open another new terminal, edit `ROCKET_PORT` in `.env` to `8003`, then execute `cargo run`.

## Mandatory Checklists (Subscriber)
-   [X] Clone https://gitlab.com/ichlaffterlalu/bambangshop-receiver to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [X] Commit: `Create Notification model struct.`
    -   [X] Commit: `Create SubscriberRequest model struct.`
    -   [X] Commit: `Create Notification database and Notification repository struct skeleton.`
    -   [X] Commit: `Implement add function in Notification repository.`
    -   [X] Commit: `Implement list_all_as_string function in Notification repository.`
    -   [X] Write answers of your learning module's "Reflection Subscriber-1" questions in this README.
-   **STAGE 3: Implement services and controllers**
    -   [X] Commit: `Create Notification service struct skeleton.`
    -   [X] Commit: `Implement subscribe function in Notification service.`
    -   [X] Commit: `Implement subscribe function in Notification controller.`
    -   [X] Commit: `Implement unsubscribe function in Notification service.`
    -   [X] Commit: `Implement unsubscribe function in Notification controller.`
    -   [X] Commit: `Implement receive_notification function in Notification service.`
    -   [X] Commit: `Implement receive function in Notification controller.`
    -   [X] Commit: `Implement list_messages function in Notification service.`
    -   [X] Commit: `Implement list function in Notification controller.`
    -   [X] Write answers of your learning module's "Reflection Subscriber-2" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Subscriber) Reflections

#### Reflection Subscriber-1


### **1. Why do we use `RwLock<>` instead of `Mutex<>` for synchronizing `Vec` of Notifications?**
We use `RwLock<>` because it allows **multiple threads to read** the `Vec` **simultaneously**, only blocking when a write operation occurs. Since our notification system mostly **reads notifications** (e.g., listing them) and only **occasionally writes** (e.g., adding new notifications), `RwLock<>` improves performance by avoiding unnecessary blocking.

If we had used `Mutex<>`, **both reads and writes would require exclusive access**, meaning even simple read operations would have to wait if another thread is holding the lock. This would create **performance bottlenecks**, especially as the number of users increases. Since our use case involves many reads and few writes, `RwLock<>` is the better choice.



### **2. Why doesn’t Rust allow mutation of static variables like Java?**
Rust prevents direct mutation of static variables to **avoid data races** and enforce **thread safety** at compile time. Unlike Java, which handles thread safety at runtime, Rust requires explicit synchronization (`RwLock<>` or `Mutex<>`) when modifying static data.

If Rust allowed mutable static variables freely, different threads could modify them simultaneously, leading to **undefined behavior**. The `lazy_static` library helps by **delaying initialization** until first use while keeping the data **safe and synchronized**. This ensures we can **mutate shared static data safely** without violating Rust’s strict memory safety guarantees.

---
#### Reflection Subscriber-2


### **1. Did you explore parts of the code outside the tutorial (e.g., `src/lib.rs`)? If yes, what did you learn? If not, why?**
Yes, I explored `src/lib.rs` and `main.rs`. From `lib.rs`, I learned how **configuration management** works using `lazy_static` and Rocket’s Figment system, allowing settings to be adjusted via environment variables. I also noticed how **error handling** is standardized with `ErrorResponse`, ensuring consistent API responses. In `main.rs`, I saw how Rocket initializes routes and manages application state, making it easier to scale and organize the project.


### **2. How does the Observer pattern make it easy to add more subscribers? What happens if we add multiple instances of the Main app?**
The Observer pattern makes it easy to add more subscribers because new instances of the Receiver can **subscribe dynamically** without modifying the Main app. Once subscribed, they automatically receive notifications, making the system flexible and loosely coupled.

However, adding multiple instances of the Main app is harder because **each instance keeps its own subscriber list**. Subscribers would have to register separately with each instance, and events in one instance wouldn’t automatically notify subscribers of another instance. To solve this, we’d need **a shared message broker or database** to sync subscribers across all Main app instances.


### **3. Have you added your own tests or improved Postman documentation? Was it useful?**
Yes, I improved my Postman collection by adding **descriptions** for each endpoint and **test scripts** to verify responses. For example, I added tests to check if notifications are properly formatted and if subscriptions return the correct data.

These improvements were very useful because they made it easier to understand how the API works and **quickly verify** if my implementation was correct. 

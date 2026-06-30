--

<!-- ========================================================== -->
<!--                     FEATURED PROJECT                        -->
<!-- ========================================================== -->

<div align="center">

# 🩸 BloodLink

### *A Digital Healthcare Platform for Smarter Blood Donation & Emergency Response*

*Connecting blood donors, hospitals, and blood banks through a unified Android ecosystem.*

<br>

<img src="assets/bloodlink/banner.png" width="100%" alt="BloodLink Banner"/>

<br><br>

![Status](https://img.shields.io/badge/Status-MVP-orange?style=for-the-badge)
![Kotlin](https://img.shields.io/badge/Kotlin-7F52FF?style=for-the-badge&logo=kotlin&logoColor=white)
![Jetpack Compose](https://img.shields.io/badge/Jetpack_Compose-4285F4?style=for-the-badge&logo=jetpackcompose&logoColor=white)
![Firebase](https://img.shields.io/badge/Firebase-Cloud_Platform-FFCA28?style=for-the-badge&logo=firebase&logoColor=black)
![MVVM](https://img.shields.io/badge/MVVM-Clean_Architecture-success?style=for-the-badge)
![Material 3](https://img.shields.io/badge/Material_3-6750A4?style=for-the-badge)

</div>

---

# ❤️ Why BloodLink?

Blood donation remains one of the most critical yet fragmented areas of healthcare. During emergencies, hospitals, blood banks, and donors often rely on disconnected communication channels, manual coordination, and delayed information sharing. These inefficiencies can slow response times when every minute matters.

BloodLink was created to explore how modern Android technology can simplify this process by bringing every participant into a single digital ecosystem.

Rather than functioning as a simple donor directory, the platform is designed around the interactions between **three independent stakeholders**—individual donors, hospitals, and blood banks—allowing each to access dedicated tools while securely sharing data through a centralized cloud infrastructure.

The long-term objective is to build a reliable, scalable, and user-focused healthcare platform capable of improving communication, transparency, and accessibility throughout the blood donation process.

---

# 🎯 Vision

BloodLink is being developed with one fundamental goal:

> **To reduce the time between a blood request and a successful donation by improving communication, accessibility, and coordination through technology.**

Every design decision—from authentication and user roles to cloud synchronization and inventory management—is driven by that objective.

---

# 🏥 Platform Overview

Unlike traditional blood donation applications that focus only on donors, BloodLink follows a multi-role platform architecture where every participant receives tools designed specifically for their responsibilities.

```
                     BloodLink Platform

                           ☁
                 Firebase Cloud Platform
                           │
      ┌────────────────────┼────────────────────┐
      │                    │                    │
      ▼                    ▼                    ▼

🩸 Donor Portal      🏥 Hospital Portal    🏦 Blood Bank Portal

Register             Patient Requests      Inventory Control
Donation History     Emergency Requests    Stock Management
Request Blood        Dashboard             Availability Updates
Nearby Requests      Communication         Distribution Tracking
Profile              Inventory             Service Management
```

Each portal operates independently while remaining synchronized through Firebase services, allowing real-time information exchange without compromising role-specific functionality.

---

# 💡 Engineering Objectives

BloodLink is much more than an Android CRUD application.

The project was intentionally designed to explore several real-world software engineering challenges:

- Designing a scalable multi-role authentication system.
- Managing independent user workflows within a shared cloud database.
- Building reusable UI components using Jetpack Compose.
- Maintaining a reactive application architecture using MVVM and StateFlow.
- Synchronizing cloud data securely with Firebase Firestore.
- Creating an interface that remains simple despite handling complex healthcare workflows.
- Building a codebase that can evolve as additional modules and services are introduced.

Throughout development, the emphasis has remained on maintainability, scalability, and long-term extensibility rather than simply implementing features.

---

# 🏛 System Architecture

BloodLink follows a **role-driven MVVM architecture** with a centralized cloud backend powered by Firebase. Rather than maintaining separate applications for each stakeholder, the platform provides independent user experiences through a unified Android application.

This architecture allows shared business logic, reusable UI components, and centralized data management while keeping donor, hospital, and blood bank workflows isolated.

```
                     Android Application

                  ┌──────────────────────┐
                  │    Jetpack Compose   │
                  │  Presentation Layer  │
                  └──────────┬───────────┘
                             │
                     ViewModels (MVVM)
                             │
                    Repository Pattern
                             │
      ┌──────────────────────┼──────────────────────┐
      │                      │                      │
 Firebase Auth         Cloud Firestore      Firebase Storage
      │                      │                      │
 Authentication      Application Data       Medical Documents
 User Roles          Inventory              Profile Images
 Session             Requests               Supporting Files
```

The application is organized to keep UI, business logic, and cloud services independent, making future feature development significantly easier.

---

# 🔐 Authentication & User Management

BloodLink implements a **role-based authentication system** that provides different application experiences based on the authenticated user's role.

## Current Authentication Methods

- Email & Password Authentication
- Google Sign-In
- Role Selection during Registration

Phone number authentication using OTP is currently under development as part of the authentication roadmap.

After successful authentication, every account is associated with one of three platform roles:

- 🩸 Blood Donor
- 🏥 Hospital
- 🏦 Blood Bank

Each role is granted access only to the features and data relevant to its responsibilities.

This approach simplifies the user experience while ensuring that operational workflows remain logically separated.

---

# ☁ Cloud Infrastructure

BloodLink uses Firebase as its backend platform, allowing rapid development while maintaining scalability and secure cloud synchronization.

### Firebase Authentication

Responsible for

- User registration
- Secure login
- Google authentication
- Session management

---

### Cloud Firestore

Firestore serves as the primary database for the platform.

It stores

- User profiles
- Blood requests
- Hospital information
- Blood bank information
- Donation records
- Inventory information
- Request history
- Application metadata

The NoSQL data model allows each user role to maintain independent collections while enabling controlled relationships between them.

---

### Firebase Storage

Storage is used for files that should not be stored directly inside Firestore.

Examples include

- Profile images
- Supporting documents
- Future medical attachments

This keeps Firestore lightweight while allowing secure cloud file management.

---

# 🧩 Role-Based Platform Design

Instead of exposing every feature to every user, BloodLink dynamically adapts the interface according to the authenticated user's role.

## 🩸 Donor Portal

Designed for individuals willing to donate blood or request blood when needed.

Core capabilities include

- Account management
- Donation history
- Blood request creation
- View active community requests
- Contact hospitals and donors
- Nearby request discovery
- Profile management

---

## 🏥 Hospital Portal

Hospitals receive operational tools designed specifically for medical staff.

Current capabilities include

- Blood inventory overview
- Patient blood requests
- Emergency request broadcasting
- Inventory monitoring
- Communication with donors
- Dashboard management

---

## 🏦 Blood Bank Portal

Blood banks manage the availability and distribution of blood units.

Capabilities include

- Inventory management
- Blood stock updates
- Distribution records
- Availability tracking
- Request handling
- Service management

Each portal follows the same design language while maintaining completely different workflows.

---

# 🌍 Location-Based Services

Finding nearby blood donors is one of the key objectives of the platform.

The current implementation uses

- User-provided city information
- Saved location details
- Google Maps integration through external map launching

Future updates will introduce device-based geolocation to improve accuracy and reduce manual location input.

---

# 📨 Communication System

Rapid communication is essential during medical emergencies.

Current communication options include

- Direct phone calls
- SMS through external messaging applications

A built-in real-time chat system is planned for future releases to improve communication between donors, hospitals, and blood banks while keeping conversations within the platform.

---

# 🔔 Notification Architecture

The notification system is currently under active development.

Planned notification categories include

- Emergency blood requests
- Donation reminders
- Blood availability updates
- Request status changes
- Inventory alerts
- Important account notifications

The objective is to deliver relevant information without overwhelming users with unnecessary notifications.

---

# 🧠 Engineering Decisions

Several design decisions were made early in development to support long-term scalability.

### Single Application • Multiple Experiences

Rather than maintaining three independent applications, BloodLink delivers separate experiences from a shared codebase using role-driven navigation.

This significantly reduces maintenance effort while ensuring a consistent user experience.

---

### Cloud-First Synchronization

Application data is synchronized through Firebase services, allowing hospitals, blood banks, and donors to work with the latest available information without manual coordination.

---

### Modular Feature Growth

Many advanced capabilities—including phone OTP authentication, in-app messaging, push notifications, and enhanced location services—have been planned as modular extensions rather than tightly coupled features.

This allows the platform to evolve incrementally without requiring architectural redesign.

---

# ✨ Platform Capabilities

BloodLink is designed as a **multi-role healthcare platform**, where each stakeholder interacts with the system through workflows tailored to their responsibilities. Instead of providing a one-size-fits-all experience, every portal is optimized around the day-to-day tasks of its users.

---

## 🩸 Donor Experience

The donor portal is designed to simplify the process of blood donation while providing transparency throughout every interaction.

### Core Features

- Secure registration using Email/Password or Google Sign-In
- Personalized donor profile
- Create blood requests
- Browse active blood requests within the community
- Donation history
- Request management
- View nearby hospitals and blood banks
- Contact hospitals directly through phone or SMS
- Blood group and profile management

The donor experience prioritizes simplicity, ensuring users can request or donate blood with minimal friction during emergencies.

---

## 🏥 Hospital Portal

Hospitals require operational tools rather than traditional consumer interfaces.

The hospital dashboard focuses on improving coordination between patients, donors, and blood banks.

### Current Features

- Hospital profile management
- Blood inventory monitoring
- Patient request management
- Emergency request creation
- Dashboard overview
- Blood availability tracking
- Communication with donors
- Service management

The interface is designed to reduce administrative effort while providing quick access to critical information.

---

## 🏦 Blood Bank Portal

Blood banks operate as inventory providers within the ecosystem.

Their workflow focuses on maintaining accurate stock information while responding efficiently to requests.

### Current Features

- Blood inventory management
- Blood stock updates
- Availability management
- Distribution tracking
- Request handling
- Dashboard analytics
- Institution profile management

Future iterations will introduce additional inventory intelligence and operational analytics.

---

# 📊 Engineering Highlights

BloodLink was developed with long-term maintainability in mind.

Rather than focusing solely on feature implementation, significant attention was given to architecture and scalability.

| Engineering Area | Implementation |
|------------------|----------------|
| Architecture | MVVM + Clean Architecture |
| UI Framework | Jetpack Compose |
| Authentication | Firebase Auth + Google Sign-In |
| Database | Cloud Firestore |
| File Storage | Firebase Storage |
| State Management | StateFlow |
| Dependency Injection | Hilt |
| Navigation | Navigation Compose |
| Backend | Firebase Cloud Platform |
| Design System | Material Design 3 |

---

# 🚧 Engineering Challenges

Developing BloodLink introduced challenges that extended beyond Android development and into system design.

### Multi-Role Application Design

One of the largest challenges was designing a single application capable of serving three completely different user groups without duplicating code or compromising usability.

Each role required unique navigation flows, independent business logic, and dedicated user interfaces while still sharing common infrastructure.

---

### Cloud Data Organization

Designing Firestore collections required balancing flexibility with maintainability.

User information, blood requests, hospitals, inventories, and blood banks all needed to remain logically separated while supporting efficient queries across the platform.

---

### User Experience During Emergencies

Unlike traditional CRUD applications, BloodLink is intended for situations where users may be under stress.

This influenced many interface decisions, emphasizing clarity, accessibility, and minimizing the number of actions required to complete important tasks.

---

### Planning for Future Expansion

Although the project is currently in the MVP stage, the architecture was intentionally designed to accommodate future capabilities such as

- Phone OTP Authentication
- Push Notifications
- In-App Messaging
- Real-time Communication
- Geolocation Services
- Administrative Dashboard
- Advanced Analytics

without requiring a complete redesign.

---

# 📚 Lessons Learned

BloodLink has been my most technically challenging Android project to date.

Through its development, I gained practical experience in

- Designing scalable Android architectures.
- Building applications around multiple user roles.
- Structuring cloud databases using Firestore.
- Managing complex application state with StateFlow.
- Developing reusable Jetpack Compose components.
- Integrating authentication with Firebase.
- Organizing medium-scale Android codebases using Clean Architecture.
- Thinking beyond feature implementation toward long-term product evolution.

More importantly, this project strengthened my understanding that successful software development is not only about writing code—it is about designing systems that remain maintainable as requirements evolve.

---

# 🚀 Current Development Status

BloodLink is currently under active development as a Minimum Viable Product (MVP).

### Implemented

- Multi-role authentication
- Email & Password login
- Google Sign-In
- Donor workflow
- Hospital workflow
- Blood Bank workflow
- Cloud synchronization
- Inventory management
- Request management
- Firebase backend

### In Progress

- Phone OTP Authentication
- Push Notifications
- Reminder System
- Emergency Alert Notifications

### Planned

- Real-time Chat
- Device Geolocation
- Interactive Maps
- Administrative Dashboard
- Advanced Reporting
- Platform Analytics

---

# 📸 Project Gallery

> Screenshots below demonstrate the current MVP implementation.

| Login | Donor Dashboard |
|--------|-----------------|
| <img src="assets/bloodlink/login.png" width="100%"> | <img src="assets/bloodlink/donor_dashboard.png" width="100%"> |

| Hospital Portal | Blood Bank Portal |
|-----------------|-------------------|
| <img src="assets/bloodlink/hospital_dashboard.png" width="100%"> | <img src="assets/bloodlink/bloodbank_dashboard.png" width="100%"> |

| Blood Requests | Inventory |
|----------------|-----------|
| <img src="assets/bloodlink/requests.png" width="100%"> | <img src="assets/bloodlink/inventory.png" width="100%"> |

---

# ❤️ Final Thoughts

BloodLink represents much more than an Android application.

It reflects my journey toward designing software that addresses real-world challenges through thoughtful architecture, modern Android development practices, and scalable cloud technologies.

As development continues, I intend to expand the platform with additional capabilities while preserving the principles that guided its initial design: simplicity, maintainability, and meaningful impact.

---

<div align="center">

### ⭐ If you enjoyed exploring this project, consider giving the repository a star!

<a href="https://github.com/devilyash10/BloodLink_Android_App">
<img src="https://img.shields.io/badge/View_Source_Code-181717?style=for-the-badge&logo=github&logoColor=white">
</a>

</div>

---
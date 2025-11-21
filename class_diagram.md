# Class Diagram - FitHub Class Booking System

This diagram represents the database schema and relationships for the FitHub Class Booking System.

```mermaid
classDiagram
    class Member {
        +String member_id PK
        +String first_name
        +String last_name
        +String email
        +String member_status
        +DateTime created_at
        +DateTime updated_at
    }

    class Class {
        +Integer class_id PK
        +String class_name
        +String description
        +String instructor
        +Integer duration_minutes
        +Integer total_spots
        +String premium_status
        +DateTime created_at
        +DateTime updated_at
    }

    class ClassSchedule {
        +Integer id PK
        +Integer class_id FK
        +Date scheduled_date
        +Time time_from
        +Time time_to
        +Integer total_spots
        +Integer taken_spots
        +String status
        +DateTime created_at
        +DateTime updated_at
    }

    class ClassBooking {
        +Integer id PK
        +String user_id FK
        +Integer schedule_id FK
        +String booking_status
        +DateTime booked_at
        +DateTime cancelled_at
        +DateTime created_at
        +DateTime updated_at
    }

    Member "1" --> "0..*" ClassBooking : makes
    Class "1" --> "0..*" ClassSchedule : has
    ClassSchedule "1" --> "0..*" ClassBooking : contains
    ClassBooking --> Member : belongs to
    ClassBooking --> ClassSchedule : books
```

## Key Relationships

- **Member to ClassBooking**: One member can make many bookings (1:N)
- **Class to ClassSchedule**: One class template can have many scheduled instances (1:N)
- **ClassSchedule to ClassBooking**: One schedule can have many bookings (1:N)

## Important Columns

- **member_status**: Determines tier level (basic, premium, vip)
- **premium_status**: Defines class access requirement (basic, premium, vip)
- **taken_spots**: Current number of bookings for a schedule
- **total_spots**: Maximum capacity for a class/schedule
- **booking_status**: Current state of booking (confirmed, cancelled)

# UML Diagrams Index - FitHub Class Booking System

This document provides an index to all UML diagrams created for the FitHub Class Booking System using Mermaid syntax.

## Overview

The FitHub Class Booking System is a FastAPI-based backend application that connects to a PostgreSQL database (hosted on Supabase). The core policy is **"View All Classes, Book Based on Tier"**, which means members can view all available classes but booking access is restricted based on their membership tier (basic, premium, or vip).

## Available Diagrams

### 1. [Class Diagram](./class_diagram.md)
**Purpose**: Represents the database schema and relationships

**Key Elements**:
- **Tables**: Member, Class, ClassSchedule, ClassBooking
- **Relationships**: Member to ClassBooking (1:N), Class to ClassSchedule (1:N), ClassSchedule to ClassBooking (1:N)
- **Important Columns**:
  - `member_status` - Membership tier (basic, premium, vip)
  - `premium_status` - Class access requirement level
  - `taken_spots` - Current bookings count
  - `total_spots` - Maximum capacity
  - `booking_status` - Booking state (confirmed, cancelled)

### 2. [Component Diagram](./component_diagram.md)
**Purpose**: Shows the major software components and their interactions

**Key Components**:
- **Frontend Layer**: React/Vue UI with components
- **Backend Layer**: FastAPI application with routers (Booking, Member, Data)
- **Data Layer**: Supabase client for PostgreSQL connection
- **Database**: PostgreSQL with 4 main tables
- **External Systems**: Team 1 (User Registration), Team 6 (Notifications)

### 3. [Use Case Diagram](./use_case_diagram.md)
**Purpose**: Illustrates actors and use cases in the system

**Actors**:
- **Member**: Can book classes, cancel bookings, view schedules, view bookings
- **Admin/Staff**: Can manage schedules and classes, view schedules

**Core Use Cases**:
- Book Class (includes: Validate Tier, Check Capacity, Prevent Duplicate)
- Cancel Class
- View Schedule
- View My Bookings
- Manage Schedules (Admin only)
- Manage Classes (Admin only)

### 4. [Sequence Diagram](./sequence_diagram.md)
**Purpose**: Models the successful transaction flow for **Book Class**

**Flow Steps**:
1. Frontend → FastAPI: POST /classes/book
2. FastAPI → DB: Fetch Schedule details
3. FastAPI → DB: Fetch Member status
4. **Conditional Check**: Validate Tier (user_tier >= class_tier)
5. **Conditional Check**: Check Capacity (taken_spots < total_spots)
6. **Conditional Check**: Prevent Duplicate (no existing active booking)
7. FastAPI → DB: Insert Booking record
8. FastAPI → DB: Increment Spot Count (taken_spots + 1)
9. FastAPI → Frontend: Return Success with booking details

### 5. [Activity Diagram](./activity_diagram.md)
**Purpose**: Models the decision workflow for **Cancelling a Class**

**Decision Nodes**:
- Is Booking Found? (Check existence)
- Is Booking Owned by User? (Security check)
- Is Booking Status = 'confirmed'? (Already cancelled check)

**Process Flow**:
1. Member requests cancellation
2. Fetch booking record
3. Validate booking existence and ownership
4. Check if already cancelled
5. Update booking status to 'cancelled'
6. **Decrement Spot Count** (taken_spots - 1)
7. Return success to frontend

## Business Rules

1. **Tier-Based Access**: Members can only book classes at or below their tier level
2. **Capacity Management**: System prevents overbooking by checking available spots
3. **Duplicate Prevention**: Members cannot book the same class schedule twice
4. **Atomic Updates**: Booking/cancellation operations update both booking status and spot counts
5. **Soft Delete**: Cancellations update status rather than deleting records

## Database Schema Summary

```
member
├── member_id (PK)
├── first_name
├── last_name
├── member_status (basic/premium/vip)
└── created_at

class
├── class_id (PK)
├── class_name
├── instructor
├── premium_status (basic/premium/vip)
└── total_spots

class_schedules
├── id (PK)
├── class_id (FK → class)
├── scheduled_date
├── time_from
├── time_to
├── total_spots
└── taken_spots

class_bookings
├── id (PK)
├── user_id (FK → member)
├── schedule_id (FK → class_schedules)
├── booking_status (confirmed/cancelled)
├── booked_at
└── cancelled_at
```

## Technology Stack

- **Backend**: FastAPI (Python)
- **Database**: PostgreSQL (Supabase)
- **Frontend**: React/Vue (Port 5173)
- **API Documentation**: Swagger/OpenAPI (built-in with FastAPI)
- **ORM/Client**: Supabase Python Client

## Integration Points

- **Team 1**: Provides membership tier and user registration data
- **Team 6**: Receives booking confirmation events for notifications

## How to View Diagrams

All diagrams are written in Mermaid syntax and can be viewed:
1. On GitHub (native Mermaid rendering in markdown files)
2. In any Mermaid-compatible markdown viewer
3. Online at [Mermaid Live Editor](https://mermaid.live/)
4. In VS Code with Mermaid preview extensions

## Next Steps

These diagrams serve as architectural documentation for:
- Development team reference
- System design reviews
- Onboarding new developers
- Integration planning with other teams
- Database schema design and validation

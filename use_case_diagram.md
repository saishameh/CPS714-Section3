# Use Case Diagram - FitHub Class Booking System

This diagram illustrates the actors and use cases in the FitHub Class Booking System.

```mermaid
graph TB
    Member([Member])
    Admin([Admin/Staff])

    subgraph "FitHub Class Booking System"
        UC1[Book Class]
        UC2[Cancel Class]
        UC3[View Schedule]
        UC4[View My Bookings]
        UC5[Manage Schedules]
        UC6[Manage Classes]
        UC7[View Member Status]
        
        subgraph "Internal Validations"
            V1[Validate Membership Tier]
            V2[Check Class Capacity]
            V3[Prevent Duplicate Booking]
        end
    end

    Member --> UC1
    Member --> UC2
    Member --> UC3
    Member --> UC4
    Member --> UC7
    
    Admin --> UC5
    Admin --> UC6
    Admin --> UC3
    
    UC1 -.includes.-> V1
    UC1 -.includes.-> V2
    UC1 -.includes.-> V3
    
    UC1 -.extends.-> UC4
    UC2 -.extends.-> UC4
    
    style Member fill:#e1f5ff
    style Admin fill:#fff4e1
    style UC1 fill:#c8e6c9
    style UC2 fill:#ffccbc
    style UC3 fill:#e1bee7
    style UC4 fill:#fff9c4
    style UC5 fill:#b2dfdb
    style UC6 fill:#f8bbd0
    style UC7 fill:#d1c4e9
```

## Actor Descriptions

### Member
Regular users of the FitHub system who can:
- **Book Class**: Reserve a spot in an available class
- **Cancel Class**: Cancel their existing booking
- **View Schedule**: Browse available classes by date
- **View My Bookings**: See their upcoming confirmed bookings
- **View Member Status**: Check their membership tier and benefits

### Admin/Staff
Administrative users who can:
- **Manage Schedules**: Create, update, or delete class schedules
- **Manage Classes**: Add, modify, or remove class templates
- **View Schedule**: Monitor class availability and bookings

## Use Case Descriptions

### Primary Use Cases

1. **Book Class**
   - **Actor**: Member
   - **Description**: Member selects a class from the schedule and creates a booking
   - **Includes**: 
     - Validate Membership Tier (check if member can access premium classes)
     - Check Class Capacity (ensure spots are available)
     - Prevent Duplicate Booking (verify member hasn't already booked)
   - **Extends**: View My Bookings (after successful booking)

2. **Cancel Class**
   - **Actor**: Member
   - **Description**: Member cancels their existing booking, freeing up a spot
   - **Extends**: View My Bookings (updates booking list)

3. **View Schedule**
   - **Actor**: Member, Admin/Staff
   - **Description**: Browse available classes filtered by date, showing capacity and details

4. **View My Bookings**
   - **Actor**: Member
   - **Description**: Display all confirmed bookings for the logged-in member

5. **Manage Schedules**
   - **Actor**: Admin/Staff
   - **Description**: Create, update, or delete specific class schedule instances

6. **Manage Classes**
   - **Actor**: Admin/Staff
   - **Description**: Add, modify, or remove class templates

7. **View Member Status**
   - **Actor**: Member
   - **Description**: Check membership tier level and associated privileges

### Internal Validation Use Cases

- **Validate Membership Tier**: Ensures member has appropriate tier for class
- **Check Class Capacity**: Verifies available spots before booking
- **Prevent Duplicate Booking**: Prevents member from booking same class twice

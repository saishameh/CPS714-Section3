# Sequence Diagram - Book Class Flow

This diagram models the successful transaction flow for booking a class in the FitHub system.

```mermaid
sequenceDiagram
    participant Frontend
    participant FastAPI
    participant ValidationLogic
    participant DB as PostgreSQL Database

    Frontend->>FastAPI: POST /classes/book<br/>{user_id, schedule_id}
    
    activate FastAPI
    Note over FastAPI: BookingRequest received
    
    FastAPI->>DB: SELECT id, class_id, scheduled_date,<br/>time_from, time_to, taken_spots,<br/>total_spots FROM class_schedules<br/>WHERE id = schedule_id<br/>JOIN class ON class_id
    activate DB
    DB-->>FastAPI: Schedule + Class details
    deactivate DB
    
    alt Schedule not found
        FastAPI-->>Frontend: Error: ClassSchedule not found
    end
    
    FastAPI->>DB: SELECT member_status FROM member<br/>WHERE member_id = user_id
    activate DB
    DB-->>FastAPI: Member details {member_status}
    deactivate DB
    
    alt Member not found
        FastAPI-->>Frontend: Error: Member not found
    end
    
    FastAPI->>ValidationLogic: Validate Tier Access
    activate ValidationLogic
    Note over ValidationLogic: Check: user_tier >= class_tier<br/>basic=1, premium=2, vip=3
    
    alt Insufficient tier
        ValidationLogic-->>FastAPI: Tier validation failed
        FastAPI-->>Frontend: Error: Upgrade membership required
    end
    ValidationLogic-->>FastAPI: Tier validation passed
    deactivate ValidationLogic
    
    FastAPI->>ValidationLogic: Check Capacity
    activate ValidationLogic
    Note over ValidationLogic: Check: taken_spots < total_spots
    
    alt Class is full
        ValidationLogic-->>FastAPI: Capacity check failed
        FastAPI-->>Frontend: Error: Class is full
    end
    ValidationLogic-->>FastAPI: Capacity available
    deactivate ValidationLogic
    
    FastAPI->>DB: SELECT * FROM class_bookings<br/>WHERE user_id = user_id<br/>AND schedule_id = schedule_id<br/>AND cancelled_at IS NULL
    activate DB
    DB-->>FastAPI: Existing booking check
    deactivate DB
    
    alt Already booked
        FastAPI-->>Frontend: Error: Already booked this class
    end
    
    Note over FastAPI: All validations passed
    
    FastAPI->>DB: INSERT INTO class_bookings<br/>(user_id, schedule_id, booking_status)<br/>VALUES (user_id, schedule_id, 'confirmed')
    activate DB
    DB-->>FastAPI: Booking record created
    deactivate DB
    
    FastAPI->>DB: UPDATE class_schedules<br/>SET taken_spots = taken_spots + 1<br/>WHERE id = schedule_id
    activate DB
    DB-->>FastAPI: Spot count incremented
    deactivate DB
    
    Note over FastAPI: Trigger notification (Team 6 integration)
    
    FastAPI-->>Frontend: Success: Booking confirmed<br/>{booking_id, class_name, date, time}
    deactivate FastAPI
    
    Frontend->>Frontend: Update UI<br/>Show "Cancel" button
```

## Flow Description

### Step-by-Step Process

1. **Request Initiation**: Frontend sends POST request with user_id and schedule_id
2. **Fetch Schedule**: Retrieve class schedule and associated class details from database
3. **Fetch Member Status**: Query member table to get user's membership tier
4. **Tier Validation**: Compare user's membership tier against class premium requirement
   - Hierarchy: basic (1) < premium (2) < vip (3)
   - User tier must be >= class tier requirement
5. **Capacity Check**: Verify that taken_spots < total_spots
6. **Duplicate Prevention**: Check if user already has an active booking for this schedule
7. **Create Booking**: Insert new booking record with confirmed status
8. **Increment Spot Count**: Update class_schedules.taken_spots by 1
9. **Return Success**: Send confirmation back to frontend with booking details

### Error Handling

The sequence includes multiple validation checkpoints:
- Schedule existence check
- Member existence check
- Tier access validation
- Capacity availability check
- Duplicate booking prevention

Each validation failure returns an appropriate error message to the frontend.

### Integration Points

- **Team 6 Notification System**: After successful booking, a notification event is triggered (currently mocked)
- **Team 1 Membership Portal**: Member status data originates from Team 1's system

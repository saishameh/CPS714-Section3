# Activity Diagram - Cancel Class Workflow

This diagram models the decision workflow for cancelling a class booking in the FitHub system.

```mermaid
flowchart TD
    Start([Member Requests Cancellation]) --> Input[Receive Cancel Request<br/>user_id, booking_id]
    
    Input --> FetchBooking[Query class_bookings table<br/>WHERE id = booking_id<br/>AND user_id = user_id]
    
    FetchBooking --> CheckExists{Is Booking Found?}
    
    CheckExists -->|No| ErrorNotFound[Return Error:<br/>Booking not found or<br/>does not belong to user]
    ErrorNotFound --> End1([End])
    
    CheckExists -->|Yes| CheckOwnership{Is Booking<br/>Owned by User?}
    
    CheckOwnership -->|No| ErrorOwnership[Return Error:<br/>Unauthorized access]
    ErrorOwnership --> End2([End])
    
    CheckOwnership -->|Yes| CheckStatus{Is Booking<br/>Status = 'confirmed'?}
    
    CheckStatus -->|No - Already Cancelled| ErrorAlreadyCancelled[Return Error:<br/>Booking already cancelled]
    ErrorAlreadyCancelled --> End3([End])
    
    CheckStatus -->|Yes| GetScheduleID[Extract schedule_id<br/>from booking record]
    
    GetScheduleID --> UpdateBooking[Update class_bookings<br/>SET booking_status = 'cancelled'<br/>SET cancelled_at = NOW]
    
    UpdateBooking --> DecrementSpots[Update class_schedules<br/>SET taken_spots = taken_spots - 1<br/>WHERE id = schedule_id]
    
    DecrementSpots --> NotifyUser[Prepare Success Response<br/>booking_id, message]
    
    NotifyUser --> Success[Return Success:<br/>Booking cancelled successfully]
    
    Success --> UpdateUI[Frontend Updates UI:<br/>- Remove from My Bookings<br/>- Show "Book" button again<br/>- Refresh schedule view]
    
    UpdateUI --> End4([End])
    
    style Start fill:#4ade80
    style Success fill:#22c55e
    style UpdateUI fill:#86efac
    style ErrorNotFound fill:#ef4444
    style ErrorOwnership fill:#ef4444
    style ErrorAlreadyCancelled fill:#f59e0b
    style CheckExists fill:#3b82f6
    style CheckOwnership fill:#3b82f6
    style CheckStatus fill:#3b82f6
    style DecrementSpots fill:#8b5cf6
    style UpdateBooking fill:#8b5cf6
```

## Workflow Description

### Decision Nodes

1. **Is Booking Found?**
   - Checks if booking exists in database matching booking_id and user_id
   - Ensures the booking record exists before proceeding

2. **Is Booking Owned by User?**
   - Validates that the user_id in the booking matches the requesting user
   - Prevents unauthorized cancellations of other members' bookings

3. **Is Booking Status = 'confirmed'?**
   - Verifies the booking hasn't already been cancelled
   - Checks if cancelled_at is NULL and booking_status is 'confirmed'
   - Prevents duplicate cancellation attempts

### Process Steps

1. **Receive Cancel Request**: Member initiates cancellation with user_id and booking_id
2. **Query Booking**: Fetch booking record from class_bookings table
3. **Validation Checks**: Execute three sequential validation checks
4. **Extract Schedule ID**: Get the associated schedule_id for spot decrement
5. **Update Booking Status**: Set booking_status to 'cancelled' and timestamp cancelled_at
6. **Decrement Spot Count**: Reduce taken_spots by 1 in class_schedules table
7. **Return Success**: Send confirmation message back to frontend
8. **Update UI**: Frontend refreshes to reflect cancellation

### Error Paths

- **Booking Not Found**: Invalid booking_id or doesn't belong to user
- **Unauthorized Access**: User doesn't own the booking (security check)
- **Already Cancelled**: Booking status is already 'cancelled'

### Database Operations

- **Read Operation**: SELECT from class_bookings table
- **Write Operations**:
  - UPDATE class_bookings (set status and timestamp)
  - UPDATE class_schedules (decrement taken_spots)

### Business Rules

1. **Ownership Verification**: Users can only cancel their own bookings
2. **Idempotency**: Prevent multiple cancellations of the same booking
3. **Atomic Updates**: Booking cancellation and spot decrement happen together
4. **Soft Delete**: Cancellations update status rather than deleting records
5. **Availability Release**: Cancelled spot immediately becomes available for other members

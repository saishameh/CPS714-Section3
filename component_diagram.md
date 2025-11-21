# Component Diagram - FitHub Class Booking System

This diagram shows the major software components and their interactions in the FitHub Class Booking System.

```mermaid
graph TB
    subgraph "Frontend Layer"
        UI[React/Vue Frontend<br/>Port: 5173]
        UIComponents[UI Components<br/>- Class List<br/>- Booking Form<br/>- My Bookings]
    end

    subgraph "Backend Layer"
        API[FastAPI Application<br/>Port: 8000]
        BookingRouter[Booking Router<br/>- /classes/schedules<br/>- /classes/book<br/>- /classes/cancel<br/>- /classes/my-bookings]
        MemberRouter[Member Router<br/>- /members/:id<br/>- /members/name]
        DataRouter[Data Router<br/>- /data/]
        AuthMiddleware[CORS Middleware]
        ValidationLogic[Business Logic<br/>- Tier Validation<br/>- Capacity Check<br/>- Duplicate Prevention]
    end

    subgraph "Data Layer"
        SupabaseClient[Supabase Client<br/>PostgreSQL Connection]
    end

    subgraph "Database"
        DB[(PostgreSQL Database<br/>Supabase)]
        MemberTable[member table]
        ClassTable[class table]
        ScheduleTable[class_schedules table]
        BookingTable[class_bookings table]
    end

    subgraph "External Systems"
        Team1[Team 1: User Registration<br/>& Membership Portal]
        Team6[Team 6: Notification<br/>& Announcement System]
    end

    UI --> AuthMiddleware
    UI --> UIComponents
    AuthMiddleware --> API
    API --> BookingRouter
    API --> MemberRouter
    API --> DataRouter
    BookingRouter --> ValidationLogic
    ValidationLogic --> SupabaseClient
    MemberRouter --> SupabaseClient
    DataRouter --> SupabaseClient
    SupabaseClient --> DB
    DB --> MemberTable
    DB --> ClassTable
    DB --> ScheduleTable
    DB --> BookingTable
    
    BookingRouter -.-> Team6
    MemberRouter -.-> Team1
    
    style UI fill:#e1f5ff
    style API fill:#fff4e1
    style DB fill:#e8f5e9
    style Team1 fill:#f3e5f5
    style Team6 fill:#f3e5f5
```

## Component Descriptions

### Frontend Layer
- **React/Vue Frontend**: Single-page application providing the user interface
- **UI Components**: Reusable components for class listing, booking, and management

### Backend Layer
- **FastAPI Application**: Main REST API server handling all business logic
- **Booking Router**: Handles class booking, cancellation, and schedule retrieval
- **Member Router**: Manages member information retrieval
- **Data Router**: Generic data endpoints for testing
- **CORS Middleware**: Enables cross-origin requests from frontend
- **Business Logic**: Validates membership tiers, checks capacity, prevents duplicates

### Data Layer
- **Supabase Client**: Python client for interacting with PostgreSQL database

### Database
- **PostgreSQL Database**: Hosted on Supabase, stores all application data
- **member table**: User membership information and status
- **class table**: Class templates and definitions
- **class_schedules table**: Scheduled class instances
- **class_bookings table**: Individual booking records

### External Systems
- **Team 1**: Provides membership tier information
- **Team 6**: Receives booking confirmations for notifications

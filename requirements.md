# System Requirements Specification

This document outlines the **technical** and **functional requirements** for core backend features of the Airbnb-like system.

---

## 1. User Authentication

### Functional Requirements
- Allow users (Guest, Host, Admin) to register and log in securely.
- Support JWT-based authentication for API access.
- Ensure password reset and email verification flows.

### API Endpoints
- **POST /api/auth/register**
  - **Input:** `{ name, email, password, role }`
  - **Output:** `{ userId, token }`
- **POST /api/auth/login**
  - **Input:** `{ email, password }`
  - **Output:** `{ userId, token }`
- **POST /api/auth/logout**
  - **Input:** `{ token }`
  - **Output:** `{ message: "Logged out successfully" }`

### Validation Rules
- Email must be unique and valid format.
- Password must be at least 8 characters, containing letters and numbers.
- Role must be one of: `guest`, `host`, `admin`.

### Performance Criteria
- Authentication response time should be **<300ms** under normal load.
- Token validation must occur in **O(1)** time complexity.

---

## 2. Property Management

### Functional Requirements
- Hosts can create, update, and delete property listings.
- Properties must include metadata: title, description, location, availability, price.
- Admin can verify property before it is visible.

### API Endpoints
- **POST /api/properties**
  - **Input:** `{ title, description, location, price, availability }`
  - **Output:** `{ propertyId, status: "pending_verification" }`
- **GET /api/properties/:id**
  - **Input:** `propertyId`
  - **Output:** `{ property details }`
- **PUT /api/properties/:id**
  - **Input:** `{ title?, description?, location?, price?, availability? }`
  - **Output:** `{ propertyId, updatedFields }`
- **DELETE /api/properties/:id**
  - **Input:** `propertyId`
  - **Output:** `{ message: "Property removed" }`

### Validation Rules
- Title: Required, max 100 chars.
- Price: Must be positive numeric.
- Location: Required (city, country).
- Availability: Boolean.

### Performance Criteria
- Database queries for property search should complete in **<500ms**.
- Must support **10,000 concurrent property records** without performance degradation.

---

## 3. Booking System

### Functional Requirements
- Guests can book available properties.
- Hosts can view and manage bookings.
- Bookings must be linked with payment processing.
- Prevent double booking of the same property/date range.

### API Endpoints
- **POST /api/bookings**
  - **Input:** `{ propertyId, guestId, checkInDate, checkOutDate }`
  - **Output:** `{ bookingId, status: "pending_payment" }`
- **GET /api/bookings/:id**
  - **Input:** `bookingId`
  - **Output:** `{ booking details }`
- **PUT /api/bookings/:id/cancel**
  - **Input:** `bookingId`
  - **Output:** `{ bookingId, status: "cancelled" }`

### Validation Rules
- Check-in date must be before check-out date.
- Property must be available for the requested dates.
- Guest must have a valid, active account.

### Performance Criteria
- Booking confirmation must complete in **<1 second**.
- The system must handle at least **100 concurrent booking requests** without errors.

---

## Notes
- All APIs must follow RESTful conventions and return JSON.
- Error responses must include both an HTTP status code and descriptive message.
- Security: Use HTTPS and encrypt sensitive data (passwords, tokens, payment details).

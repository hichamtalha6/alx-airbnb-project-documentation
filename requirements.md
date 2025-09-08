# Airbnb Clone Backend Requirements

This document outlines the functional and technical requirements for key backend features of the Airbnb Clone project.  
It includes API endpoints, input/output specifications, validation rules, and performance criteria.

---

## 1. User Authentication

### Functional Requirements
- Users can **register**, **login**, **reset password**, and **verify email**.
- Support **role-based access** (Guest, Host, Admin).

### API Endpoints
| Endpoint                  | Method | Description                        | Request Body                     | Response                        |
|----------------------------|--------|------------------------------------|---------------------------------|---------------------------------|
| /api/auth/register         | POST   | Register a new user                | { name, email, password, role } | { userId, name, email, role }   |
| /api/auth/login            | POST   | Authenticate user                  | { email, password }             | { token, user }                 |
| /api/auth/logout           | POST   | Logout user                         | { token }                        | { message }                     |
| /api/auth/password-reset   | POST   | Request password reset              | { email }                        | { message }                     |
| /api/auth/verify-email     | GET    | Verify email                        | { token }                        | { message }                     |

### Validation Rules
- Email: valid format, unique
- Password: minimum 8 characters, at least one uppercase, lowercase, and number
- Role: one of `guest`, `host`, `admin`

### Performance Criteria
- Response time < 300ms for login and registration under normal load
- Support 10,000 concurrent authentication requests

---

## 2. Property Management

### Functional Requirements
- Hosts can **create, read, update, delete** properties.
- Each property includes title, description, location, price, availability, and amenities.
- Upload and manage property photos.

### API Endpoints
| Endpoint                   | Method | Description                    | Request Body                       | Response                        |
|-----------------------------|--------|--------------------------------|-----------------------------------|---------------------------------|
| /api/properties             | POST   | Create new property            | { title, description, price, location, amenities, photos } | { propertyId, message } |
| /api/properties             | GET    | Get all properties             | -                                 | [{ property objects }]           |
| /api/properties/:id         | GET    | Get property by ID             | -                                 | { property object }              |
| /api/properties/:id         | PUT    | Update property                | { title?, description?, price?, amenities?, photos? } | { message } |
| /api/properties/:id         | DELETE | Delete property                | -                                 | { message }                     |

### Validation Rules
- Price: positive decimal
- Title and description: required, max 255 chars
- Location: valid coordinates or address
- Amenities: array of strings

### Performance Criteria
- Photo upload latency < 2 seconds for 5MB images
- Pagination for listing retrieval: response < 500ms for 1,000 properties

---

## 3. Booking System

### Functional Requirements
- Users can book properties for specific dates.
- Hosts can approve or reject bookings.
- System calculates total price including fees and taxes.

### API Endpoints
| Endpoint                   | Method | Description                        | Request Body                         | Response                        |
|-----------------------------|--------|------------------------------------|-------------------------------------|---------------------------------|
| /api/bookings               | POST   | Create a new booking              | { userId, propertyId, checkIn, checkOut, guests } | { bookingId, totalAmount, status } |
| /api/bookings/:id           | GET    | Retrieve booking by ID             | -                                   | { booking object }               |
| /api/bookings/:id           | PUT    | Update booking status              | { status }                          | { message }                     |
| /api/bookings/user/:id      | GET    | Get all bookings for a user        | -                                   | [{ booking objects }]           |

### Validation Rules
- Check-in must be before check-out
- Guests must not exceed property capacity
- Dates must be available (no overlapping bookings)

### Performance Criteria
- Booking creation response < 500ms
- Concurrent booking requests handled safely to prevent double-booking

---

## Notes
- All endpoints require **JWT authentication** except registration and login.
- All APIs return standard HTTP status codes:
  - 200 OK
  - 201 Created
  - 400 Bad Request
  - 401 Unauthorized
  - 404 Not Found
  - 500 Internal Server Error
- Logs and error tracking must be implemented for monitoring.

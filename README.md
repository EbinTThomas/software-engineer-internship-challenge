# Hospital Booking System Documentation

---

## **1. Overview**

### **1.1. Project**

The **Hospital Booking System** is an enterprise-grade solution designed to streamline hospital operations by managing treatments, doctors, and patient bookings efficiently. It facilitates seamless appointment scheduling for patients without requiring authentication, while providing admins with robust tools to oversee and manage hospital services via a comprehensive dashboard. Leveraging a **Calendly-like** approach for setting doctor availability ensures flexibility and user-friendliness. The system maintains data integrity by updating booking statuses instead of deleting records, supporting auditability and traceability.

### **1.2. User Stories**

#### **Admin**

1. **Authentication**
   - Secure login to access and manage the dashboard.
   - JWT-based authentication for all admin actions.

2. **Manage Treatments**
   - Add, update, or delete hospital treatments.

3. **Manage Doctors**
   - Add, update, or delete doctor profiles and link them to treatments.

4. **Manage Doctor Availability**
   - Define, update, or remove doctor availability using a **Calendly-like** format, specifying slot durations and buffer times.

5. **View and Cancel Bookings**
   - Access all patient bookings.
   - Cancel bookings and notify patients via email.

6. **Handle Cancellation Requests**
   - Review patient cancellation requests.
   - Approve or reject requests, updating booking statuses and notifying patients accordingly.

#### **Patient**

1. **Browse Treatments**
   - View all available treatments offered by the hospital.

2. **View Doctors by Treatment**
   - See doctors associated with a specific treatment.

3. **View Available Time Slots**
   - Check a doctor's availability based on selected date and time.

4. **Book Appointment**
   - Schedule appointments by providing necessary details without authentication.
   - Receive confirmation emails upon successful booking.

5. **Request Cancellation**
   - Submit cancellation requests by entering booking ID, phone number, and date of birth.
   - Admins process these requests for approval.

---

## **2. Guidelines**

### **2.1. Tech Stack**

- **Frontend & Backend:** Next.js with TypeScript
  - **Benefits:**
    - Server-side rendering for optimal performance and SEO.
    - Integrated API routes for backend functionalities.
    - Unified codebase for web development.
  - **UI Enhancements:**
    - **Tailwind CSS:** Utilized for rapid and responsive UI development with utility-first CSS.
    - **Shadcn:** Optional component library to enhance UI consistency and aesthetics.
    - **Note:** While a polished UI is appreciated, a working implementation holds more weight in evaluations.

- **Mobile App (Optional):** Flutter
  - **Benefits:**
    - Cross-platform development for iOS and Android.
    - Rapid development cycles with hot reload.
    - Extensive widget library for rich UI components.

- **Database:** MongoDB
  - **Benefits:**
    - Flexible schema design adaptable to changing requirements.
    - Seamless integration with JavaScript/TypeScript.
    - High scalability and reliability.

- **Alternative Technologies:**
  - **Frontend:** React.js, Vue.js
  - **Backend:** Python (FastAPI, Flask, Django REST), Go (Gin)

---

## **3. Sample References for Understanding and Ease of Building**

### **3.1. Sample MongoDB Schemas**

#### **1. Admin**

- **Collection Name:** `admins`
- **Description:** Stores admin credentials and related information.
- **Fields:**
  - `_id`: ObjectId (auto-generated)
  - `username`: String (unique, required)
  - `password`: String (hashed, required)
  - `createdAt`: Date (default: current date)

#### **2. Treatment**

- **Collection Name:** `treatments`
- **Description:** Information about treatments offered.
- **Fields:**
  - `_id`: ObjectId (auto-generated)
  - `name`: String (unique, required)
  - `description`: String (required)
  - `createdAt`: Date (default: current date)

#### **3. Doctor**

- **Collection Name:** `doctors`
- **Description:** Doctor profiles and their associated treatments.
- **Fields:**
  - `_id`: ObjectId (auto-generated)
  - `name`: String (required)
  - `specialization`: String (required)
  - `treatmentId`: ObjectId (refers to `treatments._id`, required)
  - `contact`: String (required)
  - `createdAt`: Date (default: current date)

#### **4. Availability**

- **Collection Name:** `availabilities`
- **Description:** Doctors' availability schedules.
- **Fields:**
  - `_id`: ObjectId (auto-generated)
  - `doctorId`: ObjectId (refers to `doctors._id`, required)
  - `dayOfWeek`: String (enum: ['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday'], required)
  - `startTime`: String (format: "HH:MM", required)
  - `endTime`: String (format: "HH:MM", required)
  - `slotDuration`: Number (minutes, required)
  - `bufferTime`: Number (minutes, required)
  - `createdAt`: Date (default: current date)

#### **5. Booking**

- **Collection Name:** `bookings`
- **Description:** Patient appointments with doctors.
- **Fields:**
  - `_id`: ObjectId (auto-generated)
  - `bookingId`: String (unique, required)
  - `patientName`: String (required)
  - `patientAge`: Number (required)
  - `patientGender`: String (enum: ['Male', 'Female', 'Other'], required)
  - `patientEmail`: String (required)
  - `patientPhone`: String (required)
  - `patientDOB`: String (optional)
  - `treatmentId`: ObjectId (refers to `treatments._id`, required)
  - `doctorId`: ObjectId (refers to `doctors._id`, required)
  - `date`: String (format: "YYYY-MM-DD", required)
  - `startTime`: String (format: "HH:MM", required)
  - `endTime`: String (format: "HH:MM", required)
  - `additionalNotes`: String (optional)
  - `status`: String (enum: ['Confirmed', 'Cancelled'], default: 'Confirmed')
  - `createdAt`: Date (default: current date)
  - `cancelledAt`: Date (optional)

#### **6. Cancellation Request**

- **Collection Name:** `cancellation_requests`
- **Description:** Patient cancellation requests.
- **Fields:**
  - `_id`: ObjectId (auto-generated)
  - `bookingId`: String (required)
  - `patientName`: String (required)
  - `patientPhone`: String (required)
  - `patientDOB`: String (required)
  - `requestDate`: Date (default: current date)
  - `status`: String (enum: ['Pending', 'Approved', 'Rejected'], default: 'Pending')

### **3.2. Sample API Documentation**

#### **1. Public APIs (For Patients)**

##### **1.1. Get All Treatments**

- **Endpoint:** `GET /api/v1/public/treatments`
- **Description:** Retrieves a list of all available treatments.
- **Response:**
  - **200 OK:**
    ```json
    [
      {
        "id": "60d5ec49f8d2e30b8c1a2b3c",
        "name": "Cardiology",
        "description": "Heart-related consultations and treatments"
      },
      {
        "id": "60d5ec49f8d2e30b8c1a2b3d",
        "name": "Radiology",
        "description": "Imaging and radiographic services"
      }
    ]
    ```
  - **500 Internal Server Error:**
    ```json
    {
      "error": "Internal server error"
    }
    ```

##### **1.2. Get Treatment Details**

- **Endpoint:** `GET /api/v1/public/treatments/{treatmentId}`
- **Description:** Retrieves detailed information about a specific treatment.
- **Response:**
  - **200 OK:**
    ```json
    {
      "id": "60d5ec49f8d2e30b8c1a2b3c",
      "name": "Cardiology",
      "description": "Heart-related consultations and treatments"
    }
    ```
  - **404 Not Found:**
    ```json
    {
      "error": "Treatment not found"
    }
    ```

##### **1.3. Get Doctors by Treatment**

- **Endpoint:** `GET /api/v1/public/treatments/{treatmentId}/doctors`
- **Description:** Retrieves a list of doctors associated with a specific treatment.
- **Response:**
  - **200 OK:**
    ```json
    [
      {
        "id": "60d5ec49f8d2e30b8c1a2b3e",
        "name": "Dr. Jane Doe",
        "specialization": "Cardiology"
      },
      {
        "id": "60d5ec49f8d2e30b8c1a2b3f",
        "name": "Dr. John Smith",
        "specialization": "Cardiology"
      }
    ]
    ```
  - **404 Not Found:**
    ```json
    {
      "error": "No doctors found for this treatment"
    }
    ```

##### **1.4. Get Available Time Slots for a Doctor**

- **Endpoint:** `GET /api/v1/public/doctors/{doctorId}/timeslots?date={date}`
- **Description:** Retrieves available time slots for a specific doctor on a given date.
- **Parameters:**
  - `doctorId` (Path): ID of the doctor.
  - `date` (Query): Date for which to retrieve slots (e.g., `2024-05-20`).
- **Response:**
  - **200 OK:**
    ```json
    [
      {
        "startTime": "10:00",
        "endTime": "10:30",
        "status": "Available"
      },
      {
        "startTime": "10:30",
        "endTime": "11:00",
        "status": "Booked"
      }
    ]
    ```
  - **400 Bad Request:**
    ```json
    {
      "error": "Invalid date format"
    }
    ```
  - **404 Not Found:**
    ```json
    {
      "error": "Doctor not found or no availability set for the selected date"
    }
    ```

##### **1.5. Book an Appointment**

- **Endpoint:** `POST /api/v1/public/bookings`
- **Description:** Books an appointment for a patient.
- **Body:**
  ```json
  {
    "patientName": "John Doe",
    "patientAge": 35,
    "patientGender": "Male",
    "patientEmail": "john.doe@example.com",
    "patientPhone": "+1234567890",
    "treatmentId": "60d5ec49f8d2e30b8c1a2b3c",
    "doctorId": "60d5ec49f8d2e30b8c1a2b3e",
    "date": "2024-05-20",
    "startTime": "10:00",
    "endTime": "10:30",
    "additionalNotes": "Need consultation for chest pain."
  }
  ```
- **Response:**
  - **201 Created:**
    ```json
    {
      "message": "Booking successful",
      "bookingId": "HBS123456"
    }
    ```
    - **Action:** Sends a confirmation email to `john.doe@example.com`.
  - **400 Bad Request:**
    ```json
    {
      "error": "Invalid or missing booking data"
    }
    ```
  - **409 Conflict:**
    ```json
    {
      "error": "Time slot already booked"
    }
    ```
  - **404 Not Found:**
    ```json
    {
      "error": "Doctor or treatment not found"
    }
    ```

##### **1.6. Request Booking Cancellation**

- **Endpoint:** `POST /api/v1/public/bookings/cancel-request`
- **Description:** Allows patients to request the cancellation of their bookings.
- **Body:**
  ```json
  {
    "bookingId": "HBS123456",
    "patientPhone": "+1234567890",
    "patientDOB": "1989-04-15"
  }
  ```
- **Response:**
  - **200 OK:**
    ```json
    {
      "message": "Cancellation request submitted successfully"
    }
    ```
    - **Action:** Notifies the admin of the new cancellation request.
  - **400 Bad Request:**
    ```json
    {
      "error": "Invalid or missing cancellation data"
    }
    ```
  - **404 Not Found:**
    ```json
    {
      "error": "Booking not found"
    }
    ```

#### **2. Private APIs (For Admins)**

**Authentication:** All endpoints require a valid JWT in the `Authorization` header as `Bearer <token>`.

##### **2.1. Admin Login**

- **Endpoint:** `POST /api/v1/private/admin/login`
- **Description:** Authenticates an admin and returns a JWT.
- **Body:**
  ```json
  {
    "username": "admin",
    "password": "password123"
  }
  ```
- **Response:**
  - **200 OK:**
    ```json
    {
      "token": "jwt-token-string",
      "message": "Login successful"
    }
    ```
  - **401 Unauthorized:**
    ```json
    {
      "error": "Invalid username or password"
    }
    ```

##### **2.2. Create Treatment**

- **Endpoint:** `POST /api/v1/private/treatments`
- **Description:** Adds a new treatment.
- **Body:**
  ```json
  {
    "name": "Cardiology",
    "description": "Heart-related services"
  }
  ```
- **Response:**
  - **201 Created:**
    ```json
    {
      "message": "Treatment created successfully",
      "treatment": {
        "id": "60d5ec49f8d2e30b8c1a2b4a",
        "name": "Cardiology",
        "description": "Heart-related services"
      }
    }
    ```
  - **400 Bad Request:**
    ```json
    {
      "error": "Treatment name and description are required"
    }
    ```

##### **2.3. Update Treatment**

- **Endpoint:** `PATCH /api/v1/private/treatments/{treatmentId}`
- **Description:** Updates an existing treatment.
- **Body:**
  ```json
  {
    "name": "Updated Cardiology",
    "description": "Updated description for Cardiology"
  }
  ```
- **Response:**
  - **200 OK:**
    ```json
    {
      "message": "Treatment updated successfully",
      "updatedTreatment": {
        "id": "60d5ec49f8d2e30b8c1a2b4a",
        "name": "Updated Cardiology",
        "description": "Updated description for Cardiology"
      }
    }
    ```
  - **404 Not Found:**
    ```json
    {
      "error": "Treatment not found"
    }
    ```
  - **400 Bad Request:**
    ```json
    {
      "error": "Invalid update data"
    }
    ```

##### **2.4. Delete Treatment**

- **Endpoint:** `DELETE /api/v1/private/treatments/{treatmentId}`
- **Description:** Removes a treatment from the system.
- **Response:**
  - **200 OK:**
    ```json
    {
      "message": "Treatment deleted successfully"
    }
    ```
  - **404 Not Found:**
    ```json
    {
      "error": "Treatment not found"
    }
    ```

##### **2.5. Create Doctor**

- **Endpoint:** `POST /api/v1/private/doctors`
- **Description:** Adds a new doctor and associates them with a treatment.
- **Body:**
  ```json
  {
    "name": "Dr. Alice Brown",
    "specialization": "Pediatrics",
    "treatmentId": "60d5ec49f8d2e30b8c1a2b4a",
    "contact": "alice.brown@example.com"
  }
  ```
- **Response:**
  - **201 Created:**
    ```json
    {
      "message": "Doctor created successfully",
      "doctor": {
        "id": "60d5ec49f8d2e30b8c1a2b4b",
        "name": "Dr. Alice Brown",
        "specialization": "Pediatrics",
        "treatmentId": "60d5ec49f8d2e30b8c1a2b4a",
        "contact": "alice.brown@example.com"
      }
    }
    ```
  - **400 Bad Request:**
    ```json
    {
      "error": "Doctor name, specialization, and treatmentId are required"
    }
    ```
  - **404 Not Found:**
    ```json
    {
      "error": "Associated treatment not found"
    }
    ```

##### **2.6. Update Doctor**

- **Endpoint:** `PATCH /api/v1/private/doctors/{doctorId}`
- **Description:** Updates an existing doctor's details.
- **Body:**
  ```json
  {
    "name": "Dr. Alice Green",
    "specialization": "Updated Pediatrics",
    "contact": "alice.green@example.com"
  }
  ```
- **Response:**
  - **200 OK:**
    ```json
    {
      "message": "Doctor updated successfully",
      "updatedDoctor": {
        "id": "60d5ec49f8d2e30b8c1a2b4b",
        "name": "Dr. Alice Green",
        "specialization": "Updated Pediatrics",
        "contact": "alice.green@example.com",
        "treatmentId": "60d5ec49f8d2e30b8c1a2b4a"
      }
    }
    ```
  - **404 Not Found:**
    ```json
    {
      "error": "Doctor not found"
    }
    ```
  - **400 Bad Request:**
    ```json
    {
      "error": "Invalid update data"
    }
    ```

##### **2.7. Delete Doctor**

- **Endpoint:** `DELETE /api/v1/private/doctors/{doctorId}`
- **Description:** Removes a doctor from the system.
- **Response:**
  - **200 OK:**
    ```json
    {
      "message": "Doctor deleted successfully"
    }
    ```
  - **404 Not Found:**
    ```json
    {
      "error": "Doctor not found"
    }
    ```

##### **2.8. Set Doctor Availability**

- **Endpoint:** `POST /api/v1/private/doctors/{doctorId}/availabilities`
- **Description:** Defines a doctor's availability for a specific day.
- **Body:**
  ```json
  {
    "dayOfWeek": "Monday",
    "startTime": "10:00",
    "endTime": "13:00",
    "slotDuration": 30,
    "bufferTime": 10
  }
  ```
- **Response:**
  - **201 Created:**
    ```json
    {
      "message": "Availability set successfully",
      "availability": {
        "id": "60d5ec49f8d2e30b8c1a2b4c",
        "doctorId": "60d5ec49f8d2e30b8c1a2b4b",
        "dayOfWeek": "Monday",
        "startTime": "10:00",
        "endTime": "13:00",
        "slotDuration": 30,
        "bufferTime": 10
      }
    }
    ```
  - **400 Bad Request:**
    ```json
    {
      "error": "Invalid availability data"
    }
    ```
  - **404 Not Found:**
    ```json
    {
      "error": "Doctor not found"
    }
    ```

##### **2.9. Update Availability**

- **Endpoint:** `PATCH /api/v1/private/doctors/{doctorId}/availabilities/{availabilityId}`
- **Description:** Updates an existing availability entry for a doctor.
- **Body:**
  ```json
  {
    "startTime": "10:15",
    "endTime": "13:15",
    "slotDuration": 15,
    "bufferTime": 5
  }
  ```
- **Response:**
  - **200 OK:**
    ```json
    {
      "message": "Availability updated successfully",
      "updatedAvailability": {
        "id": "60d5ec49f8d2e30b8c1a2b4c",
        "doctorId": "60d5ec49f8d2e30b8c1a2b4b",
        "dayOfWeek": "Monday",
        "startTime": "10:15",
        "endTime": "13:15",
        "slotDuration": 15,
        "bufferTime": 5
      }
    }
    ```
  - **404 Not Found:**
    ```json
    {
      "error": "Availability not found for this doctor"
    }
    ```
  - **400 Bad Request:**
    ```json
    {
      "error": "Invalid update data"
    }
    ```

##### **2.10. Delete Availability**

- **Endpoint:** `DELETE /api/v1/private/doctors/{doctorId}/availabilities/{availabilityId}`
- **Description:** Removes an availability entry for a doctor.
- **Response:**
  - **200 OK:**
    ```json
    {
      "message": "Availability deleted successfully"
    }
    ```
  - **404 Not Found:**
    ```json
    {
      "error": "Availability not found for this doctor"
    }
    ```

##### **2.11. View All Bookings**

- **Endpoint:** `GET /api/v1/private/bookings`
- **Description:** Retrieves a list of all patient bookings.
- **Response:**
  - **200 OK:**
    ```json
    [
      {
        "id": "60d5ec49f8d2e30b8c1a2b5a",
        "bookingId": "HBS123456",
        "patientName": "John Doe",
        "patientAge": 35,
        "patientGender": "Male",
        "patientEmail": "john.doe@example.com",
        "patientPhone": "+1234567890",
        "treatmentId": "60d5ec49f8d2e30b8c1a2b3c",
        "doctorId": "60d5ec49f8d2e30b8c1a2b3e",
        "date": "2024-05-20",
        "startTime": "10:00",
        "endTime": "10:30",
        "additionalNotes": "Need consultation for chest pain.",
        "status": "Confirmed",
        "createdAt": "2024-04-25T10:00:00Z"
      },
      {
        "id": "60d5ec49f8d2e30b8c1a2b5b",
        "bookingId": "HBS789010",
        "patientName": "Mary Jane",
        "patientAge": 29,
        "patientGender": "Female",
        "patientEmail": "mary.jane@example.com",
        "patientPhone": "+0987654321",
        "treatmentId": "60d5ec49f8d2e30b8c1a2b3d",
        "doctorId": "60d5ec49f8d2e30b8c1a2b3f",
        "date": "2024-05-21",
        "startTime": "11:00",
        "endTime": "11:30",
        "additionalNotes": "",
        "status": "Confirmed",
        "createdAt": "2024-04-25T11:00:00Z"
      }
    ]
    ```
  - **500 Internal Server Error:**
    ```json
    {
      "error": "Internal server error"
    }
    ```

##### **2.12. Cancel Booking**

- **Endpoint:** `PATCH /api/v1/private/bookings/{bookingId}/cancel`
- **Description:** Cancels a specific patient booking and notifies the patient.
- **Response:**
  - **200 OK:**
    ```json
    {
      "message": "Booking cancelled successfully",
      "updatedBooking": {
        "id": "60d5ec49f8d2e30b8c1a2b5a",
        "bookingId": "HBS123456",
        "patientName": "John Doe",
        "patientAge": 35,
        "patientGender": "Male",
        "patientEmail": "john.doe@example.com",
        "patientPhone": "+1234567890",
        "treatmentId": "60d5ec49f8d2e30b8c1a2b3c",
        "doctorId": "60d5ec49f8d2e30b8c1a2b3e",
        "date": "2024-05-20",
        "startTime": "10:00",
        "endTime": "10:30",
        "additionalNotes": "Need consultation for chest pain.",
        "status": "Cancelled",
        "createdAt": "2024-04-25T10:00:00Z",
        "cancelledAt": "2024-05-01T12:00:00Z"
      }
    }
    ```
    - **Action:** Sends a cancellation email to the patient.
  - **404 Not Found:**
    ```json
    {
      "error": "Booking not found"
    }
    ```

##### **2.13. View Cancellation Requests**

- **Endpoint:** `GET /api/v1/private/cancellation-requests`
- **Description:** Retrieves all cancellation requests submitted by patients.
- **Response:**
  - **200 OK:**
    ```json
    [
      {
        "id": "60d5ec49f8d2e30b8c1a2b6a",
        "bookingId": "HBS123456",
        "patientName": "John Doe",
        "patientPhone": "+1234567890",
        "patientDOB": "1989-04-15",
        "requestDate": "2024-05-01T12:00:00Z",
        "status": "Pending"
      },
      {
        "id": "60d5ec49f8d2e30b8c1a2b6b",
        "bookingId": "HBS789010",
        "patientName": "Mary Jane",
        "patientPhone": "+0987654321",
        "patientDOB": "1995-07-22",
        "requestDate": "2024-05-02T09:30:00Z",
        "status": "Pending"
      }
    ]
    ```
  - **500 Internal Server Error:**
    ```json
    {
      "error": "Internal server error"
    }
    ```

##### **2.14. Approve Cancellation Request**

- **Endpoint:** `PATCH /api/v1/private/cancellation-requests/{requestId}/approve`
- **Description:** Approves a cancellation request, updates the booking status, and notifies the patient.
- **Response:**
  - **200 OK:**
    ```json
    {
      "message": "Cancellation request approved and booking cancelled successfully",
      "updatedBooking": {
        "id": "60d5ec49f8d2e30b8c1a2b5a",
        "bookingId": "HBS123456",
        "patientName": "John Doe",
        "patientAge": 35,
        "patientGender": "Male",
        "patientEmail": "john.doe@example.com",
        "patientPhone": "+1234567890",
        "treatmentId": "60d5ec49f8d2e30b8c1a2b3c",
        "doctorId": "60d5ec49f8d2e30b8c1a2b3e",
        "date": "2024-05-20",
        "startTime": "10:00",
        "endTime": "10:30",
        "additionalNotes": "Need consultation for chest pain.",
        "status": "Cancelled",
        "createdAt": "2024-04-25T10:00:00Z",
        "cancelledAt": "2024-05-01T12:00:00Z"
      }
    }
    ```
    - **Action:** Sends a cancellation confirmation email to the patient.
  - **404 Not Found:**
    ```json
    {
      "error": "Cancellation request not found"
    }
    ```
  - **400 Bad Request:**
    ```json
    {
      "error": "Cancellation request already processed"
    }
    ```

### **3.3. Sample Emails**

#### **3.1. Booking Confirmation Email**

- **Trigger:** Successful booking (`POST /api/v1/public/bookings`)
- **Recipient:** `patientEmail`
- **Subject:** `Booking Confirmation - Hospital Booking System`
- **Body:**
  ```
  Dear [Patient Name],

  Thank you for booking an appointment with us. Here are your booking details:

  Booking ID: HBS123456
  Treatment: Cardiology
  Doctor: Dr. Jane Doe
  Date: 2024-05-20
  Time: 10:00 - 10:30

  If you have any questions or need to reschedule, please contact us.

  Best regards,
  [Hospital Name]
  ```

#### **3.2. Booking Cancellation Email**

- **Trigger:** Successful cancellation (`PATCH /api/v1/private/bookings/{bookingId}/cancel`) or approval of a cancellation request.
- **Recipient:** `patientEmail`
- **Subject:** `Booking Cancellation - Hospital Booking System`
- **Body:**
  ```
  Dear [Patient Name],

  Your appointment has been successfully cancelled. Here are the details of the cancelled booking:

  Booking ID: HBS123456
  Treatment: Cardiology
  Doctor: Dr. Jane Doe
  Date: 2024-05-20
  Time: 10:00 - 10:30

  If this cancellation was a mistake or you wish to reschedule, please contact us at [Hospital Contact Information].

  Best regards,
  [Hospital Name]
  ```

#### **3.3. Cancellation Request Notification Email (Admin)**

- **Trigger:** Submission of a cancellation request by a patient.
- **Recipient:** Admin email(s).
- **Subject:** `New Cancellation Request - Hospital Booking System`
- **Body:**
  ```
  Dear Admin,

  A new cancellation request has been submitted.

  Booking ID: HBS123456
  Patient Name: John Doe
  Phone: +1234567890
  Date of Birth: 1989-04-15
  Request Date: 2024-05-01T12:00:00Z

  Please review and take the necessary action.

  Best regards,
  [Hospital Booking System]
  ```

### **3.4. Sample Pages Required**

- **Admin Interface:**
  - **Dashboard:** Overview of key metrics and activities.
  - **Treatments Management:** Add, update, delete treatments.
  - **Doctors Management:** Add, update, delete doctors and link to treatments.
  - **Availability Management:** Define and manage doctor availability.
  - **Bookings Management:** View all bookings and cancel as needed.
  - **Cancellation Requests:** Review and process cancellation requests.

- **Patient Interface:**
  - **Browse Treatments:** View all available treatments.
  - **View Doctors:** See doctors associated with a selected treatment.
  - **Check Availability:** View available time slots for a selected doctor and date.
  - **Book Appointment:** Form to schedule an appointment.
  - **Request Cancellation:** Form to submit a cancellation request.

### **3.5. Sample Directory Structure**

```
app/
├── api/
│   └── v1/
│       ├── public/
│       │   ├── treatments/
│       │   │   ├── route.ts
│       │   │   └── [treatmentId]/
│       │   │       └── route.ts
│       │   ├── doctors/
│       │   │   └── [doctorId]/
│       │   │       ├── availabilities/
│       │   │       │   └── route.ts
│       │   │       └── timeslots/
│       │   │           └── route.ts
│       │   ├── bookings/
│       │   │   ├── route.ts
│       │   │   └── cancel-request/
│       │   │       └── route.ts
│       └── private/
│           ├── admin/
│           │   └── login/
│           │       └── route.ts
│           ├── treatments/
│           │   └── route.ts
│           ├── doctors/
│           │   ├── route.ts
│           │   └── [doctorId]/
│           │       ├── availabilities/
│           │       │   └── route.ts
│           │       └── timeslots/
│           │           └── route.ts
│           ├── bookings/
│           │   ├── route.ts
│           │   └── [bookingId]/
│           │       └── route.ts
│           └── cancellations/
│               ├── route.ts
│               └── [requestId]/
│                   └── route.ts
├── admin/
│   ├── page.tsx
│   ├── treatments/
│   │   └── page.tsx
│   ├── doctors/
│   │   └── page.tsx
│   ├── bookings/
│   │   └── page.tsx
│   └── cancellations/
│       └── page.tsx
├── bookings/
│   ├── new/
│   │   └── page.tsx
│   └── cancel-request/
│       └── page.tsx
├── treatments/
│   ├── page.tsx
│   └── [treatmentId]/
│       └── doctors/
│           └── page.tsx
├── doctors/
│   └── [doctorId]/
│       └── availability/
│           └── page.tsx
├── page.tsx
└── styles/
```

**Explanation:**

- **api/v1/public/**: Public APIs accessible by patients for browsing treatments, viewing doctors, checking availability, booking appointments, and requesting cancellations.
- **api/v1/private/**: Private APIs secured with JWT for admins to manage treatments, doctors, availabilities, bookings, and handle cancellation requests.
- **admin/**: Admin-facing pages including dashboard and management interfaces for treatments, doctors, bookings, and cancellations.
- **bookings/**: Patient-facing pages for booking new appointments and requesting cancellations.
- **treatments/**: Pages for patients to browse treatments and view associated doctors.
- **doctors/**: Pages for patients to view a doctor's availability.
- **styles/**: Centralized styling files for consistent UI/UX across the application.

### **3.6. Sample README.md**


# Hospital Booking System

## Overview

The **Hospital Booking System** is a comprehensive solution designed to manage hospital treatments, doctor profiles, and patient bookings efficiently. It offers a user-friendly interface for patients to schedule appointments without the need for authentication, while providing admins with robust tools to oversee hospital operations.

## Features

- **Admin Dashboard:** Monitor key metrics, manage treatments and doctors, oversee bookings, and handle cancellation requests.
- **Patient Booking:** Seamlessly book appointments, view treatments and associated doctors, and request cancellations.
- **Secure Authentication:** JWT-based authentication for admin actions.
- **Flexible Scheduling:** Define doctor availability with customizable slot durations and buffer times.
- **Automated Notifications:** Email confirmations for bookings and cancellations.
- **Priority on Functionality:** Emphasis on delivering a fully functional system ensures reliable and efficient operations over intricate UI designs.

## Installation

1. **Clone the Repository:**
   ```bash
   git clone https://github.com/your-repo/hospital-booking-system.git
   cd hospital-booking-system
   ```
2. **Install Dependencies:**
   ```bash
   npm install
   ```
3. **Configure Environment Variables:**
   Create a `.env.local` file in the root directory and add the following:
   ```env
   MONGODB_URI=mongodb+srv://<user>:<password>@cluster.mongodb.net/<database>
   JWT_SECRET=your_secret_key
   EMAIL_SERVICE=Gmail
   EMAIL_USER=your_email@example.com
   EMAIL_PASS=your_email_password
   ```
4. **Start the Development Server:**
   ```bash
   npm run dev
   ```
   - **Access the Application:** Open [http://localhost:3000](http://localhost:3000) in your browser.

---

## **4. Additional Notes**

### **4.1. Data Integrity & Auditability**

- **Booking Management:** Instead of deleting bookings, their statuses are updated to ensure historical data remains intact for auditing and reporting purposes.
- **Cancellation Workflow:** A structured workflow for handling cancellation requests ensures clarity and accountability, with timestamps and status tracking for each request.

### **4.2. Security Best Practices**

- **Password Protection:** Admin passwords are securely hashed before storage to protect against unauthorized access.
- **JWT Handling:** Tokens are securely generated and validated to maintain the integrity of admin sessions.
- **Input Sanitization:** All user inputs are validated and sanitized to prevent security breaches such as SQL injection and XSS attacks.

### **4.3. Scalability & Maintainability**

- **Modular Architecture:** The directory structure is organized for scalability, allowing easy addition of new features and components.
- **Code Standards:** Adherence to coding standards and best practices ensures a maintainable and scalable codebase.

### **4.4. UI Considerations**

- **Tailwind CSS & Shadcn:** Utilizing Tailwind CSS for rapid, responsive design and shadcn for consistent UI components is encouraged. While a polished UI enhances user experience, the primary focus should be on delivering a fully functional system. A robust implementation will be weighted more heavily than the intricacies of the UI in evaluations.


### **4.5. Enterprise-Quality Considerations (Optional)**

- **Data Integrity:** Update booking statuses instead of deleting records to preserve historical data.
- **Security:**
  - **Password Hashing:** Use strong algorithms like bcrypt for hashing admin passwords.
  - **JWT Security:** Utilize strong, unpredictable secrets and secure storage for JWTs.
  - **Input Validation:** Implement thorough validation on both frontend and backend to prevent vulnerabilities.
  - **Rate Limiting:** Protect APIs from abuse with rate limiting mechanisms.
- **Error Handling:**
  - Provide consistent and meaningful error messages.
  - Implement server-side logging for debugging and monitoring.
- **Code Quality:**
  - Maintain clean, readable, and well-documented code.
  - Use ESLint and Prettier for consistent code formatting and linting.
  - Incorporate unit and integration tests to ensure reliability.

### **4.6. Future Enhancements**

- **Patient Authentication:** Enable patients to create accounts for managing appointments.
- **Time Zone Support:** Handle appointments across different time zones.
- **Recurring Appointments:** Allow booking of recurring appointments.
- **Role-Based Access Control:** Implement granular admin roles with specific permissions.
- **Additional Notifications:** Integrate SMS notifications alongside email confirmations.

---

Good luck, and happy coding!
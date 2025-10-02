# CareMatch Data Model

## Core Entities

### 1. User
- `user_id` (PK)  
- `first_name`  
- `last_name`  
- `email` (unique)  
- `phone_number`  
- `role` (enum: `nurse`, `hospital_admin`)  
- `password_hash`  
- `created_at`  
- `updated_at`  

---

### 2. Nurse Profile
- `nurse_id` (FK → User)  
- `license_number`  
- `license_state`  
- `certifications` (array or join table)  
- `specialties` (e.g., ICU, ER, Pediatrics)  
- `experience_years`  
- `availability_schedule` (JSON or calendar events)  
- `preferred_radius_km`  
- `rating` (float, avg of hospital reviews)  

---

### 3. Hospital / Facility
- `hospital_id` (PK)  
- `name`  
- `address`  
- `city`  
- `state`  
- `zip_code`  
- `latitude`  
- `longitude`  
- `contact_person` (FK → User)  
- `created_at`  
- `updated_at`  

---

### 4. Shift
- `shift_id` (PK)  
- `hospital_id` (FK → Hospital)  
- `role_required` (enum: RN, LPN, CNA, etc.)  
- `certifications_required` (array or join table)  
- `specialty_required`  
- `start_time`  
- `end_time`  
- `hourly_rate`  
- `status` (enum: `open`, `filled`, `cancelled`, `completed`)  
- `created_at`  

---

### 5. Shift Application / Assignment
- `application_id` (PK)  
- `shift_id` (FK → Shift)  
- `nurse_id` (FK → Nurse)  
- `status` (enum: `applied`, `accepted`, `declined`, `cancelled`, `completed`)  
- `applied_at`  
- `updated_at`  

---

### 6. Payments
- `payment_id` (PK)  
- `shift_id` (FK → Shift)  
- `nurse_id` (FK → Nurse)  
- `amount`  
- `status` (enum: `pending`, `processed`, `failed`)  
- `transaction_date`  
- `payout_method` (ACH, Stripe, PayPal)  

---

### 7. Ratings & Reviews
- `review_id` (PK)  
- `shift_id` (FK → Shift)  
- `nurse_id` (FK → Nurse)  
- `hospital_id` (FK → Hospital)  
- `rating` (1–5)  
- `comments`  
- `created_at`  

---

### 8. Notifications
- `notification_id` (PK)  
- `user_id` (FK → User)  
- `type` (enum: `new_shift`, `shift_update`, `payment_status`, `credential_expiry`)  
- `message`  
- `read_status` (boolean)  
- `created_at`  

---

## Entity Relationships (ERD – Text Form)

User (1) ── (1:1) Nurse Profile
User (1) ── (M:1) Hospital

Hospital (1) ── (M) Shift
Shift (1) ── (M) Shift Application
Nurse (1) ── (M) Shift Application

Shift (1) ── (1) Payment
Shift (1) ── (M) Reviews
Nurse (1) ── (M) Reviews
Hospital (1) ── (M) Reviews

User (1) ── (M) Notifications


---

## Design Considerations
- **Scalability**: Indexing by `hospital_id`, `nurse_id`, `status`, and geolocation.  
- **Compliance**: Encrypt nurse credentials (HIPAA).  
- **Flexibility**: Use join tables for certifications/specialties.  
- **Audit Trails**: Track shift updates, credential checks, and payments.

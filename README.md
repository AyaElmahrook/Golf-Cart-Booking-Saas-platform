⛳ Golf Cart Booking SaaS Platform — System Design
Complete system design document for a multi-tenant SaaS golf cart booking platform. Covers requirements, data model, ERD, business rules, user stories, and UI screens.

📄 Document: Requirement Survey & Design — Issue 01
👤 Author: Ayah Almahrouq
📅 Date: 2026-05-18

📋 Table of Contents
Overview
Problem Statement
User Roles
System Architecture
Database Design
Business Rules
User Stories
Screens & Features
Reports
Practical Scenarios
Future Enhancements

🌍 Overview
A SaaS platform that automates and streamlines golf cart fleet booking, assignment, and management — replacing manual/disorganized processes that lead to booking conflicts, downtime, and unpaid rides.

Core capabilities:

📅 Online booking by customers or staff
🛹 Golf cart & driver fleet management
🔋 Battery & maintenance tracking
💳 Multi-method payment processing
📊 Operational reports & dashboards
🔒 Role-based access control
🎯 Problem Statement

Pain Point	Impact
Manual/disorganized booking	Booking conflicts, double-assignments
No real-time cart availability	Downtime, idle fleet
Cash-only payments	Revenue leakage, no audit trail
No maintenance tracking	Cart breakdowns during service
No driver shift visibility	Misassigned rides, driver conflicts

👥 User Roles
Role	Access Level
Admin	Full system access — users, pricing, carts, locations, reports
Operations Staff	Booking management, customer follow-up, dashboards
Dispatcher	Cart/driver assignment, booking confirmation
Driver	View shifts, view/update booking status
Customer/Guest	Self-book, view history, make payments

🏗️ System Architecture
┌─────────────────────────────────────────────────────┐
│                   SaaS Platform                      │
├──────────┬──────────┬──────────┬──────────┬────────┤
│ Customer │ Staff    │ Dispatch │ Driver   │ Admin  │
│ Portal   │ Portal   │ Portal   │ App      │ Panel  │
├──────────┴──────────┴──────────┴──────────┴────────┤
│              API Gateway / Business Logic           │
├──────────┬──────────┬──────────┬──────────┬────────┤
│ Booking  │ Payment  │ Fleet    │ Driver   │ Report │
│ Service  │ Service  │ Service  │ Service  │ Service│
├──────────┴──────────┴──────────┴──────────┴────────┤
│                    Database Layer                   │
│  Users | Carts | Drivers | Bookings | Payments      │
│  Locations | Maintenance | Audit Logs               │
└─────────────────────────────────────────────────────┘
Service Types:

self_drive — Customer drives the cart (no driver assigned)
with_driver — A driver is assigned for the ride
🗄️ Database Design
Entity Relationship Diagram (ERD)
🔗 View on Lucidchart
(Access: hr.taheiya@gmail.com)

Core Tables
Table	Purpose
users	Authentication & authorization — login credentials, roles, active status
roles	System roles: admin, staff, driver, customer
user-role	Links users to roles (many-to-many)
locations	Zones/areas with GPS coordinates
customers	Business entity — person booking and paying
drivers	Employee drivers with shift availability
shifts	Define regular shift timings
driver-shifts	Each driver's working hours by day of week
golf-carts	Physical asset — identity, capacity, battery, status
bookings	Core record — customer, cart, driver, locations, time, price
booking-status-history	Full audit trail of every status change
payments	Financial record — method, promo codes, taxes, status
maintenance-requests	Cart maintenance — type, priority, cost, dates
audit-logs	Activity log — every action with old/new values (JSON)
Booking Status Flow
pending → confirmed → in_progress → completed
    ↓           ↓
 cancelled    no_show
Golf Cart Status
active | under_maintenance | retired | out_of_service

📏 Business Rules
#	Rule	Category
1	A cart cannot be double-booked — no time overlap	Booking
2	A driver cannot be double-assigned — no time overlap	Booking
3	A cart with under_maintenance status cannot be booked	Availability
4	A cart with battery below threshold cannot be booked	Availability
5	Passenger count must not exceed cart capacity	Booking
6	self_drive = no driver; with_driver = driver required	Booking
7	Driver must be on-shift at booking time (for with_driver)	Booking
8	Booking must be confirmed before start time	Booking
9	Cart restores to active when maintenance is completed	Maintenance
10	Deactivated users cannot log in or perform any action	Access Control
11	All booking status changes logged in booking_status_history	Audit
12	All CUD operations logged in audit_logs with old/new values	Audit

📖 User Stories
#	As a...	I want to...	So that...
1	Guest / Customer	Book a golf cart online (pickup location, time, passengers, driving type)	I can reserve a cart in advance and avoid waiting
2	Operations Staff	View and manage all bookings for today	I can monitor daily flow and respond to issues
3	Dispatcher	Assign a cart or driver to a booking	A booking can be confirmed
4	Driver	View my assigned shifts and upcoming bookings	I know my schedule and can prepare
5	Admin	Manage all system configurations (carts, shifts, users, pricing, drivers)	Settings are always accurate and up to date
6	Operations Staff	Record a maintenance request for a golf cart	The cart is taken offline and not booked
7	Customer	Apply a promo code or voucher to my booking	I can receive a discount
8	Admin	View operational reports (bookings, revenue, utilization, maintenance)	I can make data-driven decisions
9	Operations Staff	Record and track customer booking history	I can resolve disputes and provide good service

🖥️ Screens & Features
#	Screen	Key Functions
1	Login	Credential validation, role-based redirect, failed login logging
2	Dashboard	Today's bookings, cart/driver availability, quick actions
3	Customer Management	CRUD, import/export, search, deactivate
4	Golf Cart Management	CRUD, battery monitoring, maintenance history, low-battery alerts
5	Driver Management	CRUD, shift scheduling by day of week, booking assignment
6	Location/Zone Management	CRUD zones, GPS coordinates
7	Booking Form	Select customer/locations/time → validates all rules → calculates price → applies promo
8	Booking Management	View all bookings, filter by status/date/customer, confirm/cancel/reassign
9	Payment	Record payments, track method/promo/discount/tax/status
10	Maintenance Management	Create/update maintenance requests, auto status change on cart
11	Reports	Booking volume, cart utilization, driver performance, payment summary, maintenance cost
12	Audit Logs	View all system actions with timestamps, filter by user/event/date

📊 Reports
#	Report	Key Metrics
1	Booking Volume Report	Total bookings by status, by service type, average duration
2	Cart Utilization Report	Booked hours vs. available hours, utilization %, idle time
3	Driver Performance Report	Trips completed, hours worked, on-time rate, no-show rate
4	Payment Report	Total revenue, paid vs. unpaid, by method, discounts & taxes
5	Maintenance Report	Requests by type/priority, cost per cart, downtime

🧪 Practical Scenarios
Booking Conflict Detection (SQL)
SELECT *
FROM bookings
WHERE golf_cart_id = 'CART-001'
  AND status = 'confirmed'
  AND start_time < '16:00'   -- new booking end
  AND end_time   > '15:00';  -- new booking start
Two Service Types Change Request
Question	Answer
New field needed?	Add service_type enum to bookings table
Driver always required?	No — only for with_driver type
Driver field nullable?	Yes — driver_id is null for self_drive
New rules?	Self-drive → validate customer license/approval; With-driver → verify driver on-shift
Pricing?	May differ by service type

🚀 Future Enhancements
📱 Customer mobile app (iOS & Android)
📍 Real-time GIS tracking for cart locations
🤖 Customer support chatbot
📲 Multi-channel notifications (SMS, WhatsApp, Email, push)
🌐 Multi-currency & foreign payment support
🏨 Integration with resort/property management systems
📦 Booking packages & subscription plans
💬 Integration with payment gateway

# 📚 Inventory Management System - Comprehensive Documentation

**Created:** June 2026  
**For:** Second-year students and developers  
**Purpose:** Complete guide to understand, use, and develop this inventory tracking system

---

## 📋 Table of Contents

1. [Project Overview](#project-overview)
2. [Installation & Setup](#installation--setup)
3. [Architecture & Design](#architecture--design)
4. [Modules & Components](#modules--components)
5. [Models & AI Technologies](#models--ai-technologies)
6. [Database Schema](#database-schema)
7. [How to Use](#how-to-use)
8. [API Reference](#api-reference)
9. [Workflow Examples](#workflow-examples)
10. [Common Issues & Troubleshooting](#common-issues--troubleshooting)
11. [Development Guide](#development-guide)
12. [Future Enhancements](#future-enhancements)

---

## 🎯 Project Overview

### What is This Project?

**Inventory Management System** is an AI-powered application designed to track equipment/product check-ins and check-outs using:
- 🎭 **Facial Recognition** - Identifies who is checking items
- 📱 **QR Code Scanning** - Identifies what is being checked
- 📊 **Database Recording** - Logs complete history
- 🎤 **Voice Feedback** - Confirms actions to users

### Why is it Useful?

**Problem:** College/Lab equipment is hard to track. Items go missing, nobody knows who borrowed what.

**Solution:** Automate the tracking process!

```
Manual System:          Automated System:
┌────────────┐         ┌────────────┐
│ User signs │         │ User shows │
│ logbook    │         │ face & QR  │
└─────┬──────┘         └─────┬──────┘
      │                      │
      ↓                      ↓
  Takes 5 mins         Instant logging
  Error-prone          Accurate
  Slow                 Fast
```

### Real-World Use Case

**Scenario:** AIAT College Lab Equipment Management

1. **Setup:** Each equipment has a QR code sticker
2. **Borrowing:** 
   - Student walks to kiosk
   - Camera scans their face
   - Student scans equipment QR code
   - System confirms: "Welcome Raj! You've checked out Camera #5"
3. **Tracking:** Admin dashboard shows who has what
4. **Returning:** Reverse process - equipment status updates automatically

---

## 🔧 Installation & Setup

### Prerequisites

**System Requirements:**
- Python 3.8+
- Linux/Mac (primary), Windows (secondary)
- Webcam for face recognition
- 4GB+ RAM

**Install Required Libraries:**

```bash
# Navigate to project directory
cd inventory_management_stemland

# Install dependencies (create if not exists)
pip install -r requirements.txt
```

**Required Python Packages:**
```
opencv-python>=4.5.0
insightface>=0.7.0
numpy>=1.19.0
faiss-cpu>=1.7.0
Pillow>=8.0.0
pyttsx3>=2.90
pyzbar>=0.1.8
sqlite3 (built-in)
tkinter (built-in, may need: sudo apt-get install python3-tk)
```

### First-Time Setup

```bash
# 1. Initialize the database
python3 main.py

# 2. The system will create:
#    - DB_FILE (SQLite database)
#    - face_encodings.pkl (stored face data)
#    - tables (users, products, transactions)

# 3. Add your first admin user (follow the GUI prompts)

# 4. Register faces by taking photos
```

### Configuration Files

**Icons Folder (`icons/`):**
```
icons/
├── admin_icon.ico          # Main window icon
├── reload.ico              # Refresh button icon
└── search_icon.ico         # Search button icon
```

**Cascade Files (Face Detection):**
```
├── haarcascade_frontalface_default.xml   # Detects faces
└── haarcascade_eye.xml                    # Detects eyes
```

---

## 🏗️ Architecture & Design

### Layered Architecture (4-Tier Model)

```
┌─────────────────────────────────────────────────────┐
│         Presentation Layer (UI)                      │
│  - Admin Window, User Management, Product Manager    │
│  - Handles user interactions and displays results    │
└──────────────────┬──────────────────────────────────┘
                   │
┌──────────────────▼──────────────────────────────────┐
│         Business Logic Layer (Core)                  │
│  - Check-in/Check-out workflows                     │
│  - Authentication & Login verification              │
│  - Product history management                       │
└──────────────────┬──────────────────────────────────┘
                   │
        ┌──────────┴──────────┐
        │                     │
┌───────▼───────┐    ┌────────▼──────────┐
│  Utils Layer  │    │  Database Layer   │
│  (Tools)      │    │  (Data Access)    │
│               │    │                   │
│ - QR Scanner  │    │ - CRUD Ops        │
│ - Face Recog  │    │ - Queries         │
│ - Text-Speech │    │ - Schema          │
│ - UI Helpers  │    │                   │
└───────────────┘    └───────────────────┘
```

### Design Principles

| Principle | Meaning | Benefit |
|-----------|---------|---------|
| **Separation of Concerns** | Each layer has one job | Easy to maintain & debug |
| **Unidirectional Dependency** | UI → Core → Utils/DB (no reverse) | Clear data flow, less coupling |
| **Modularity** | Features are self-contained | Easy to add/remove features |
| **Reusability** | Utilities can be used elsewhere | Save development time |
| **Testability** | Each module can be tested alone | Find bugs faster |

### Data Flow Diagram

```
User Input
    ↓
┌─────────────┐
│ UI Layer    │ (Receives input from user)
└──────┬──────┘
       ↓
┌──────────────────┐
│ Core Logic       │ (Processes the request)
└──────┬───────────┘
       ↓
    ┌──┴──┐
    ↓     ↓
┌────────┐ ┌──────────┐
│Utils   │ │Database  │ (Executes operations)
└────┬───┘ └──────┬───┘
     ↓            ↓
   Models      Data Storage
     ↓            ↓
    Output → Display to User
```

---

## 📦 Modules & Components

### 1. **UI Layer** (`ui/` folder)

**Purpose:** Graphical User Interface for users to interact with the system

#### `admin_window.py` - Main Window Controller
- Creates the main application window
- Sets up 3 tabs for different functionality
- Manages keyboard shortcuts

```python
# Example: How it's structured
class AdminWindow:
    - Tab 1: History View (view all transactions)
    - Tab 2: User Management (add/remove users)
    - Tab 3: Product Manager (manage inventory)
```

#### `user_management_ui.py` - User Management Tab
**Functions:**
- `setup_tab2()` - Creates user management interface
- Add new users
- Promote/demote user roles
- Remove users
- View all users

**User Types:**
```
┌──────────────┐
│ User Types   │
├──────────────┤
│ Admin        │ (Full access)
│ Manager      │ (Can manage products)
│ User         │ (Can only check in/out)
└──────────────┘
```

#### `product_manager_ui.py` - Product Manager Tab
**Functions:**
- `setup_tab3()` - Creates product management interface
- Add new products/equipment
- Generate QR codes for items
- Modify product details
- Delete products
- View all products

**Product Information Stored:**
- Product ID (Unique)
- Product Name
- Category
- Status (Available/Borrowed)
- QR Code (Image)

---

### 2. **Core Layer** (`core/` folder)

**Purpose:** Business logic - the "brain" of the application

#### `product_management.py` - Inventory Workflow
**Main Function: `check_product()`**
```
This is the MAIN workflow:

    User shows face
         ↓
    Face recognized
         ↓
    User scans QR code
         ↓
    Product identified
         ↓
    Check product status
         ↓
    Update database (check-in or check-out)
         ↓
    Record transaction
         ↓
    Give user feedback
```

**Key Functions:**
- `check_product(tree_widget)` - Main check-in/out workflow
- `show_items(username)` - Show items a user has borrowed
- `show_items_admin(tree_widget)` - Show all transactions (admin view)
- `search_product(search_term, tree_widget)` - Search history
- `get_product_history()` - Retrieve complete transaction history

#### `login.py` - Authentication System
**Purpose:** Verify who is logging in

**Methods:**
1. **Face Recognition Login**
   - User's face is recognized by system
   - Fast & secure
   
2. **Password Login**
   - Admin credentials
   - Username + hashed password

**Key Functions:**
- `open_login_page()` - Display login window
- `verify_login(username, password)` - Check credentials
- `authenticate_with_face()` - Face-based authentication

---

### 3. **Utilities Layer** (`utils/` folder)

**Purpose:** Reusable helper functions

#### `face_recognition_utils.py` - AI Face Recognition
**Model Used:** InsightFace (buffalo_sc)

**Key Concepts:**

1. **Face Encoding:**
```
Physical Face → Camera → InsightFace → 512-D Vector
(Your actual face) (Input) (AI Model) (Face "fingerprint")
```

2. **Face Matching:**
```
Your face → Encode → Compare with Database → Find closest match
              ↓
           "Match score: 0.95" (95% confidence)
                ↓
           If > 0.6: It's you!
           If < 0.6: Unknown person
```

**Key Functions:**
- `load_known_encodings()` - Load stored face data
- `recognize_user()` - Identify who is in front of camera
- `register_user_face(username)` - Save a new person's face
- `find_matching_face(face_encoding)` - Find closest match in database
- `encode_face(image)` - Convert face image to vector

**Important Parameters:**
- `VERIFY_THRESHOLD = 0.6` - Minimum confidence for recognition
- `detection_size = (320, 320)` - Size for face detection
- `vector_dimension = 512` - Size of face encoding

#### `qr_utils.py` - QR Code Scanning
**Purpose:** Read QR codes from camera or images

**How it Works:**
```
Camera Frame → Detect QR patterns → Decode → Extract data
    ↓
Example QR content: "PROD_12345"
```

**Key Functions:**
- `scan_qr_code()` - Continuously scan until QR found
- `decode_qr_from_image(image)` - Decode QR from image file
- `is_qr_detected(frame)` - Check if QR is visible

**Uses:** `pyzbar` library for QR decoding

#### `text_utils.py` - Text-to-Speech
**Purpose:** Give audio feedback to users

**Example Usage:**
```
System: "Welcome Raj! You checked out Camera #5"
(Spoken aloud to user)
```

**Key Functions:**
- `text_to_speech(message)` - Convert text to audio
- `set_speech_rate(rate)` - Change speaking speed
- `is_audio_available()` - Check if audio is working

#### `ui_utils.py` - UI Helpers
**Purpose:** Helper functions for the interface

**Key Functions:**
- `setup_placeholder(widget)` - Create placeholder text
- `resource_path(filename)` - Find files (PyInstaller compatible)
- `get_icon(icon_name)` - Load icon files
- `format_timestamp(datetime)` - Format dates nicely

---

### 4. **Database Layer** (`db/` folder)

**Purpose:** Data storage and retrieval

#### `database_init.py` - Database Schema
**Creates these tables:**

**Table 1: users**
```sql
CREATE TABLE users (
    user_id INTEGER PRIMARY KEY,
    username TEXT UNIQUE,
    password_hash TEXT,
    user_type TEXT,              -- 'Admin', 'Manager', 'User'
    registration_date TIMESTAMP
);
```

**Table 2: products**
```sql
CREATE TABLE products (
    product_id INTEGER PRIMARY KEY,
    product_name TEXT,
    category TEXT,
    qr_code_data TEXT,
    status TEXT,                 -- 'Available' or 'Borrowed'
    registered_date TIMESTAMP
);
```

**Table 3: transactions**
```sql
CREATE TABLE transactions (
    transaction_id INTEGER PRIMARY KEY,
    user_id INTEGER,
    product_id INTEGER,
    action TEXT,                 -- 'Check-out' or 'Check-in'
    timestamp TIMESTAMP,
    FOREIGN KEY(user_id) REFERENCES users(user_id),
    FOREIGN KEY(product_id) REFERENCES products(product_id)
);
```

**Table 4: face_encodings**
```sql
CREATE TABLE face_encodings (
    encoding_id INTEGER PRIMARY KEY,
    user_id INTEGER,
    encoding_vector BLOB,        -- Stored as binary
    registered_date TIMESTAMP,
    FOREIGN KEY(user_id) REFERENCES users(user_id)
);
```

#### `database_utils.py` - Database Operations

**CRUD Operations** (Create, Read, Update, Delete):

| Operation | Function | Example |
|-----------|----------|---------|
| **Create** | `add_user(username, password, type)` | Add new user |
| **Read** | `fetch_user_data(user_id)` | Get user info |
| **Update** | `update_user_type(user_id, new_type)` | Change user role |
| **Delete** | `remove_user(user_id)` | Delete user |

**Key Functions:**
- `execute_query(sql, params)` - Run any SQL query
- `hash_password(password)` - Secure password hashing
- `verify_password(password_hash, password)` - Check password
- `add_transaction(user_id, product_id, action)` - Log check-in/out
- `get_transaction_history()` - Retrieve all transactions

**Security Features:**
- Passwords are hashed (not stored in plain text)
- Uses SQLite for local, secure data storage
- No network exposure

---

## 🧠 Models & AI Technologies

### 1. Face Recognition Model: InsightFace

**Model Name:** `buffalo_sc` (Medium-sized, balanced performance)

**Components:**
```
InsightFace = Detection + Encoding + Matching
                   ↓           ↓          ↓
              Find faces  Convert to   Compare
              in image    vectors      vectors
```

**Detection Component:**
- **Purpose:** Find faces in camera frames
- **Model:** RetinaFace
- **Output:** Face bounding boxes (coordinates)

**Encoding Component:**
- **Purpose:** Convert face image to 512-dimensional vector
- **Model:** W600k MobileNet (ArcFace)
- **Output:** 512-D numerical vector representing face features

**Matching Component:**
- **Purpose:** Find closest match in database
- **Algorithm:** FAISS IndexFlatIP (Inner Product)
- **Metric:** Cosine Similarity (values 0-1, higher = more similar)

**Example:**
```
Your Face Image → Detection → Crop face → Normalize → Encode
                                              ↓
                                        [0.12, 0.45, ..., 0.89]  (512 numbers)
                                              ↓
                              Compare with stored encodings
                                              ↓
                                        "95% match with Raj"
```

### 2. QR Code Detection

**Technology:** OpenCV + pyzbar

**Process:**
```
Camera Frame → Find QR patterns → Extract data → Parse
    ↓
Example: "EQUIPMENT_ID_12345"
```

**Advantages:**
- Fast & reliable
- Works with any smartphone-readable QR code
- No internet needed

### 3. Face Database: FAISS

**What is FAISS?**
- Fast similarity search library
- Used to find closest face match quickly
- More efficient than comparing one-by-one

**How it Works:**
```
Incoming face encoding
        ↓
    Index search
        ↓
Find top-k similar faces
        ↓
Return closest match
```

---

## 📊 Database Schema

### Entity-Relationship Diagram

```
┌──────────────┐         ┌─────────────────┐
│   users      │         │   products      │
├──────────────┤         ├─────────────────┤
│ user_id (PK) │         │ product_id (PK) │
│ username     │         │ product_name    │
│ password     │         │ category        │
│ user_type    │         │ status          │
└───────┬──────┘         └────────┬────────┘
        │                         │
        └─────────┬───────────────┘
                  │
            ┌─────▼────────────┐
            │  transactions    │
            ├──────────────────┤
            │ transaction_id   │
            │ user_id (FK)     │
            │ product_id (FK)  │
            │ action           │
            │ timestamp        │
            └──────────────────┘

┌──────────────────────┐
│  face_encodings      │
├──────────────────────┤
│ encoding_id (PK)     │
│ user_id (FK)         │
│ encoding_vector      │
│ registered_date      │
└──────────────────────┘
```

### Sample Data

**Users Table:**
```
user_id | username | password_hash      | user_type | registration_date
--------|----------|-------------------|-----------|-------------------
1       | admin    | $2b$12$abc...     | Admin     | 2024-01-15
2       | raj      | $2b$12$def...     | User      | 2024-02-20
3       | priya    | $2b$12$ghi...     | Manager   | 2024-03-10
```

**Products Table:**
```
product_id | product_name | category    | status    | qr_code_data
-----------|--------------|-------------|-----------|-------------
1          | Canon Camera | Photography | Available | PROD_001
2          | Laptop       | Computing   | Borrowed  | PROD_002
3          | Projector    | Equipment   | Available | PROD_003
```

**Transactions Table:**
```
transaction_id | user_id | product_id | action     | timestamp
---------------|---------|------------|---------| -------------------
1              | 2       | 1          | Check-out  | 2024-06-09 14:30:00
2              | 2       | 1          | Check-in   | 2024-06-09 16:45:00
3              | 3       | 2          | Check-out  | 2024-06-09 15:20:00
```

---

## 🎮 How to Use

### Starting the Application

```bash
cd /path/to/inventory_management_stemland
python3 main.py
```

**First Launch:**
1. Database initializes automatically
2. GUI window opens
3. See login screen or main menu

### User Workflows

#### **Workflow 1: Check Out Equipment**

```
Step 1: Open Application
        ↓
Step 2: Face Recognition Screen appears
        └─→ Look at camera
        └─→ System recognizes you
        └─→ Greeting: "Welcome, Raj!"
        ↓
Step 3: Scan Equipment QR Code
        └─→ Point QR code at camera
        └─→ System reads: "Camera #5"
        ↓
Step 4: Confirmation
        └─→ "You checked out Camera #5"
        └─→ Audio feedback plays
        └─→ Database updates
        ↓
Step 5: Take equipment and go!
```

#### **Workflow 2: Check In Equipment**

```
Same as check-out, but system automatically:
- Finds the last check-out transaction
- Records check-in time
- Updates status to "Available"
- Calculates usage duration
```

#### **Workflow 3: Admin - Add New User**

```
Step 1: Click "Admin Window"
        ↓
Step 2: Go to "User Management" tab
        ↓
Step 3: Click "Add User" button
        ↓
Step 4: Fill form:
        - Username
        - Password
        - User Type (Admin/Manager/User)
        ↓
Step 5: Register Face:
        - Click "Register Face"
        - Look at camera
        - System takes 5-10 photos
        - Face added to database
        ↓
Step 5: Save
        ↓
User created! Ready to use.
```

#### **Workflow 4: Admin - Add New Equipment**

```
Step 1: Go to "Product Manager" tab
        ↓
Step 2: Click "Add Product"
        ↓
Step 3: Fill form:
        - Product Name
        - Category
        - Description
        ↓
Step 4: Generate QR Code
        - Click "Generate QR"
        - System creates unique QR
        - QR displays on screen
        ↓
Step 5: Print & Stick
        - Print the QR code image
        - Stick on equipment
        ↓
Product ready for check-out!
```

#### **Workflow 5: View History**

```
Step 1: Click "History" tab
        ↓
Step 2: See all transactions:
        - Who (Username)
        - What (Equipment)
        - When (Timestamp)
        - Action (Check-in/out)
        ↓
Step 3: Search (Optional)
        - Enter username or product name
        - See filtered results
        ↓
Step 4: Export (if available)
        - Right-click
        - Export as CSV/PDF
```

---

## 🔌 API Reference

### Database Functions

#### User Operations

```python
# Add a new user
add_user(username: str, password: str, user_type: str) → bool

# Get user information
fetch_user_data(user_id: int) → dict
# Returns: {'user_id': 1, 'username': 'raj', 'user_type': 'User'}

# Update user type
update_user_type(user_id: int, new_type: str) → bool

# Remove user
remove_user(user_id: int) → bool

# Authenticate user
verify_login(username: str, password: str) → bool

# Hash password securely
hash_password(password: str) → str
```

#### Product Operations

```python
# Add product
add_product(name: str, category: str, qr_data: str) → int
# Returns: product_id

# Get product info
get_product(product_id: int) → dict
# Returns: {'product_id': 1, 'name': 'Camera', 'status': 'Available'}

# Update product status
update_product_status(product_id: int, status: str) → bool
# status: 'Available' or 'Borrowed'

# Get all products
get_all_products() → list
```

#### Transaction Operations

```python
# Log a check-in or check-out
add_transaction(user_id: int, product_id: int, action: str) → int
# action: 'Check-out' or 'Check-in'

# Get transaction history
get_transaction_history() → list

# Get user's transactions
get_user_transactions(user_id: int) → list

# Get product transaction history
get_product_transactions(product_id: int) → list
```

### Face Recognition Functions

```python
# Load all stored face encodings from database
load_known_encodings() → dict
# Returns: {user_id: encoding_vector, ...}

# Recognize user from camera
recognize_user() → tuple
# Returns: (user_id, username, confidence_score)

# Register new user's face
register_user_face(username: str, num_photos: int = 10) → bool

# Find matching face in database
find_matching_face(face_encoding) → tuple
# Returns: (user_id, confidence_score)

# Convert face image to encoding
encode_face(image) → array
# Returns: 512-dimensional vector
```

### QR Code Functions

```python
# Scan QR code from camera
scan_qr_code() → str
# Returns: QR code content (e.g., "PROD_12345")

# Decode QR from image file
decode_qr_from_image(image_path: str) → str

# Generate QR code image
generate_qr_code(data: str, output_path: str) → bool
```

### UI Functions

```python
# Text-to-speech
text_to_speech(message: str, rate: int = 150) → None

# Get resource path (handles PyInstaller)
resource_path(relative_path: str) → str

# Setup placeholder text in field
setup_placeholder(widget, placeholder_text: str) → None
```

---

## 📝 Workflow Examples

### Example 1: Complete Check-Out Process

```python
from core import check_product
from utils import recognize_user, scan_qr_code
from db import add_transaction

# Step 1: Recognize user
user_id, username, confidence = recognize_user()
print(f"User recognized: {username} (confidence: {confidence:.2%})")

# Step 2: Scan QR code
qr_data = scan_qr_code()
print(f"QR scanned: {qr_data}")

# Step 3: Extract product ID from QR
product_id = int(qr_data.split('_')[1])

# Step 4: Log transaction
transaction_id = add_transaction(user_id, product_id, "Check-out")
print(f"Transaction recorded: {transaction_id}")

# Step 5: Give feedback
text_to_speech(f"Welcome {username}! You checked out product {product_id}")
```

### Example 2: Find User's Current Borrowed Items

```python
from db import get_user_transactions, get_product
from utils import text_to_speech

user_id = 2  # Raj's ID

# Get all transactions for this user
transactions = get_user_transactions(user_id)

# Filter for active (unclosed) check-outs
borrowed_items = []
for trans in transactions:
    if trans['action'] == 'Check-out':
        # Check if there's a matching check-in
        has_checkin = any(
            t['product_id'] == trans['product_id'] and 
            t['action'] == 'Check-in' and 
            t['timestamp'] > trans['timestamp']
            for t in transactions
        )
        if not has_checkin:
            borrowed_items.append(trans['product_id'])

# Show items
for product_id in borrowed_items:
    product = get_product(product_id)
    print(f"Borrowed: {product['name']}")
    text_to_speech(f"You have borrowed {product['name']}")
```

### Example 3: Add New User with Face Registration

```python
from db import add_user, hash_password
from utils import register_user_face
import bcrypt

username = "priya"
password = "secure_password_123"
user_type = "User"

# Step 1: Hash password
password_hash = hash_password(password)

# Step 2: Add to database
user_id = add_user(username, password_hash, user_type)
print(f"User created with ID: {user_id}")

# Step 3: Register face
success = register_user_face(username, num_photos=10)
if success:
    print(f"Face registered for {username}")
else:
    print("Face registration failed")
```

---

## 🐛 Common Issues & Troubleshooting

### Issue 1: Face Recognition Not Working

**Symptoms:** System says "Face not recognized" even when your face is clear

**Solutions:**
1. **Check lighting**
   - Ensure good lighting (not too dark)
   - Avoid backlighting
   - Position yourself 1-2 meters from camera

2. **Retrain face model**
   ```bash
   # Delete old face encodings
   rm face_encodings.pkl
   
   # Re-register your face
   # Run application and re-do face registration
   ```

3. **Verify camera**
   ```bash
   # Test if camera works
   python3 -c "import cv2; cap = cv2.VideoCapture(0); print(cap.isOpened())"
   ```

### Issue 2: QR Code Not Scanning

**Symptoms:** System can't read QR code

**Solutions:**
1. **Check QR quality**
   - Ensure QR code is not damaged
   - Make sure QR is visible and not blurry
   - Print with good quality

2. **Adjust camera position**
   - Hold QR code 20-40cm from camera
   - Face camera directly
   - Avoid reflections

3. **Test QR decoder**
   ```bash
   python3 -c "from utils import decode_qr_from_image; print(decode_qr_from_image('qr_image.png'))"
   ```

### Issue 3: Database Locked or Corrupted

**Symptoms:** "Database is locked" error

**Solutions:**
```bash
# Close all instances of the app
# Check for stuck processes
ps aux | grep python3

# Kill stuck process
kill -9 <PID>

# Restart application
python3 main.py
```

### Issue 4: Audio Not Playing

**Symptoms:** Text-to-speech doesn't work

**Solutions:**
```bash
# Check audio system
python3 -c "import pyttsx3; pyttsx3.init().say('Test'); pyttsx3.init().runAndWait()"

# Check speakers
amixer set Master 100%

# Test speakers
speaker-test -t wav -c 2
```

### Issue 5: GUI Window Not Opening

**Symptoms:** No window appears, just command-line output

**Solutions:**
```bash
# Check if running in headless mode
echo $DISPLAY

# Set display if needed
export DISPLAY=:0

# Try again
python3 main.py
```

### Issue 6: Dependencies Missing

**Symptoms:** "ModuleNotFoundError: No module named 'insightface'"

**Solutions:**
```bash
# Install all dependencies
pip install -r requirements.txt

# Or install specific package
pip install insightface

# Verify installation
python3 -c "import insightface; print(insightface.__version__)"
```

---

## 👨‍💻 Development Guide

### Project Structure for Developers

```
inventory_management_stemland/
├── main.py                          # Entry point
│
├── db/                              # Database layer
│   ├── __init__.py
│   ├── database_utils.py           # CRUD operations
│   └── database_init.py            # Schema creation
│
├── utils/                           # Utilities
│   ├── __init__.py
│   ├── face_recognition_utils.py   # Face AI
│   ├── qr_utils.py                 # QR scanning
│   ├── text_utils.py               # Text-to-speech
│   └── ui_utils.py                 # UI helpers
│
├── core/                            # Business logic
│   ├── __init__.py
│   ├── product_management.py       # Check-in/out workflow
│   └── login.py                    # Authentication
│
├── ui/                              # User interface
│   ├── __init__.py
│   ├── admin_window.py             # Main window
│   ├── user_management_ui.py       # User tab
│   └── product_manager_ui.py       # Product tab
│
└── icons/                           # Icons & images
```

### Adding a New Feature

#### Example: Add Product Category Filter

**Step 1: Update Database**
```python
# db/database_utils.py
def get_products_by_category(category: str) -> list:
    """Get all products in a category"""
    query = "SELECT * FROM products WHERE category = ?"
    return execute_query(query, (category,))
```

**Step 2: Add Business Logic**
```python
# core/product_management.py
def filter_products_by_category(category: str, tree_widget):
    """Display products filtered by category in tree view"""
    products = get_products_by_category(category)
    for product in products:
        tree_widget.insert('', 'end', values=(
            product['product_id'],
            product['product_name'],
            category,
            product['status']
        ))
```

**Step 3: Add UI**
```python
# ui/product_manager_ui.py
def setup_category_filter():
    """Create dropdown to filter by category"""
    category_combo = ttk.Combobox(frame, values=[
        'Photography',
        'Computing',
        'Equipment',
        'Books'
    ])
    category_combo.bind('<<ComboboxSelected>>', 
        lambda e: filter_products_by_category(
            category_combo.get(), 
            tree_widget
        ))
```

### Code Style Guidelines

```python
# 1. Use clear variable names
good_name = "user_id"
bad_name = "uid"

# 2. Add docstrings to functions
def recognize_user():
    """
    Identify user from camera face recognition.
    
    Returns:
        tuple: (user_id, username, confidence_score)
    """
    pass

# 3. Handle errors gracefully
try:
    user_id, username, conf = recognize_user()
except Exception as e:
    print(f"Error recognizing user: {e}")
    text_to_speech("Face recognition failed")

# 4. Use type hints
def add_user(username: str, password: str, user_type: str) -> bool:
    pass
```

### Testing Your Changes

```bash
# Test individual module
python3 -m pytest tests/test_database.py -v

# Test with print statements
python3 -c "from db import execute_query; print(execute_query('SELECT COUNT(*) FROM users'))"

# Run full application
python3 main.py
```

### Common Development Tasks

| Task | How to Do It |
|------|-------------|
| Add new database table | Edit `db/database_init.py`, add `CREATE TABLE` |
| Add new UI button | Edit `ui/*.py`, add button widget and callback |
| Add new face model | Edit `utils/face_recognition_utils.py` |
| Change QR scanning timeout | Edit `utils/qr_utils.py`, update TIMEOUT constant |
| Add new user type | Edit database enum, update authentication logic |

---

## 🚀 Future Enhancements

### Planned Features

| Feature | Benefit | Difficulty |
|---------|---------|-----------|
| **Email Notifications** | Notify admins of late returns | Low |
| **Mobile App** | Access from smartphone | High |
| **Analytics Dashboard** | View usage statistics | Medium |
| **Automatic Reminders** | Remind users to return items | Low |
| **Multi-location Support** | Track items across multiple buildings | High |
| **Barcode Scanning** | Support barcodes in addition to QR | Low |
| **Biometric Authentication** | Fingerprint login option | Medium |
| **IoT Integration** | Automatic lock/unlock equipment | Very High |
| **Blockchain Ledger** | Tamper-proof transaction log | Very High |

### Optimization Ideas

1. **Performance:**
   - Cache face encodings in memory
   - Use GPU for face recognition
   - Optimize database queries

2. **Accuracy:**
   - Improve lighting for face detection
   - Use multiple camera angles
   - Train custom model on college faces

3. **Security:**
   - Two-factor authentication
   - Encryption for sensitive data
   - API authentication tokens

4. **User Experience:**
   - Mobile companion app
   - Web dashboard
   - Real-time notifications

---

## 📖 Additional Resources

### Learning Materials

**Face Recognition (InsightFace):**
- GitHub: https://github.com/deepinsight/insightface
- Paper: https://arxiv.org/abs/2012.02805
- Tutorial: https://docs.opencv.org/master/d5/d5f/tutorial_py_face_detection.html

**QR Codes:**
- Specification: https://www.qr-code.com/en/about/standards.html
- Python Library: https://pypi.org/project/pyzbar/

**Database Design:**
- SQL Tutorial: https://www.w3schools.com/sql/
- Normalization: https://en.wikipedia.org/wiki/Database_normalization

**GUI Development (Tkinter):**
- Official Docs: https://docs.python.org/3/library/tkinter.html
- Tutorial: https://realpython.com/python-gui-tkinter/

### Related Technologies

- **OpenCV:** Computer vision library
- **FAISS:** Fast similarity search
- **Tkinter:** GUI framework
- **SQLite:** Embedded database
- **NumPy:** Numerical computing

---

## 📞 Getting Help

**If you're stuck:**
1. Check the troubleshooting section above
2. Review the code comments and docstrings
3. Check console error messages (scroll up!)
4. Search GitHub issues for similar problems
5. Ask your instructor or team lead

---

## 📄 License & Credits

**Project:** Inventory Management System  
**Institution:** AIAT College  
**Developed by:** Stemland Team  
**Last Updated:** June 2026

---

## ✅ Conclusion

This inventory management system demonstrates:
- ✅ **AI/ML Integration** - Face recognition with InsightFace
- ✅ **Database Design** - Proper schema and CRUD operations
- ✅ **Software Architecture** - Layered, modular design
- ✅ **Real-World Application** - Solves actual inventory problem
- ✅ **Professional Code** - Clean, documented, maintainable

**You now have a complete understanding of this project!** 🎉

Feel free to explore the code, make improvements, and build on top of this foundation.

Happy coding! 🚀

# 🎤 EchoWarehouse

**AI-Powered Voice-Controlled Warehouse Management System**

Modern, multiplatform warehouse management system with voice control powered by artificial intelligence. Speak to the system naturally: *"Add 50 kilograms of cement to shelf 12"* - and it's done!

[![.NET](https://img.shields.io/badge/.NET-8.0-512BD4?logo=dotnet)](https://dotnet.microsoft.com/)
[![React](https://img.shields.io/badge/React-18-61DAFB?logo=react)](https://reactjs.org/)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-15+-336791?logo=postgresql)](https://www.postgresql.org/)
[![Azure](https://img.shields.io/badge/Azure-Speech%20Services-0078D4?logo=microsoft-azure)](https://azure.microsoft.com/en-us/services/cognitive-services/speech-services/)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)

---

## 📋 Table of Contents

- [Features](#-features)
- [Technology Stack](#-technology-stack)
- [Architecture](#-architecture)
- [Database Schema](#-database-schema)
- [Installation](#-installation)
- [Configuration](#-configuration)
- [Authentication](#-authentication)
- [Usage](#-usage)
- [API Documentation](#-api-documentation)
- [Voice Commands](#-voice-commands)
- [Internationalization](#-internationalization)
- [Project Structure](#-project-structure)
- [Deployment](#-deployment)
- [License](#-license)

---

## ✨ Features

### 🔐 Authentication & Security
- **Secure user authentication** with JWT tokens
- **Protected API endpoints** - authentication required for all operations
- **Registration with secret code** - prevents unauthorized registrations
- **Role-based access** (extendable for future admin features)

### 🎙️ Voice Control (AI-powered)
- **Multi-language speech recognition** (English, Hungarian) with Azure Speech Services
- **Natural language processing** with Google Gemini API
- **Real-time voice-to-action conversion**
- Supported operations:
  - 📦 Add items via voice command
  - 📤 Issue items
  - ✏️ Modify products
  - 🔍 Search
  - 🗑️ Delete

### 📊 Warehouse Management
- **Full CRUD operations** for products
- **Multiple units of measurement** (pieces, kg, g, l, ml, etc.)
- **Automatic conversions** between weight ⟷ volume
- **Density-based calculations**
- **Dynamic VAT calculation** - VAT rate stored in database, loaded on app startup
- **Net ⟷ Gross price conversion** - automatic calculation in frontend
- **Location tracking** (shelf, warehouse)
- **Serial number & article number** management
- **Stock movement tracking**
- **History & audit trail**

### 🌐 Internationalization (i18n)
- **Database-driven localization** - all UI text stored as resource keys in PostgreSQL
- **One-time load on startup** - all translations fetched once and cached in Context
- **LocalStorage persistence** - selected locale saved locally (e.g., en-US, hu-HU)
- **useLocalization hook** - simple key resolution in components
- **No language parameter in URLs** - locale managed entirely client-side
- **Instant language switching** - no API calls needed after initial load
- **Offline support** - works without backend once translations are loaded

### 💰 Dynamic VAT Management
- **Database-driven VAT rate** - configurable without code changes
- **Frontend caching** - VAT rate loaded once on app startup
- **Automatic price calculation** - enter net or gross, the other is calculated automatically
- **Historical tracking** - VAT rate changes tracked with effective dates

### 🔍 Search & Filter
- Quick search by name, article number, serial number
- Filter by location
- Stock level alerts
- Full-text search

### 📈 Reporting & Analytics
- Stock movement reports
- Voice command analytics
- Real-time inventory status
- Export functionality (CSV, Excel)

---

## 🛠️ Technology Stack

### Backend
```
✅ .NET 8.0 (multiplatform: Windows, Linux, macOS)
✅ ASP.NET Core Web API
✅ ASP.NET Core Identity (authentication)
✅ JWT Bearer Authentication
✅ Entity Framework Core 8.0 (Code-First)
✅ Npgsql (PostgreSQL provider)
✅ Dapper (raw SQL queries)
✅ Azure.CognitiveServices.Speech
✅ Google.Cloud.AIPlatform.V1 (Gemini API)
✅ Swagger/OpenAPI (API documentation)
✅ AutoMapper
✅ FluentValidation
✅ Serilog (structured logging)
```

### Design Patterns
- **Repository Pattern** - data access layer abstraction
- **Dependency Injection** - ASP.NET Core built-in DI container
- **Unit of Work Pattern** - transaction management
- **CQRS-lite** - separation of commands and queries

### Frontend
```
✅ React 18 + TypeScript
✅ Vite (build tool)
✅ Tailwind CSS
✅ Axios (HTTP client)
✅ Context API (state management + localization)
✅ LocalStorage (locale + auth token persistence)
✅ Custom useLocalization hook
✅ Custom useAuth hook
✅ Protected Routes
✅ Web Speech API
```

### Database
```
✅ PostgreSQL 15+
✅ Entity Framework Core Migrations
✅ Raw SQL support for complex queries
✅ Full-text search (PostgreSQL native)
✅ JSONB support (voice command logs)
✅ Resource key storage for i18n
✅ User authentication storage
✅ VAT settings storage
```

### DevOps & Hosting
```
✅ Azure App Service (F1 Free Tier)
✅ Firebase Hosting (frontend)
✅ Supabase (PostgreSQL hosting)
✅ GitHub Actions (CI/CD)
✅ Docker support
✅ Environment variable management (.env files)
```

---

## 🏗️ Architecture

### High-Level Architecture

```
┌──────────────────────────────────────────────────────┐
│                 React Frontend                        │
│  ┌────────────────────────────────────────────────┐  │
│  │  Authentication Check (Login/Register)         │  │
│  │  → JWT token in LocalStorage                   │  │
│  └────────────────────────────────────────────────┘  │
│  ┌────────────────────────────────────────────────┐  │
│  │  App Initialization (if authenticated):        │  │
│  │  1. Load VAT rate (ONE TIME)                   │  │
│  │  2. Load localization (ONE TIME)               │  │
│  │  3. Store in Context                           │  │
│  └────────────────────────────────────────────────┘  │
│  ┌────────────────────────────────────────────────┐  │
│  │  Voice Input  │  Product CRUD  │  Dashboard   │  │
│  │  All requests include JWT token               │  │
│  │  Prices calculated with cached VAT rate       │  │
│  └────────────────────────────────────────────────┘  │
└───────────────────────┬──────────────────────────────┘
                        │ HTTPS/REST API + JWT
                        ▼
┌──────────────────────────────────────────────────────┐
│            ASP.NET Core Web API (.NET 8)             │
│  ┌────────────────────────────────────────────────┐  │
│  │  JWT Authentication Middleware                 │  │
│  │  → Validates token on every request            │  │
│  └────────────────────────────────────────────────┘  │
│  ┌────────────────────────────────────────────────┐  │
│  │           Controllers Layer                    │  │
│  │  AuthController (login/register)               │  │
│  │  VoiceController │ ProductsController │ ...    │  │
│  │  LocalizationController │ SettingsController  │  │
│  │  Swagger UI at /swagger                       │  │
│  └────────────────┬───────────────────────────────┘  │
│                   │                                   │
│  ┌────────────────▼───────────────────────────────┐  │
│  │           Services Layer                       │  │
│  │  AuthService │ VoiceService │ AIService        │  │
│  │  LocalizationService │ SettingsService         │  │
│  └────────────────┬───────────────────────────────┘  │
│                   │                                   │
│  ┌────────────────▼───────────────────────────────┐  │
│  │      Repository Pattern + UnitOfWork           │  │
│  │  IUserRepo │ IProductRepo │ IVatSettingsRepo  │  │
│  └────────────────┬───────────────────────────────┘  │
│                   │                                   │
│  ┌────────────────▼───────────────────────────────┐  │
│  │         Data Access Layer                      │  │
│  │  EF Core Context │ Dapper (Raw SQL)            │  │
│  └────────────────┬───────────────────────────────┘  │
└────────────────────┼──────────────────────────────────┘
                     │
                     ▼
      ┌──────────────────────────────────┐
      │   PostgreSQL Database            │
      │  Users (authentication)          │
      │  Products │ History │ VoiceLog   │
      │  ResourceKeys (i18n)             │
      │  VatSettings (tax rate)          │
      └──────────────────────────────────┘

      ┌──────────────────────────────────┐
      │   External Services              │
      │  Azure Speech │ Google Gemini    │
      └──────────────────────────────────┘

      ┌──────────────────────────────────┐
      │   Environment Variables          │
      │  REGISTRATION_SECRET (not in git)│
      └──────────────────────────────────┘
```

### Backend Architecture Layers

```
EchoWarehouse.API/
├── Controllers/          // API endpoints
├── Services/            // Business logic
│   ├── Interfaces/
│   └── Implementations/
├── Repositories/        // Data access (Repository Pattern)
│   ├── Interfaces/
│   └── Implementations/
├── Data/               // EF Core Context, Migrations
├── Models/             // Domain entities
│   ├── Entities/
│   ├── DTOs/
│   └── ViewModels/
├── Middleware/         // Custom middleware + JWT Auth
├── Validators/         // FluentValidation
└── Extensions/         // Service extensions, helpers
```

### Application Flow

```
1. User opens app
   ↓
2. Check LocalStorage for JWT token
   ↓
3a. No token → Show Login/Register screen
3b. Has token → Validate with backend
   ↓
4. If authenticated:
   - Load VAT rate: GET /api/settings/vat (ONE TIME)
   - Load localization: GET /api/localization/all?locale=en-US (ONE TIME)
   - Store both in Context
   ↓
5. Main app ready:
   - All requests include Authorization: Bearer {token}
   - Prices calculated with cached VAT
   - UI text from cached localization
   ↓
6. Logout → Clear token, redirect to login
```

---

## 💾 Database Schema

### Entity Relationship Diagram

```
┌─────────────────┐
│      Users      │
└─────────────────┘
        (no direct relations - authentication only)

┌─────────────────┐
│    Products     │
└────────┬────────┘
         │
         │ 1:N
         │
         ├─────────────────┐
         │                 │
         ▼                 ▼
┌─────────────────┐  ┌─────────────────┐
│     History     │  │ Voice Commands  │
└─────────────────┘  └─────────────────┘

┌─────────────────┐
│  Resource Keys  │  (independent - i18n)
└─────────────────┘

┌─────────────────┐
│  VAT Settings   │  (independent - tax configuration)
└─────────────────┘
```

### Tables Overview

| Table Name | Purpose | Key Relations |
|------------|---------|---------------|
| `users` | Store user accounts for authentication | Independent |
| `products` | Store all warehouse products and inventory | Parent to `history` and `voice_commands` |
| `history` | Audit trail of all product changes | Foreign key to `products` |
| `voice_commands` | Log of all voice interactions and AI parsing | Foreign key to `products` (nullable) |
| `resource_keys` | Localization strings for UI | Independent |
| `vat_settings` | VAT/Tax rate configuration | Independent |

---

### Table: `users`

**Purpose:** User authentication and authorization

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| `id` | UUID | Primary Key, Default: auto-generated | Unique user identifier |
| `username` | VARCHAR(50) | NOT NULL, UNIQUE | Username for login |
| `email` | VARCHAR(255) | NOT NULL, UNIQUE | User email address |
| `password_hash` | VARCHAR(255) | NOT NULL | Hashed password (bcrypt) |
| `role` | VARCHAR(20) | NOT NULL, Default: 'user' | User role (user, admin) |
| `is_active` | BOOLEAN | Default: true | Account active status |
| `created_at` | TIMESTAMP | Default: NOW() | Account creation timestamp |
| `updated_at` | TIMESTAMP | Default: NOW() | Last update timestamp |
| `last_login` | TIMESTAMP | NULL | Last login timestamp |

**Indexes:**
- Primary Key: `id`
- Unique Index on: `username`, `email`
- Index on: `role`, `is_active`

**Notes:**
- Passwords are hashed using bcrypt with salt
- JWT tokens are issued upon successful login
- Role field is extendable for future RBAC implementation

---

### Table: `vat_settings`

**Purpose:** Store VAT/Tax rate configuration

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| `id` | UUID | Primary Key, Default: auto-generated | Unique setting identifier |
| `rate` | DECIMAL(5,2) | NOT NULL | VAT rate percentage (e.g., 27.00 for 27%) |
| `is_active` | BOOLEAN | NOT NULL, Default: true | Whether this rate is currently active |
| `effective_from` | TIMESTAMP | NOT NULL | Date from which this rate is effective |
| `description` | TEXT | NULL | Description or reason for the rate |
| `created_at` | TIMESTAMP | Default: NOW() | Record creation timestamp |
| `updated_at` | TIMESTAMP | Default: NOW() | Last update timestamp |

**Indexes:**
- Primary Key: `id`
- Index on: `is_active`, `effective_from`

**Business Rules:**
- Only one record should have `is_active = true` at a time
- Frontend loads the active VAT rate on app startup
- Historical rates are preserved for audit purposes

**Example records:**

| rate | is_active | effective_from | description |
|------|-----------|----------------|-------------|
| 27.00 | true | 2024-01-01 | Hungarian standard VAT rate |
| 25.00 | false | 2023-01-01 | Previous VAT rate |

---

### Table: `products`

**Purpose:** Main inventory table storing all product information

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| `id` | UUID | Primary Key, Default: auto-generated | Unique product identifier |
| `name` | VARCHAR(255) | NOT NULL | Product name |
| `description` | TEXT | NULL | Detailed product description |
| `quantity` | DECIMAL(10,2) | NOT NULL, Default: 0 | Current stock quantity |
| `unit` | VARCHAR(20) | NULL | Unit of measurement (kg, l, pieces, etc.) |
| `density` | DECIMAL(10,4) | NULL | Product density for weight/volume conversion |
| `density_unit` | VARCHAR(20) | NULL | Density unit (g/cm³, kg/l, etc.) |
| `warehouse_entry_date` | TIMESTAMP | NOT NULL | Date when product entered warehouse |
| `serial_number` | VARCHAR(100) | NULL | Product serial number |
| `article_number` | VARCHAR(100) | NULL | Product article/SKU number |
| `location` | VARCHAR(100) | NULL | Storage location (shelf, zone, etc.) |
| `net_price` | DECIMAL(10,2) | NULL | Net price (without tax) |
| `gross_price` | DECIMAL(10,2) | NULL | Gross price (with tax) |
| `voice_created` | BOOLEAN | Default: false | Flag indicating if created via voice command |
| `created_at` | TIMESTAMP | Default: NOW() | Record creation timestamp |
| `updated_at` | TIMESTAMP | Default: NOW() | Last update timestamp |

**Indexes:**
- Primary Key: `id`
- Index on: `name`, `article_number`, `serial_number`, `location`, `created_at`

**Notes:**
- Price calculation (net ↔ gross) is done on frontend using cached VAT rate
- Both net_price and gross_price are stored for data integrity

---

### Table: `history`

**Purpose:** Audit trail tracking all product changes and stock movements

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| `id` | UUID | Primary Key, Default: auto-generated | Unique history record identifier |
| `product_id` | UUID | NOT NULL, Foreign Key | Reference to products table |
| `action_type` | VARCHAR(50) | NOT NULL | Action type: created, updated, added, removed |
| `quantity_change` | DECIMAL(10,2) | NULL | Amount of quantity changed |
| `unit` | VARCHAR(20) | NULL | Unit of measurement for the change |
| `quantity_before` | DECIMAL(10,2) | NULL | Quantity before the action |
| `quantity_after` | DECIMAL(10,2) | NULL | Quantity after the action |
| `description` | TEXT | NULL | Additional description of the change |
| `created_at` | TIMESTAMP | Default: NOW() | When the action occurred |

**Foreign Keys:**
- `product_id` → `products.id` (ON DELETE CASCADE)

**Indexes:**
- Primary Key: `id`
- Index on: `product_id`, `created_at`, `action_type`

---

### Table: `voice_commands`

**Purpose:** Log all voice command interactions and AI parsing results

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| `id` | UUID | Primary Key, Default: auto-generated | Unique command identifier |
| `raw_transcript` | TEXT | NOT NULL | Original speech-to-text output |
| `parsed_intent` | JSONB | NULL | AI-parsed intent (JSON structure) |
| `action_taken` | VARCHAR(50) | NULL | Action executed: add, remove, update, search, delete |
| `product_id` | UUID | NULL, Foreign Key | Reference to affected product (if any) |
| `locale` | VARCHAR(10) | NULL | Language code used (en-US, hu-HU) |
| `success` | BOOLEAN | Default: true | Whether command executed successfully |
| `error_message` | TEXT | NULL | Error details if command failed |
| `processing_time_ms` | INTEGER | NULL | Time taken to process command (milliseconds) |
| `created_at` | TIMESTAMP | Default: NOW() | Command timestamp |

**Foreign Keys:**
- `product_id` → `products.id` (ON DELETE SET NULL)

**Indexes:**
- Primary Key: `id`
- Index on: `created_at`, `locale`, `action_taken`, `success`
- GIN Index on: `parsed_intent` (for JSONB queries)

**Example `parsed_intent` JSONB structure:**
```json
{
  "action": "add",
  "product": "cement",
  "quantity": 50,
  "unit": "kg",
  "location": "shelf 12",
  "confidence": 0.95
}
```

---

### Table: `resource_keys`

**Purpose:** Store all UI text translations for internationalization

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| `id` | UUID | Primary Key, Default: auto-generated | Unique resource key identifier |
| `key` | VARCHAR(255) | NOT NULL | Resource key (e.g., UI_Product_Name) |
| `locale` | VARCHAR(10) | NOT NULL | Language code (en-US, hu-HU) |
| `value` | TEXT | NOT NULL | Translated text value |
| `category` | VARCHAR(50) | NULL | Category: product, voice, common, menu, etc. |
| `description` | TEXT | NULL | Description for translators |
| `created_at` | TIMESTAMP | Default: NOW() | Record creation timestamp |
| `updated_at` | TIMESTAMP | Default: NOW() | Last update timestamp |

**Unique Constraints:**
- Unique combination of (`key`, `locale`)

**Indexes:**
- Primary Key: `id`
- Unique Index on: (`key`, `locale`)
- Index on: `locale`, `category`

**Example records:**

| key | locale | value | category |
|-----|--------|-------|----------|
| UI_Product_Name | en-US | Product Name | product |
| UI_Product_Name | hu-HU | Termék neve | product |
| UI_Voice_Listening | en-US | Listening... | voice |
| UI_Voice_Listening | hu-HU | Hallgatlak... | voice |
| UI_Auth_Login | en-US | Login | auth |
| UI_Auth_Login | hu-HU | Bejelentkezés | auth |

---

### Database Views

#### View: `product_statistics`

**Purpose:** Aggregate product transaction statistics

**Columns:**
- `id` - Product ID
- `name` - Product name
- `quantity` - Current quantity
- `unit` - Unit of measurement
- `location` - Storage location
- `transaction_count` - Total number of transactions
- `total_added` - Total quantity added
- `total_removed` - Total quantity removed
- `last_transaction_date` - Most recent transaction

**Source:** Aggregates data from `products` and `history` tables

---

#### View: `voice_analytics`

**Purpose:** Voice command usage and performance metrics

**Columns:**
- `locale` - Language code
- `action_taken` - Command action type
- `command_count` - Total commands
- `avg_processing_time` - Average processing time
- `success_count` - Successful commands
- `error_count` - Failed commands
- `success_rate` - Percentage of successful commands

**Source:** Aggregates data from `voice_commands` table

---

### Data Relationships

```
users (independent - authentication only)

products (1) ──────< (N) history
   │
   │ (optional)
   │
   └──────< (N) voice_commands

resource_keys (independent - no foreign keys)

vat_settings (independent - no foreign keys)
```

**Cascade Rules:**
- Delete product → Cascade delete all history records
- Delete product → Set product_id to NULL in voice_commands
- Delete user → No cascades (users are independent)

---

## 🚀 Installation

### Prerequisites

- **.NET 8.0 SDK** - [Download](https://dotnet.microsoft.com/download/dotnet/8.0)
- **Node.js 18+** and **npm** - [Download](https://nodejs.org/)
- **PostgreSQL 15+** - [Download](https://www.postgresql.org/download/) OR [Supabase](https://supabase.com) account
- **Azure Speech Services** - [Free tier](https://azure.microsoft.com/en-us/services/cognitive-services/speech-services/)
- **Google Cloud Account** with Gemini API access - [Google AI Studio](https://ai.google.dev/)
- **Git** - [Download](https://git-scm.com/)

### 1️⃣ Clone Repository

```bash
git clone https://github.com/OmniStruo/EchoWarehouse.git
cd EchoWarehouse
```

### 2️⃣ Backend Setup

```bash
cd backend/EchoWarehouse.API

# Install NuGet packages
dotnet restore

# Configure environment variables
# Create appsettings.Development.json (NOT in git)
# OR create .env file

# IMPORTANT: Set registration secret
# This secret is required for user registration
# Keep it secure and DO NOT commit to git
```

**Create `appsettings.Development.json`:**
```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Host=localhost;Database=echowarehouse;Username=postgres;Password=yourpassword"
  },
  "Authentication": {
    "RegistrationSecret": "your-super-secret-registration-code-here",
    "JwtSecret": "your-jwt-secret-key-minimum-32-characters",
    "JwtIssuer": "EchoWarehouse",
    "JwtAudience": "EchoWarehouse",
    "JwtExpirationHours": 24
  },
  "AzureSpeech": {
    "SubscriptionKey": "YOUR_AZURE_SPEECH_KEY",
    "Region": "westeurope"
  },
  "GoogleGemini": {
    "ApiKey": "YOUR_GOOGLE_GEMINI_API_KEY",
    "ProjectId": "your-project-id",
    "Location": "us-central1",
    "Model": "gemini-1.5-flash"
  }
}
```

**Add to `.gitignore`:**
```
appsettings.Development.json
.env
*.env
```

```bash
# Run migrations
dotnet ef database update

# Seed initial data (localization + default VAT)
dotnet run --seed-data

# Run application
dotnet run
```

Backend available at: `https://localhost:5001`

**Swagger UI available at:** `https://localhost:5001/swagger`

### 3️⃣ Frontend Setup

```bash
cd ../../frontend

# Install dependencies
npm install

# Environment variables
cp .env.example .env
# Edit .env file
```

**Edit `.env`:**
```env
VITE_API_URL=https://localhost:5001
VITE_ENABLE_VOICE=true
VITE_DEFAULT_LOCALE=en-US
```

```bash
# Start development server
npm run dev
```

Frontend available at: `http://localhost:5173`

### 4️⃣ Create First User

**Option A: Using API directly (Postman, curl, etc.)**

```bash
curl -X POST https://localhost:5001/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{
    "username": "admin",
    "email": "admin@echowarehouse.com",
    "password": "SecurePassword123!",
    "registrationSecret": "your-super-secret-registration-code-here"
  }'
```

**Option B: Using Swagger UI**

1. Navigate to `https://localhost:5001/swagger`
2. Find `POST /api/auth/register`
3. Click "Try it out"
4. Fill in the request body with registration secret
5. Execute

### 5️⃣ Login

After creating a user, login via:
- Frontend: `http://localhost:5173` (login screen)
- API: `POST /api/auth/login`

### 6️⃣ Docker (Optional)

```bash
# Start full stack (backend + PostgreSQL)
docker-compose up -d

# PostgreSQL only
docker-compose up -d postgres
```

---

## ⚙️ Configuration

### Backend Configuration (`appsettings.json`)

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Host=localhost;Database=echowarehouse;Username=postgres;Password=yourpassword;SSL Mode=Prefer"
  },
  "Authentication": {
    "RegistrationSecret": "SET_IN_ENVIRONMENT_VARIABLE",
    "JwtSecret": "SET_IN_ENVIRONMENT_VARIABLE",
    "JwtIssuer": "EchoWarehouse",
    "JwtAudience": "EchoWarehouse",
    "JwtExpirationHours": 24
  },
  "AzureSpeech": {
    "SubscriptionKey": "YOUR_AZURE_SPEECH_KEY",
    "Region": "westeurope"
  },
  "GoogleGemini": {
    "ApiKey": "YOUR_GOOGLE_GEMINI_API_KEY",
    "ProjectId": "your-project-id",
    "Location": "us-central1",
    "Model": "gemini-1.5-flash"
  },
  "Localization": {
    "DefaultLocale": "en-US",
    "SupportedLocales": ["en-US", "hu-HU"]
  },
  "VatSettings": {
    "DefaultRate": 27.00
  },
  "Cors": {
    "AllowedOrigins": [
      "http://localhost:5173",
      "https://yourapp.web.app"
    ]
  },
  "Swagger": {
    "Enabled": true,
    "Title": "EchoWarehouse API",
    "Version": "v1",
    "Description": "AI-Powered Voice-Controlled Warehouse Management System API"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning",
      "Microsoft.EntityFrameworkCore": "Warning"
    }
  }
}
```

### Environment Variables (Production - REQUIRED)

**⚠️ NEVER commit these to git!**

```bash
# Database
ConnectionStrings__DefaultConnection="Host=your-db-host;Database=echowarehouse;Username=user;Password=pass"

# Authentication (CRITICAL - KEEP SECRET)
Authentication__RegistrationSecret="your-super-secret-code"
Authentication__JwtSecret="your-jwt-secret-minimum-32-chars"

# External Services
AzureSpeech__SubscriptionKey="..."
GoogleGemini__ApiKey="..."

# Or use Azure Key Vault (recommended for production)
```

### Frontend Configuration (`.env`)

```env
VITE_API_URL=https://localhost:5001
VITE_ENABLE_VOICE=true
VITE_DEFAULT_LOCALE=en-US
```

---

## 🔐 Authentication

### Registration Flow

**Endpoint:** `POST /api/auth/register`

**Request:**
```json
{
  "username": "johndoe",
  "email": "john@example.com",
  "password": "SecurePassword123!",
  "registrationSecret": "your-super-secret-registration-code"
}
```

**Response (Success):**
```json
{
  "success": true,
  "message": "User registered successfully",
  "userId": "550e8400-e29b-41d4-a716-446655440000"
}
```

**Response (Invalid Secret):**
```json
{
  "success": false,
  "message": "Invalid registration secret"
}
```

**Notes:**
- Registration secret is configured in `appsettings.json` or environment variable
- Secret is **NOT** stored in database
- This prevents unauthorized users from creating accounts
- Only users with the secret can register

---

### Login Flow

**Endpoint:** `POST /api/auth/login`

**Request:**
```json
{
  "username": "johndoe",
  "password": "SecurePassword123!"
}
```

**Response (Success):**
```json
{
  "success": true,
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "expiresAt": "2026-02-08T10:30:00Z",
  "user": {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "username": "johndoe",
    "email": "john@example.com",
    "role": "user"
  }
}
```

**Response (Invalid Credentials):**
```json
{
  "success": false,
  "message": "Invalid username or password"
}
```

**Frontend Flow:**
1. User enters credentials
2. POST to `/api/auth/login`
3. Receive JWT token
4. Store token in LocalStorage
5. Include token in all subsequent requests: `Authorization: Bearer {token}`
6. Load VAT settings and localization
7. Show main app

---

### Protected Routes

**All API endpoints except `/api/auth/*` require authentication.**

**Request Header:**
```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

**Unauthorized Response (401):**
```json
{
  "message": "Unauthorized",
  "details": "Invalid or expired token"
}
```

**Frontend Protected Routes:**
- `/` (dashboard)
- `/products`
- `/history`
- `/settings`
- All other routes except `/login` and `/register`

---

## 📖 Usage

### First Time Setup

1. **Backend must be running** with database migrated and seeded
2. **Open frontend** at `http://localhost:5173`
3. **You'll see the registration screen** (first user only)
4. **Enter registration details + secret code**
5. **Login** with your credentials

### Daily Usage

1. **Open the app** at `http://localhost:5173`
2. **Login** if not already authenticated
3. **App loads:**
   - VAT rate from backend (cached)
   - Localization from backend (cached)
   - Main interface becomes available
4. **Select language** (English/Hungarian) from dropdown
5. **Use voice or manual entry:**
   - Click microphone 🎤 for voice commands
   - Use forms for manual product entry
   - Prices auto-calculate based on VAT rate
6. **Logout** when done

### VAT Calculation Examples

**Frontend automatically calculates net ↔ gross:**

**Example 1: User enters Net Price**
- User inputs: Net Price = 1000
- VAT rate (loaded from backend) = 27%
- Frontend calculates: Gross Price = 1000 × 1.27 = 1270
- Both values saved to database

**Example 2: User enters Gross Price**
- User inputs: Gross Price = 1270
- VAT rate (loaded from backend) = 27%
- Frontend calculates: Net Price = 1270 / 1.27 = 1000
- Both values saved to database

**Formula used:**
```typescript
// Net to Gross
gross = net * (1 + vatRate / 100)

// Gross to Net
net = gross / (1 + vatRate / 100)
```

### Swagger API Documentation

Access the interactive API documentation at:

**Development:** `https://localhost:5001/swagger`

**Production:** `https://your-api.azurewebsites.net/swagger`

**Note:** Some endpoints in Swagger require authentication. Click "Authorize" and enter your JWT token.

---

## 🔌 API Documentation

### Base URL
```
Development: https://localhost:5001/api
Production: https://your-api.azurewebsites.net/api
```

### API Endpoints Overview

For full interactive API documentation, visit **[Swagger UI](/swagger)** after starting the backend.

#### **Authentication (Public - No Auth Required)**

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/auth/register` | Register new user (requires secret) |
| POST | `/api/auth/login` | Login and get JWT token |

#### **Authentication (Protected - Auth Required)**

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/auth/logout` | Logout current user |
| GET | `/api/auth/me` | Get current user info |
| PUT | `/api/auth/change-password` | Change password |

#### **Settings (Protected)**

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/settings/vat` | Get active VAT rate (called once on app startup) |

#### **Localization (Protected - Called ONCE on app startup)**

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/localization/all?locale={locale}` | Get ALL resource keys for a locale |
| GET | `/api/localization/locales` | Get supported locales |

#### **Products (Protected)**

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/products` | Get all products (paginated) |
| GET | `/api/products/{id}` | Get single product by ID |
| POST | `/api/products` | Create new product |
| PUT | `/api/products/{id}` | Update product |
| DELETE | `/api/products/{id}` | Delete product |
| GET | `/api/products/search?q={query}` | Search products |
| POST | `/api/products/{id}/stock` | Stock operation (add/remove) |
| GET | `/api/products/statistics` | Get product statistics |

#### **Voice (Protected)**

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/voice/process` | Process voice command |
| GET | `/api/voice/history` | Get voice command history |
| GET | `/api/voice/analytics` | Get voice analytics |

#### **History (Protected)**

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/history` | Get all operations |
| GET | `/api/history/product/{id}` | Get product history |
| GET | `/api/history/export` | Export history (CSV/Excel) |

#### **Health (Public)**

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/health` | Health check endpoint |
| GET | `/api/health/ready` | Readiness probe |
| GET | `/api/health/live` | Liveness probe |

### Example Responses

#### Register User

**Request:**
```http
POST /api/auth/register
Content-Type: application/json

{
  "username": "johndoe",
  "email": "john@example.com",
  "password": "SecurePassword123!",
  "registrationSecret": "your-super-secret-code"
}
```

**Response:**
```json
{
  "success": true,
  "message": "User registered successfully",
  "userId": "550e8400-e29b-41d4-a716-446655440000"
}
```

#### Login

**Request:**
```http
POST /api/auth/login
Content-Type: application/json

{
  "username": "johndoe",
  "password": "SecurePassword123!"
}
```

**Response:**
```json
{
  "success": true,
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI1NTBlODQwMC1lMjliLTQxZDQtYTcxNi00NDY2NTU0NDAwMDAiLCJ1bmlxdWVfbmFtZSI6ImpvaG5kb2UiLCJyb2xlIjoidXNlciIsImV4cCI6MTczODkzNDIwMH0...",
  "expiresAt": "2026-02-08T10:30:00Z",
  "user": {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "username": "johndoe",
    "email": "john@example.com",
    "role": "user"
  }
}
```

#### Get VAT Rate (App Startup)

**Request:**
```http
GET /api/settings/vat
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

**Response:**
```json
{
  "id": "660f9511-f3ac-41d4-a716-446655440000",
  "rate": 27.00,
  "effectiveFrom": "2024-01-01T00:00:00Z",
  "description": "Hungarian standard VAT rate"
}
```

#### Get ALL Localization Resources (App Startup)

**Request:**
```http
GET /api/localization/all?locale=en-US
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

**Response:**
```json
{
  "locale": "en-US",
  "resources": {
    "UI_App_Title": "EchoWarehouse",
    "UI_Auth_Login": "Login",
    "UI_Auth_Register": "Register",
    "UI_Auth_Username": "Username",
    "UI_Auth_Password": "Password",
    "UI_Product_Name": "Product Name",
    "UI_Product_Quantity": "Quantity",
    "UI_Product_NetPrice": "Net Price",
    "UI_Product_GrossPrice": "Gross Price",
    "UI_Voice_Listening": "Listening...",
    "UI_Button_Save": "Save",
    "UI_Message_Success": "Operation successful"
  },
  "totalKeys": 150,
  "loadedAt": "2026-02-07T10:30:00Z"
}
```

#### Create Product (Voice)

**Request:**
```http
POST /api/voice/process
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
Content-Type: application/json

{
  "transcript": "Add 50 kilograms of cement to shelf 12",
  "locale": "en-US"
}
```

**Response:**
```json
{
  "success": true,
  "action": "add",
  "message": "Successfully added: 50 kg Cement (Location: Shelf 12)",
  "product": {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "name": "Cement",
    "quantity": 50,
    "unit": "kg",
    "location": "Shelf 12",
    "voiceCreated": true,
    "createdAt": "2026-02-07T10:30:00Z"
  },
  "processingTimeMs": 1250
}
```

---

## 🎤 Voice Commands

### Supported Command Patterns

#### Add Item (English)
```
"Add [quantity] [unit] [product name] to [location]"
"Add 100 pieces of screws"
"New product: 5 liters of paint"
```

#### Add Item (Hungarian)
```
"Vegyél fel [mennyiség] [egység] [termék név] a [lokáció] polcra"
"Adj hozzá 100 darab csavart"
"Új termék: 5 liter festék"
```

#### Issue Stock (English)
```
"Issue [quantity] [unit] of [product name]"
"Remove 20 kilograms from cement"
"Issue: 50 pieces of screws"
```

#### Issue Stock (Hungarian)
```
"Adj ki [mennyiség] [egység] [termék név]"
"Vegyél ki 20 kilogramm cementből"
"Kiadás: 50 darab csavar"
```

#### Search (English)
```
"Search for [product name]"
"Where is the cement?"
"Show me the screws"
```

#### Search (Hungarian)
```
"Keress rá [termék név]"
"Hol van a cement?"
"Mutasd meg a csavarokat"
```

#### Update (English)
```
"Update [product name] quantity to [new quantity]"
"Change cement to 200 kilograms"
```

#### Update (Hungarian)
```
"Módosítsd a [termék név] mennyiségét [új mennyiség]-re"
"A cement legyen 200 kilogramm"
```

#### Delete (English)
```
"Delete [product name]"
"Remove paint from the system"
```

#### Delete (Hungarian)
```
"Törölj [termék név]"
"Vedd ki a festéket a rendszerből"
```

### AI Intent Parsing

The system uses Google Gemini 1.5 Flash for natural language understanding. The AI extracts:
- Action type (add, remove, update, search, delete)
- Product name
- Quantity and unit
- Location
- Additional metadata (serial number, article number if mentioned)
- Confidence score

---

## 🌐 Internationalization

### How It Works

1. **Database Storage**: All UI text is stored as resource keys in PostgreSQL
2. **One-Time Load**: Frontend fetches all keys for selected locale on app startup (after authentication)
3. **Context Storage**: Translations stored in React Context (no prop drilling)
4. **Simple Hook**: `useLocalization()` hook provides `t()` function
5. **No URL Parameters**: Locale stored in LocalStorage, not in API requests
6. **Instant Switching**: Language change reloads context from backend

### Resource Key Naming Convention

```
UI_{Section}_{Element}_{Property}

Examples:
- UI_Auth_Login
- UI_Auth_Register
- UI_Product_Name
- UI_Product_Quantity
- UI_Voice_Listening
- UI_Button_Save
- UI_Message_Success
- UI_Menu_Dashboard
```

### Supported Locales

- **en-US** - English (United States)
- **hu-HU** - Hungarian (Hungary)

### Adding New Locales

1. Add locale code to `appsettings.json` under `Localization:SupportedLocales`
2. Add resource keys to database with new locale code
3. Frontend automatically detects new locale from API

### Adding New Resource Keys

Add keys to the `resource_keys` table with appropriate locale codes. The frontend will automatically pick them up on next language load or app restart.

---

## 📁 Project Structure

### Backend

```
EchoWarehouse/
├── backend/
│   ├── EchoWarehouse.API/
│   │   ├── Controllers/
│   │   │   ├── AuthController.cs              # Authentication
│   │   │   ├── ProductsController.cs
│   │   │   ├── VoiceController.cs
│   │   │   ├── HistoryController.cs
│   │   │   ├── LocalizationController.cs
│   │   │   ├── SettingsController.cs         # VAT settings
│   │   │   └── HealthController.cs
│   │   ├── Services/
│   │   │   ├── Interfaces/
│   │   │   │   ├── IAuthService.cs
│   │   │   │   ├── ITokenService.cs
│   │   │   │   ├── IVoiceService.cs
│   │   │   │   ├── ISpeechService.cs
│   │   │   │   ├── IAIService.cs
│   │   │   │   ├── IProductService.cs
│   │   │   │   ├── ILocalizationService.cs
│   │   │   │   └── IVatSettingsService.cs
│   │   │   └── Implementations/
│   │   │       ├── AuthService.cs
│   │   │       ├── TokenService.cs
│   │   │       ├── VoiceService.cs
│   │   │       ├── AzureSpeechService.cs
│   │   │       ├── GoogleGeminiService.cs
│   │   │       ├── ProductService.cs
│   │   │       ├── LocalizationService.cs
│   │   │       └── VatSettingsService.cs
│   │   ├── Repositories/
│   │   │   ├── Interfaces/
│   │   │   │   ├── IGenericRepository.cs
│   │   │   │   ├── IUserRepository.cs
│   │   │   │   ├── IProductRepository.cs
│   │   │   │   ├── IVoiceCommandRepository.cs
│   │   │   │   ├── IHistoryRepository.cs
│   │   │   │   ├── IResourceKeyRepository.cs
│   │   │   │   ├── IVatSettingsRepository.cs
│   │   │   │   └── IUnitOfWork.cs
│   │   │   └── Implementations/
│   │   │       ├── GenericRepository.cs
│   │   │       ├── UserRepository.cs
│   │   │       ├── ProductRepository.cs
│   │   │       ├── VoiceCommandRepository.cs
│   │   │       ├── HistoryRepository.cs
│   │   │       ├── ResourceKeyRepository.cs
│   │   │       ├── VatSettingsRepository.cs
│   │   │       └── UnitOfWork.cs
│   │   ├── Data/
│   │   │   ├── AppDbContext.cs
│   │   │   ├── Migrations/
│   │   │   └── Seed/
│   │   │       ├── LocalizationSeeder.cs
│   │   │       └── VatSettingsSeeder.cs
│   │   ├── Models/
│   │   │   ├── Entities/
│   │   │   │   ├── User.cs
│   │   │   │   ├── Product.cs
│   │   │   │   ├── VoiceCommand.cs
│   │   │   │   ├── History.cs
│   │   │   │   ├── ResourceKey.cs
│   │   │   │   ├── VatSettings.cs
│   │   │   │   └── BaseEntity.cs
│   │   │   ├── DTOs/
│   │   │   │   ├── Auth/
│   │   │   │   │   ├── RegisterDto.cs
│   │   │   │   │   ├── LoginDto.cs
│   │   │   │   │   └── AuthResponseDto.cs
│   │   │   │   ├── ProductDto.cs
│   │   │   │   ├── VoiceCommandDto.cs
│   │   │   │   ├── VoiceIntentDto.cs
│   │   │   │   ├── LocalizationDto.cs
│   │   │   │   ├── VatSettingsDto.cs
│   │   │   │   └── CreateProductDto.cs
│   │   │   └── ViewModels/
│   │   │       ├── ProductViewModel.cs
│   │   │       └── PaginatedResponse.cs
│   │   ├── Middleware/
│   │   │   ├── ErrorHandlingMiddleware.cs
│   │   │   ├── RequestLoggingMiddleware.cs
│   │   │   └── JwtMiddleware.cs
│   │   ├── Validators/
│   │   │   ├── RegisterValidator.cs
│   │   │   ├── LoginValidator.cs
│   │   │   ├── ProductValidator.cs
│   │   │   └── VoiceCommandValidator.cs
│   │   ├── Extensions/
│   │   │   ├── ServiceCollectionExtensions.cs
│   │   │   ├── UnitConversionExtensions.cs
│   │   │   └── StringExtensions.cs
│   │   ├── Mappings/
│   │   │   └── AutoMapperProfile.cs
│   │   ├── appsettings.json
│   │   ├── appsettings.Development.json       # NOT in git
│   │   ├── .gitignore                         # Must include .env, appsettings.Development.json
│   │   ├── Program.cs
│   │   └── EchoWarehouse.API.csproj
│   ├── EchoWarehouse.Tests/
│   │   ├── Unit/
│   │   │   ├── AuthServiceTests.cs
│   │   │   ├── VatCalculationTests.cs
│   │   │   └── ...
│   │   ├── Integration/
│   │   └── EchoWarehouse.Tests.csproj
│   └── EchoWarehouse.sln
```

### Frontend

```
frontend/
├── src/
│   ├── components/
│   │   ├── auth/
│   │   │   ├── LoginForm.tsx
│   │   │   ├── RegisterForm.tsx
│   │   │   └── ProtectedRoute.tsx
│   │   ├── voice/
│   │   │   ├── VoiceInput.tsx
│   │   │   ├── VoiceStatus.tsx
│   │   │   └── VoiceHistory.tsx
│   │   ├── products/
│   │   │   ├── ProductList.tsx
│   │   │   ├── ProductCard.tsx
│   │   │   ├── ProductForm.tsx              # VAT calculation here
│   │   │   ├── ProductDetails.tsx
│   │   │   └── ProductSearch.tsx
│   │   ├── common/
│   │   │   ├── Navbar.tsx
│   │   │   ├── LanguageSwitcher.tsx
│   │   │   ├── Loader.tsx
│   │   │   ├── ErrorBoundary.tsx
│   │   │   └── Modal.tsx
│   │   └── layout/
│   │       ├── Layout.tsx
│   │       └── Sidebar.tsx
│   ├── services/
│   │   ├── api.ts                          # Axios with JWT interceptor
│   │   ├── authService.ts
│   │   ├── voiceService.ts
│   │   ├── productService.ts
│   │   ├── localizationService.ts
│   │   └── vatService.ts
│   ├── hooks/
│   │   ├── useAuth.ts                      # Authentication hook
│   │   ├── useVoice.ts
│   │   ├── useProducts.ts
│   │   ├── useLocalization.ts
│   │   ├── useVat.ts                       # VAT calculation hook
│   │   └── useDebounce.ts
│   ├── context/
│   │   ├── AuthContext.tsx                 # Auth state
│   │   ├── LocalizationContext.tsx         # All translations
│   │   ├── VatContext.tsx                  # VAT rate
│   │   └── AppContext.tsx
│   ├── types/
│   │   ├── auth.types.ts
│   │   ├── product.types.ts
│   │   ├── voice.types.ts
│   │   ├── localization.types.ts
│   │   ├── vat.types.ts
│   │   └── api.types.ts
│   ├── utils/
│   │   ├── formatters.ts
│   │   ├── validators.ts
│   │   ├── vatCalculator.ts               # VAT calculation logic
│   │   └── constants.ts
│   ├── pages/
│   │   ├── Login.tsx
│   │   ├── Register.tsx
│   │   ├── Home.tsx
│   │   ├── Products.tsx
│   │   ├── History.tsx
│   │   └── Settings.tsx
│   ├── App.tsx
│   ├── main.tsx
│   └── index.css
├── public/
├── .env.example
├── .env                                    # NOT in git
├── .gitignore                              # Must include .env
├── package.json
├── tsconfig.json
├── vite.config.ts
└── tailwind.config.js
```

---

## 🚢 Deployment

### Backend - Azure App Service

#### Using Azure CLI

```bash
# Login
az login

# Create resource group
az group create --name EchoWarehouse-RG --location westeurope

# Create App Service Plan (F1 Free)
az appservice plan create \
  --name EchoWarehouse-Plan \
  --resource-group EchoWarehouse-RG \
  --sku F1 \
  --is-linux

# Create Web App
az webapp create \
  --name echowarehouse-api \
  --resource-group EchoWarehouse-RG \
  --plan EchoWarehouse-Plan \
  --runtime "DOTNET|8.0"

# Configure environment variables (CRITICAL - SET SECRETS!)
az webapp config appsettings set \
  --name echowarehouse-api \
  --resource-group EchoWarehouse-RG \
  --settings \
    ConnectionStrings__DefaultConnection="Host=..." \
    Authentication__RegistrationSecret="YOUR-SECRET-CODE" \
    Authentication__JwtSecret="YOUR-JWT-SECRET-MIN-32-CHARS" \
    AzureSpeech__SubscriptionKey="..." \
    GoogleGemini__ApiKey="..."

# Deploy
dotnet publish -c Release
cd bin/Release/net8.0/publish
zip -r deploy.zip .
az webapp deployment source config-zip \
  --resource-group EchoWarehouse-RG \
  --name echowarehouse-api \
  --src deploy.zip
```

#### Using GitHub Actions

Create `.github/workflows/deploy-backend.yml`:

```yaml
name: Deploy Backend to Azure

on:
  push:
    branches: [ main ]
    paths:
      - 'backend/**'

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Setup .NET
      uses: actions/setup-dotnet@v3
      with:
        dotnet-version: '8.0.x'
    
    - name: Restore dependencies
      run: dotnet restore backend/EchoWarehouse.API/EchoWarehouse.API.csproj
    
    - name: Build
      run: dotnet build backend/EchoWarehouse.API/EchoWarehouse.API.csproj -c Release --no-restore
    
    - name: Publish
      run: dotnet publish backend/EchoWarehouse.API/EchoWarehouse.API.csproj -c Release -o ./publish
    
    - name: Deploy to Azure Web App
      uses: azure/webapps-deploy@v2
      with:
        app-name: echowarehouse-api
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: ./publish
```

**⚠️ IMPORTANT: Set GitHub Secrets**
- Go to repository Settings → Secrets → Actions
- Add `AZURE_WEBAPP_PUBLISH_PROFILE`
- Set environment variables in Azure Portal

### Frontend - Firebase Hosting

```bash
# Install Firebase CLI
npm install -g firebase-tools

# Login
firebase login

# Initialize project
cd frontend
firebase init hosting

# Build
npm run build

# Deploy
firebase deploy --only hosting
```

#### Using GitHub Actions

Create `.github/workflows/deploy-frontend.yml`:

```yaml
name: Deploy Frontend to Firebase

on:
  push:
    branches: [ main ]
    paths:
      - 'frontend/**'

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Setup Node.js
      uses: actions/setup-node@v3
      with:
        node-version: '18'
    
    - name: Install dependencies
      run: cd frontend && npm ci
    
    - name: Build
      run: cd frontend && npm run build
      env:
        VITE_API_URL: ${{ secrets.VITE_API_URL }}
    
    - name: Deploy to Firebase
      uses: FirebaseExtended/action-hosting-deploy@v0
      with:
        repoToken: '${{ secrets.GITHUB_TOKEN }}'
        firebaseServiceAccount: '${{ secrets.FIREBASE_SERVICE_ACCOUNT }}'
        channelId: live
        projectId: echowarehouse
```

### Docker Deployment

#### docker-compose.yml

```yaml
version: '3.8'

services:
  postgres:
    image: postgres:15-alpine
    environment:
      POSTGRES_DB: echowarehouse
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 10s
      timeout: 5s
      retries: 5

  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile
    ports:
      - "5001:8080"
    environment:
      - ConnectionStrings__DefaultConnection=Host=postgres;Database=echowarehouse;Username=postgres;Password=${POSTGRES_PASSWORD}
      - Authentication__RegistrationSecret=${REGISTRATION_SECRET}
      - Authentication__JwtSecret=${JWT_SECRET}
      - AzureSpeech__SubscriptionKey=${AZURE_SPEECH_KEY}
      - GoogleGemini__ApiKey=${GOOGLE_GEMINI_KEY}
      - Swagger__Enabled=true
    depends_on:
      postgres:
        condition: service_healthy
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8080/api/health"]
      interval: 30s
      timeout: 10s
      retries: 3

volumes:
  postgres_data:
```

#### .env file for Docker (NOT in git!)

```env
POSTGRES_PASSWORD=your-secure-postgres-password
REGISTRATION_SECRET=your-super-secret-registration-code
JWT_SECRET=your-jwt-secret-minimum-32-characters-long
AZURE_SPEECH_KEY=your-azure-speech-key
GOOGLE_GEMINI_KEY=your-google-gemini-api-key
```

#### Running with Docker

```bash
# Create .env file first (see above)

# Start services
docker-compose up -d

# View logs
docker-compose logs -f backend

# Stop services
docker-compose down

# Stop and remove volumes
docker-compose down -v
```

---

## 🧪 Testing

### Backend Tests

```bash
cd backend

# Run all tests
dotnet test

# Run with coverage
dotnet test /p:CollectCoverage=true /p:CoverageReportFormat=opencover

# Run specific test project
dotnet test EchoWarehouse.Tests/EchoWarehouse.Tests.csproj

# Run specific test
dotnet test --filter "FullyQualifiedName~AuthServiceTests"
```

### Frontend Tests

```bash
cd frontend

# Run unit tests
npm run test

# Run tests in watch mode
npm run test:watch

# Run with coverage
npm run test:coverage

# E2E tests (Playwright)
npm run test:e2e
```

---

## 🔒 Security Best Practices

### Environment Variables

**Never commit these files:**
- `appsettings.Development.json`
- `.env`
- `*.env`
- Any file containing secrets

**Always add to `.gitignore`:**
```gitignore
# Secrets
appsettings.Development.json
.env
.env.*
!.env.example

# User-specific files
*.user
*.suo
```

### Production Checklist

- [ ] Set strong `REGISTRATION_SECRET` (minimum 32 characters)
- [ ] Set strong `JWT_SECRET` (minimum 32 characters)
- [ ] Use HTTPS in production
- [ ] Use Azure Key Vault or similar for secrets
- [ ] Enable CORS only for trusted origins
- [ ] Set secure password policy
- [ ] Enable rate limiting
- [ ] Enable logging and monitoring
- [ ] Regular security audits
- [ ] Keep dependencies updated

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## 👤 Author

**OmniStruo Team**

- GitHub: [@OmniStruo](https://github.com/OmniStruo)
- Project: [EchoWarehouse](https://github.com/OmniStruo/EchoWarehouse)

---

**Made with ❤️ and 🎤 by OmniStruo**

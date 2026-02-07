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
- [Installation](#-installation)
- [Configuration](#-configuration)
- [Usage](#-usage)
- [API Documentation](#-api-documentation)
- [Voice Commands](#-voice-commands)
- [Internationalization](#-internationalization)
- [Project Structure](#-project-structure)
- [Deployment](#-deployment)
- [Development](#-development)
- [License](#-license)

---

## ✨ Features

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
- **Price management** (net/gross, VAT support)
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
✅ Entity Framework Core 8.0 (Code-First)
✅ Npgsql (PostgreSQL provider)
✅ Dapper (raw SQL queries)
✅ Azure.CognitiveServices.Speech
✅ Google.Cloud.AIPlatform.V1 (Gemini API)
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
✅ LocalStorage (locale persistence)
✅ Custom useLocalization hook
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
```

### DevOps & Hosting
```
✅ Azure App Service (F1 Free Tier)
✅ Firebase Hosting (frontend)
✅ Supabase (PostgreSQL hosting)
✅ GitHub Actions (CI/CD)
✅ Docker support
```

---

## 🏗️ Architecture

### High-Level Architecture

```
┌──────────────────────────────────────────────────────┐
│                 React Frontend                        │
│  ┌────────────────────────────────────────────────┐  │
│  │  LocalizationContext (all resource keys)      │  │
│  │  - Loaded once on app startup                 │  │
│  │  - Stored in React Context                    │  │
│  │  - useLocalization() hook for key resolution  │  │
│  └────────────────────────────────────────────────┘  │
│  ┌────────────────────────────────────────────────┐  │
│  │  Voice Input  │  Product CRUD  │  Dashboard   │  │
│  │  All text via useLocalization('UI_KEY')       │  │
│  └────────────────────────────────────────────────┘  │
└───────────────────────┬──────────────────────────────┘
                        │ HTTPS/REST API
                        │ (lang NOT in every request)
                        ▼
┌──────────────────────────────────────────────────────┐
│            ASP.NET Core Web API (.NET 8)             │
│  ┌────────────────────────────────────────────────┐  │
│  │           Controllers Layer                    │  │
│  │  VoiceController │ ProductsController │ ...    │  │
│  │  LocalizationController (one-time load)       │  │
│  └────────────────┬───────────────────────────────┘  │
│                   │                                   │
│  ┌────────────────▼───────────────────────────────┐  │
│  │           Services Layer                       │  │
│  │  VoiceService │ SpeechService │ AIService      │  │
│  │  LocalizationService (get all keys by locale) │  │
│  └────────────────┬───────────────────────────────┘  │
│                   │                                   │
│  ┌────────────────▼─────────────────────────��─────┐  │
│  │      Repository Pattern + UnitOfWork           │  │
│  │  IProductRepo │ IVoiceRepo │ IHistoryRepo      │  │
│  │  IResourceKeyRepo (localization)               │  │
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
      │  Products │ History │ VoiceLog   │
      │  ResourceKeys (all UI text)      │
      │  - Key: UI_Product_Name          │
      │  - Locale: en-US                 │
      │  - Value: "Product Name"         │
      └──────────────────────────────────┘

      ┌──────────────────────────────────┐
      │   External Services              │
      │  Azure Speech │ Google Gemini    │
      └──────────────────────────────────┘
```

### Localization Flow

```
1. User opens app
   ↓
2. Check LocalStorage for saved locale (e.g., "hu-HU")
   ↓
3. Call GET /api/localization/all?locale=hu-HU (ONE TIME)
   ↓
4. Backend returns ALL resource keys for that locale
   {
     "UI_Product_Name": "Termék neve",
     "UI_Product_Quantity": "Mennyiség",
     "UI_Voice_Listening": "Hallgatlak...",
     ...500+ keys
   }
   ↓
5. Store in LocalizationContext (React Context)
   ↓
6. Components use: const t = useLocalization()
                    <h1>{t('UI_Product_Name')}</h1>
   ↓
7. Language switch: Clear context, fetch new locale, reload context
   ↓
8. NO MORE API CALLS for translations!
```

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

# Configure database connection string
# Edit appsettings.json or appsettings.Development.json

# Run migrations
dotnet ef database update

# Seed resource keys (initial localization data)
dotnet run --seed-localization

# Run application
dotnet run
```

Backend available at: `https://localhost:5001`

### 3️⃣ Frontend Setup

```bash
cd ../../frontend

# Install dependencies
npm install

# Environment variables
cp .env.example .env
# Edit .env file (API URL, etc.)

# Start development server
npm run dev
```

Frontend available at: `http://localhost:5173`

### 4️⃣ Docker (Optional)

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
  "Cors": {
    "AllowedOrigins": [
      "http://localhost:5173",
      "https://yourapp.web.app"
    ]
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

### Frontend Configuration (`.env`)

```env
VITE_API_URL=https://localhost:5001
VITE_ENABLE_VOICE=true
VITE_DEFAULT_LOCALE=en-US
```

### Environment Variables (Production)

```bash
# Azure App Service
ConnectionStrings__DefaultConnection="Host=..."
AzureSpeech__SubscriptionKey="..."
GoogleGemini__ApiKey="..."

# Or use Azure Key Vault (recommended)
```

---

## 📖 Usage

### Web Interface

1. **Open the frontend** in your browser
2. **Select language** (English/Hungarian) from dropdown
3. **All UI text updates instantly** (no API calls, loaded from Context)
4. **Click the microphone icon** 🎤
5. **Speak** naturally in your chosen language:
   - *"Add 50 kilograms of cement to shelf 12"*
   - *"Issue 20 pieces of screws"*
   - *"Search for cement"*
   - *"Update cement quantity to 100 kilograms"*
6. **The system automatically** executes the action

### Manual Entry

- Use the traditional form to add products
- Edit existing items
- Manage stock movements (receive/issue)

---

## 🔌 API Documentation

### Base URL
```
Development: https://localhost:5001/api
Production: https://your-api.azurewebsites.net/api
```

### Endpoints

#### **Localization (Called ONCE on app startup)**

```http
# Get ALL resource keys for a locale - called only on app start or language change
GET /api/localization/all?locale={locale}

# Get supported locales
GET /api/localization/locales
```

#### **Products**

```http
GET    /api/products              # Get all products
GET    /api/products/{id}         # Get single product
POST   /api/products              # Create new product
PUT    /api/products/{id}         # Update product
DELETE /api/products/{id}         # Delete product
GET    /api/products/search?q=... # Search
POST   /api/products/{id}/stock   # Stock operation
```

#### **Voice**

```http
POST   /api/voice/process         # Process voice command
GET    /api/voice/history         # Command history
GET    /api/voice/analytics       # Voice analytics
```

#### **History**

```http
GET    /api/history               # All operations
GET    /api/history/product/{id}  # Product history
```

### Example Request/Response

#### Get ALL Localization Resources (App Startup)

**Request:**
```http
GET /api/localization/all?locale=en-US
```

**Response:**
```json
{
  "locale": "en-US",
  "resources": {
    "UI_App_Title": "EchoWarehouse",
    "UI_Product_Name": "Product Name",
    "UI_Product_Description": "Description",
    "UI_Product_Quantity": "Quantity",
    "UI_Product_Unit": "Unit",
    "UI_Product_Location": "Location",
    "UI_Product_SerialNumber": "Serial Number",
    "UI_Product_ArticleNumber": "Article Number",
    "UI_Product_NetPrice": "Net Price",
    "UI_Product_GrossPrice": "Gross Price",
    "UI_Voice_Listening": "Listening...",
    "UI_Voice_Speak": "Click to Speak",
    "UI_Voice_Heard": "I heard",
    "UI_Voice_Result": "Result",
    "UI_Button_Save": "Save",
    "UI_Button_Cancel": "Cancel",
    "UI_Button_Edit": "Edit",
    "UI_Button_Delete": "Delete",
    "UI_Button_Add": "Add",
    "UI_Button_Search": "Search",
    "UI_Message_Success": "Operation successful",
    "UI_Message_Error": "An error occurred",
    "UI_Message_Loading": "Loading...",
    "UI_Language_English": "English",
    "UI_Language_Hungarian": "Hungarian",
    "UI_Menu_Dashboard": "Dashboard",
    "UI_Menu_Products": "Products",
    "UI_Menu_History": "History",
    "UI_Menu_Settings": "Settings"
  },
  "totalKeys": 150,
  "loadedAt": "2026-02-07T10:30:00Z"
}
```

**Hungarian Response:**
```http
GET /api/localization/all?locale=hu-HU
```

```json
{
  "locale": "hu-HU",
  "resources": {
    "UI_App_Title": "EchoWarehouse",
    "UI_Product_Name": "Termék neve",
    "UI_Product_Description": "Leírás",
    "UI_Product_Quantity": "Mennyiség",
    "UI_Product_Unit": "Mértékegység",
    "UI_Product_Location": "Lokáció",
    "UI_Product_SerialNumber": "Szériaszám",
    "UI_Product_ArticleNumber": "Cikkszám",
    "UI_Product_NetPrice": "Nettó ár",
    "UI_Product_GrossPrice": "Bruttó ár",
    "UI_Voice_Listening": "Hallgatlak...",
    "UI_Voice_Speak": "Kattints a beszédhez",
    "UI_Voice_Heard": "Hallottam",
    "UI_Voice_Result": "Eredmény",
    "UI_Button_Save": "Mentés",
    "UI_Button_Cancel": "Mégse",
    "UI_Button_Edit": "Szerkesztés",
    "UI_Button_Delete": "Törlés",
    "UI_Button_Add": "Hozzáadás",
    "UI_Button_Search": "Keresés",
    "UI_Message_Success": "Sikeres művelet",
    "UI_Message_Error": "Hiba történt",
    "UI_Message_Loading": "Betöltés...",
    "UI_Language_English": "Angol",
    "UI_Language_Hungarian": "Magyar",
    "UI_Menu_Dashboard": "Irányítópult",
    "UI_Menu_Products": "Termékek",
    "UI_Menu_History": "Előzmények",
    "UI_Menu_Settings": "Beállítások"
  },
  "totalKeys": 150,
  "loadedAt": "2026-02-07T10:30:00Z"
}
```

#### Get Supported Locales

**Request:**
```http
GET /api/localization/locales
```

**Response:**
```json
{
  "locales": [
    {
      "code": "en-US",
      "name": "English (United States)",
      "nativeName": "English",
      "isDefault": true
    },
    {
      "code": "hu-HU",
      "name": "Hungarian (Hungary)",
      "nativeName": "Magyar",
      "isDefault": false
    }
  ]
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

---

## 🌐 Internationalization

### Database Schema for Localization

```sql
-- Resource Keys table (all UI text stored here)
CREATE TABLE resource_keys (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    key VARCHAR(255) NOT NULL,              -- 'UI_Product_Name'
    locale VARCHAR(10) NOT NULL,            -- 'en-US', 'hu-HU'
    value TEXT NOT NULL,                    -- 'Product Name'
    category VARCHAR(50),                   -- 'product', 'voice', 'common'
    description TEXT,                       -- For translators
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW(),
    UNIQUE(key, locale)
);

-- Indexes for performance
CREATE INDEX idx_resource_keys_locale ON resource_keys(locale);
CREATE INDEX idx_resource_keys_key ON resource_keys(key);
CREATE INDEX idx_resource_keys_category ON resource_keys(category);
```

### Sample Resource Keys

```sql
-- English (en-US)
INSERT INTO resource_keys (key, locale, value, category, description) VALUES
('UI_Product_Name', 'en-US', 'Product Name', 'product', 'Label for product name field'),
('UI_Product_Quantity', 'en-US', 'Quantity', 'product', 'Label for quantity field'),
('UI_Voice_Listening', 'en-US', 'Listening...', 'voice', 'Status text while listening'),
('UI_Button_Save', 'en-US', 'Save', 'common', 'Save button text'),
('UI_Message_Success', 'en-US', 'Operation successful', 'common', 'Success message');

-- Hungarian (hu-HU)
INSERT INTO resource_keys (key, locale, value, category, description) VALUES
('UI_Product_Name', 'hu-HU', 'Termék neve', 'product', 'Termék név mező címke'),
('UI_Product_Quantity', 'hu-HU', 'Mennyiség', 'product', 'Mennyiség mező címke'),
('UI_Voice_Listening', 'hu-HU', 'Hallgatlak...', 'voice', 'Státusz szöveg hallgatás közben'),
('UI_Button_Save', 'hu-HU', 'Mentés', 'common', 'Mentés gomb szöveg'),
('UI_Message_Success', 'hu-HU', 'Sikeres művelet', 'common', 'Siker üzenet');
```

### Backend Implementation

#### Localization Service

```csharp
// Services/Interfaces/ILocalizationService.cs
public interface ILocalizationService
{
    Task<Dictionary<string, string>> GetAllResourcesAsync(string locale);
    Task<IEnumerable<string>> GetSupportedLocalesAsync();
    Task SeedDefaultResourcesAsync();
}

// Services/Implementations/LocalizationService.cs
public class LocalizationService : ILocalizationService
{
    private readonly IResourceKeyRepository _resourceKeyRepo;
    private readonly IMemoryCache _cache;
    private readonly IConfiguration _configuration;
    private readonly ILogger<LocalizationService> _logger;

    public LocalizationService(
        IResourceKeyRepository resourceKeyRepo,
        IMemoryCache cache,
        IConfiguration configuration,
        ILogger<LocalizationService> logger)
    {
        _resourceKeyRepo = resourceKeyRepo;
        _cache = cache;
        _configuration = configuration;
        _logger = logger;
    }

    public async Task<Dictionary<string, string>> GetAllResourcesAsync(string locale)
    {
        // Check cache first (optional server-side caching)
        var cacheKey = $"resources_{locale}";
        
        if (_cache.TryGetValue(cacheKey, out Dictionary<string, string>? cached))
        {
            _logger.LogInformation("Returning cached resources for locale: {Locale}", locale);
            return cached!;
        }

        // Load all resource keys for this locale from database
        var resources = await _resourceKeyRepo.GetByLocaleAsync(locale);
        var dictionary = resources.ToDictionary(r => r.Key, r => r.Value);

        _logger.LogInformation(
            "Loaded {Count} resource keys for locale: {Locale}", 
            dictionary.Count, 
            locale);

        // Cache on server side for 1 hour (optional)
        _cache.Set(cacheKey, dictionary, TimeSpan.FromHours(1));

        return dictionary;
    }

    public async Task<IEnumerable<string>> GetSupportedLocalesAsync()
    {
        var supportedLocales = _configuration
            .GetSection("Localization:SupportedLocales")
            .Get<string[]>();

        return supportedLocales ?? new[] { "en-US" };
    }

    public async Task SeedDefaultResourcesAsync()
    {
        _logger.LogInformation("Seeding default resource keys...");

        var defaultKeys = new[]
        {
            // Common - English
            new ResourceKey { Key = "UI_App_Title", Locale = "en-US", Value = "EchoWarehouse", Category = "common" },
            new ResourceKey { Key = "UI_Button_Save", Locale = "en-US", Value = "Save", Category = "common" },
            new ResourceKey { Key = "UI_Button_Cancel", Locale = "en-US", Value = "Cancel", Category = "common" },
            new ResourceKey { Key = "UI_Button_Edit", Locale = "en-US", Value = "Edit", Category = "common" },
            new ResourceKey { Key = "UI_Button_Delete", Locale = "en-US", Value = "Delete", Category = "common" },
            new ResourceKey { Key = "UI_Button_Add", Locale = "en-US", Value = "Add", Category = "common" },
            new ResourceKey { Key = "UI_Message_Success", Locale = "en-US", Value = "Operation successful", Category = "common" },
            new ResourceKey { Key = "UI_Message_Error", Locale = "en-US", Value = "An error occurred", Category = "common" },
            
            // Product - English
            new ResourceKey { Key = "UI_Product_Name", Locale = "en-US", Value = "Product Name", Category = "product" },
            new ResourceKey { Key = "UI_Product_Quantity", Locale = "en-US", Value = "Quantity", Category = "product" },
            new ResourceKey { Key = "UI_Product_Location", Locale = "en-US", Value = "Location", Category = "product" },
            
            // Voice - English
            new ResourceKey { Key = "UI_Voice_Listening", Locale = "en-US", Value = "Listening...", Category = "voice" },
            new ResourceKey { Key = "UI_Voice_Speak", Locale = "en-US", Value = "Click to Speak", Category = "voice" },

            // Common - Hungarian
            new ResourceKey { Key = "UI_App_Title", Locale = "hu-HU", Value = "EchoWarehouse", Category = "common" },
            new ResourceKey { Key = "UI_Button_Save", Locale = "hu-HU", Value = "Mentés", Category = "common" },
            new ResourceKey { Key = "UI_Button_Cancel", Locale = "hu-HU", Value = "Mégse", Category = "common" },
            new ResourceKey { Key = "UI_Button_Edit", Locale = "hu-HU", Value = "Szerkesztés", Category = "common" },
            new ResourceKey { Key = "UI_Button_Delete", Locale = "hu-HU", Value = "Törlés", Category = "common" },
            new ResourceKey { Key = "UI_Button_Add", Locale = "hu-HU", Value = "Hozzáadás", Category = "common" },
            new ResourceKey { Key = "UI_Message_Success", Locale = "hu-HU", Value = "Sikeres művelet", Category = "common" },
            new ResourceKey { Key = "UI_Message_Error", Locale = "hu-HU", Value = "Hiba történt", Category = "common" },
            
            // Product - Hungarian
            new ResourceKey { Key = "UI_Product_Name", Locale = "hu-HU", Value = "Termék neve", Category = "product" },
            new ResourceKey { Key = "UI_Product_Quantity", Locale = "hu-HU", Value = "Mennyiség", Category = "product" },
            new ResourceKey { Key = "UI_Product_Location", Locale = "hu-HU", Value = "Lokáció", Category = "product" },
            
            // Voice - Hungarian
            new ResourceKey { Key = "UI_Voice_Listening", Locale = "hu-HU", Value = "Hallgatlak...", Category = "voice" },
            new ResourceKey { Key = "UI_Voice_Speak", Locale = "hu-HU", Value = "Kattints a beszédhez", Category = "voice" }
        };

        await _resourceKeyRepo.SeedAsync(defaultKeys);
        _logger.LogInformation("Seeded {Count} resource keys", defaultKeys.Length);
    }
}
```

#### Localization Controller

```csharp
// Controllers/LocalizationController.cs
[ApiController]
[Route("api/[controller]")]
public class LocalizationController : ControllerBase
{
    private readonly ILocalizationService _localizationService;
    private readonly ILogger<LocalizationController> _logger;

    public LocalizationController(
        ILocalizationService localizationService,
        ILogger<LocalizationController> logger)
    {
        _localizationService = localizationService;
        _logger = logger;
    }

    /// <summary>
    /// Get ALL resource keys for a locale - called ONCE on app startup
    /// </summary>
    [HttpGet("all")]
    public async Task<IActionResult> GetAllResources([FromQuery] string locale = "en-US")
    {
        _logger.LogInformation("Loading all resources for locale: {Locale}", locale);

        var resources = await _localizationService.GetAllResourcesAsync(locale);

        return Ok(new
        {
            Locale = locale,
            Resources = resources,
            TotalKeys = resources.Count,
            LoadedAt = DateTime.UtcNow
        });
    }

    /// <summary>
    /// Get supported locales
    /// </summary>
    [HttpGet("locales")]
    public async Task<IActionResult> GetSupportedLocales()
    {
        var locales = await _localizationService.GetSupportedLocalesAsync();

        return Ok(new
        {
            Locales = locales.Select(l => new
            {
                Code = l,
                Name = GetLocaleName(l),
                NativeName = GetNativeName(l),
                IsDefault = l == "en-US"
            })
        });
    }

    private string GetLocaleName(string locale) => locale switch
    {
        "en-US" => "English (United States)",
        "hu-HU" => "Hungarian (Hungary)",
        _ => locale
    };

    private string GetNativeName(string locale) => locale switch
    {
        "en-US" => "English",
        "hu-HU" => "Magyar",
        _ => locale
    };
}
```

#### Resource Key Repository

```csharp
// Repositories/Interfaces/IResourceKeyRepository.cs
public interface IResourceKeyRepository : IGenericRepository<ResourceKey>
{
    Task<IEnumerable<ResourceKey>> GetByLocaleAsync(string locale);
    Task<IEnumerable<ResourceKey>> GetByCategoryAsync(string category, string locale);
    Task<string?> GetValueAsync(string key, string locale);
    Task SeedAsync(IEnumerable<ResourceKey> keys);
}

// Repositories/Implementations/ResourceKeyRepository.cs
public class ResourceKeyRepository : GenericRepository<ResourceKey>, IResourceKeyRepository
{
    public ResourceKeyRepository(AppDbContext context) : base(context)
    {
    }

    public async Task<IEnumerable<ResourceKey>> GetByLocaleAsync(string locale)
    {
        return await _context.ResourceKeys
            .Where(r => r.Locale == locale)
            .OrderBy(r => r.Key)
            .AsNoTracking()
            .ToListAsync();
    }

    public async Task<IEnumerable<ResourceKey>> GetByCategoryAsync(
        string category, 
        string locale)
    {
        return await _context.ResourceKeys
            .Where(r => r.Category == category && r.Locale == locale)
            .AsNoTracking()
            .ToListAsync();
    }

    public async Task<string?> GetValueAsync(string key, string locale)
    {
        var resource = await _context.ResourceKeys
            .AsNoTracking()
            .FirstOrDefaultAsync(r => r.Key == key && r.Locale == locale);
        
        return resource?.Value;
    }

    public async Task SeedAsync(IEnumerable<ResourceKey> keys)
    {
        foreach (var key in keys)
        {
            var exists = await _context.ResourceKeys
                .AnyAsync(r => r.Key == key.Key && r.Locale == key.Locale);
            
            if (!exists)
            {
                await _context.ResourceKeys.AddAsync(key);
            }
        }
        
        await _context.SaveChangesAsync();
    }
}
```

### Frontend Implementation

#### Localization Context

```typescript
// context/LocalizationContext.tsx
import React, { createContext, useContext, useState, useEffect, ReactNode } from 'react';
import axios from 'axios';

interface LocalizationContextType {
  locale: string;
  resources: Record<string, string>;
  isLoading: boolean;
  error: string | null;
  changeLocale: (newLocale: string) => Promise<void>;
  t: (key: string, fallback?: string) => string;
}

const LocalizationContext = createContext<LocalizationContextType | undefined>(undefined);

const API_URL = import.meta.env.VITE_API_URL;
const DEFAULT_LOCALE = import.meta.env.VITE_DEFAULT_LOCALE || 'en-US';

export const LocalizationProvider: React.FC<{ children: ReactNode }> = ({ children }) => {
  const [locale, setLocale] = useState<string>(
    localStorage.getItem('locale') || DEFAULT_LOCALE
  );
  const [resources, setResources] = useState<Record<string, string>>({});
  const [isLoading, setIsLoading] = useState<boolean>(true);
  const [error, setError] = useState<string | null>(null);

  // Load resources from API
  const loadResources = async (targetLocale: string) => {
    setIsLoading(true);
    setError(null);

    try {
      console.log(`Loading resources for locale: ${targetLocale}`);
      
      const response = await axios.get(`${API_URL}/api/localization/all`, {
        params: { locale: targetLocale }
      });

      const { resources: fetchedResources, totalKeys } = response.data;

      console.log(`Loaded ${totalKeys} resource keys for ${targetLocale}`);

      // Store in state
      setResources(fetchedResources);

      // Save locale to LocalStorage
      localStorage.setItem('locale', targetLocale);
      setLocale(targetLocale);

      setIsLoading(false);
    } catch (err) {
      console.error('Failed to load localization resources:', err);
      setError('Failed to load translations');
      setIsLoading(false);
    }
  };

  // Load resources on mount
  useEffect(() => {
    loadResources(locale);
  }, []);

  // Change locale function
  const changeLocale = async (newLocale: string) => {
    if (newLocale === locale) return;
    await loadResources(newLocale);
  };

  // Translation function with fallback
  const t = (key: string, fallback?: string): string => {
    if (resources[key]) {
      return resources[key];
    }

    // Fallback to key or provided fallback
    console.warn(`Translation key not found: ${key}`);
    return fallback || key;
  };

  return (
    <LocalizationContext.Provider
      value={{
        locale,
        resources,
        isLoading,
        error,
        changeLocale,
        t
      }}
    >
      {children}
    </LocalizationContext.Provider>
  );
};

export default LocalizationContext;
```

#### useLocalization Hook

```typescript
// hooks/useLocalization.ts
import { useContext } from 'react';
import LocalizationContext from '../context/LocalizationContext';

export const useLocalization = () => {
  const context = useContext(LocalizationContext);

  if (!context) {
    throw new Error('useLocalization must be used within LocalizationProvider');
  }

  return context;
};
```

#### App Setup

```typescript
// main.tsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import App from './App';
import { LocalizationProvider } from './context/LocalizationContext';
import './index.css';

ReactDOM.createRoot(document.getElementById('root')!).render(
  <React.StrictMode>
    <LocalizationProvider>
      <App />
    </LocalizationProvider>
  </React.StrictMode>
);
```

```typescript
// App.tsx
import React from 'react';
import { useLocalization } from './hooks/useLocalization';
import ProductList from './components/products/ProductList';
import VoiceInput from './components/voice/VoiceInput';
import LanguageSwitcher from './components/common/LanguageSwitcher';

const App: React.FC = () => {
  const { isLoading, error, t } = useLocalization();

  if (isLoading) {
    return (
      <div className="flex items-center justify-center h-screen">
        <div className="text-xl">{t('UI_Message_Loading', 'Loading...')}</div>
      </div>
    );
  }

  if (error) {
    return (
      <div className="flex items-center justify-center h-screen">
        <div className="text-xl text-red-600">{error}</div>
      </div>
    );
  }

  return (
    <div className="min-h-screen bg-gray-100">
      <nav className="bg-white shadow-lg">
        <div className="max-w-7xl mx-auto px-4 py-4 flex justify-between items-center">
          <h1 className="text-2xl font-bold">{t('UI_App_Title')}</h1>
          <LanguageSwitcher />
        </div>
      </nav>

      <main className="max-w-7xl mx-auto px-4 py-8">
        <VoiceInput />
        <ProductList />
      </main>
    </div>
  );
};

export default App;
```

#### Component Usage Example

```typescript
// components/products/ProductForm.tsx
import React, { useState } from 'react';
import { useLocalization } from '../../hooks/useLocalization';

interface ProductFormProps {
  onSubmit: (data: any) => void;
  onCancel: () => void;
}

const ProductForm: React.FC<ProductFormProps> = ({ onSubmit, onCancel }) => {
  const { t } = useLocalization();
  const [name, setName] = useState('');
  const [quantity, setQuantity] = useState('');
  const [location, setLocation] = useState('');

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    onSubmit({ name, quantity: parseFloat(quantity), location });
  };

  return (
    <form onSubmit={handleSubmit} className="space-y-4">
      <h2 className="text-xl font-bold">{t('UI_Product_Add')}</h2>

      <div>
        <label className="block text-sm font-medium">
          {t('UI_Product_Name')}
        </label>
        <input
          type="text"
          value={name}
          onChange={(e) => setName(e.target.value)}
          placeholder={t('UI_Product_Name')}
          className="mt-1 block w-full rounded-md border-gray-300 shadow-sm"
          required
        />
      </div>

      <div>
        <label className="block text-sm font-medium">
          {t('UI_Product_Quantity')}
        </label>
        <input
          type="number"
          value={quantity}
          onChange={(e) => setQuantity(e.target.value)}
          placeholder={t('UI_Product_Quantity')}
          className="mt-1 block w-full rounded-md border-gray-300 shadow-sm"
          required
        />
      </div>

      <div>
        <label className="block text-sm font-medium">
          {t('UI_Product_Location')}
        </label>
        <input
          type="text"
          value={location}
          onChange={(e) => setLocation(e.target.value)}
          placeholder={t('UI_Product_Location')}
          className="mt-1 block w-full rounded-md border-gray-300 shadow-sm"
        />
      </div>

      <div className="flex gap-2">
        <button
          type="submit"
          className="px-4 py-2 bg-blue-600 text-white rounded-lg hover:bg-blue-700"
        >
          {t('UI_Button_Save')}
        </button>
        <button
          type="button"
          onClick={onCancel}
          className="px-4 py-2 bg-gray-300 text-gray-700 rounded-lg hover:bg-gray-400"
        >
          {t('UI_Button_Cancel')}
        </button>
      </div>
    </form>
  );
};

export default ProductForm;
```

#### Language Switcher Component

```typescript
// components/common/LanguageSwitcher.tsx
import React from 'react';
import { useLocalization } from '../../hooks/useLocalization';

const LanguageSwitcher: React.FC = () => {
  const { locale, changeLocale, t } = useLocalization();

  const languages = [
    { code: 'en-US', name: 'English', flag: '🇬🇧' },
    { code: 'hu-HU', name: 'Magyar', flag: '🇭🇺' }
  ];

  return (
    <div className="flex gap-2">
      {languages.map((lang) => (
        <button
          key={lang.code}
          onClick={() => changeLocale(lang.code)}
          className={`px-3 py-2 rounded-lg transition-colors ${
            locale === lang.code
              ? 'bg-blue-600 text-white'
              : 'bg-gray-200 text-gray-700 hover:bg-gray-300'
          }`}
          disabled={locale === lang.code}
        >
          <span className="mr-2">{lang.flag}</span>
          {lang.name}
        </button>
      ))}
    </div>
  );
};

export default LanguageSwitcher;
```

---

## 📁 Project Structure

### Backend

```
EchoWarehouse/
├── backend/
│   ├── EchoWarehouse.API/
│   │   ├── Controllers/
│   │   │   ├── ProductsController.cs
│   │   │   ├── VoiceController.cs
│   │   │   ├── HistoryController.cs
│   │   │   ├── LocalizationController.cs       # One-time load endpoint
│   │   │   └── HealthController.cs
│   │   ├── Services/
│   │   │   ├── Interfaces/
│   │   │   │   ├── IVoiceService.cs
│   │   │   │   ├── ISpeechService.cs
│   │   │   │   ├── IAIService.cs
│   │   │   │   ├── IProductService.cs
│   │   │   │   └── ILocalizationService.cs
│   │   │   └── Implementations/
│   │   │       ├── VoiceService.cs
│   │   │       ├── AzureSpeechService.cs
│   │   │       ├── GoogleGeminiService.cs
│   │   │       ├── ProductService.cs
│   │   │       └── LocalizationService.cs
│   │   ├── Repositories/
│   │   │   ├── Interfaces/
│   │   │   │   ├── IGenericRepository.cs
│   │   │   │   ├── IProductRepository.cs
│   │   │   │   ├── IVoiceCommandRepository.cs
│   │   │   │   ├── IHistoryRepository.cs
│   │   │   │   ├── IResourceKeyRepository.cs
│   │   │   │   └── IUnitOfWork.cs
│   │   │   └── Implementations/
│   │   │       ├── GenericRepository.cs
│   │   │       ├── ProductRepository.cs
│   │   │       ├── VoiceCommandRepository.cs
│   │   │       ├── HistoryRepository.cs
│   │   │       ├── ResourceKeyRepository.cs
│   │   │       └── UnitOfWork.cs
│   │   ├── Data/
│   │   │   ├── AppDbContext.cs
│   │   │   ├── Migrations/
│   │   │   └── Seed/
│   │   │       └── LocalizationSeeder.cs
│   │   ├── Models/
│   │   │   ├── Entities/
│   │   │   │   ├── Product.cs
│   │   │   │   ├── VoiceCommand.cs
│   │   │   │   ├── History.cs
│   │   │   │   ├── ResourceKey.cs           # UI text storage
│   │   │   │   └── BaseEntity.cs
│   │   │   ├── DTOs/
│   │   │   │   ├── ProductDto.cs
│   │   │   │   ├── VoiceCommandDto.cs
│   │   │   │   ├── VoiceIntentDto.cs
│   │   │   │   ├── LocalizationDto.cs
│   │   │   │   └── CreateProductDto.cs
│   │   │   └── ViewModels/
│   │   │       ├── ProductViewModel.cs
│   │   │       └── PaginatedResponse.cs
│   │   ├── Middleware/
│   │   │   ├── ErrorHandlingMiddleware.cs
│   │   │   └── RequestLoggingMiddleware.cs
│   │   ├── Validators/
│   │   │   ├── ProductValidator.cs
│   │   │   └── VoiceCommandValidator.cs
│   │   ├── Extensions/
│   │   │   ├── ServiceCollectionExtensions.cs
│   │   │   ├── UnitConversionExtensions.cs
│   │   │   └── StringExtensions.cs
│   │   ├── Mappings/
│   │   │   └── AutoMapperProfile.cs
│   │   ├── appsettings.json
│   │   ├── appsettings.Development.json
│   │   ├── Program.cs
│   │   └── EchoWarehouse.API.csproj
│   ├── EchoWarehouse.Tests/
│   │   ├── Unit/
│   │   ├── Integration/
│   │   └── EchoWarehouse.Tests.csproj
│   └── EchoWarehouse.sln
```

### Frontend

```
frontend/
├── src/
│   ├── components/
│   │   ├── voice/
│   │   │   ├── VoiceInput.tsx
│   │   │   ├── VoiceStatus.tsx
│   │   │   └── VoiceHistory.tsx
│   │   ├── products/
│   │   │   ├── ProductList.tsx
│   │   │   ├── ProductCard.tsx
│   │   │   ├── ProductForm.tsx              # Uses t('UI_Product_Name')
│   │   │   ├── ProductDetails.tsx
│   │   │   └── ProductSearch.tsx
│   │   ├── common/
│   │   │   ├── Navbar.tsx
│   │   │   ├── LanguageSwitcher.tsx         # Calls changeLocale()
│   │   │   ├── Loader.tsx
│   │   │   ├── ErrorBoundary.tsx
│   │   │   └── Modal.tsx
│   │   └── layout/
│   │       ├── Layout.tsx
│   │       └── Sidebar.tsx
│   ├── services/
│   │   ├── api.ts
│   │   ├── voiceService.ts
│   │   ├── productService.ts
│   │   └── localizationService.ts           # Optional utility functions
│   ├── hooks/
│   │   ├── useVoice.ts
│   │   ├── useProducts.ts
│   │   ├── useLocalization.ts               # Main hook: returns t()
│   │   └── useDebounce.ts
│   ├── context/
│   │   ├── LocalizationContext.tsx          # Stores ALL resources
│   │   └── AppContext.tsx
│   ├── types/
│   │   ├── product.types.ts
│   │   ├── voice.types.ts
│   │   ├── localization.types.ts
│   │   └── api.types.ts
│   ├── utils/
│   │   ├── formatters.ts
│   │   ├── validators.ts
│   │   └── constants.ts
│   ├── pages/
│   │   ├── Home.tsx
│   │   ├── Products.tsx
│   │   ├── History.tsx
│   │   └── Settings.tsx
│   ├── App.tsx
│   ├── main.tsx                             # Wraps with LocalizationProvider
│   └── index.css
├── public/
├── .env.example
├── .env
├── package.json
├── tsconfig.json
├── vite.config.ts
└── tailwind.config.js
```

---

## 🚢 Deployment

### Backend - Azure App Service

#### Azure CLI

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

# Configure environment variables
az webapp config appsettings set \
  --name echowarehouse-api \
  --resource-group EchoWarehouse-RG \
  --settings \
    ConnectionStrings__DefaultConnection="Host=..." \
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

#### GitHub Actions

`.github/workflows/deploy-backend.yml`:

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

#### GitHub Actions

`.github/workflows/deploy-frontend.yml`:

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

### Docker

`docker-compose.yml`:

```yaml
version: '3.8'

services:
  postgres:
    image: postgres:15-alpine
    environment:
      POSTGRES_DB: echowarehouse
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: yourpassword
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data

  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile
    ports:
      - "5001:8080"
    environment:
      - ConnectionStrings__DefaultConnection=Host=postgres;Database=echowarehouse;Username=postgres;Password=yourpassword
      - AzureSpeech__SubscriptionKey=${AZURE_SPEECH_KEY}
      - GoogleGemini__ApiKey=${GOOGLE_GEMINI_KEY}
    depends_on:
      - postgres

volumes:
  postgres_data:
```

---

## 👨‍💻 Development

### Testing

```bash
# Backend unit tests
cd backend
dotnet test

# Frontend tests
cd frontend
npm run test

# E2E tests (Playwright)
npm run test:e2e
```

### Adding New Resource Keys

#### Backend (Database)

```sql
-- Add new keys to database
INSERT INTO resource_keys (key, locale, value, category, description) VALUES
('UI_New_Feature', 'en-US', 'New Feature', 'common', 'New feature label'),
('UI_New_Feature', 'hu-HU', 'Új funkció', 'common', 'Új funkció címke');
```

#### Frontend Usage

```typescript
// Immediately available after language switch (reloads context)
const { t } = useLocalization();
<h1>{t('UI_New_Feature')}</h1>
```

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## 👤 Author

**OmniStruo Team**

- GitHub: [@OmniStruo](https://github.com/OmniStruo)
- Project: [EchoWarehouse](https://github.com/OmniStruo/EchoWarehouse)

**Devs**

---


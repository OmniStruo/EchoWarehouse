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
- **Database-driven localization** - resource keys stored in PostgreSQL
- **Dynamic language switching** (English, Hungarian)
- **Frontend caching** - translations loaded on app startup
- **Fallback mechanism** - defaults to English if translation missing
- **Admin interface** for translation management (future feature)

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
✅ i18next (internationalization)
✅ Context API (state management)
✅ Web Speech API
```

### Database
```
✅ PostgreSQL 15+
✅ Entity Framework Core Migrations
✅ Raw SQL support for complex queries
✅ Full-text search (PostgreSQL native)
✅ JSONB support (voice command logs)
✅ Localization resource storage
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
│  │  Voice Input  │  Product CRUD  │  Dashboard   │  │
│  │  i18n (cached resource keys from DB)           │  │
│  └────────────────────────────────────────────────┘  │
└───────────────────────┬──────────────────────────────┘
                        │ HTTPS/REST API
                        ▼
┌──────────────────────────────────────────────────────┐
│            ASP.NET Core Web API (.NET 8)             │
│  ┌────────────────────────────────────────────────┐  │
│  │           Controllers Layer                    │  │
│  │  VoiceController │ ProductsController │ ...    │  │
│  │  LocalizationController (resource keys API)   │  │
│  └────────────────┬───────────────────────────────┘  │
│                   │                                   │
│  ┌────────────────▼───────────────────────────────┐  │
│  │           Services Layer                       │  │
│  │  VoiceService │ SpeechService │ AIService      │  │
│  │  LocalizationService (i18n)                    │  │
│  └────────────────┬───────────────────────────────┘  │
│                   │                                   │
│  ┌────────────────▼───────────────────────────────┐  │
│  │      Repository Pattern + UnitOfWork           │  │
│  │  IProductRepo │ IVoiceRepo │ IHistoryRepo      │  │
│  │  IResourceKeyRepo (localization)               │  │
│  └────────────────┬───────────────────────────────┘  │
│                   │                                   │
│  ┌────────────────▼───────────────────────────────┐  │
│  │         Data Access Layer                      │  │
│  │  EF Core Context │ Dapper (Raw SQL)            │  │
│  └────────────────┬─���─────────────────────────────┘  │
└────────────────────┼──────────────────────────────────┘
                     │
                     ▼
      ┌──────────────────────────────────┐
      │   PostgreSQL Database            │
      │  Products │ History │ VoiceLog   │
      │  ResourceKeys (i18n) │ Languages │
      └──────────────────────────────────┘

      ┌──────────────────────────────────┐
      │   External Services              │
      │  Azure Speech │ Google Gemini    │
      └──────────────────────────────────┘
```

### Backend Architecture Layers

```csharp
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
├── Middleware/         // Custom middleware
├── Validators/         // FluentValidation
└── Extensions/         // Service extensions, helpers
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

# Seed localization data (optional)
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
    "DefaultLanguage": "en",
    "SupportedLanguages": ["en", "hu"],
    "CacheDurationMinutes": 60
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
VITE_DEFAULT_LANGUAGE=en
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
3. **Click the microphone icon** 🎤
4. **Speak** naturally in your chosen language:
   - *"Add 50 kilograms of cement to shelf 12"*
   - *"Issue 20 pieces of screws"*
   - *"Search for cement"*
   - *"Update cement quantity to 100 kilograms"*
5. **The system automatically** executes the action

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

#### **Localization**

```http
GET    /api/localization/resources?lang={code}  # Get all resource keys
GET    /api/localization/languages              # Get supported languages
```

#### **History**

```http
GET    /api/history               # All operations
GET    /api/history/product/{id}  # Product history
```

### Example Request/Response

#### Get Localization Resources

**Request:**
```http
GET /api/localization/resources?lang=en
```

**Response:**
```json
{
  "language": "en",
  "resources": {
    "app.title": "EchoWarehouse",
    "product.name": "Product Name",
    "product.quantity": "Quantity",
    "product.location": "Location",
    "voice.listening": "Listening...",
    "voice.command.add": "Add product",
    "voice.command.remove": "Remove stock",
    "button.save": "Save",
    "button.cancel": "Cancel",
    "message.success": "Operation successful",
    "message.error": "An error occurred"
  },
  "cachedAt": "2026-02-07T10:30:00Z"
}
```

#### Create Product (Voice)

**Request:**
```http
POST /api/voice/process
Content-Type: application/json

{
  "transcript": "Add 50 kilograms of cement to shelf 12",
  "language": "en"
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

#### Get Products

**Request:**
```http
GET /api/products?page=1&pageSize=20&lang=en
```

**Response:**
```json
{
  "data": [
    {
      "id": "550e8400-e29b-41d4-a716-446655440000",
      "name": "Cement",
      "description": "Portland cement",
      "quantity": 50,
      "unit": "kg",
      "location": "Shelf 12",
      "serialNumber": null,
      "articleNumber": "CEM-001",
      "netPrice": 5000,
      "grossPrice": 6350,
      "voiceCreated": true,
      "warehouseEntryDate": "2026-02-07T00:00:00Z",
      "createdAt": "2026-02-07T10:30:00Z",
      "updatedAt": "2026-02-07T10:30:00Z"
    }
  ],
  "pageNumber": 1,
  "pageSize": 20,
  "totalPages": 1,
  "totalRecords": 1
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

The system uses Google Gemini 1.5 Flash for natural language understanding:

```json
{
  "transcript": "Add 50 kilograms of cement to shelf 12",
  "language": "en",
  "parsed": {
    "action": "add",
    "product": "cement",
    "quantity": 50,
    "unit": "kg",
    "location": "shelf 12",
    "confidence": 0.95
  }
}
```

---

## 🌐 Internationalization

### Database Schema for Localization

```sql
-- Languages table
CREATE TABLE languages (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    code VARCHAR(10) NOT NULL UNIQUE,  -- 'en', 'hu'
    name VARCHAR(100) NOT NULL,        -- 'English', 'Hungarian'
    native_name VARCHAR(100),          -- 'English', 'Magyar'
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT NOW()
);

-- Resource Keys table
CREATE TABLE resource_keys (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    key VARCHAR(255) NOT NULL,         -- 'product.name'
    language_code VARCHAR(10) NOT NULL REFERENCES languages(code),
    value TEXT NOT NULL,               -- 'Product Name'
    category VARCHAR(50),              -- 'product', 'voice', 'common'
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW(),
    UNIQUE(key, language_code)
);

-- Indexes
CREATE INDEX idx_resource_keys_lang ON resource_keys(language_code);
CREATE INDEX idx_resource_keys_key ON resource_keys(key);
CREATE INDEX idx_resource_keys_category ON resource_keys(category);
```

### Frontend i18n Implementation

```typescript
// services/localizationService.ts
import axios from 'axios';
import i18n from 'i18next';
import { initReactI18next } from 'react-i18next';

const API_URL = import.meta.env.VITE_API_URL;

export const initializeLocalization = async () => {
  // Fetch all resource keys from backend
  const languages = ['en', 'hu'];
  const resources: any = {};

  for (const lang of languages) {
    const response = await axios.get(
      `${API_URL}/api/localization/resources?lang=${lang}`
    );
    
    resources[lang] = {
      translation: response.data.resources
    };
    
    // Cache in localStorage
    localStorage.setItem(
      `i18n_${lang}`,
      JSON.stringify({
        data: response.data.resources,
        timestamp: Date.now()
      })
    );
  }

  // Initialize i18next
  await i18n
    .use(initReactI18next)
    .init({
      resources,
      lng: localStorage.getItem('language') || 'en',
      fallbackLng: 'en',
      interpolation: {
        escapeValue: false
      }
    });
};

// Check cache validity (refresh every 24h)
export const shouldRefreshCache = (lang: string): boolean => {
  const cached = localStorage.getItem(`i18n_${lang}`);
  if (!cached) return true;
  
  const { timestamp } = JSON.parse(cached);
  const hoursSinceCache = (Date.now() - timestamp) / (1000 * 60 * 60);
  
  return hoursSinceCache > 24;
};
```

```typescript
// main.tsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import App from './App';
import { initializeLocalization } from './services/localizationService';
import './index.css';

// Initialize localization before rendering
initializeLocalization().then(() => {
  ReactDOM.createRoot(document.getElementById('root')!).render(
    <React.StrictMode>
      <App />
    </React.StrictMode>
  );
});
```

```typescript
// Component usage example
import { useTranslation } from 'react-i18next';

const ProductForm: React.FC = () => {
  const { t, i18n } = useTranslation();

  const changeLanguage = (lang: string) => {
    i18n.changeLanguage(lang);
    localStorage.setItem('language', lang);
  };

  return (
    <div>
      <h1>{t('app.title')}</h1>
      
      <select onChange={(e) => changeLanguage(e.target.value)}>
        <option value="en">English</option>
        <option value="hu">Magyar</option>
      </select>
      
      <form>
        <label>{t('product.name')}</label>
        <input type="text" placeholder={t('product.name')} />
        
        <label>{t('product.quantity')}</label>
        <input type="number" placeholder={t('product.quantity')} />
        
        <button type="submit">{t('button.save')}</button>
      </form>
    </div>
  );
};
```

### Backend Localization Service

```csharp
// Services/Interfaces/ILocalizationService.cs
public interface ILocalizationService
{
    Task<Dictionary<string, string>> GetResourcesAsync(string languageCode);
    Task<IEnumerable<Language>> GetSupportedLanguagesAsync();
    Task<string> GetResourceAsync(string key, string languageCode);
    Task SeedDefaultResourcesAsync();
}

// Services/Implementations/LocalizationService.cs
public class LocalizationService : ILocalizationService
{
    private readonly IResourceKeyRepository _resourceKeyRepo;
    private readonly IMemoryCache _cache;
    private readonly ILogger<LocalizationService> _logger;

    public LocalizationService(
        IResourceKeyRepository resourceKeyRepo,
        IMemoryCache cache,
        ILogger<LocalizationService> logger)
    {
        _resourceKeyRepo = resourceKeyRepo;
        _cache = cache;
        _logger = logger;
    }

    public async Task<Dictionary<string, string>> GetResourcesAsync(
        string languageCode)
    {
        var cacheKey = $"resources_{languageCode}";
        
        if (_cache.TryGetValue(cacheKey, out Dictionary<string, string>? cached))
        {
            return cached!;
        }

        var resources = await _resourceKeyRepo.GetByLanguageAsync(languageCode);
        var dictionary = resources.ToDictionary(r => r.Key, r => r.Value);

        _cache.Set(cacheKey, dictionary, TimeSpan.FromHours(24));

        return dictionary;
    }

    public async Task SeedDefaultResourcesAsync()
    {
        var defaultKeys = new[]
        {
            // English
            new ResourceKey { Key = "app.title", LanguageCode = "en", Value = "EchoWarehouse", Category = "common" },
            new ResourceKey { Key = "product.name", LanguageCode = "en", Value = "Product Name", Category = "product" },
            new ResourceKey { Key = "product.quantity", LanguageCode = "en", Value = "Quantity", Category = "product" },
            new ResourceKey { Key = "voice.listening", LanguageCode = "en", Value = "Listening...", Category = "voice" },
            
            // Hungarian
            new ResourceKey { Key = "app.title", LanguageCode = "hu", Value = "EchoWarehouse", Category = "common" },
            new ResourceKey { Key = "product.name", LanguageCode = "hu", Value = "Termék neve", Category = "product" },
            new ResourceKey { Key = "product.quantity", LanguageCode = "hu", Value = "Mennyiség", Category = "product" },
            new ResourceKey { Key = "voice.listening", LanguageCode = "hu", Value = "Hallgatlak...", Category = "voice" }
        };

        await _resourceKeyRepo.SeedAsync(defaultKeys);
    }
}

// Controllers/LocalizationController.cs
[ApiController]
[Route("api/[controller]")]
public class LocalizationController : ControllerBase
{
    private readonly ILocalizationService _localizationService;

    public LocalizationController(ILocalizationService localizationService)
    {
        _localizationService = localizationService;
    }

    [HttpGet("resources")]
    public async Task<IActionResult> GetResources([FromQuery] string lang = "en")
    {
        var resources = await _localizationService.GetResourcesAsync(lang);
        
        return Ok(new
        {
            Language = lang,
            Resources = resources,
            CachedAt = DateTime.UtcNow
        });
    }

    [HttpGet("languages")]
    public async Task<IActionResult> GetLanguages()
    {
        var languages = await _localizationService.GetSupportedLanguagesAsync();
        return Ok(languages);
    }
}
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
│   │   │   ├── LocalizationController.cs
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
│   │   ���       ├── GoogleGeminiService.cs
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
│   │   │   │   ├── Language.cs
│   │   │   │   ├── ResourceKey.cs
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
│   │   │   ├── ProductForm.tsx
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
│   │   ├── api.ts
│   │   ├── voiceService.ts
│   │   ├── productService.ts
│   │   └── localizationService.ts
│   ├── hooks/
│   │   ├── useVoice.ts
│   │   ├── useProducts.ts
│   │   └── useDebounce.ts
│   ├── context/
│   │   ├── AppContext.tsx
│   │   └── LanguageContext.tsx
│   ├── types/
│   │   ├── product.types.ts
│   │   ├── voice.types.ts
│   ��   ├── localization.types.ts
│   │   └── api.types.ts
│   ├── utils/
│   │   ├── formatters.ts
│   │   ├── validators.ts
│   │   └── constants.ts
│   ├── locales/
│   │   └── i18n.config.ts
│   ├── pages/
│   │   ├── Home.tsx
│   │   ├── Products.tsx
│   │   ├── History.tsx
│   │   └── Settings.tsx
│   ├── App.tsx
│   ├── main.tsx
│   └── index.css
├── public/
│   └── locales/
│       └── .gitkeep
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

`backend/Dockerfile`:

```dockerfile
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
WORKDIR /app
EXPOSE 8080

FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /src
COPY ["EchoWarehouse.API/EchoWarehouse.API.csproj", "EchoWarehouse.API/"]
RUN dotnet restore "EchoWarehouse.API/EchoWarehouse.API.csproj"
COPY . .
WORKDIR "/src/EchoWarehouse.API"
RUN dotnet build "EchoWarehouse.API.csproj" -c Release -o /app/build

FROM build AS publish
RUN dotnet publish "EchoWarehouse.API.csproj" -c Release -o /app/publish

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "EchoWarehouse.API.dll"]
```

---

## 👨‍💻 Development

### Backend Code Examples

#### Google Gemini Service Implementation

```csharp
// Services/Implementations/GoogleGeminiService.cs
using Google.Cloud.AIPlatform.V1;
using Google.Protobuf.WellKnownTypes;

public class GoogleGeminiService : IAIService
{
    private readonly PredictionServiceClient _client;
    private readonly string _projectId;
    private readonly string _location;
    private readonly string _model;
    private readonly ILogger<GoogleGeminiService> _logger;

    public GoogleGeminiService(
        IConfiguration configuration,
        ILogger<GoogleGeminiService> logger)
    {
        var credentials = GoogleCredential.FromFile("path/to/key.json");
        _client = new PredictionServiceClientBuilder
        {
            Credential = credentials
        }.Build();

        _projectId = configuration["GoogleGemini:ProjectId"]!;
        _location = configuration["GoogleGemini:Location"]!;
        _model = configuration["GoogleGemini:Model"]!;
        _logger = logger;
    }

    public async Task<VoiceIntent> ParseIntentAsync(
        string transcript, 
        string language = "en")
    {
        var prompt = language == "hu" 
            ? CreateHungarianPrompt(transcript)
            : CreateEnglishPrompt(transcript);

        try
        {
            var endpoint = $"projects/{_projectId}/locations/{_location}/publishers/google/models/{_model}";
            
            var request = new PredictRequest
            {
                Endpoint = endpoint,
                Instances =
                {
                    Value.ForStruct(new Struct
                    {
                        Fields =
                        {
                            ["content"] = Value.ForString(prompt)
                        }
                    })
                },
                Parameters = Value.ForStruct(new Struct
                {
                    Fields =
                    {
                        ["temperature"] = Value.ForNumber(0.2),
                        ["maxOutputTokens"] = Value.ForNumber(256)
                    }
                })
            };

            var response = await _client.PredictAsync(request);
            var jsonResponse = response.Predictions[0].StructValue.Fields["content"].StringValue;
            
            var intent = JsonSerializer.Deserialize<VoiceIntent>(jsonResponse);
            
            _logger.LogInformation(
                "Gemini parsed intent: {Action} for product {Product}",
                intent?.Action,
                intent?.Product);

            return intent ?? throw new InvalidOperationException("Failed to parse intent");
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Gemini API error");
            throw;
        }
    }

    private string CreateEnglishPrompt(string transcript)
    {
        return $@"Parse this English warehouse command into JSON format:
'{transcript}'

JSON schema:
{{
    ""action"": ""add"" | ""remove"" | ""update"" | ""search"" | ""delete"",
    ""product"": ""product name"",
    ""quantity"": 123,
    ""unit"": ""pieces"" | ""kg"" | ""l"" etc.,
    ""location"": ""shelf/location"",
    ""serialNumber"": null | ""string"",
    ""articleNumber"": null | ""string"",
    ""confidence"": 0.0-1.0
}}

Return ONLY valid JSON, no additional text.";
    }

    private string CreateHungarianPrompt(string transcript)
    {
        return $@"Elemezd ezt a magyar nyelvű raktári parancsot JSON formátumban:
'{transcript}'

JSON séma:
{{
    ""action"": ""add"" | ""remove"" | ""update"" | ""search"" | ""delete"",
    ""product"": ""termék neve"",
    ""quantity"": 123,
    ""unit"": ""darab"" | ""kg"" | ""l"" stb.,
    ""location"": ""polc/hely"",
    ""serialNumber"": null | ""string"",
    ""articleNumber"": null | ""string"",
    ""confidence"": 0.0-1.0
}}

Csak érvényes JSON-t adj vissza, semmi mást.";
    }
}
```

#### Resource Key Repository (Localization)

```csharp
// Repositories/Implementations/ResourceKeyRepository.cs
public class ResourceKeyRepository : GenericRepository<ResourceKey>, IResourceKeyRepository
{
    public ResourceKeyRepository(AppDbContext context) : base(context)
    {
    }

    public async Task<IEnumerable<ResourceKey>> GetByLanguageAsync(string languageCode)
    {
        return await _context.ResourceKeys
            .Where(r => r.LanguageCode == languageCode)
            .OrderBy(r => r.Key)
            .ToListAsync();
    }

    public async Task<IEnumerable<ResourceKey>> GetByCategoryAsync(
        string category, 
        string languageCode)
    {
        return await _context.ResourceKeys
            .Where(r => r.Category == category && r.LanguageCode == languageCode)
            .ToListAsync();
    }

    public async Task<string?> GetValueAsync(string key, string languageCode)
    {
        var resource = await _context.ResourceKeys
            .FirstOrDefaultAsync(r => r.Key == key && r.LanguageCode == languageCode);
        
        return resource?.Value;
    }

    public async Task SeedAsync(IEnumerable<ResourceKey> keys)
    {
        foreach (var key in keys)
        {
            var exists = await _context.ResourceKeys
                .AnyAsync(r => r.Key == key.Key && r.LanguageCode == key.LanguageCode);
            
            if (!exists)
            {
                await _context.ResourceKeys.AddAsync(key);
            }
        }
        
        await _context.SaveChangesAsync();
    }
}
```

#### Database Context with Localization

```csharp
// Data/AppDbContext.cs
public class AppDbContext : DbContext
{
    public AppDbContext(DbContextOptions<AppDbContext> options) : base(options)
    {
    }

    public DbSet<Product> Products { get; set; }
    public DbSet<VoiceCommand> VoiceCommands { get; set; }
    public DbSet<History> History { get; set; }
    public DbSet<Language> Languages { get; set; }
    public DbSet<ResourceKey> ResourceKeys { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        // Product configuration
        modelBuilder.Entity<Product>(entity =>
        {
            entity.HasKey(e => e.Id);
            entity.Property(e => e.Name).IsRequired().HasMaxLength(255);
            entity.Property(e => e.Quantity).HasColumnType("decimal(10,2)");
            entity.HasIndex(e => e.Name);
            entity.HasIndex(e => e.Location);
        });

        // Language configuration
        modelBuilder.Entity<Language>(entity =>
        {
            entity.HasKey(e => e.Id);
            entity.Property(e => e.Code).IsRequired().HasMaxLength(10);
            entity.HasIndex(e => e.Code).IsUnique();
        });

        // ResourceKey configuration
        modelBuilder.Entity<ResourceKey>(entity =>
        {
            entity.HasKey(e => e.Id);
            entity.Property(e => e.Key).IsRequired().HasMaxLength(255);
            entity.Property(e => e.LanguageCode).IsRequired().HasMaxLength(10);
            entity.Property(e => e.Value).IsRequired();
            
            entity.HasIndex(e => new { e.Key, e.LanguageCode }).IsUnique();
            entity.HasIndex(e => e.LanguageCode);
            entity.HasIndex(e => e.Category);

            entity.HasOne<Language>()
                .WithMany()
                .HasForeignKey(e => e.LanguageCode)
                .HasPrincipalKey(l => l.Code);
        });

        // Seed default languages
        modelBuilder.Entity<Language>().HasData(
            new Language { Id = Guid.NewGuid(), Code = "en", Name = "English", NativeName = "English", IsActive = true },
            new Language { Id = Guid.NewGuid(), Code = "hu", Name = "Hungarian", NativeName = "Magyar", IsActive = true }
        );
    }
}
```

### Frontend Code Examples

#### Language Switcher Component

```typescript
// components/common/LanguageSwitcher.tsx
import React from 'react';
import { useTranslation } from 'react-i18next';

const LanguageSwitcher: React.FC = () => {
  const { i18n } = useTranslation();

  const languages = [
    { code: 'en', name: 'English', flag: '🇬🇧' },
    { code: 'hu', name: 'Magyar', flag: '🇭🇺' }
  ];

  const changeLanguage = (code: string) => {
    i18n.changeLanguage(code);
    localStorage.setItem('language', code);
  };

  return (
    <div className="flex gap-2">
      {languages.map((lang) => (
        <button
          key={lang.code}
          onClick={() => changeLanguage(lang.code)}
          className={`px-3 py-2 rounded-lg transition-colors ${
            i18n.language === lang.code
              ? 'bg-blue-600 text-white'
              : 'bg-gray-200 text-gray-700 hover:bg-gray-300'
          }`}
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

#### Voice Input with Language Support

```typescript
// components/voice/VoiceInput.tsx
import React, { useState } from 'react';
import { useTranslation } from 'react-i18next';
import { processVoiceCommand } from '../../services/voiceService';

const VoiceInput: React.FC = () => {
  const { t, i18n } = useTranslation();
  const [isListening, setIsListening] = useState(false);
  const [transcript, setTranscript] = useState('');
  const [result, setResult] = useState<any>(null);

  const startListening = () => {
    const recognition = new (window as any).webkitSpeechRecognition();
    
    // Set language based on current i18n language
    recognition.lang = i18n.language === 'hu' ? 'hu-HU' : 'en-US';
    recognition.continuous = false;
    recognition.interimResults = false;

    recognition.onstart = () => setIsListening(true);

    recognition.onresult = async (event: any) => {
      const text = event.results[0][0].transcript;
      setTranscript(text);
      setIsListening(false);

      try {
        const commandResult = await processVoiceCommand({
          transcript: text,
          language: i18n.language
        });
        setResult(commandResult);
      } catch (error) {
        console.error('Voice processing error:', error);
      }
    };

    recognition.onerror = () => setIsListening(false);
    recognition.start();
  };

  return (
    <div className="voice-input p-6 bg-white rounded-lg shadow-lg">
      <button
        onClick={startListening}
        disabled={isListening}
        className={`w-full py-4 rounded-lg font-semibold transition-all ${
          isListening
            ? 'bg-red-500 text-white animate-pulse'
            : 'bg-blue-600 text-white hover:bg-blue-700'
        }`}
      >
        {isListening ? (
          <>🎤 {t('voice.listening')}</>
        ) : (
          <>🎙️ {t('voice.speak')}</>
        )}
      </button>

      {transcript && (
        <div className="mt-4 p-4 bg-gray-100 rounded">
          <strong>{t('voice.heard')}:</strong> {transcript}
        </div>
      )}

      {result && (
        <div className="mt-4 p-4 bg-green-100 rounded">
          <strong>{t('voice.result')}:</strong>
          <p>{result.message}</p>
        </div>
      )}
    </div>
  );
};

export default VoiceInput;
```

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

# MediRep AI - Design Document

## Project Overview

**Project Name:** MediRep AI - Healthcare Intelligence Platform  
**Track:** AI for Healthcare & Life Sciences  
**Version:** 1.0  
**Last Updated:** February 12, 2026

## Executive Summary

This document outlines the technical architecture, system design, and implementation strategy for MediRep AI, an AI-powered healthcare intelligence platform. The system combines retrieval-augmented generation (RAG), multi-modal AI, and a verified pharmacist marketplace to provide accurate medical information and human escalation when needed.

## Design Principles

### 1. Evidence-First Architecture
- Every response must be traceable to a source
- Prefer database facts over LLM generation
- Explicit uncertainty handling with human escalation

### 2. Safety by Design
- Strict mode boundaries (insurance vs clinical queries)
- Multi-layer validation for drug interactions
- Clear medical disclaimers on all outputs

### 3. Progressive Enhancement
- Core functionality works without JavaScript
- Graceful degradation for older browsers
- Offline-first for critical features

### 4. Privacy and Security
- Row-level security (RLS) for all user data
- No storage of sensitive medical records
- End-to-end encryption for consultations

### 5. Performance and Scalability
- Edge caching for static content
- Database query optimization with indexes
- Horizontal scaling for API servers

## System Architecture

### High-Level Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                        Client Layer                          │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │   Web App    │  │  Mobile Web  │  │   PWA        │      │
│  │  (Next.js)   │  │  (Responsive)│  │ (Future)     │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
└─────────────────────────────────────────────────────────────┘
                            │
                            │ HTTPS/WSS
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                      API Gateway Layer                       │
│  ┌──────────────────────────────────────────────────────┐   │
│  │              FastAPI Backend (Python)                 │   │
│  │  - Rate Limiting (SlowAPI)                           │   │
│  │  - CORS Policy                                       │   │
│  │  - JWT Authentication                                │   │
│  │  - Request Validation (Pydantic)                     │   │
│  └──────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
                            │
        ┌───────────────────┼───────────────────┐
        ▼                   ▼                   ▼
┌──────────────┐  ┌──────────────┐  ┌──────────────┐
│   AI Layer   │  │  Data Layer  │  │ Integration  │
│              │  │              │  │    Layer     │
└──────────────┘  └──────────────┘  └──────────────┘
```

### Detailed Component Architecture


#### AI Layer Components

```
┌─────────────────────────────────────────────────────────────┐
│                        AI Services                           │
│                                                              │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  Gemini Service (Primary LLM)                        │   │
│  │  - Chat generation with context                      │   │
│  │  - Vision (pill identification)                      │   │
│  │  - Audio transcription                               │   │
│  │  - Intent classification                             │   │
│  └──────────────────────────────────────────────────────┘   │
│                                                              │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  Groq Service (Fallback LLM)                         │   │
│  │  - English-only fallback                             │   │
│  │  - Faster inference for simple queries               │   │
│  └──────────────────────────────────────────────────────┘   │
│                                                              │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  RAG Service (Retrieval-Augmented Generation)        │   │
│  │  - Hybrid search (vector + text)                     │   │
│  │  - Intent-based reranking                            │   │
│  │  - Context formatting for LLM                        │   │
│  └──────────────────────────────────────────────────────┘   │
│                                                              │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  Embedding Service                                    │   │
│  │  - Sentence Transformers (all-MiniLM-L6-v2)          │   │
│  │  - 384-dimensional vectors                           │   │
│  │  - Local inference (no API calls)                    │   │
│  └──────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
```

#### Data Layer Components

```
┌─────────────────────────────────────────────────────────────┐
│                      Data Services                           │
│                                                              │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  Supabase (PostgreSQL + Auth)                        │   │
│  │  - User profiles and authentication                  │   │
│  │  - Chat sessions and messages                        │   │
│  │  - Pharmacist profiles and schedules                 │   │
│  │  - Consultation bookings and payments                │   │
│  │  - Insurance schemes and package rates               │   │
│  │  - Row-level security (RLS) policies                 │   │
│  └──────────────────────────────────────────────────────┘   │
│                                                              │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  Turso (LibSQL - Drug Database)                      │   │
│  │  - 250,000+ Indian medicines                         │   │
│  │  - Full-text search with FTS5                        │   │
│  │  - Edge replication for low latency                  │   │
│  └──────────────────────────────────────────────────────┘   │
│                                                              │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  Qdrant (Vector Database)                            │   │
│  │  - drug_embeddings collection (250k vectors)         │   │
│  │  - medical_qa collection (47k Q&A pairs)             │   │
│  │  - HNSW index for fast similarity search             │   │
│  └──────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
```


#### Integration Layer Components

```
┌─────────────────────────────────────────────────────────────┐
│                   External Integrations                      │
│                                                              │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  Razorpay (Payment Gateway)                          │   │
│  │  - Order creation and payment processing             │   │
│  │  - Webhook verification                              │   │
│  │  - Payout API for pharmacists                        │   │
│  └──────────────────────────────────────────────────────┘   │
│                                                              │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  Agora (Real-time Communication)                     │   │
│  │  - Voice/video call infrastructure                   │   │
│  │  - Token generation for secure access                │   │
│  │  - Call quality monitoring                           │   │
│  └──────────────────────────────────────────────────────┘   │
│                                                              │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  Resend (Email Service)                              │   │
│  │  - Transactional emails                              │   │
│  │  - Booking confirmations                             │   │
│  │  - Admin notifications                               │   │
│  └──────────────────────────────────────────────────────┘   │
│                                                              │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  OpenFDA / RxNorm / PubChem                          │   │
│  │  - Drug label data                                   │   │
│  │  - Chemical structures                               │   │
│  │  - Classification data                               │   │
│  └──────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
```

## Data Flow Diagrams

### 1. Chat Query Flow (RAG Pipeline)

```
User Query
    │
    ▼
┌─────────────────────┐
│ Intent Detection    │ ← Gemini API
│ (plan_intent)       │
└─────────────────────┘
    │
    ├─→ Intent: INFO/SUBSTITUTE/INTERACTION
    │   └─→ Extract drug names
    │
    ▼
┌─────────────────────┐
│ Hybrid Retrieval    │
│ (RAG Service)       │
└─────────────────────┘
    │
    ├─→ Qdrant: Semantic search (drug_embeddings + medical_qa)
    ├─→ Turso: Exact match + fuzzy search
    └─→ Rerank by intent weights
    │
    ▼
┌─────────────────────┐
│ Context Assembly    │
└─────────────────────┘
    │
    ├─→ Drug info from Turso
    ├─→ RAG context from Qdrant
    ├─→ Patient context (if provided)
    └─→ Mode-specific instructions
    │
    ▼
┌─────────────────────┐
│ LLM Generation      │ ← Gemini/Groq
│ (generate_response) │
└─────────────────────┘
    │
    ├─→ Apply system prompt
    ├─→ Enforce format constraints
    └─→ Extract citations
    │
    ▼
┌─────────────────────┐
│ Response Post-      │
│ Processing          │
└─────────────────────┘
    │
    ├─→ Strip trailing CTAs
    ├─→ Generate suggestions
    └─→ Format for display
    │
    ▼
Response to User
```


### 2. Pharmacist Consultation Booking Flow

```
User Browses Marketplace
    │
    ▼
┌─────────────────────┐
│ Select Pharmacist   │
│ & Time Slot         │
└─────────────────────┘
    │
    ▼
┌─────────────────────┐
│ Create Booking      │ → Supabase: consultations table
│ (Status: pending)   │
└─────────────────────┘
    │
    ▼
┌─────────────────────┐
│ Create Razorpay     │ ← Razorpay API
│ Order               │
└─────────────────────┘
    │
    ▼
┌─────────────────────┐
│ User Completes      │
│ Payment             │
└─────────────────────┘
    │
    ▼
┌─────────────────────┐
│ Razorpay Webhook    │ → Verify signature
│ (payment.captured)  │
└─────────────────────┘
    │
    ▼
┌─────────────────────┐
│ Update Booking      │ → Supabase: payment_status = 'paid'
│ Status              │   Generate Agora channel
└─────────────────────┘
    │
    ├─→ Send confirmation email (Resend)
    └─→ Notify pharmacist
    │
    ▼
┌─────────────────────┐
│ User Joins Call     │ ← Agora SDK
│ (at scheduled time) │   Generate token with channel name
└─────────────────────┘
    │
    ▼
┌─────────────────────┐
│ Consultation        │
│ (Voice/Video)       │
└─────────────────────┘
    │
    ▼
┌─────────────────────┐
│ Post-Consultation   │
│ Rating & Review     │
└─────────────────────┘
    │
    ▼
┌─────────────────────┐
│ Payout Calculation  │ → Platform fee: 20%
│ (Weekly batch)      │   Pharmacist net: 80%
└─────────────────────┘
```

### 3. Drug Search Flow (Multi-Source)

```
User Search Query
    │
    ▼
┌─────────────────────┐
│ Normalize Query     │
│ (lowercase, trim)   │
└─────────────────────┘
    │
    ▼
┌─────────────────────┐
│ Check Cache         │
│ (LRU with TTL)      │
└─────────────────────┘
    │
    ├─→ Cache Hit → Return cached results
    │
    └─→ Cache Miss
        │
        ▼
    ┌─────────────────────┐
    │ Parallel Search     │
    └─────────────────────┘
        │
        ├─→ Qdrant: Semantic vector search
        │   └─→ Embed query → Search drug_embeddings
        │
        ├─→ Turso: Full-text search (FTS5)
        │   └─→ Match name, generic_name, description
        │
        └─→ OpenFDA: Fallback for international drugs
            └─→ Search brand_name, generic_name
        │
        ▼
    ┌─────────────────────┐
    │ Merge & Deduplicate │
    │ (by drug name)      │
    └─────────────────────┘
        │
        ▼
    ┌─────────────────────┐
    │ Rank Results        │
    │ - Exact match first │
    │ - Prefix match      │
    │ - Substring match   │
    │ - Prefer monotherapy│
    └─────────────────────┘
        │
        ▼
    ┌─────────────────────┐
    │ Cache Results       │
    │ (TTL: 1 hour)       │
    └─────────────────────┘
        │
        ▼
    Return Top 10 Results
```


## Database Schema Design

### Supabase (PostgreSQL) Schema

#### Users and Authentication (Managed by Supabase Auth)
```sql
-- auth.users (built-in Supabase table)
-- id, email, encrypted_password, email_confirmed_at, etc.

-- Extended user profiles
CREATE TABLE user_profiles (
    id UUID PRIMARY KEY REFERENCES auth.users(id) ON DELETE CASCADE,
    full_name TEXT,
    phone TEXT,
    role TEXT CHECK (role IN ('patient', 'pharmacist', 'admin')),
    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- RLS Policy: Users can only read/update their own profile
ALTER TABLE user_profiles ENABLE ROW LEVEL SECURITY;
CREATE POLICY "Users can view own profile" ON user_profiles
    FOR SELECT USING (auth.uid() = id);
CREATE POLICY "Users can update own profile" ON user_profiles
    FOR UPDATE USING (auth.uid() = id);
```

#### Chat Sessions
```sql
CREATE TABLE chat_sessions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES auth.users(id) ON DELETE CASCADE,
    title TEXT NOT NULL DEFAULT 'New Conversation',
    is_archived BOOLEAN DEFAULT FALSE,
    patient_context JSONB, -- {age, sex, weight, conditions, meds}
    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ DEFAULT NOW(),
    last_message_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE INDEX idx_sessions_user_id ON chat_sessions(user_id);
CREATE INDEX idx_sessions_last_message ON chat_sessions(last_message_at DESC);

-- RLS: Users can only access their own sessions
ALTER TABLE chat_sessions ENABLE ROW LEVEL SECURITY;
CREATE POLICY "Users can manage own sessions" ON chat_sessions
    USING (auth.uid() = user_id);
```

#### Chat Messages
```sql
CREATE TABLE chat_messages (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    session_id UUID NOT NULL REFERENCES chat_sessions(id) ON DELETE CASCADE,
    role TEXT NOT NULL CHECK (role IN ('user', 'assistant')),
    content TEXT NOT NULL,
    citations JSONB, -- [{title, url, source}]
    created_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE INDEX idx_messages_session_id ON chat_messages(session_id);
CREATE INDEX idx_messages_created_at ON chat_messages(created_at DESC);

-- RLS: Users can only access messages from their own sessions
ALTER TABLE chat_messages ENABLE ROW LEVEL SECURITY;
CREATE POLICY "Users can view own messages" ON chat_messages
    FOR SELECT USING (
        EXISTS (
            SELECT 1 FROM chat_sessions
            WHERE chat_sessions.id = chat_messages.session_id
            AND chat_sessions.user_id = auth.uid()
        )
    );
```


#### Pharmacist Profiles
```sql
CREATE TABLE pharmacist_profiles (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL UNIQUE REFERENCES auth.users(id) ON DELETE CASCADE,
    full_name TEXT NOT NULL,
    phone TEXT NOT NULL,
    license_number TEXT NOT NULL UNIQUE,
    license_image_url TEXT NOT NULL,
    license_state TEXT,
    specializations TEXT[] DEFAULT '{}',
    experience_years INTEGER DEFAULT 0,
    languages TEXT[] DEFAULT '{"English", "Hindi"}',
    education TEXT,
    bio TEXT,
    rate INTEGER NOT NULL DEFAULT 299 CHECK (rate >= 99 AND rate <= 9999),
    duration_minutes INTEGER NOT NULL DEFAULT 15 CHECK (duration_minutes IN (15, 30, 45, 60)),
    upi_id TEXT,
    profile_image_url TEXT,
    rating_avg NUMERIC(3,2) DEFAULT 0.00,
    rating_count INTEGER DEFAULT 0,
    completed_consultations INTEGER DEFAULT 0,
    is_available BOOLEAN DEFAULT FALSE,
    verification_status TEXT DEFAULT 'pending' CHECK (verification_status IN ('pending', 'approved', 'rejected')),
    verified_at TIMESTAMPTZ,
    verified_by UUID REFERENCES auth.users(id),
    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE INDEX idx_pharmacist_user_id ON pharmacist_profiles(user_id);
CREATE INDEX idx_pharmacist_verification ON pharmacist_profiles(verification_status);
CREATE INDEX idx_pharmacist_available ON pharmacist_profiles(is_available) WHERE is_available = TRUE;

-- RLS: Public can view approved pharmacists, owners can manage their own
ALTER TABLE pharmacist_profiles ENABLE ROW LEVEL SECURITY;
CREATE POLICY "Anyone can view approved pharmacists" ON pharmacist_profiles
    FOR SELECT USING (verification_status = 'approved');
CREATE POLICY "Pharmacists can update own profile" ON pharmacist_profiles
    FOR UPDATE USING (auth.uid() = user_id);
```

#### Consultations
```sql
CREATE TABLE consultations (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    patient_id UUID NOT NULL REFERENCES auth.users(id) ON DELETE CASCADE,
    pharmacist_id UUID NOT NULL REFERENCES pharmacist_profiles(id),
    scheduled_at TIMESTAMPTZ NOT NULL,
    duration_minutes INTEGER NOT NULL,
    status TEXT DEFAULT 'pending' CHECK (status IN ('pending', 'confirmed', 'completed', 'cancelled')),
    patient_concern TEXT,
    amount INTEGER NOT NULL, -- in paise (₹299 = 29900)
    payment_status TEXT DEFAULT 'pending' CHECK (payment_status IN ('pending', 'paid', 'refunded', 'failed')),
    razorpay_order_id TEXT,
    razorpay_payment_id TEXT,
    agora_channel TEXT,
    rating INTEGER CHECK (rating >= 1 AND rating <= 5),
    review TEXT,
    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE INDEX idx_consultations_patient ON consultations(patient_id);
CREATE INDEX idx_consultations_pharmacist ON consultations(pharmacist_id);
CREATE INDEX idx_consultations_scheduled ON consultations(scheduled_at);
CREATE INDEX idx_consultations_status ON consultations(status);

-- RLS: Patients and pharmacists can view their own consultations
ALTER TABLE consultations ENABLE ROW LEVEL SECURITY;
CREATE POLICY "Users can view own consultations" ON consultations
    FOR SELECT USING (
        auth.uid() = patient_id OR
        auth.uid() IN (SELECT user_id FROM pharmacist_profiles WHERE id = pharmacist_id)
    );
```


#### Insurance Schemes and Package Rates
```sql
CREATE TABLE insurance_schemes (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    scheme_code TEXT NOT NULL UNIQUE, -- PMJAY, CGHS, PRIVATE
    scheme_name TEXT NOT NULL,
    scheme_full_name TEXT,
    scheme_type TEXT, -- government, private
    coverage_limit_display TEXT,
    coverage_type TEXT,
    eligibility JSONB, -- Array of eligibility criteria
    covered_items JSONB, -- Array of covered items
    excluded_items JSONB, -- Array of exclusions
    drug_coverage JSONB, -- Drug coverage details
    helpline TEXT,
    website TEXT,
    source_url TEXT,
    last_verified_at TIMESTAMPTZ,
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE TABLE insurance_package_rates (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    scheme_id UUID NOT NULL REFERENCES insurance_schemes(id) ON DELETE CASCADE,
    package_code TEXT NOT NULL,
    procedure_name TEXT NOT NULL,
    procedure_name_normalized TEXT NOT NULL, -- Lowercase for search
    category TEXT,
    sub_category TEXT,
    rate_inr INTEGER NOT NULL,
    rate_display TEXT,
    includes_implants BOOLEAN DEFAULT FALSE,
    special_conditions TEXT,
    created_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE INDEX idx_package_rates_scheme ON insurance_package_rates(scheme_id);
CREATE INDEX idx_package_rates_category ON insurance_package_rates(category);
CREATE INDEX idx_package_rates_normalized ON insurance_package_rates USING GIN(to_tsvector('english', procedure_name_normalized));

-- RLS: Public read access for insurance data
ALTER TABLE insurance_schemes ENABLE ROW LEVEL SECURITY;
CREATE POLICY "Anyone can view active schemes" ON insurance_schemes
    FOR SELECT USING (is_active = TRUE);

ALTER TABLE insurance_package_rates ENABLE ROW LEVEL SECURITY;
CREATE POLICY "Anyone can view package rates" ON insurance_package_rates
    FOR SELECT USING (TRUE);
```

### Turso (LibSQL) Schema

```sql
-- Indian Medicines Database
CREATE TABLE drugs (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT NOT NULL,
    generic_name TEXT,
    manufacturer TEXT,
    price REAL,
    price_raw TEXT,
    pack_size TEXT,
    therapeutic_class TEXT,
    action_class TEXT,
    side_effects TEXT,
    substitutes TEXT, -- Comma-separated
    description TEXT,
    is_discontinued BOOLEAN DEFAULT 0,
    created_at TEXT DEFAULT CURRENT_TIMESTAMP,
    updated_at TEXT DEFAULT CURRENT_TIMESTAMP
);

-- Full-text search index
CREATE VIRTUAL TABLE drugs_fts USING fts5(
    name, generic_name, description, therapeutic_class,
    content='drugs', content_rowid='id'
);

-- Indexes for performance
CREATE INDEX idx_drugs_name ON drugs(name COLLATE NOCASE);
CREATE INDEX idx_drugs_generic ON drugs(generic_name COLLATE NOCASE);
CREATE INDEX idx_drugs_price ON drugs(price);
CREATE INDEX idx_drugs_therapeutic ON drugs(therapeutic_class);
```


### Qdrant Collections Schema

```python
# Collection: drug_embeddings
{
    "vectors": {
        "size": 384,  # all-MiniLM-L6-v2 embedding dimension
        "distance": "Cosine"
    },
    "payload_schema": {
        "drug_id": "integer",      # Foreign key to Turso drugs.id
        "drug_name": "keyword",    # For filtering
        "generic_name": "keyword",
        "therapeutic_class": "keyword"
    }
}

# Collection: medical_qa
{
    "vectors": {
        "size": 384,
        "distance": "Cosine"
    },
    "payload_schema": {
        "question": "text",
        "answer": "text",
        "source": "keyword",  # NIH, MedQuAD, PubMed
        "category": "keyword" # symptoms, diagnosis, treatment, etc.
    }
}
```

## API Design

### REST API Endpoints

#### Authentication
```
POST   /api/auth/signup          # Create new user account
POST   /api/auth/login           # Login with email/password
POST   /api/auth/logout          # Logout and invalidate token
POST   /api/auth/refresh         # Refresh access token
POST   /api/auth/forgot-password # Send password reset email
POST   /api/auth/reset-password  # Reset password with token
```

#### Chat
```
POST   /api/chat                 # Send message and get response
GET    /api/sessions             # List user's chat sessions
POST   /api/sessions             # Create new session
GET    /api/sessions/{id}        # Get session with messages
PATCH  /api/sessions/{id}        # Update session (title, archive)
DELETE /api/sessions/{id}        # Delete session
```

#### Drugs
```
GET    /api/drugs/search?q={query}           # Search drugs
GET    /api/drugs/{name}                     # Get drug details
GET    /api/drugs/substitutes?drug_name={name} # Find cheaper alternatives
POST   /api/drugs/interactions               # Check drug interactions
```

#### Vision
```
POST   /api/vision/identify-pill  # Upload image, get pill identification
```

#### Voice
```
POST   /api/voice/transcribe      # Upload audio, get text transcription
POST   /api/voice/tts             # Convert text to speech
```

#### Marketplace
```
GET    /api/marketplace           # List verified pharmacists
GET    /api/marketplace/{id}      # Get pharmacist profile
POST   /api/pharmacist/register   # Register as pharmacist
PATCH  /api/pharmacist/profile    # Update pharmacist profile
GET    /api/pharmacist/schedule   # Get availability schedule
POST   /api/pharmacist/schedule   # Set availability slots
```

#### Consultations
```
POST   /api/consultations/book    # Book consultation (creates Razorpay order)
GET    /api/consultations         # List user's consultations
GET    /api/consultations/{id}    # Get consultation details
POST   /api/consultations/{id}/join # Get Agora token to join call
POST   /api/consultations/{id}/review # Submit rating and review
```

#### Admin
```
GET    /api/admin/users           # List all users (admin only)
GET    /api/admin/verify          # List pending pharmacist verifications
POST   /api/admin/verify/{id}     # Approve/reject pharmacist
GET    /api/admin/payouts         # List pending payouts
POST   /api/admin/payouts/process # Process batch payouts
```


### WebSocket Events (Socket.IO)

```
# Client → Server
emit('join_consultation', {consultation_id})
emit('leave_consultation', {consultation_id})
emit('chat_message', {consultation_id, message})

# Server → Client
on('consultation_started', {consultation_id, participants})
on('consultation_ended', {consultation_id})
on('chat_message', {sender_id, message, timestamp})
on('participant_joined', {user_id, name})
on('participant_left', {user_id})
```

## Frontend Architecture

### Technology Stack
- **Framework:** Next.js 14 (App Router)
- **Language:** TypeScript (strict mode)
- **Styling:** Tailwind CSS + shadcn/ui components
- **State Management:** React Context + Zustand (for complex state)
- **Forms:** React Hook Form + Zod validation
- **API Client:** Fetch API with custom hooks
- **Real-time:** Socket.IO client
- **Video/Voice:** Agora Web SDK

### Page Structure

```
app/
├── page.tsx                    # Landing page
├── layout.tsx                  # Root layout with providers
├── auth/
│   ├── login/page.tsx
│   ├── signup/page.tsx
│   ├── forgot-password/page.tsx
│   └── reset-password/page.tsx
├── dashboard/
│   ├── layout.tsx              # Dashboard layout with sidebar
│   ├── page.tsx                # Dashboard home
│   ├── Chat/page.tsx           # AI chat interface
│   ├── InteractionGraph/page.tsx
│   ├── PillScanner/page.tsx
│   ├── PatientContext/page.tsx
│   ├── BookPharmacist/page.tsx
│   └── settings/page.tsx
├── consultations/
│   ├── page.tsx                # List consultations
│   └── [id]/page.tsx           # Consultation detail with video
├── pharmacist/
│   ├── dashboard/page.tsx      # Pharmacist dashboard
│   ├── consultations/
│   │   ├── page.tsx
│   │   └── [id]/page.tsx
│   └── earnings/page.tsx
├── admin/
│   ├── layout.tsx
│   ├── page.tsx                # Admin dashboard
│   ├── users/page.tsx
│   ├── verify/page.tsx         # Pharmacist verification
│   └── payouts/page.tsx
└── marketplace/page.tsx        # Browse pharmacists
```

### Component Architecture

```
components/
├── ui/                         # shadcn/ui primitives
│   ├── button.tsx
│   ├── input.tsx
│   ├── card.tsx
│   ├── dialog.tsx
│   └── ...
├── chat/
│   ├── ChatInterface.tsx       # Main chat component
│   ├── MessageList.tsx
│   ├── MessageInput.tsx
│   ├── CitationCard.tsx
│   └── SuggestionChips.tsx
├── drug/
│   ├── DrugCard.tsx
│   ├── DrugSearchBar.tsx
│   ├── InteractionMatrix.tsx
│   └── SubstituteList.tsx
├── marketplace/
│   ├── PharmacistCard.tsx
│   ├── PharmacistFilters.tsx
│   ├── BookingModal.tsx
│   └── RatingStars.tsx
├── consultation/
│   ├── VideoCall.tsx           # Agora integration
│   ├── CallControls.tsx
│   └── ChatPanel.tsx
└── layout/
    ├── Sidebar.tsx
    ├── Header.tsx
    └── Footer.tsx
```


## Security Design

### Authentication Flow

```
1. User Registration
   ├─→ Frontend: Collect email, password, name
   ├─→ Supabase Auth: Create user account
   ├─→ Backend: Create user_profile record
   └─→ Send verification email

2. User Login
   ├─→ Frontend: Submit credentials
   ├─→ Supabase Auth: Verify and issue JWT
   ├─→ Store access token (memory) + refresh token (httpOnly cookie)
   └─→ Redirect to dashboard

3. API Request
   ├─→ Frontend: Include Authorization: Bearer {token}
   ├─→ Backend: Verify JWT signature and expiry
   ├─→ Extract user_id from token
   └─→ Apply RLS policies in database queries

4. Token Refresh
   ├─→ Access token expires (1 hour)
   ├─→ Frontend: Detect 401 response
   ├─→ Call refresh endpoint with refresh token
   ├─→ Receive new access token
   └─→ Retry original request
```

### Row-Level Security (RLS) Policies

```sql
-- Example: Chat sessions
-- Users can only access their own sessions
CREATE POLICY "Users can manage own sessions" ON chat_sessions
    USING (auth.uid() = user_id);

-- Example: Consultations
-- Patients and pharmacists can view their own consultations
CREATE POLICY "Users can view own consultations" ON consultations
    FOR SELECT USING (
        auth.uid() = patient_id OR
        auth.uid() IN (SELECT user_id FROM pharmacist_profiles WHERE id = pharmacist_id)
    );

-- Example: Pharmacist profiles
-- Public can view approved pharmacists
CREATE POLICY "Anyone can view approved pharmacists" ON pharmacist_profiles
    FOR SELECT USING (verification_status = 'approved');

-- Pharmacists can update their own profile
CREATE POLICY "Pharmacists can update own profile" ON pharmacist_profiles
    FOR UPDATE USING (auth.uid() = user_id);
```

### Payment Security

```
1. Order Creation
   ├─→ Backend: Create consultation record (status: pending)
   ├─→ Razorpay: Create order with amount, currency
   ├─→ Store razorpay_order_id in database
   └─→ Return order details to frontend

2. Payment Processing
   ├─→ Frontend: Razorpay Checkout modal
   ├─→ User completes payment
   ├─→ Razorpay: Redirect to success/failure URL
   └─→ Razorpay: Send webhook to backend

3. Webhook Verification
   ├─→ Backend: Receive webhook payload
   ├─→ Verify signature using RAZORPAY_WEBHOOK_SECRET
   ├─→ If valid: Update payment_status = 'paid'
   ├─→ Generate Agora channel and token
   ├─→ Send confirmation email
   └─→ If invalid: Log and reject

4. Payout Processing
   ├─→ Admin: Trigger weekly payout batch
   ├─→ Backend: Calculate net amount (80% of consultation fee)
   ├─→ Razorpay Payout API: Transfer to pharmacist UPI
   ├─→ Update payout record with status
   └─→ Send payout confirmation email
```

### Data Encryption

```
# In Transit
- TLS 1.3 for all HTTPS connections
- WSS (WebSocket Secure) for real-time communication
- Agora: DTLS-SRTP for media encryption

# At Rest
- Supabase: AES-256 encryption for database
- Turso: Encrypted storage
- File uploads: Encrypted S3 buckets (if used)

# Sensitive Data Handling
- Passwords: bcrypt hashing (Supabase Auth)
- Payment tokens: Never stored, only Razorpay IDs
- License images: Stored with restricted access URLs
- PII: Minimal collection, encrypted at rest
```


## Performance Optimization

### Caching Strategy

```
┌─────────────────────────────────────────────────────────────┐
│                      Caching Layers                          │
│                                                              │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  Browser Cache (Service Worker)                      │   │
│  │  - Static assets (JS, CSS, images)                   │   │
│  │  - API responses (short TTL)                         │   │
│  │  - Offline fallback pages                            │   │
│  └──────────────────────────────────────────────────────┘   │
│                          ▼                                   │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  CDN Cache (Vercel Edge)                             │   │
│  │  - Static pages and assets                           │   │
│  │  - API responses with Cache-Control headers          │   │
│  │  - TTL: 1 hour for static, 5 min for dynamic         │   │
│  └──────────────────────────────────────────────────────┘   │
│                          ▼                                   │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  Application Cache (Python LRU)                      │   │
│  │  - Drug info: 1 hour TTL                             │   │
│  │  - Search results: 1 hour TTL                        │   │
│  │  - Insurance data: 24 hour TTL                       │   │
│  │  - Max size: 1000 entries                            │   │
│  └──────────────────────────────────────────────────────┘   │
│                          ▼                                   │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  Database Query Cache                                │   │
│  │  - Supabase: Built-in query caching                  │   │
│  │  - Turso: Edge replication for low latency           │   │
│  │  - Qdrant: In-memory HNSW index                      │   │
│  └──────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
```

### Database Optimization

```sql
-- Indexes for fast lookups
CREATE INDEX idx_sessions_user_id ON chat_sessions(user_id);
CREATE INDEX idx_sessions_last_message ON chat_sessions(last_message_at DESC);
CREATE INDEX idx_messages_session_id ON chat_messages(session_id);
CREATE INDEX idx_consultations_patient ON consultations(patient_id);
CREATE INDEX idx_consultations_pharmacist ON consultations(pharmacist_id);
CREATE INDEX idx_consultations_scheduled ON consultations(scheduled_at);

-- Composite indexes for common queries
CREATE INDEX idx_pharmacist_available_rating ON pharmacist_profiles(is_available, rating_avg DESC)
    WHERE verification_status = 'approved';

-- Full-text search indexes
CREATE INDEX idx_package_rates_normalized ON insurance_package_rates
    USING GIN(to_tsvector('english', procedure_name_normalized));

-- Partial indexes for filtered queries
CREATE INDEX idx_pharmacist_approved ON pharmacist_profiles(verification_status)
    WHERE verification_status = 'approved';
```

### API Rate Limiting

```python
from slowapi import Limiter
from slowapi.util import get_remote_address

limiter = Limiter(key_func=get_remote_address)

# Global rate limit
@app.middleware("http")
async def rate_limit_middleware(request: Request, call_next):
    # 100 requests per minute per IP
    pass

# Endpoint-specific limits
@app.post("/api/chat")
@limiter.limit("20/minute")  # Chat: 20 per minute
async def chat_endpoint():
    pass

@app.get("/api/drugs/search")
@limiter.limit("60/minute")  # Search: 60 per minute
async def search_drugs():
    pass

@app.post("/api/vision/identify-pill")
@limiter.limit("10/minute")  # Vision: 10 per minute (expensive)
async def identify_pill():
    pass
```

### Lazy Loading and Code Splitting

```typescript
// Next.js dynamic imports for code splitting
import dynamic from 'next/dynamic';

// Lazy load heavy components
const VideoCall = dynamic(() => import('@/components/consultation/VideoCall'), {
  loading: () => <LoadingSpinner />,
  ssr: false // Client-side only
});

const InteractionGraph = dynamic(() => import('@/components/drug/InteractionGraph'), {
  loading: () => <LoadingSpinner />
});

// Lazy load Agora SDK (large bundle)
const AgoraRTC = dynamic(() => import('agora-rtc-sdk-ng'), {
  ssr: false
});
```


## Error Handling and Resilience

### Error Handling Strategy

```python
# Backend: Structured error responses
class APIError(Exception):
    def __init__(self, status_code: int, message: str, details: dict = None):
        self.status_code = status_code
        self.message = message
        self.details = details or {}

@app.exception_handler(APIError)
async def api_error_handler(request: Request, exc: APIError):
    return JSONResponse(
        status_code=exc.status_code,
        content={
            "error": exc.message,
            "details": exc.details,
            "timestamp": datetime.utcnow().isoformat()
        }
    )

# Example usage
if not GEMINI_API_KEY:
    raise APIError(503, "AI service unavailable", {"reason": "API key not configured"})
```

```typescript
// Frontend: Centralized error handling
class APIClient {
  async request(endpoint: string, options: RequestInit) {
    try {
      const response = await fetch(endpoint, options);
      
      if (!response.ok) {
        const error = await response.json();
        throw new APIError(response.status, error.message, error.details);
      }
      
      return await response.json();
    } catch (error) {
      if (error instanceof APIError) {
        // Show user-friendly error message
        toast.error(error.message);
      } else {
        // Network or unexpected error
        toast.error("Something went wrong. Please try again.");
      }
      throw error;
    }
  }
}
```

### Fallback Mechanisms

```python
# AI Service Fallback: Gemini → Groq → Error
async def generate_response(message: str, **kwargs):
    try:
        # Try Gemini first
        return await _generate_with_gemini(message, **kwargs)
    except asyncio.TimeoutError:
        logger.warning("Gemini timeout, trying Groq fallback")
        try:
            return await _generate_with_groq(message, **kwargs)
        except Exception as groq_error:
            logger.error(f"Groq fallback failed: {groq_error}")
            raise APIError(503, "AI service temporarily unavailable")
    except Exception as e:
        logger.error(f"Gemini error: {e}")
        raise APIError(500, "Failed to generate response")

# Database Fallback: Qdrant → Turso → OpenFDA
async def search_drugs(query: str):
    results = []
    
    # Try Qdrant semantic search
    try:
        qdrant_results = await qdrant_service.search_similar(query)
        results.extend(qdrant_results)
    except Exception as e:
        logger.warning(f"Qdrant search failed: {e}")
    
    # Fallback to Turso text search
    if len(results) < 5:
        try:
            turso_results = await turso_service.search_drugs(query)
            results.extend(turso_results)
        except Exception as e:
            logger.warning(f"Turso search failed: {e}")
    
    # Last resort: OpenFDA
    if len(results) < 3:
        try:
            fda_results = await search_openfda(query)
            results.extend(fda_results)
        except Exception as e:
            logger.warning(f"OpenFDA search failed: {e}")
    
    return results[:10]
```

### Circuit Breaker Pattern

```python
from circuitbreaker import circuit

@circuit(failure_threshold=5, recovery_timeout=60)
async def call_external_api(url: str):
    """
    Circuit breaker for external API calls.
    Opens circuit after 5 failures, closes after 60 seconds.
    """
    async with httpx.AsyncClient() as client:
        response = await client.get(url, timeout=10.0)
        response.raise_for_status()
        return response.json()
```


## Monitoring and Observability

### Logging Strategy

```python
import logging
import structlog

# Structured logging configuration
structlog.configure(
    processors=[
        structlog.stdlib.filter_by_level,
        structlog.stdlib.add_logger_name,
        structlog.stdlib.add_log_level,
        structlog.stdlib.PositionalArgumentsFormatter(),
        structlog.processors.TimeStamper(fmt="iso"),
        structlog.processors.StackInfoRenderer(),
        structlog.processors.format_exc_info,
        structlog.processors.UnicodeDecoder(),
        structlog.processors.JSONRenderer()
    ],
    context_class=dict,
    logger_factory=structlog.stdlib.LoggerFactory(),
    cache_logger_on_first_use=True,
)

logger = structlog.get_logger()

# Usage
logger.info("chat_request", 
    user_id=user_id, 
    session_id=session_id, 
    message_length=len(message),
    mode=chat_mode
)

logger.error("payment_webhook_failed",
    order_id=order_id,
    error=str(e),
    signature_valid=False
)
```

### Metrics Collection

```python
from prometheus_client import Counter, Histogram, Gauge

# Request metrics
http_requests_total = Counter(
    'http_requests_total',
    'Total HTTP requests',
    ['method', 'endpoint', 'status']
)

http_request_duration = Histogram(
    'http_request_duration_seconds',
    'HTTP request duration',
    ['method', 'endpoint']
)

# Business metrics
chat_messages_total = Counter(
    'chat_messages_total',
    'Total chat messages',
    ['mode', 'language']
)

consultations_booked = Counter(
    'consultations_booked_total',
    'Total consultations booked',
    ['status']
)

active_consultations = Gauge(
    'active_consultations',
    'Currently active consultations'
)

# AI metrics
ai_requests_total = Counter(
    'ai_requests_total',
    'Total AI API requests',
    ['provider', 'status']
)

ai_request_duration = Histogram(
    'ai_request_duration_seconds',
    'AI API request duration',
    ['provider']
)
```

### Health Checks

```python
@app.get("/health")
async def health_check():
    """Basic health check endpoint."""
    return {"status": "healthy", "service": "MediRep AI"}

@app.get("/health/detailed")
async def detailed_health_check():
    """Detailed health check with dependency status."""
    health = {
        "status": "healthy",
        "timestamp": datetime.utcnow().isoformat(),
        "dependencies": {}
    }
    
    # Check Supabase
    try:
        supabase = SupabaseService.get_client()
        await supabase.table("user_profiles").select("id").limit(1).execute()
        health["dependencies"]["supabase"] = "healthy"
    except Exception as e:
        health["dependencies"]["supabase"] = f"unhealthy: {str(e)}"
        health["status"] = "degraded"
    
    # Check Turso
    try:
        conn = turso_service.get_connection()
        conn.execute("SELECT 1").fetchone()
        health["dependencies"]["turso"] = "healthy"
    except Exception as e:
        health["dependencies"]["turso"] = f"unhealthy: {str(e)}"
        health["status"] = "degraded"
    
    # Check Qdrant
    try:
        client = qdrant_service.get_client()
        client.get_collections()
        health["dependencies"]["qdrant"] = "healthy"
    except Exception as e:
        health["dependencies"]["qdrant"] = f"unhealthy: {str(e)}"
        health["status"] = "degraded"
    
    # Check Gemini API
    if GEMINI_API_KEY:
        health["dependencies"]["gemini"] = "configured"
    else:
        health["dependencies"]["gemini"] = "not configured"
        health["status"] = "degraded"
    
    return health
```


## Deployment Architecture

### Infrastructure Overview

```
┌─────────────────────────────────────────────────────────────┐
│                      Production Stack                        │
│                                                              │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  Frontend (Vercel)                                   │   │
│  │  - Next.js App Router                                │   │
│  │  - Edge Functions for API routes                     │   │
│  │  - CDN for static assets                             │   │
│  │  - Automatic HTTPS                                   │   │
│  └──────────────────────────────────────────────────────┘   │
│                          │                                   │
│                          ▼                                   │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  Backend (Railway / Render)                          │   │
│  │  - FastAPI application                               │   │
│  │  - Uvicorn ASGI server                               │   │
│  │  - Auto-scaling based on CPU/memory                  │   │
│  │  - Health check monitoring                           │   │
│  └──────────────────────────────────────────────────────┘   │
│                          │                                   │
│          ┌───────────────┼───────────────┐                  │
│          ▼               ▼               ▼                  │
│  ┌──────────────┐ ┌──────────────┐ ┌──────────────┐        │
│  │  Supabase    │ │    Turso     │ │   Qdrant     │        │
│  │  (Postgres)  │ │   (LibSQL)   │ │   (Vector)   │        │
│  └──────────────┘ └──────────────┘ └──────────────┘        │
└─────────────────────────────────────────────────────────────┘
```

### Environment Configuration

```bash
# Production Environment Variables

# AI Services
GEMINI_API_KEY=<google-ai-studio-key>
GEMINI_MODEL=gemini-2.5-flash
GROQ_API_KEY=<groq-api-key>

# Databases
SUPABASE_URL=https://<project>.supabase.co
SUPABASE_KEY=<anon-key>
SUPABASE_SERVICE_ROLE_KEY=<service-role-key>
TURSO_DATABASE_URL=libsql://<db>.turso.io
TURSO_AUTH_TOKEN=<turso-token>
QDRANT_URL=https://<cluster>.qdrant.io
QDRANT_API_KEY=<qdrant-key>

# Payment & Communication
RAZORPAY_KEY_ID=<razorpay-key>
RAZORPAY_KEY_SECRET=<razorpay-secret>
RAZORPAY_WEBHOOK_SECRET=<webhook-secret>
AGORA_APP_ID=<agora-app-id>
AGORA_APP_CERTIFICATE=<agora-cert>
RESEND_API_KEY=<resend-key>

# Server Configuration
PORT=8000
ENV=production
ALLOWED_ORIGINS=https://medirep-ai.vercel.app
FRONTEND_URL=https://medirep-ai.vercel.app

# Admin
ADMIN_EMAILS=admin@medirep.ai
EMAIL_FROM=MediRep AI <notifications@medirep.ai>
```

### CI/CD Pipeline

```yaml
# .github/workflows/deploy.yml
name: Deploy to Production

on:
  push:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-python@v4
        with:
          python-version: '3.11'
      - name: Install dependencies
        run: |
          cd backend
          pip install -r requirements.txt
      - name: Run tests
        run: |
          cd backend
          pytest tests/ --cov=. --cov-report=xml
      - name: Upload coverage
        uses: codecov/codecov-action@v3

  deploy-backend:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Deploy to Railway
        run: |
          railway up --service backend
        env:
          RAILWAY_TOKEN: ${{ secrets.RAILWAY_TOKEN }}

  deploy-frontend:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Deploy to Vercel
        run: |
          vercel --prod --token=${{ secrets.VERCEL_TOKEN }}
```


## Testing Strategy

### Unit Tests

```python
# backend/tests/test_drug_service.py
import pytest
from services import drug_service

@pytest.mark.asyncio
async def test_search_drugs_returns_results():
    results = await drug_service.search_drugs("paracetamol", limit=5)
    assert len(results) > 0
    assert any("paracetamol" in r.name.lower() for r in results)

@pytest.mark.asyncio
async def test_get_drug_info_enrichment():
    info = await drug_service.get_drug_info("Dolo 650", enrich=True)
    assert info is not None
    assert info.name == "Dolo 650"
    assert len(info.indications) > 0  # Should be enriched by LLM

@pytest.mark.asyncio
async def test_find_cheaper_substitutes():
    substitutes = await drug_service.find_cheaper_substitutes("Crocin 650")
    assert len(substitutes) > 0
    # All substitutes should be cheaper
    original = await drug_service.get_drug_info("Crocin 650")
    for sub in substitutes:
        if sub.price and original.price:
            assert sub.price < original.price
```

```typescript
// frontend/tests/components/ChatInterface.test.tsx
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import ChatInterface from '@/components/chat/ChatInterface';

describe('ChatInterface', () => {
  it('renders message input', () => {
    render(<ChatInterface sessionId="test-session" />);
    expect(screen.getByPlaceholderText(/ask a medical question/i)).toBeInTheDocument();
  });

  it('sends message on submit', async () => {
    const mockSendMessage = jest.fn();
    render(<ChatInterface sessionId="test-session" onSendMessage={mockSendMessage} />);
    
    const input = screen.getByPlaceholderText(/ask a medical question/i);
    fireEvent.change(input, { target: { value: 'What is aspirin used for?' } });
    fireEvent.submit(input.closest('form'));
    
    await waitFor(() => {
      expect(mockSendMessage).toHaveBeenCalledWith('What is aspirin used for?');
    });
  });

  it('displays citations when provided', () => {
    const message = {
      role: 'assistant',
      content: 'Aspirin is used for pain relief.',
      citations: [{ title: 'FDA Label', url: 'https://fda.gov/...', source: 'FDA' }]
    };
    
    render(<ChatInterface sessionId="test-session" messages={[message]} />);
    expect(screen.getByText('FDA Label')).toBeInTheDocument();
  });
});
```

### Integration Tests

```python
# backend/tests/integration/test_chat_flow.py
import pytest
from fastapi.testclient import TestClient
from main import app

client = TestClient(app)

@pytest.fixture
def auth_headers():
    # Create test user and get token
    response = client.post("/api/auth/signup", json={
        "email": "test@example.com",
        "password": "Test123!@#"
    })
    token = response.json()["access_token"]
    return {"Authorization": f"Bearer {token}"}

def test_chat_flow_with_drug_query(auth_headers):
    # Send chat message
    response = client.post("/api/chat", 
        headers=auth_headers,
        json={
            "message": "What is metformin used for?",
            "chat_mode": "normal"
        }
    )
    
    assert response.status_code == 200
    data = response.json()
    assert "response" in data
    assert "session_id" in data
    assert len(data["citations"]) > 0
    
    # Verify session was created
    session_id = data["session_id"]
    response = client.get(f"/api/sessions/{session_id}", headers=auth_headers)
    assert response.status_code == 200
    session = response.json()
    assert session["message_count"] >= 2  # User + assistant

def test_insurance_mode_rejects_clinical_query(auth_headers):
    response = client.post("/api/chat",
        headers=auth_headers,
        json={
            "message": "What are the side effects of aspirin?",
            "chat_mode": "insurance"
        }
    )
    
    assert response.status_code == 200
    data = response.json()
    # Should reject clinical query in insurance mode
    assert "insurance" in data["response"].lower() or "coverage" in data["response"].lower()
```

### End-to-End Tests

```typescript
// frontend/tests/e2e/consultation-booking.spec.ts
import { test, expect } from '@playwright/test';

test.describe('Consultation Booking Flow', () => {
  test('complete booking flow', async ({ page }) => {
    // Login
    await page.goto('/auth/login');
    await page.fill('input[name="email"]', 'test@example.com');
    await page.fill('input[name="password"]', 'Test123!@#');
    await page.click('button[type="submit"]');
    
    // Navigate to marketplace
    await page.goto('/marketplace');
    await expect(page.locator('h1')).toContainText('Find a Pharmacist');
    
    // Select first pharmacist
    await page.click('[data-testid="pharmacist-card"]:first-child');
    
    // Select time slot
    await page.click('[data-testid="time-slot"]:first-child');
    
    // Enter concern
    await page.fill('textarea[name="concern"]', 'Need advice on medication');
    
    // Proceed to payment
    await page.click('button:has-text("Book Consultation")');
    
    // Mock Razorpay payment (in test environment)
    await page.waitForSelector('[data-testid="razorpay-modal"]');
    await page.click('[data-testid="mock-payment-success"]');
    
    // Verify booking confirmation
    await expect(page.locator('[data-testid="booking-confirmed"]')).toBeVisible();
    
    // Verify consultation appears in list
    await page.goto('/consultations');
    await expect(page.locator('[data-testid="consultation-item"]')).toHaveCount(1);
  });
});
```


## Accessibility and Internationalization

### Accessibility (WCAG 2.1 Level AA)

```typescript
// Semantic HTML
<nav aria-label="Main navigation">
  <ul role="list">
    <li><a href="/dashboard">Dashboard</a></li>
    <li><a href="/marketplace">Marketplace</a></li>
  </ul>
</nav>

// Keyboard navigation
<button
  onClick={handleSubmit}
  onKeyDown={(e) => e.key === 'Enter' && handleSubmit()}
  aria-label="Send message"
>
  <SendIcon aria-hidden="true" />
</button>

// Screen reader announcements
<div role="status" aria-live="polite" aria-atomic="true">
  {isLoading ? 'Loading response...' : ''}
</div>

// Focus management
useEffect(() => {
  if (modalOpen) {
    modalRef.current?.focus();
  }
}, [modalOpen]);

// Color contrast
// All text meets WCAG AA contrast ratio (4.5:1 for normal text, 3:1 for large text)
const colors = {
  text: '#1a1a1a',      // Contrast ratio: 16.5:1 on white
  textMuted: '#666666', // Contrast ratio: 5.7:1 on white
  primary: '#c85a3a',   // Contrast ratio: 4.6:1 on white
};

// Alt text for images
<img 
  src="/pill-image.jpg" 
  alt="White round tablet with 'DOLO 650' imprint"
/>
```

### Internationalization (i18n)

```typescript
// next-i18next configuration
// i18n.config.ts
export const i18nConfig = {
  locales: ['en', 'hi', 'ta', 'te', 'bn', 'mr', 'gu', 'pa', 'kn', 'ml', 'or'],
  defaultLocale: 'en',
  localeDetection: true,
};

// Translation files
// locales/en/common.json
{
  "nav": {
    "dashboard": "Dashboard",
    "marketplace": "Marketplace",
    "consultations": "Consultations"
  },
  "chat": {
    "placeholder": "Ask a medical question...",
    "send": "Send",
    "loading": "Thinking..."
  },
  "errors": {
    "network": "Network error. Please check your connection.",
    "unauthorized": "Please log in to continue."
  }
}

// locales/hi/common.json
{
  "nav": {
    "dashboard": "डैशबोर्ड",
    "marketplace": "बाज़ार",
    "consultations": "परामर्श"
  },
  "chat": {
    "placeholder": "एक चिकित्सा प्रश्न पूछें...",
    "send": "भेजें",
    "loading": "सोच रहा हूँ..."
  }
}

// Usage in components
import { useTranslation } from 'next-i18next';

export default function ChatInterface() {
  const { t } = useTranslation('common');
  
  return (
    <input 
      placeholder={t('chat.placeholder')}
      aria-label={t('chat.placeholder')}
    />
  );
}
```

### Right-to-Left (RTL) Support

```typescript
// Automatic RTL detection
import { useRouter } from 'next/router';

export default function Layout({ children }) {
  const { locale } = useRouter();
  const isRTL = ['ar', 'ur'].includes(locale);
  
  return (
    <div dir={isRTL ? 'rtl' : 'ltr'}>
      {children}
    </div>
  );
}

// RTL-aware styles
<div className={cn(
  'flex gap-4',
  isRTL ? 'flex-row-reverse' : 'flex-row'
)}>
  <Icon />
  <Text />
</div>
```


## Data Privacy and Compliance

### GDPR/DPDPA Compliance

```python
# Data minimization
class UserProfile(BaseModel):
    """Only collect essential data."""
    id: UUID
    email: EmailStr
    full_name: str
    phone: Optional[str]  # Optional, not required
    # NO: date_of_birth, address, medical_history (not stored)

# Right to access
@app.get("/api/user/data-export")
async def export_user_data(user_id: UUID = Depends(get_current_user)):
    """Export all user data in machine-readable format."""
    data = {
        "profile": await get_user_profile(user_id),
        "sessions": await get_user_sessions(user_id),
        "consultations": await get_user_consultations(user_id),
        "payments": await get_user_payments(user_id)
    }
    return JSONResponse(content=data)

# Right to deletion
@app.delete("/api/user/account")
async def delete_user_account(user_id: UUID = Depends(get_current_user)):
    """Permanently delete user account and all associated data."""
    # Delete in order (foreign key constraints)
    await delete_chat_messages(user_id)
    await delete_chat_sessions(user_id)
    await delete_consultations(user_id)
    await delete_user_profile(user_id)
    await supabase.auth.admin.delete_user(user_id)
    
    return {"message": "Account deleted successfully"}

# Data retention policy
async def cleanup_old_data():
    """Delete data older than retention period."""
    # Delete archived sessions older than 2 years
    await supabase.table("chat_sessions").delete().match({
        "is_archived": True,
        "updated_at": f"< {two_years_ago}"
    }).execute()
    
    # Delete completed consultations older than 1 year
    await supabase.table("consultations").delete().match({
        "status": "completed",
        "scheduled_at": f"< {one_year_ago}"
    }).execute()
```

### Medical Disclaimer

```typescript
// Displayed on all medical information pages
export const MedicalDisclaimer = () => (
  <div className="bg-yellow-50 border border-yellow-200 rounded-lg p-4 mb-6">
    <div className="flex items-start gap-3">
      <AlertTriangle className="h-5 w-5 text-yellow-600 flex-shrink-0 mt-0.5" />
      <div className="text-sm text-yellow-800">
        <p className="font-semibold mb-1">Medical Disclaimer</p>
        <p>
          This information is for educational purposes only and is not a substitute 
          for professional medical advice, diagnosis, or treatment. Always seek the 
          advice of your physician or other qualified health provider with any 
          questions you may have regarding a medical condition.
        </p>
        <p className="mt-2">
          MediRep AI uses synthetic and publicly available data. Drug information 
          may not be complete or up-to-date. Verify all information with official 
          sources before making medical decisions.
        </p>
      </div>
    </div>
  </div>
);

// Terms of Service acceptance
const [acceptedTerms, setAcceptedTerms] = useState(false);

<Checkbox
  checked={acceptedTerms}
  onCheckedChange={setAcceptedTerms}
  required
/>
<label>
  I agree to the{' '}
  <Link href="/terms" className="text-primary underline">
    Terms of Service
  </Link>{' '}
  and{' '}
  <Link href="/privacy" className="text-primary underline">
    Privacy Policy
  </Link>
</label>
```

### Audit Logging

```python
# Log all critical actions
class AuditLog(BaseModel):
    id: UUID
    user_id: UUID
    action: str  # login, chat_message, booking_created, payment_completed
    resource_type: str  # user, session, consultation, payment
    resource_id: Optional[str]
    metadata: dict
    ip_address: str
    user_agent: str
    timestamp: datetime

async def log_audit_event(
    user_id: UUID,
    action: str,
    resource_type: str,
    resource_id: Optional[str] = None,
    metadata: dict = None,
    request: Request = None
):
    """Log audit event to database."""
    await supabase.table("audit_logs").insert({
        "user_id": str(user_id),
        "action": action,
        "resource_type": resource_type,
        "resource_id": resource_id,
        "metadata": metadata or {},
        "ip_address": request.client.host if request else None,
        "user_agent": request.headers.get("user-agent") if request else None,
        "timestamp": datetime.utcnow().isoformat()
    }).execute()

# Usage
await log_audit_event(
    user_id=user.id,
    action="consultation_booked",
    resource_type="consultation",
    resource_id=consultation.id,
    metadata={"pharmacist_id": pharmacist.id, "amount": amount},
    request=request
)
```


## Future Enhancements and Scalability

### Phase 2: Advanced Features

```
1. Telemedicine Integration
   - Full EHR integration with FHIR standard
   - E-prescription generation and transmission
   - Integration with pharmacy networks
   - Lab test ordering and result tracking

2. AI Diagnostics
   - Symptom checker with differential diagnosis
   - Risk assessment for chronic conditions
   - Predictive analytics for disease progression
   - Integration with wearables (Apple Health, Google Fit)

3. Medication Management
   - Medication reminders with push notifications
   - Adherence tracking and reporting
   - Refill reminders and auto-ordering
   - Drug interaction alerts for new prescriptions

4. Insurance Claims
   - Direct claim filing with insurance providers
   - Real-time claim status tracking
   - Pre-authorization assistance
   - Reimbursement calculation and tracking

5. Clinical Trials Matching
   - Match patients with relevant clinical trials
   - Eligibility screening automation
   - Trial information and consent management
   - Progress tracking and reporting
```

### Scalability Considerations

```
┌─────────────────────────────────────────────────────────────┐
│                  Scaling Strategy                            │
│                                                              │
│  Current (MVP): 1,000 concurrent users                       │
│  ├─→ Single backend instance                                │
│  ├─→ Supabase free tier                                     │
│  ├─→ Turso starter plan                                     │
│  └─→ Qdrant cloud free tier                                 │
│                                                              │
│  Phase 1 (10,000 users):                                     │
│  ├─→ Horizontal scaling: 3-5 backend instances              │
│  ├─→ Load balancer (Railway/Render built-in)                │
│  ├─→ Supabase Pro plan with read replicas                   │
│  ├─→ Turso Pro with edge replication                        │
│  ├─→ Qdrant cloud with dedicated cluster                    │
│  └─→ Redis for session caching                              │
│                                                              │
│  Phase 2 (100,000 users):                                    │
│  ├─→ Kubernetes cluster for backend                         │
│  ├─→ Auto-scaling based on metrics                          │
│  ├─→ Database sharding by region                            │
│  ├─→ CDN for static assets (Cloudflare)                     │
│  ├─→ Message queue for async tasks (RabbitMQ/Redis)         │
│  ├─→ Separate microservices:                                │
│  │   ├─→ Chat service                                       │
│  │   ├─→ Marketplace service                                │
│  │   ├─→ Payment service                                    │
│  │   └─→ Notification service                               │
│  └─→ Multi-region deployment                                │
└─────────────────────────────────────────────────────────────┘
```

### Database Scaling

```sql
-- Partitioning for large tables
CREATE TABLE chat_messages_2024_01 PARTITION OF chat_messages
    FOR VALUES FROM ('2024-01-01') TO ('2024-02-01');

CREATE TABLE chat_messages_2024_02 PARTITION OF chat_messages
    FOR VALUES FROM ('2024-02-01') TO ('2024-03-01');

-- Archival strategy
CREATE TABLE chat_messages_archive (
    LIKE chat_messages INCLUDING ALL
);

-- Move old messages to archive (monthly job)
INSERT INTO chat_messages_archive
SELECT * FROM chat_messages
WHERE created_at < NOW() - INTERVAL '1 year';

DELETE FROM chat_messages
WHERE created_at < NOW() - INTERVAL '1 year';

-- Read replicas for analytics
-- Primary: Write operations
-- Replica 1: User-facing reads
-- Replica 2: Analytics and reporting
```

### Microservices Architecture (Future)

```
┌─────────────────────────────────────────────────────────────┐
│                  Microservices Architecture                  │
│                                                              │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  API Gateway (Kong/Nginx)                            │   │
│  │  - Authentication                                    │   │
│  │  - Rate limiting                                     │   │
│  │  - Request routing                                   │   │
│  └──────────────────────────────────────────────────────┘   │
│                          │                                   │
│          ┌───────────────┼───────────────┐                  │
│          ▼               ▼               ▼                  │
│  ┌──────────────┐ ┌──────────────┐ ┌──────────────┐        │
│  │ Chat Service │ │ Marketplace  │ │   Payment    │        │
│  │              │ │   Service    │ │   Service    │        │
│  │ - RAG        │ │ - Pharmacist │ │ - Razorpay   │        │
│  │ - LLM calls  │ │   profiles   │ │ - Payouts    │        │
│  │ - Sessions   │ │ - Bookings   │ │ - Webhooks   │        │
│  └──────────────┘ └──────────────┘ └──────────────┘        │
│          │               │               │                  │
│          └───────────────┼───────────────┘                  │
│                          ▼                                   │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  Message Queue (RabbitMQ/Kafka)                      │   │
│  │  - Async task processing                             │   │
│  │  - Event-driven communication                        │   │
│  └──────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
```

## Conclusion

This design document outlines a comprehensive, scalable, and secure architecture for MediRep AI. The system is built with:

- **Evidence-first approach:** All medical information is traceable to sources
- **Safety by design:** Strict mode boundaries and human escalation
- **Privacy and security:** RLS, encryption, and compliance with regulations
- **Performance:** Caching, indexing, and optimization at every layer
- **Scalability:** Designed to grow from MVP to enterprise scale

The architecture balances immediate needs (MVP delivery) with future growth (microservices, multi-region), ensuring the platform can evolve as user needs and scale increase.

---

**Document Status:** Draft v1.0  
**Next Review:** March 2026  
**Owner:** Engineering Team

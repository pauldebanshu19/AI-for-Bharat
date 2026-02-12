# MediRep AI - Requirements Document

## Project Overview

**Project Name:** MediRep AI - Healthcare Intelligence Platform  
**Track:** AI for Healthcare & Life Sciences  
**Version:** 1.0  
**Last Updated:** February 12, 2026

## Executive Summary

MediRep AI is an AI-powered healthcare intelligence platform designed to improve efficiency, understanding, and support within healthcare ecosystems. The solution provides clinical information summarization, workflow automation for healthcare professionals, patient education, care navigation, and decision-support systems using synthetic and publicly available data.

## Problem Statement

Healthcare professionals face critical challenges:
- **Information Overload:** Medical representatives and healthcare workers spend excessive time searching for drug information, interactions, and clinical guidelines
- **Patient Safety Risks:** Drug-drug interactions and contraindications are often missed due to fragmented information systems
- **Access Barriers:** Patients struggle to understand medical information and access qualified pharmacist consultations
- **Administrative Burden:** Insurance coverage verification and reimbursement information is difficult to access and understand
- **Language Barriers:** Medical information is often unavailable in regional Indian languages

## Solution Overview

MediRep AI addresses these challenges through:
1. **AI-Powered Medical Assistant:** Context-aware chat system with strict evidence-based responses
2. **Drug Intelligence System:** Comprehensive database of 250,000+ Indian medicines with semantic search
3. **Interaction Checker:** Real-time drug-drug interaction analysis with severity assessment
4. **Pharmacist Marketplace:** Verified pharmacist consultation platform with secure payments
5. **Multi-Modal Interface:** Voice, text, and image-based interactions
6. **Multi-Language Support:** 10+ Indian languages including Hindi, Tamil, Telugu, Bengali

## Functional Requirements

### 1. Core AI Chat System

#### FR-1.1: Context-Aware Medical Q&A
- **Priority:** Critical
- **Description:** Provide medical information with patient context awareness
- **Acceptance Criteria:**
  - System accepts patient context (age, sex, weight, conditions, current medications)
  - Responses are personalized based on patient profile
  - System maintains conversation history across sessions
  - Maximum response time: 5 seconds for standard queries
  - Minimum accuracy: 95% for drug information queries

#### FR-1.2: Multi-Mode Operation
- **Priority:** Critical
- **Description:** Support specialized query modes for different use cases
- **Modes:**
  - **Normal Mode:** General medical information
  - **Insurance Mode:** Coverage and reimbursement information (PM-JAY, CGHS)
  - **MOA Mode:** Mechanism of Action explanations
  - **Rep Mode:** Pharmaceutical representative simulation
  - **Company-Specific Rep Mode:** Brand-specific product information
- **Acceptance Criteria:**
  - Mode switching within 1 second
  - Strict boundary enforcement (insurance mode rejects clinical queries)
  - Mode-specific context retrieval

#### FR-1.3: Citation and Source Tracking
- **Priority:** High
- **Description:** Provide verifiable sources for all medical information
- **Acceptance Criteria:**
  - All responses include source citations (FDA, NIH, Database)
  - URLs from trusted domains only (fda.gov, nih.gov, ncbi.nlm.nih.gov)
  - Maximum 3 citations per response
  - Source verification within 2 seconds

### 2. Drug Intelligence System

#### FR-2.1: Drug Search and Discovery
- **Priority:** Critical
- **Description:** Multi-source drug search with fuzzy matching
- **Data Sources:**
  - Turso Database: 250,000+ Indian medicines
  - Qdrant Vector Search: Semantic similarity search
  - OpenFDA: International drug database fallback
- **Acceptance Criteria:**
  - Search results within 2 seconds
  - Minimum 10 results per query
  - Support for brand names, generic names, and therapeutic classes
  - Fuzzy matching with 80% accuracy threshold

#### FR-2.2: Comprehensive Drug Information
- **Priority:** Critical
- **Description:** Detailed drug profiles with clinical and commercial data
- **Information Fields:**
  - Name, generic name, manufacturer
  - Indications, dosage, warnings, contraindications
  - Side effects, drug interactions
  - Price (MRP), pack size, substitutes
  - NLEM status, DPCO control, schedule classification
  - Chemical formula, SMILES notation
- **Acceptance Criteria:**
  - 100% coverage for Indian market drugs
  - LLM enrichment for missing clinical data
  - Price accuracy within ±5%
  - Update frequency: Weekly for prices, monthly for clinical data

#### FR-2.3: Substitute Finder
- **Priority:** High
- **Description:** Find cheaper alternatives with same therapeutic effect
- **Acceptance Criteria:**
  - Identify substitutes with same generic composition
  - Sort by price (ascending)
  - Include Jan Aushadhi alternatives when available
  - Display price savings percentage
  - Maximum 10 substitutes per query

### 3. Drug Interaction Analysis

#### FR-3.1: Multi-Drug Interaction Check
- **Priority:** Critical
- **Description:** Analyze interactions between multiple medications
- **Acceptance Criteria:**
  - Support 2-10 drugs per analysis
  - Severity classification: Minor, Moderate, Major
  - Mechanism explanation for each interaction
  - Clinical recommendations for management
  - Response time: <3 seconds for 5 drugs

#### FR-3.2: Enhanced Interaction Mathematics
- **Priority:** Medium
- **Description:** AUC-based pharmacokinetic calculations
- **Features:**
  - AUC ratio calculation (R = 1 + [I]/Ki)
  - Enzyme inhibition modeling
  - Metabolic pathway visualization
  - Chemical structure display
- **Acceptance Criteria:**
  - Mathematical accuracy: ±2%
  - Include formula and calculation steps
  - Generate reaction diagrams

### 4. Vision and OCR Capabilities

#### FR-4.1: Pill Identification
- **Priority:** High
- **Description:** Identify medications from images
- **Acceptance Criteria:**
  - Support JPEG, PNG, WebP formats
  - Maximum image size: 10MB
  - OCR confidence score >70%
  - Match to database with >80% confidence
  - Response time: <5 seconds

#### FR-4.2: Prescription Scanning
- **Priority:** Medium
- **Description:** Extract medication information from prescriptions
- **Acceptance Criteria:**
  - Support handwritten and printed prescriptions
  - Extract drug names, dosages, frequency
  - Confidence scoring for each field
  - Validation against drug database

### 5. Voice Interface

#### FR-5.1: Speech-to-Text
- **Priority:** High
- **Description:** Transcribe medical queries from audio
- **Acceptance Criteria:**
  - Support English and Hindi
  - Accuracy: >90% for clear audio
  - Maximum audio length: 60 seconds
  - Response time: <3 seconds

#### FR-5.2: Text-to-Speech
- **Priority:** Medium
- **Description:** Convert responses to natural speech
- **Acceptance Criteria:**
  - Support multiple voice profiles
  - Natural prosody and pronunciation
  - Medical term accuracy: 100%
  - Output formats: WAV, MP3

### 6. Pharmacist Marketplace

#### FR-6.1: Pharmacist Registration and Verification
- **Priority:** Critical
- **Description:** Onboard and verify licensed pharmacists
- **Required Information:**
  - Full name, phone, license number
  - License image upload
  - State of registration
  - Specializations, experience, education
  - Consultation rate and duration
  - UPI ID for payments
- **Acceptance Criteria:**
  - AI-powered license verification
  - Manual admin review for approval
  - Verification within 48 hours
  - Profile completeness check

#### FR-6.2: Pharmacist Discovery
- **Priority:** High
- **Description:** Browse and search verified pharmacists
- **Search Filters:**
  - Specialization
  - Language
  - Experience
  - Rating
  - Availability
  - Price range
- **Acceptance Criteria:**
  - Search results within 1 second
  - Real-time availability status
  - Profile preview with key information

#### FR-6.3: Consultation Booking
- **Priority:** Critical
- **Description:** Schedule and pay for pharmacist consultations
- **Booking Flow:**
  1. Select pharmacist and time slot
  2. Enter patient concern (optional)
  3. Secure payment via Razorpay
  4. Receive confirmation and access token
- **Acceptance Criteria:**
  - Payment processing within 10 seconds
  - Automatic booking confirmation
  - Calendar integration
  - Reminder notifications (24h, 1h before)

#### FR-6.4: Video/Voice Consultation
- **Priority:** Critical
- **Description:** Real-time consultation via Agora
- **Features:**
  - Voice-only or video call
  - Screen sharing
  - Chat during call
  - Call recording (with consent)
- **Acceptance Criteria:**
  - Call quality: HD audio, 720p video minimum
  - Latency: <200ms
  - Connection success rate: >98%
  - Automatic reconnection on network issues

#### FR-6.5: Payment and Payout System
- **Priority:** Critical
- **Description:** Secure payment processing and pharmacist payouts
- **Payment Flow:**
  - Patient pays via Razorpay (UPI, cards, wallets)
  - Platform fee: 20% of consultation rate
  - Pharmacist payout: Weekly or on-demand (minimum ₹500)
- **Acceptance Criteria:**
  - PCI DSS compliant
  - Webhook verification for payment status
  - Automatic payout calculation
  - Transaction history and invoices

#### FR-6.6: Rating and Review System
- **Priority:** High
- **Description:** Post-consultation feedback mechanism
- **Acceptance Criteria:**
  - 1-5 star rating (required)
  - Written review (optional, max 1000 chars)
  - Review moderation for inappropriate content
  - Average rating calculation and display

### 7. Insurance and Reimbursement

#### FR-7.1: PM-JAY Package Rate Lookup
- **Priority:** High
- **Description:** Search HBP 2.2 package rates for procedures
- **Acceptance Criteria:**
  - 5000+ procedures in database
  - Multi-word search with fuzzy matching
  - Display package code, rate, category
  - Include special conditions and exclusions
  - Update frequency: Quarterly (aligned with NHA updates)

#### FR-7.2: Multi-Scheme Coverage Comparison
- **Priority:** Medium
- **Description:** Compare coverage across PM-JAY, CGHS, private insurance
- **Acceptance Criteria:**
  - Side-by-side comparison view
  - Highlight coverage differences
  - Eligibility criteria display
  - Helpline and website links

### 8. Session Management

#### FR-8.1: Chat Session Persistence
- **Priority:** High
- **Description:** Save and resume conversations
- **Acceptance Criteria:**
  - Automatic session creation on first message
  - Session title auto-generation or manual edit
  - Archive/unarchive functionality
  - Session list with last message preview
  - Maximum 100 sessions per user

#### FR-8.2: Patient Context Persistence
- **Priority:** Medium
- **Description:** Save patient profile across sessions
- **Acceptance Criteria:**
  - One-time profile setup
  - Auto-apply to new sessions
  - Edit profile anytime
  - Privacy controls (who can see)

### 9. Multi-Language Support

#### FR-9.1: Language Detection and Translation
- **Priority:** High
- **Description:** Automatic language detection and response translation
- **Supported Languages:**
  - English, Hindi, Tamil, Telugu, Bengali
  - Marathi, Gujarati, Punjabi, Kannada, Malayalam, Odia
- **Acceptance Criteria:**
  - Auto-detect language with >95% accuracy
  - Translate responses maintaining medical accuracy
  - Preserve drug names and technical terms
  - Language preference persistence

### 10. Admin and Moderation

#### FR-10.1: User Management
- **Priority:** High
- **Description:** Admin dashboard for user oversight
- **Features:**
  - User list with filters (role, status, registration date)
  - User detail view
  - Account suspension/activation
  - Activity logs

#### FR-10.2: Pharmacist Verification
- **Priority:** Critical
- **Description:** Manual review and approval of pharmacist applications
- **Features:**
  - Pending applications queue
  - License image viewer
  - AI extraction results review
  - Approve/reject with reason
  - Email notifications

#### FR-10.3: Payout Management
- **Priority:** High
- **Description:** Process pharmacist payouts
- **Features:**
  - Pending payout list
  - Payout calculation verification
  - Bulk payout processing
  - Payout history and reconciliation

## Non-Functional Requirements

### NFR-1: Performance
- **Response Time:**
  - Chat responses: <5 seconds (95th percentile)
  - Search queries: <2 seconds
  - Image processing: <5 seconds
  - Page load: <3 seconds
- **Throughput:**
  - Support 1000 concurrent users
  - 10,000 API requests per minute
- **Scalability:**
  - Horizontal scaling for API servers
  - Database read replicas for high traffic

### NFR-2: Reliability
- **Availability:** 99.5% uptime (excluding planned maintenance)
- **Data Durability:** 99.999% (five nines)
- **Backup:** Daily automated backups with 30-day retention
- **Disaster Recovery:** RTO <4 hours, RPO <1 hour

### NFR-3: Security
- **Authentication:** JWT-based with refresh tokens
- **Authorization:** Row-level security (RLS) in Supabase
- **Data Encryption:**
  - In transit: TLS 1.3
  - At rest: AES-256
- **PII Protection:**
  - No storage of sensitive medical records
  - Anonymized analytics data
  - GDPR/DPDPA compliance
- **API Security:**
  - Rate limiting: 100 requests/minute per user
  - CORS policy enforcement
  - Input validation and sanitization

### NFR-4: Usability
- **Accessibility:** WCAG 2.1 Level AA compliance
- **Mobile Responsiveness:** Support for 320px+ screen widths
- **Browser Support:** Chrome, Firefox, Safari, Edge (last 2 versions)
- **Offline Capability:** Service worker for basic functionality

### NFR-5: Maintainability
- **Code Quality:**
  - TypeScript for frontend (strict mode)
  - Python type hints for backend
  - 80%+ test coverage
- **Documentation:**
  - API documentation (OpenAPI/Swagger)
  - Component storybook
  - Deployment runbooks
- **Monitoring:**
  - Application performance monitoring (APM)
  - Error tracking (Sentry)
  - Usage analytics

### NFR-6: Compliance
- **Medical Disclaimer:** Clear disclaimers on all medical information
- **Data Residency:** India-based data storage for Indian users
- **Audit Logging:** All critical actions logged with timestamps
- **Terms of Service:** Explicit user agreement required

## Data Requirements

### DR-1: Drug Database
- **Source:** Curated Indian medicines dataset
- **Size:** 250,000+ entries
- **Update Frequency:** Weekly for prices, monthly for clinical data
- **Storage:** Turso (LibSQL) for structured data, Qdrant for vectors

### DR-2: Medical Knowledge Base
- **Source:** MedQuAD (NIH), PubMed, FDA labels
- **Size:** 47,000+ Q&A pairs
- **Update Frequency:** Quarterly
- **Storage:** Qdrant vector database

### DR-3: Insurance Data
- **Source:** PM-JAY HBP 2.2 Manual, CGHS guidelines
- **Size:** 5,000+ procedures
- **Update Frequency:** Quarterly (aligned with official updates)
- **Storage:** Supabase PostgreSQL

### DR-4: User Data
- **Types:** Profiles, sessions, bookings, payments
- **Retention:** Active users indefinitely, inactive >2 years deleted
- **Backup:** Daily with 30-day retention
- **Storage:** Supabase PostgreSQL with RLS

## Integration Requirements

### INT-1: AI/ML Services
- **Google Gemini API:** Primary LLM for chat, vision, transcription
- **Groq API:** Fallback LLM for English queries
- **Sentence Transformers:** Local embedding model (all-MiniLM-L6-v2)

### INT-2: Payment Gateway
- **Razorpay:** Payment processing, webhooks, payouts
- **Supported Methods:** UPI, cards, wallets, net banking

### INT-3: Communication
- **Agora:** Real-time voice/video calls
- **Resend:** Transactional emails
- **Socket.IO:** Real-time chat updates

### INT-4: External APIs
- **OpenFDA:** Drug label and enforcement data
- **RxNorm/RxClass:** Drug classification
- **PubChem:** Chemical structures
- **Tavily:** Web search for current information

## User Roles and Permissions

### Role: Patient
- **Permissions:**
  - Access chat assistant
  - Search drugs and check interactions
  - View own profile and sessions
  - Book pharmacist consultations
  - Submit ratings and reviews

### Role: Pharmacist
- **Permissions:**
  - All patient permissions
  - Manage availability schedule
  - View booked consultations
  - Join consultation calls
  - View earnings and payout history

### Role: Admin
- **Permissions:**
  - All pharmacist permissions
  - View all users and sessions
  - Verify pharmacist applications
  - Process payouts
  - Access analytics dashboard
  - Manage system configuration

## Success Metrics

### User Engagement
- **Daily Active Users (DAU):** Target 1,000 within 3 months
- **Session Duration:** Average 5+ minutes
- **Return Rate:** 40%+ weekly return rate
- **Feature Adoption:** 60%+ users try marketplace within first month

### Quality Metrics
- **Response Accuracy:** 95%+ for drug information queries
- **Citation Rate:** 90%+ responses include sources
- **User Satisfaction:** 4.0+ average rating
- **Pharmacist Satisfaction:** 4.2+ average rating

### Business Metrics
- **Consultation Bookings:** 100+ per month by month 3
- **Booking Completion Rate:** 85%+
- **Average Consultation Value:** ₹300+
- **Platform Revenue:** ₹20,000+ per month by month 3

### Technical Metrics
- **API Uptime:** 99.5%+
- **P95 Response Time:** <5 seconds
- **Error Rate:** <1%
- **Search Relevance:** 80%+ users find desired drug in top 5 results

## Constraints and Assumptions

### Constraints
1. **Data Limitation:** No access to real patient medical records (synthetic data only)
2. **Regulatory:** Not a licensed medical device; for informational purposes only
3. **Budget:** Limited to free-tier or low-cost services where possible
4. **Timeline:** MVP delivery within 3 months

### Assumptions
1. Users have stable internet connection (3G minimum)
2. Pharmacists have valid licenses and professional liability insurance
3. Payment gateway (Razorpay) maintains 99%+ uptime
4. AI APIs (Gemini, Groq) remain accessible and affordable
5. Users accept terms of service and medical disclaimers

## Risks and Mitigations

| Risk | Impact | Probability | Mitigation |
|------|--------|-------------|------------|
| AI hallucination leading to incorrect medical advice | Critical | Medium | Strict citation requirements, human escalation, clear disclaimers |
| Payment fraud or disputes | High | Low | Razorpay fraud detection, clear refund policy, transaction logging |
| Pharmacist license fraud | High | Low | AI + manual verification, periodic re-verification, user reporting |
| API rate limits or costs | Medium | Medium | Caching, fallback providers, usage monitoring and alerts |
| Data breach | Critical | Low | Encryption, RLS, security audits, incident response plan |
| Scalability issues under load | Medium | Medium | Load testing, auto-scaling, CDN for static assets |

## Future Enhancements (Out of Scope for MVP)

1. **Telemedicine Integration:** Full EHR integration and e-prescriptions
2. **AI Diagnostics:** Symptom checker with differential diagnosis
3. **Medication Reminders:** Push notifications for medication adherence
4. **Health Tracking:** Integration with wearables and health apps
5. **Insurance Claims:** Direct claim filing and tracking
6. **Pharmacy Network:** Partner with pharmacies for home delivery
7. **Clinical Trials:** Match patients with relevant clinical trials
8. **Medical Tourism:** Connect international patients with Indian healthcare

## Glossary

- **AUC:** Area Under the Curve (pharmacokinetics)
- **CGHS:** Central Government Health Scheme
- **DPCO:** Drug Price Control Order
- **HBP:** Health Benefit Package
- **MOA:** Mechanism of Action
- **NLEM:** National List of Essential Medicines
- **PM-JAY:** Pradhan Mantri Jan Arogya Yojana (Ayushman Bharat)
- **RAG:** Retrieval-Augmented Generation
- **RLS:** Row-Level Security
- **SMILES:** Simplified Molecular Input Line Entry System

---

**Document Status:** Draft v1.0  
**Next Review:** March 2026  
**Owner:** Product Team

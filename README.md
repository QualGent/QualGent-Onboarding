# QualGent Platform - Technical Onboarding Guide

**Last Updated:** October 2025
**Version:** 1.0
**Target Audience:** New Engineering Hires

---

## Table of Contents

1. [Executive Summary](#executive-summary)
2. [Platform Architecture Overview](#platform-architecture-overview)
3. [Technology Stack Summary](#technology-stack-summary)
4. [Repository Guide](#repository-guide)
5. [Data Flow & Integration](#data-flow--integration)
6. [Development Environment Setup](#development-environment-setup)
7. [Testing & Quality Assurance](#testing--quality-assurance)
8. [Deployment & Infrastructure](#deployment--infrastructure)
9. [Monitoring & Operations](#monitoring--operations)
10. [Security Considerations](#security-considerations)
11. [Common Workflows](#common-workflows)
12. [Troubleshooting Guide](#troubleshooting-guide)
13. [Key Contacts & Resources](#key-contacts--resources)

---

## Executive Summary

**QualGent** is an AI-powered mobile application testing platform that combines cutting-edge artificial intelligence with enterprise-grade automation infrastructure to deliver autonomous, intelligent testing at scale.

### What We Do

QualGent enables companies to test their mobile applications (iOS and Android) using natural language instructions. Our AI agents understand test requirements, execute tests on real devices or emulators, detect bugs automatically, and verify their own actions - all without requiring traditional test scripts.

### Core Value Proposition

- **AI-Driven Testing**: Natural language test instructions powered by Claude AI
- **Scalable Infrastructure**: Cloud-based emulator fleet on Google Cloud Platform
- **Intelligent Bug Detection**: Automated error detection and reporting
- **Self-Verification**: AI-powered video analysis to validate test accuracy
- **Enterprise Ready**: Production-grade reliability, monitoring, and security

### Key Statistics

- **Languages**: Python (primary), JavaScript/React, Terraform
- **Total Codebase**: 4 major repositories, 250+ Python files
- **Infrastructure**: Google Cloud Platform (GKE, GCE, Cloud Run)
- **AI Models**: Anthropic Claude Sonnet 4, Google Gemini 2.5 Flash
- **Test Automation**: Appium 2.11.1, Selenium 4.6.0, BrowserStack Cloud
- **Database**: Supabase (PostgreSQL-based)

---

## Platform Architecture Overview

### High-Level System Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        QualGent Platform                         │
└─────────────────────────────────────────────────────────────────┘

┌──────────────────┐         ┌──────────────────────────────────┐
│   User/Client    │────────▶│   QualGent3-Service (GKE)        │
│   Submits Test   │         │   - FastAPI Backend              │
│   via API/UI     │         │   - Multi-process Job Queue      │
└──────────────────┘         │   - Vertex AI Integration        │
                              │   - Supabase Database            │
                              └────────┬─────────────────────────┘
                                       │
                                       │ Requests Emulator
                                       ▼
                              ┌──────────────────────────────────┐
                              │ QualGent-Emulator-Manager        │
                              │ (Cloud Run)                      │
                              │   - Emulator Allocation          │
                              │   - Priority Queue Management    │
                              │   - Auto-scaling                 │
                              └────────┬─────────────────────────┘
                                       │
                                       │ Allocates Emulator IP
                                       ▼
                              ┌──────────────────────────────────┐
                              │ QualGent-Emulator (GCE)          │
                              │   - Android Emulator Pool        │
                              │   - VNC/WebRTC Access            │
                              │   - Appium Server                │
                              └────────┬─────────────────────────┘
                                       │
                                       │ Executes Tests
                                       ▼
                              ┌──────────────────────────────────┐
                              │       AppiumCUA                  │
                              │   - AI Agent Loop (Claude)       │
                              │   - Computer Use Tools           │
                              │   - Bug Detection Engine         │
                              │   - AI Verification (Gemini)     │
                              └──────────────────────────────────┘
                                       │
                                       │ Reports Results
                                       ▼
                              ┌──────────────────────────────────┐
                              │      Supabase Database           │
                              │   - Test Results                 │
                              │   - Logs & Screenshots           │
                              │   - Video Storage                │
                              └──────────────────────────────────┘
```

### Component Interaction Flow

**1. Test Submission**
```
Client → QualGent3-Service API → Job Queue → Worker Process
```

**2. Emulator Allocation**
```
Worker → Emulator-Manager API → Priority Queue → Available Emulator IP
```

**3. Test Execution**
```
Worker → AppiumCUA → Appium Driver → Android Emulator → App Under Test
         ↓
      Claude AI (interprets instructions)
         ↓
      Computer Use Tools (executes actions)
         ↓
      Bug Detector (monitors for errors)
         ↓
      Screenshot/Video Capture
```

**4. Verification & Reporting**
```
AppiumCUA → Gemini AI (analyzes video) → Verification Report
         ↓
      ResultReporter → Supabase → Client Notification
```

---

## Technology Stack Summary

### Backend Services

| Component | Technology | Version | Purpose |
|-----------|-----------|---------|---------|
| **Web Framework** | FastAPI | 0.104.1+ | REST API, async request handling |
| **ASGI Server** | Uvicorn | 0.24.0+ | Production web server |
| **Database** | Supabase (PostgreSQL) | 2.0.2+ | Data persistence, file storage |
| **AI Model (Primary)** | Anthropic Claude | Sonnet 4 | Test execution logic, reasoning |
| **AI Model (Verification)** | Google Gemini | 2.5 Flash | Video verification |
| **Container Runtime** | Docker | Latest | Containerization |
| **Orchestration (Service)** | Kubernetes (GKE) | Latest | QualGent3-Service deployment |
| **Orchestration (Emulator)** | Terraform + GCE | Latest | Emulator infrastructure |
| **Orchestration (Manager)** | Cloud Run | Latest | Emulator manager deployment |

### Mobile Testing Stack

| Component | Technology | Version | Purpose |
|-----------|-----------|---------|---------|
| **Automation Framework** | Appium | 2.11.1 | Mobile app automation |
| **Driver** | UIAutomator2 | 3.8.0 | Android automation driver |
| **Browser Automation** | Selenium | 4.6.0 | WebDriver protocol |
| **Cloud Testing** | BrowserStack | SDK 1.30.5 | Cloud device testing |
| **Emulator** | Android Emulator | API 35 | Android 15 emulator |
| **Remote Access** | VNC, noVNC, WebRTC | Various | Emulator visualization |

### AI & ML

| Component | Provider | Model | Purpose |
|-----------|----------|-------|---------|
| **Primary AI** | Anthropic | Claude Sonnet 4 | Test interpretation, decision-making |
| **Verification AI** | Google | Gemini 2.5 Flash | Video analysis, verification |
| **AI Platform** | Google Vertex AI | Latest | Production AI deployment |
| **Alternative** | AWS Bedrock | - | Alternative AI provider (supported) |

### Frontend & UI

| Component | Technology | Version | Purpose |
|-----------|-----------|---------|---------|
| **UI Framework** | React | 18.3.1 | Emulator web interface |
| **Build Tool** | Vite | 5.4.20 | Fast frontend builds |
| **Web Server** | Express.js | 4.21.2 | React app hosting |
| **Streaming** | WebRTC | 1.0.18 | Low-latency emulator streaming |

### Infrastructure & Cloud

| Service | Provider | Purpose |
|---------|----------|---------|
| **Compute** | Google Cloud (GCE) | Emulator hosting |
| **Container Orchestration** | Google Kubernetes Engine (GKE) | Service backend |
| **Serverless** | Google Cloud Run | Emulator manager |
| **Infrastructure as Code** | Terraform | GCP resource provisioning |
| **Monitoring** | Google Cloud Monitoring | Performance tracking |
| **Logging** | Google Cloud Logging | Centralized logs |
| **Error Tracking** | Sentry | Error monitoring |

### Testing & Quality

| Tool | Version | Purpose |
|------|---------|---------|
| **pytest** | 7.4.3 | Test framework |
| **pytest-selenium** | 4.0.1 | Selenium integration |
| **pytest-xdist** | 3.8.0 | Distributed testing |
| **pytest-html** | 4.1.1 | HTML reports |
| **RobotFramework** | 7.3.2 | Keyword-driven testing |

### Development Tools

| Tool | Purpose |
|------|---------|
| **black** | Code formatting (Python) |
| **flake8** | Linting (Python) |
| **mypy** | Static type checking |
| **structlog** | Structured logging |
| **python-dotenv** | Environment management |

---

## Repository Guide

### 1. QualGent3-Service

**Location**: `/Users/aaronyujiaze/code/QualGent3-Service`
**Primary Language**: Python
**Deployment**: Google Kubernetes Engine (GKE)

#### Purpose
FastAPI backend service that orchestrates the entire testing workflow. Acts as the central coordinator for job management, emulator allocation, test execution, and result reporting.

#### Key Components

**API Layer** (`app/api/routes.py` - 777 lines)
- Health checks: `/`, `/health`, `/liveness`
- Job management: `POST /job/start`, `GET /job/{job_id}/status`, `GET /jobs`
- Smoke tests: `POST /job/smoke-test`
- Webhooks: Twilio SMS integration, device SMS capture
- Kubernetes: Pod discovery, distribution

**Job Manager** (`app/jobs/manager.py` - 2,193 lines)
- Multi-process worker pool
- Thread-safe job queue (no Redis dependency)
- Organization-based concurrency limiting
- Kubernetes pod discovery
- Heartbeat and health monitoring
- Job recovery and state persistence

**Database** (`app/core/database.py`)
- Supabase connection management
- Connection pooling with throttling
- Prevents pool exhaustion via semaphore-based limiting
- Per-process connection caps

**Kubernetes Integration** (`app/core/kubernetes_manager.py`)
- Auto-detects GKE environment
- Background pod discovery thread
- Health tracking and job distribution

**Logging** (`app/core/logging.py` - 616 lines)
- Thread-safe logger instances
- Worker process logging with context
- Structured JSON logging
- Supabase log persistence

**BrowserStack Integration** (`app/utils/browserstack_runner.py` - 577 lines)
- Subprocess management for test execution
- Real-time output streaming
- Performance metric generation
- Session logging

#### Configuration
```bash
# Key Environment Variables
SUPABASE_URL, SUPABASE_KEY, SUPABASE_SERVICE_ROLE_KEY
BROWSERSTACK_USERNAME, BROWSERSTACK_ACCESS_KEY
VERTEX_AI_PROJECT_ID, VERTEX_AI_REGION
MAX_WORKERS, WORKER_TIMEOUT, JOB_QUEUE_SIZE
SENTRY_DSN
```

#### Entry Points
- `main.py`: FastAPI application
- `start.sh`: Startup script with validation
- `run_jobs.py`: Job runner script

#### Docker & Deployment
- Base: `python:3.11-slim`
- k8s manifests in `k8s/` directory
- 3 replicas default
- Resource limits: 512Mi memory, 500m CPU
- Liveness probe: `/liveness`
- Readiness probe: `/health`

#### Important Files
- `config.py` (238 lines): Centralized configuration
- `requirements.txt`: 95+ dependencies
- `DEPLOYMENT.md`: Deployment guide
- `MULTI_PROCESS_README.md`: Architecture documentation

---

### 2. AppiumCUA

**Location**: `/Users/aaronyujiaze/code/AppiumCUA`
**Primary Language**: Python
**Type**: Library/Framework (Git Submodule)

#### Purpose
AI-powered Computer Use Agent for autonomous mobile app testing. Interprets natural language instructions, executes tests via Appium, detects bugs, and verifies its own actions using AI.

**CUA = Computer Use Agent**: An AI agent that uses Claude to understand and execute mobile app testing tasks autonomously.

#### Key Components

**Agent Loop** (`cua/agent_loop.py`)
- Main agentic sampling loop
- Calls Anthropic API with Claude Sonnet 4
- Context compaction and memory management
- Supports multiple providers: Anthropic, Bedrock, Vertex AI
- Sentry integration

**Computer Use Tools** (`cua/tools/`)
- `AndroidComputerTool`: ADB-based Android control
- `AppiumComputerTool`: Appium-based BrowserStack testing
- `ScreenshotCapture`: Multi-format screenshot handling
- `CoordinateScaler`: Screen coordinate transformation
- `KeyboardHandler`, `MouseHandler`, `TextInputHandler`

**Bug Detection** (`cua/tools/bug_detector.py`)
- Text pattern matching for errors
- Visual analysis (blank screens, dialogs)
- Context-based detection (stuck states, repeated actions)
- Severity levels: CRITICAL, HIGH, MEDIUM, LOW
- Configurable sensitivity

**AI Verification** (`cua/tools/verification_providers/`)
- `PostTestVerifier`: AI-powered verification against video
- `GeminiProvider`: Google Gemini implementation
- `BaseVerificationProvider`: Extensible interface
- Frame-by-frame video analysis

**Test Execution Management**
- `StepLogger`: Records test steps with timestamps
- `OutputManager`: Standardized output directory structure
- `UploadManager`: Supabase file upload
- `DatabaseManager`: Test data persistence

**Reporting**
- `ResultReporter`: Final test result reporting
- `BrowserStackReporter`: BrowserStack session status
- `DiscoverabilityReporter`: Feature discoverability tracking
- `StreamingOrchestrator`: Post-test pipeline

#### Platform Entry Points
- `android/new_cua_android.py`: Android test execution
- `ios/new_cua_ios.py`: iOS test execution
- Both use pytest with BrowserStack SDK

#### Configuration
```bash
# Key Environment Variables
ANTHROPIC_API_KEY
GOOGLE_API_KEY  # For Gemini verification
BROWSERSTACK_USERNAME, BROWSERSTACK_ACCESS_KEY
SUPABASE_URL, SUPABASE_KEY, SUPABASE_SERVICE_ROLE_KEY
CLAUDE_MODEL_VERSION=4  # or '3.5'
AI_PROVIDER=vertex  # or 'anthropic', 'bedrock'
GEMINI_MODEL_NAME=gemini-2.5-flash
```

#### Output Structure
```
outputs/
└── {client_id}/{user_id}/{test_run_id}/{app}/{app_version}/
    └── {test_id}_{timestamp}/
        ├── json/
        │   ├── steps.json                    # Execution data
        │   └── verification_analysis.json    # AI verification
        ├── logs/
        │   ├── steps_readable.txt            # Human-readable log
        │   └── verification_report.txt       # Verification report
        ├── screenshots/
        │   └── step_XXX_after.png           # Action screenshots
        └── videos/
            └── browserstack_{session_id}.mp4 # Recording
```

#### Key Features
- Multi-modal AI integration (Claude + Gemini)
- Advanced bug detection (text, visual, context)
- Sophisticated output management
- Comprehensive verification system
- Production-ready with Sentry monitoring

#### Documentation
- `README.md` (341 lines): Main documentation
- `docs/bug_detection_explained.md`: Bug detection methods
- `docs/verification_output_structure.md`: Output format
- `docs/running_tests_and_demos.md`: Test execution guide

---

### 3. QualGent-Emulator

**Location**: `/Users/aaronyujiaze/code/QualGent-Emulator`
**Primary Language**: Python, Terraform, JavaScript/React
**Deployment**: Google Compute Engine (GCE)

#### Purpose
Scalable Android emulator environment deployed on Google Cloud Platform. Provides headless Android emulators with multiple access methods (VNC, WebRTC, Appium) for testing automation.

#### Key Components

**Android Emulator Container** (`docker/Dockerfile.android-emulator`)
- Base: Ubuntu 24.04 LTS
- Android SDK: API Level 35 (Android 15)
- X11/Xvfb: Virtual frame buffer (1080x1920)
- x11vnc: VNC server (port 5900)
- noVNC: Web-based VNC (port 6080)
- Appium: Port 4723
- ADB: Port 5037, 5555
- Emulator gRPC: Port 8554

**Process Management** (`docker/supervisord.conf`)
- Supervisor manages 8 services:
  1. xvfb (X server)
  2. fluxbox (window manager)
  3. x11vnc (VNC server)
  4. novnc (web VNC)
  5. socat-5555 (port forwarding)
  6. emulator (Android emulator)
  7. adb-server (ADB daemon)
  8. appium (Appium server)
  9. tmp-cleanup (cleanup service)

**WebRTC Frontend** (`docker/webrtc-frontend/`)
- React 18.3 + Vite 5.4
- Express.js server (port 8080)
- android-emulator-webrtc library
- Low-latency browser streaming

**Networking**
- Envoy Proxy: gRPC-Web translator (port 8555)
- Nginx: Reverse proxy, rate limiting, SSL termination

**Terraform Infrastructure** (`terraform/`)
- Modules: GCE instance provisioning
- Environments: production, test, local
- Auto-scaling instance groups
- Health checks and monitoring

#### Access Methods
1. **Direct VNC**: Port 5900
2. **noVNC Web**: Port 6080 (browser-based)
3. **WebRTC**: Port 8080 (low-latency streaming)
4. **ADB**: Port 5555 (remote device control)
5. **Appium**: Port 4723 (automation API)

#### Configuration
```bash
# Key Environment Variables
DEVICE=Samsung Galaxy S10
SCREEN_WIDTH=1080
SCREEN_HEIGHT=1920
VNC_PASSWORD=android
EMULATOR_MEMORY=6096  # 6GB RAM
EMULATOR_CORES=4
APPIUM_PORT=4723
```

#### Deployment

**Local Development**:
```bash
./scripts/start-local.sh
# Accesses at: http://localhost:6080 (VNC) or http://localhost:8080 (WebRTC)
```

**Production (GCE)**:
```bash
./scripts/deploy-production.sh
# Deploys via Terraform
# Auto-scaling instance groups
# Health checks and monitoring
```

**Management**:
```bash
./scripts/manage-production.sh status    # Show instance status
./scripts/manage-production.sh scale N   # Scale to N instances
./scripts/manage-production.sh update    # Update instances
```

#### Infrastructure
- Machine type: n1-standard-4 (4 vCPU, 15GB RAM)
- Disk: 100GB SSD
- Nested virtualization: Enabled (KVM support)
- Health check: HTTP on port 6080
- Auto-healing: Enabled

#### Testing
- pytest configuration in `conftest.py`
- Test fixtures for Appium/Selenium
- Emulator reset functionality
- Multiple test files for validation

#### Documentation
- `README.md`: Main documentation
- `USAGE_GUIDE.md`: Testing usage guide
- `docs/GETTING_STARTED.md`: Quick start
- `docs/GCE_DEPLOYMENT.md`: GCE deployment guide
- `docs/PRODUCTION_DEPLOYMENT.md`: Production setup
- `docs/TROUBLESHOOTING.md`: Common issues

---

### 4. QualGent-Emulator-Manager

**Location**: `/Users/aaronyujiaze/code/QualGent-Emulator-Manager`
**Primary Language**: Python
**Deployment**: Google Cloud Run

#### Purpose
FastAPI service that manages emulator allocation, job queuing, and auto-scaling. Acts as the intelligent broker between test jobs and available emulators.

#### Key Components

**API Layer** (`app/api/emulator_endpoints.py`)

Endpoints:
- `POST /emulator/get_emulator`: Allocate emulator or queue job
- `POST /emulator/release`: Release emulator back to pool
- `GET /emulator/stats`: Emulator utilization statistics
- `GET /emulator/queue/stats`: Queue statistics
- `GET /emulator/queue/job/{job_id}`: Job status
- `GET /health`: Health check
- `GET /queue/visualization`: Detailed queue view

**Emulator Manager** (`app/core/emulator_manager.py`)
- `discover_emulators()`: Uses gcloud CLI to find running instances
- `get_emulator_for_job()`: Atomic allocation to job
- `release_emulator()`: Return to pool
- `validate_and_release_emulator()`: Verify ownership before release
- `_should_scale_up()`: Check if scaling needed (< 20% available)
- `_scale_up_emulators()`: Add instances via gcloud
- `get_emulator_stats()`: Utilization metrics

**Job Queue Manager** (`app/core/job_queue_manager.py`)
- Priority queue (1000 job capacity)
- Three worker threads
- FIFO within priority levels
- Status tracking for all jobs

**Database Layer** (`schema/emulator_table.sql`)

Table: `emulators`
```sql
- id UUID PRIMARY KEY
- emulator_name TEXT UNIQUE
- emulator_ip INET NOT NULL
- port INTEGER (default 4723)
- status TEXT (RUNNING, STOPPING, TERMINATED, etc.)
- availability TEXT ('free' or 'assigned')
- assigned_job_id TEXT
- allocated_at TIMESTAMPTZ
- discovered_at TIMESTAMPTZ
- last_updated TIMESTAMPTZ
```

Key Functions:
- `get_and_assign_emulator()`: Atomic allocation with `FOR UPDATE SKIP LOCKED`
- `free_emulator_from_job()`: Release with validation
- `upsert_emulator_from_discovery()`: Update from gcloud
- `get_emulator_stats()`: Aggregate statistics
- `cleanup_old_assignments()`: Safety net for stale assignments (> 2 hours)

**Indexes** (for performance):
- Available emulators (free AND running)
- Assigned emulators
- Name and status lookups

#### Configuration
```bash
# Key Environment Variables
SUPABASE_URL, SUPABASE_KEY, SUPABASE_SERVICE_ROLE_KEY
GOOGLE_APPLICATION_CREDENTIALS
SCALE_UP_COUNT=5  # Instances to add when scaling
SCALING_THRESHOLD=20  # Availability percentage
MAX_WAIT_TIME=300  # Max wait time in seconds
HOST=0.0.0.0
PORT=8000
```

#### Management Workflow

**1. Discovery Phase**
```bash
gcloud compute instances list --filter=name~android-emulator
→ Parse instance metadata (name, zone, status, IP)
→ Store in PostgreSQL
```

**2. Allocation Phase (Atomic)**
```sql
get_and_assign_emulator(job_id)
├─ FOR UPDATE SKIP LOCKED  -- Prevents race conditions
├─ SELECT first available (free AND running)
├─ UPDATE to assigned status
├─ SET assigned_job_id = job_id
└─ Return emulator IP
```

**3. Queue Management**
```
No emulator available?
→ Add to priority queue
→ Worker threads monitor
→ When emulator frees up → allocate to next job
```

**4. Release Phase**
```
Validate job_id matches assignment
→ SET availability = 'free'
→ SET assigned_job_id = NULL
→ Trigger queue processing
```

**5. Scaling (Background)**
```
Monitor availability < 20%?
→ gcloud compute instance-groups resize
→ Wait 60s
→ Refresh emulator list
→ (Runs in daemon thread)
```

#### Deployment

**Local**:
```bash
docker-compose up --build
# or
python main.py
```

**Cloud Run**:
```bash
./deploy_to_cloud_run.sh
# Builds Docker image
# Pushes to GCR
# Deploys to Cloud Run
```

#### Key Features
- Race condition prevention (database-level locking)
- Priority queue (1000 job capacity)
- Auto-scaling (configurable threshold)
- High concurrency support
- Comprehensive error logging
- Health checks for reliability
- Automatic cleanup of stale assignments

#### Testing
- `test_concurrent_requests.py`: Concurrent allocation tests
- `test_20_concurrent_jobs.py`: Load testing
- `scripts/test_queue.py`: Queue functionality
- `scripts/test_scaling.py`: Scaling verification
- `scripts/step_by_step_demo.py`: Educational demo

#### Documentation
- `README.md` (23.8 KB): Comprehensive guide
- `API_DOCUMENTATION.md` (13.5 KB): API reference
- `THREE_THREAD_ARCHITECTURE.md`: Architecture details
- `DEMO_GUIDE.md`: Demo instructions

---

## Data Flow & Integration

### Complete Test Execution Flow

```
┌─────────────────────────────────────────────────────────────────┐
│ STEP 1: Test Submission                                         │
└─────────────────────────────────────────────────────────────────┘

Client/UI
    ↓ POST /job/start
QualGent3-Service (FastAPI)
    ↓ Validate request
JobManager (manager.py)
    ↓ Queue job
Worker Pool (multiprocessing)
    ↓ Pick up job


┌─────────────────────────────────────────────────────────────────┐
│ STEP 2: Emulator Allocation                                     │
└─────────────────────────────────────────────────────────────────┘

Worker Process
    ↓ POST /emulator/get_emulator
QualGent-Emulator-Manager (Cloud Run)
    ↓ Check available emulators
Database (Supabase)
    ↓ FOR UPDATE SKIP LOCKED (atomic)
    ├─ Available? → Return emulator IP
    └─ None? → Queue job in priority queue
         ↓ Worker thread monitors
         ↓ Emulator freed? → Assign to waiting job
         └─ Return emulator IP


┌─────────────────────────────────────────────────────────────────┐
│ STEP 3: Test Execution                                          │
└─────────────────────────────────────────────────────────────────┘

Worker Process (QualGent3-Service)
    ↓ Run pytest with AppiumCUA
AppiumCUA (new_cua_android.py)
    ↓ Initialize Appium driver
Agent Loop (agent_loop.py)
    ↓ Read test instructions from Supabase
    ↓ Call Claude AI (Vertex AI)
Claude Sonnet 4
    ↓ Interpret instruction → Generate action
Computer Use Tool (AndroidComputerTool)
    ↓ Execute action via Appium
    ↓ adb commands → Emulator
Android Emulator (GCE)
    ↓ Perform action on app
    ↓ Capture screenshot
Bug Detector (bug_detector.py)
    ↓ Analyze screenshot/state
    ↓ Detect errors? → Flag bug
Step Logger (step_logger.py)
    ↓ Record step with timestamp
    ↓ Continue until test complete


┌─────────────────────────────────────────────────────────────────┐
│ STEP 4: Video Download & Verification                           │
└─────────────────────────────────────────────────────────────────┘

AppiumCUA
    ↓ Test complete → Download BrowserStack video
    ↓ Trim video to test duration
Post-Test Verifier (verification_providers/)
    ↓ Upload video to Gemini
Gemini 2.5 Flash
    ↓ Analyze video frame-by-frame
    ↓ Compare with test steps
    ↓ Return verification report
         ├─ Accuracy score
         ├─ Discrepancies
         └─ Recommendations


┌─────────────────────────────────────────────────────────────────┐
│ STEP 5: Result Reporting                                        │
└─────────────────────────────────────────────────────────────────┘

Result Reporter (result_reporter.py)
    ↓ Compile all data
    ↓ Upload to Supabase:
         ├─ Test results
         ├─ Screenshots
         ├─ Videos
         ├─ Logs (JSON + readable text)
         └─ Verification report
BrowserStack Reporter
    ↓ Update BrowserStack session status
Streaming Orchestrator
    ↓ Stream logs to Supabase (real-time)
Database Manager
    ↓ Update job status in Supabase


┌─────────────────────────────────────────────────────────────────┐
│ STEP 6: Emulator Release                                        │
└─────────────────────────────────────────────────────────────────┘

Worker Process
    ↓ POST /emulator/release
QualGent-Emulator-Manager
    ↓ Validate job_id
    ↓ Free emulator
    ↓ Check queue for waiting jobs
    └─ Assign to next job if any
```

### Database Schema Overview

**Supabase Tables** (Used across services):

1. **jobs** (QualGent3-Service)
   - Job tracking and status
   - Queued/started/completed timestamps
   - Result data and error information

2. **organizations** (QualGent3-Service)
   - Organization/client management
   - `is_paid` flag for smoke test filtering

3. **test_runs** (AppiumCUA)
   - Test execution records
   - Links to test cases and app files

4. **test_case_developer_logs** (AppiumCUA)
   - Real-time log streaming
   - Step-by-step execution logs

5. **test_run_files** (AppiumCUA)
   - File metadata (screenshots, videos, logs)
   - Supabase storage bucket references

6. **inbound_sms** (QualGent3-Service)
   - SMS message capture (Twilio webhook)
   - OTP code extraction

7. **emulators** (Emulator-Manager)
   - Emulator instance registry
   - Status, availability, assignment tracking

**Supabase Storage Buckets**:
- `qualgent-upload`: Test artifacts (screenshots, videos, APKs)

### API Integration Points

**QualGent3-Service → Emulator-Manager**:
```bash
POST /emulator/get_emulator
{
  "job_id": "uuid",
  "os_type": "android",
  "priority": 0
}
→ Returns: {"emulator_ip": "35.x.x.x", "port": 4723}
```

**QualGent3-Service → Emulator-Manager (Release)**:
```bash
POST /emulator/release
{
  "job_id": "uuid",
  "emulator_ip": "35.x.x.x"
}
→ Returns: {"status": "released"}
```

**Worker → Appium (via AppiumCUA)**:
```python
driver = webdriver.Remote(
    f"http://{emulator_ip}:4723",
    capabilities={
        "platformName": "Android",
        "automationName": "UiAutomator2",
        ...
    }
)
```

**AppiumCUA → Claude AI (Vertex AI)**:
```python
from anthropic import AnthropicVertex

client = AnthropicVertex(
    project_id="project-id",
    region="us-east5"
)

response = client.messages.create(
    model="claude-sonnet-4-20250514",
    messages=[...],
    tools=[...],  # Computer use tools
)
```

**AppiumCUA → Gemini (Verification)**:
```python
import google.generativeai as genai

model = genai.GenerativeModel("gemini-2.5-flash")
response = model.generate_content([
    video_file,  # Native video upload
    verification_prompt
])
```

---

## Development Environment Setup

### Prerequisites

**Required Software**:
- Python 3.11+ (recommended: pyenv for version management)
- Node.js 20.x LTS (for WebRTC frontend)
- Docker Desktop
- Docker Compose
- Google Cloud SDK (`gcloud` CLI)
- Terraform (for infrastructure changes)
- Git

**Cloud Accounts**:
- Google Cloud Platform account with billing enabled
- Supabase account (or self-hosted instance)
- Anthropic API key (for Claude)
- Google AI Studio API key (for Gemini)
- BrowserStack account (optional, for cloud devices)
- Twilio account (optional, for SMS webhooks)

### Initial Setup

#### 1. Clone Repositories

```bash
cd ~/code  # or your preferred directory

# Clone all repositories
git clone https://github.com/QualGent/QualGent3-Service.git
git clone https://github.com/QualGent/AppiumCUA.git
git clone https://github.com/QualGent/QualGent-Emulator.git
git clone https://github.com/QualGent/QualGent-Emulator-Manager.git

# Note: AppiumCUA is a git submodule in QualGent3-Service
cd QualGent3-Service
git submodule update --init --recursive
```

#### 2. Google Cloud Setup

```bash
# Install gcloud CLI (if not already installed)
# See: https://cloud.google.com/sdk/docs/install

# Authenticate
gcloud auth login
gcloud auth application-default login

# Set project
gcloud config set project YOUR_PROJECT_ID

# Enable required APIs
gcloud services enable compute.googleapis.com
gcloud services enable container.googleapis.com
gcloud services enable run.googleapis.com
gcloud services enable monitoring.googleapis.com
gcloud services enable logging.googleapis.com
gcloud services enable artifactregistry.googleapis.com
```

#### 3. Service-Specific Setup

**QualGent3-Service**:
```bash
cd QualGent3-Service

# Create virtual environment
python3.11 -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Copy environment template
cp env.example .env

# Edit .env with your credentials
# Required: SUPABASE_URL, SUPABASE_KEY, BROWSERSTACK_*, VERTEX_AI_*, etc.
nano .env

# Run locally
python main.py
# or
uvicorn main:app --host 0.0.0.0 --port 8000 --reload
```

**AppiumCUA**:
```bash
cd AppiumCUA

python3.11 -m venv venv
source venv/bin/activate

pip install -r requirements.txt

cp .env.example .env
# Edit with ANTHROPIC_API_KEY, GOOGLE_API_KEY, etc.

# Run Android tests (requires emulator or BrowserStack)
cd android
browserstack-sdk pytest -s new_cua_android.py \
  --test-run-id "test-uuid" \
  --app-file-id "app-uuid"
```

**QualGent-Emulator**:
```bash
cd QualGent-Emulator

# Local development (Docker)
cd docker
cp env.example .env
# Edit if needed

# Start local emulator
../scripts/start-local.sh

# Access at:
# VNC: http://localhost:6080 (password: android)
# WebRTC: http://localhost:8080

# For production deployment:
cd terraform/environments/production
cp terraform.tfvars.example terraform.tfvars
# Edit with your GCP project ID, credentials, etc.
terraform init
terraform plan
terraform apply
```

**QualGent-Emulator-Manager**:
```bash
cd QualGent-Emulator-Manager

python3.11 -m venv venv
source venv/bin/activate

pip install -r requirements.txt

cp .env.example .env
# Edit with SUPABASE_* and GOOGLE_APPLICATION_CREDENTIALS

# Run locally
python main.py

# Test API
curl http://localhost:8000/health
curl -X POST http://localhost:8000/emulator/get_emulator \
  -H "Content-Type: application/json" \
  -d '{"job_id":"test-123","os_type":"android","priority":0}'
```

#### 4. Database Setup (Supabase)

```bash
# Create Supabase project at https://supabase.com

# Run SQL migrations (in Supabase SQL Editor):
# 1. QualGent3-Service: Create jobs, organizations, inbound_sms tables
# 2. Emulator-Manager: Run schema/emulator_table.sql
# 3. AppiumCUA: Create test_runs, test_case_developer_logs tables

# Create storage bucket: qualgent-upload
# Set up RLS policies as needed
```

### IDE Configuration

**VS Code** (Recommended):
```json
// .vscode/settings.json
{
  "python.defaultInterpreterPath": "${workspaceFolder}/venv/bin/python",
  "python.linting.enabled": true,
  "python.linting.flake8Enabled": true,
  "python.formatting.provider": "black",
  "editor.formatOnSave": true,
  "[python]": {
    "editor.codeActionsOnSave": {
      "source.organizeImports": true
    }
  }
}
```

**Extensions**:
- Python (Microsoft)
- Pylance
- Docker
- Terraform
- GitLens

---

## Testing & Quality Assurance

### Testing Strategy

**Unit Tests**:
- Mock external dependencies (Supabase, Anthropic, gcloud)
- Fast execution (< 1 second per test)
- Location: `tests/unit/` in AppiumCUA

**Integration Tests**:
- Test with real external services
- Slower execution
- Location: `tests/integration/` in AppiumCUA

**End-to-End Tests**:
- Full workflow tests
- Use local emulator or BrowserStack
- Examples: `test_local_emulator.py`, `test_20_concurrent_jobs.py`

### Running Tests

**QualGent3-Service**:
```bash
# Run specific test files
pytest test_multi_process.py -v
pytest test_connection_throttling.py -v
pytest test_logging.py -v

# Run all tests
pytest tests/ -v
```

**AppiumCUA**:
```bash
# Unit tests (no external deps)
pytest tests/unit/ -v

# Integration tests
pytest tests/integration/ -v

# With coverage
pytest tests/ --cov=cua --cov-report=html

# Specific test
pytest tests/unit/test_context_compactor.py::test_compact_messages -v
```

**QualGent-Emulator**:
```bash
# Local emulator tests
pytest tests/ --use-local-emulator -v

# Specific interaction test
pytest test_local_emulator.py::TestLocalAndroidEmulator::test_basic_interaction -v

# Without emulator reset (for debugging)
pytest test_debug.py --use-local-emulator --no-reset-emulator -s
```

**QualGent-Emulator-Manager**:
```bash
# All tests
pytest -v

# Concurrent request tests
pytest test_concurrent_requests.py -v

# Load test (20+ concurrent jobs)
pytest test_20_concurrent_jobs.py -v

# Demo scripts
python scripts/step_by_step_demo.py
python scripts/test_scaling.py
```

### Test Configuration

**pytest.ini** (Create in repository root):
```ini
[pytest]
testpaths = tests
python_files = test_*.py
python_classes = Test*
python_functions = test_*
markers =
    unit: Unit tests (no external dependencies)
    integration: Integration tests (external dependencies)
    slow: Slow tests (> 10 seconds)
    local: Local emulator tests
```

**Command-line Options** (AppiumCUA):
```bash
--use-local-emulator    # Use local emulator instead of BrowserStack
--no-reset-emulator     # Skip state reset between tests
--test-run-id UUID      # Unique test identifier
--test-name NAME        # Human-readable test name
--bug-detection-level   # high, medium, low (sensitivity)
```

### Quality Checks

**Code Formatting**:
```bash
# Format with black
black .

# Check formatting
black --check .
```

**Linting**:
```bash
# Run flake8
flake8 app/ tests/

# With custom config
flake8 --max-line-length=100 --exclude=venv,__pycache__
```

**Type Checking**:
```bash
# Run mypy
mypy app/
```

**Pre-commit Hooks** (Recommended):
```bash
# Install pre-commit
pip install pre-commit

# Install hooks
pre-commit install

# .pre-commit-config.yaml
repos:
  - repo: https://github.com/psf/black
    rev: 23.11.0
    hooks:
      - id: black
  - repo: https://github.com/pycqa/flake8
    rev: 6.1.0
    hooks:
      - id: flake8
```

---

## Deployment & Infrastructure

### Deployment Architecture

```
┌──────────────────────────────────────────────────────────────┐
│                    Google Cloud Platform                      │
├──────────────────────────────────────────────────────────────┤
│                                                               │
│  ┌────────────────────────────────────────────────────────┐  │
│  │  Google Kubernetes Engine (GKE)                        │  │
│  │                                                         │  │
│  │  ┌──────────────────────────────────────────────────┐  │  │
│  │  │  QualGent3-Service (3 replicas)                  │  │  │
│  │  │  - FastAPI backend                               │  │  │
│  │  │  - Multi-process workers                         │  │  │
│  │  │  - Liveness/Readiness probes                     │  │  │
│  │  │  - LoadBalancer Service                          │  │  │
│  │  └──────────────────────────────────────────────────┘  │  │
│  └────────────────────────────────────────────────────────┘  │
│                                                               │
│  ┌────────────────────────────────────────────────────────┐  │
│  │  Google Compute Engine (GCE)                           │  │
│  │                                                         │  │
│  │  ┌──────────────────────────────────────────────────┐  │  │
│  │  │  Android Emulator Pool (auto-scaling)            │  │  │
│  │  │  - Instance Group Manager                        │  │  │
│  │  │  - n1-standard-4 machines                        │  │  │
│  │  │  - Ubuntu 24.04 + Android API 35                 │  │  │
│  │  │  - Health checks + Auto-healing                  │  │  │
│  │  └──────────────────────────────────────────────────┘  │  │
│  └────────────────────────────────────────────────────────┘  │
│                                                               │
│  ┌────────────────────────────────────────────────────────┐  │
│  │  Google Cloud Run                                      │  │
│  │                                                         │  │
│  │  ┌──────────────────────────────────────────────────┐  │  │
│  │  │  QualGent-Emulator-Manager                       │  │  │
│  │  │  - FastAPI emulator allocation API               │  │  │
│  │  │  - Auto-scaling (serverless)                     │  │  │
│  │  │  - Priority queue management                     │  │  │
│  │  └──────────────────────────────────────────────────┘  │  │
│  └────────────────────────────────────────────────────────┘  │
│                                                               │
└──────────────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────────────┐
│                    External Services                          │
├──────────────────────────────────────────────────────────────┤
│  - Supabase (PostgreSQL + Storage)                           │
│  - Google Vertex AI (Claude Sonnet 4)                        │
│  - Google AI Platform (Gemini 2.5 Flash)                     │
│  - BrowserStack (Optional cloud devices)                     │
│  - Sentry (Error tracking)                                   │
└──────────────────────────────────────────────────────────────┘
```

### Deployment Procedures

#### QualGent3-Service (GKE)

**Build Docker Image**:
```bash
cd QualGent3-Service

# Build
docker build -t qualgent3-service:latest .

# Tag for Google Artifact Registry
docker tag qualgent3-service:latest \
  us-east1-docker.pkg.dev/YOUR_PROJECT/qualgent3/backend-service:v1.0.0

# Push
docker push us-east1-docker.pkg.dev/YOUR_PROJECT/qualgent3/backend-service:v1.0.0
```

**Deploy to GKE**:
```bash
# Update image in k8s/deployment.yaml
# image: us-east1-docker.pkg.dev/YOUR_PROJECT/qualgent3/backend-service:v1.0.0

# Apply configuration
kubectl apply -f k8s/namespace.yaml
kubectl apply -f k8s/configmap.yaml
kubectl apply -f k8s/deployment.yaml
kubectl apply -f k8s/service.yaml

# Check status
kubectl get pods -n qualgent3-service
kubectl get svc -n qualgent3-service

# View logs
kubectl logs -f deployment/backend-service -n qualgent3-service

# Scale
kubectl scale deployment backend-service --replicas=5 -n qualgent3-service
```

**Rolling Update**:
```bash
# Update image tag in deployment.yaml
# Apply changes
kubectl apply -f k8s/deployment.yaml

# Monitor rollout
kubectl rollout status deployment/backend-service -n qualgent3-service

# Rollback if needed
kubectl rollout undo deployment/backend-service -n qualgent3-service
```

#### QualGent-Emulator (GCE)

**Deploy with Terraform**:
```bash
cd QualGent-Emulator/terraform/environments/production

# Initialize
terraform init

# Plan
terraform plan -out=tfplan

# Apply
terraform apply tfplan

# Check status
gcloud compute instances list --filter="name~android-emulator"

# View logs
gcloud compute ssh android-emulator-1 --zone=us-central1-a
sudo docker logs android-emulator
```

**Update Deployment**:
```bash
# Build new Docker image
cd QualGent-Emulator
./scripts/build-emulator-image.sh

# Push to registry
docker tag qualgent/android-emulator:api35 \
  gcr.io/YOUR_PROJECT/android-emulator:api35-v2
docker push gcr.io/YOUR_PROJECT/android-emulator:api35-v2

# Update Terraform variable
# In terraform.tfvars: docker_image = "gcr.io/YOUR_PROJECT/android-emulator:api35-v2"

# Apply update
terraform apply

# Or use script
./scripts/deploy-production-update.sh
```

**Scale Emulator Pool**:
```bash
# Via script
./scripts/manage-production.sh scale 10

# Or directly
gcloud compute instance-groups managed resize android-emulator-group \
  --size=10 --zone=us-central1-a
```

#### QualGent-Emulator-Manager (Cloud Run)

**Deploy**:
```bash
cd QualGent-Emulator-Manager

# Build and deploy (using script)
./deploy_to_cloud_run.sh

# Or manually:
# Build
gcloud builds submit --tag gcr.io/YOUR_PROJECT/emulator-manager

# Deploy
gcloud run deploy emulator-manager \
  --image gcr.io/YOUR_PROJECT/emulator-manager \
  --platform managed \
  --region us-central1 \
  --allow-unauthenticated \
  --set-env-vars SUPABASE_URL=...,SUPABASE_KEY=... \
  --service-account emulator-manager@YOUR_PROJECT.iam.gserviceaccount.com \
  --memory 1Gi \
  --cpu 2 \
  --max-instances 10

# Get URL
gcloud run services describe emulator-manager --region us-central1 --format='value(status.url)'
```

**Update**:
```bash
# Rebuild and deploy
./deploy_to_cloud_run.sh

# Cloud Run will automatically handle traffic migration
```

#### AppiumCUA

**Note**: AppiumCUA is a library, not a standalone service. It's deployed as part of QualGent3-Service (as a git submodule).

**Update AppiumCUA**:
```bash
cd QualGent3-Service/AppiumCUA
git pull origin main

cd ..
git add AppiumCUA
git commit -m "Update AppiumCUA to latest version"
git push

# Rebuild and redeploy QualGent3-Service
# (See QualGent3-Service deployment steps above)
```

### Infrastructure Management

**Terraform State**:
```bash
# Backend configuration for state storage
# In terraform/environments/production/main.tf:
terraform {
  backend "gcs" {
    bucket = "qualgent-terraform-state"
    prefix = "production"
  }
}

# Initialize with remote backend
terraform init -backend-config="bucket=qualgent-terraform-state"
```

**Resource Monitoring**:
```bash
# View GCE instances
gcloud compute instances list

# View GKE clusters
gcloud container clusters list

# View Cloud Run services
gcloud run services list

# View logs
gcloud logging read "resource.type=k8s_container" --limit 50
gcloud logging read "resource.type=gce_instance" --limit 50
```

---

## Monitoring & Operations

### Monitoring Stack

**Google Cloud Monitoring**:
- Metrics: CPU, memory, disk, network for all GCE/GKE instances
- Custom metrics: Job queue size, emulator utilization
- Dashboards: Pre-configured for emulator pool

**Sentry**:
- Error tracking across all services
- Performance monitoring
- Release tracking
- Breadcrumb trails for debugging

**Application Logs**:
- Google Cloud Logging: Centralized log aggregation
- Structured JSON logs: Easy parsing and querying
- Log levels: DEBUG, INFO, WARNING, ERROR, CRITICAL

### Key Metrics to Monitor

**QualGent3-Service**:
- Job queue size (alert if > 100)
- Worker process count
- Active jobs per worker
- Database connection pool utilization
- Job completion rate
- Job failure rate
- Average job duration

**QualGent-Emulator**:
- Emulator availability (alert if < 20%)
- Emulator health status
- CPU/memory utilization per instance
- VNC/Appium connection counts
- Average emulator uptime

**QualGent-Emulator-Manager**:
- Queue size (alert if > 500)
- Average wait time
- Allocation success rate
- Release frequency
- Scaling events

**AppiumCUA**:
- Test success rate
- Bug detection rate
- Average test duration
- Verification accuracy
- AI API latency (Claude, Gemini)

### Health Checks

**Service Health Endpoints**:
```bash
# QualGent3-Service
curl https://service.qualgent.com/health
# Returns: {"status":"healthy","database":"connected","workers":20,"active_jobs":5}

# Emulator-Manager
curl https://emulator-manager.qualgent.com/health
# Returns: {"status":"healthy","database":"connected"}

# Emulator (VNC health check)
curl http://EMULATOR_IP:6080
# Returns: 200 OK if noVNC running
```

**Kubernetes Probes** (QualGent3-Service):
```yaml
# Liveness Probe
livenessProbe:
  httpGet:
    path: /liveness
    port: 80
  initialDelaySeconds: 30
  periodSeconds: 10
  timeoutSeconds: 5
  failureThreshold: 3

# Readiness Probe
readinessProbe:
  httpGet:
    path: /health
    port: 80
  initialDelaySeconds: 5
  periodSeconds: 5
  timeoutSeconds: 3
  failureThreshold: 3
```

### Alerting

**Critical Alerts**:
1. Service down (5xx errors > 10/min)
2. Database connection failures
3. Emulator availability < 10%
4. Job queue > 1000
5. Worker process crashes
6. Disk space > 90%

**Warning Alerts**:
1. Emulator availability < 20%
2. Job queue > 500
3. High AI API latency (> 30s)
4. Job failure rate > 20%
5. Memory utilization > 80%

**Alert Channels**:
- Email: ops@qualgent.com
- Slack: #qualgent-alerts
- PagerDuty: On-call engineer (critical only)

### Log Analysis

**Query Examples** (Google Cloud Logging):

```bash
# Find errors in last hour
gcloud logging read "resource.type=k8s_container AND severity=ERROR AND timestamp>\"2025-10-16T10:00:00Z\"" --limit 100

# Find failed jobs
gcloud logging read "jsonPayload.job_status=\"failed\"" --limit 50

# Find slow tests (> 5 minutes)
gcloud logging read "jsonPayload.test_duration>300" --limit 50

# Find emulator allocation failures
gcloud logging read "jsonPayload.message=~\"emulator allocation failed\"" --limit 50
```

**Log Structure** (JSON):
```json
{
  "timestamp": "2025-10-16T10:30:00Z",
  "severity": "INFO",
  "job_id": "uuid",
  "test_run_id": "uuid",
  "worker_id": "worker-1",
  "message": "Test execution completed successfully",
  "duration": 120.5,
  "result": "passed"
}
```

### Performance Optimization

**Database**:
- Connection pooling: 96 connections, 80% utilization
- Per-process throttling: 3-4 concurrent operations
- Indexes on frequently queried columns

**Job Queue**:
- In-memory queue (no Redis overhead)
- Multi-process with semaphore locks
- Priority-based processing

**Emulator Pool**:
- Auto-scaling based on availability
- Pre-warmed instances (faster allocation)
- Health-based auto-healing

**AI API**:
- Context compaction to reduce token usage
- Streaming responses for faster feedback
- Retry logic with exponential backoff

### Backup & Disaster Recovery

**Database Backups** (Supabase):
- Automatic daily backups
- Point-in-time recovery (7 days)
- Manual snapshots before major deployments

**Configuration Backups**:
- Infrastructure as Code (Terraform state in GCS)
- Kubernetes manifests in Git
- Environment variables in Google Secret Manager

**Recovery Procedures**:
1. Service outage: Scale up replicas, check logs
2. Database corruption: Restore from backup
3. Emulator pool failure: Terraform destroy + apply
4. Data loss: Restore from Supabase backup

---

## Security Considerations

### Authentication & Authorization

**API Authentication**:
- Supabase: Service role key for elevated operations
- BrowserStack: Username + access key
- Anthropic: API key (Vertex AI service account)
- Google Cloud: Service account JSON key

**Secret Management**:
- Environment variables (local development)
- Google Secret Manager (production)
- Never commit secrets to Git (.gitignore)

**Service Accounts** (GCP):
- `vertex-ksa`: Vertex AI access for QualGent3-Service
- `emulator-manager-sa`: Emulator discovery and scaling
- Principle of least privilege (minimal permissions)

### Network Security

**Firewall Rules** (GCE):
- Allow: 22 (SSH), 6080 (VNC), 4723 (Appium), 8080 (WebRTC)
- Restrict: SSH only from authorized IPs
- Internal: GKE ↔ GCE communication via internal IPs

**CORS** (API):
- Production: Whitelist specific origins
- Development: Allow all (currently set)

**SSL/TLS**:
- HTTPS for all external endpoints
- Let's Encrypt certificates (k8s)
- CloudFlare SSL (optional)

### Data Protection

**PII Handling**:
- No PII in logs (sanitize sensitive data)
- Encryption at rest (Supabase)
- Encryption in transit (HTTPS/TLS)

**Database Security**:
- Row-level security (RLS) policies
- Service role key for internal operations only
- Regular security updates

**Container Security**:
- Non-root user in containers
- Minimal base images (slim, alpine)
- Regular image updates
- Vulnerability scanning

### Compliance

**Best Practices**:
- Regular security audits
- Dependency updates (Dependabot)
- Code reviews for all changes
- Access logs and audit trails
- Incident response plan

**Documentation**:
- See `docs/SECURITY_CHECKLIST.md` in each repository
- Update as new threats emerge

---

## Common Workflows

### Workflow 1: Running a Test

```bash
# Step 1: Submit test via API
curl -X POST https://service.qualgent.com/job/start \
  -H "Content-Type: application/json" \
  -d '{
    "test_run_id": "uuid",
    "test_case_id": "uuid",
    "app_file_id": "uuid",
    "client_id": "uuid",
    "user_id": "uuid"
  }'

# Returns: {"job_id": "job-uuid", "status": "queued"}

# Step 2: Check status
curl https://service.qualgent.com/job/job-uuid/status

# Returns: {"status": "running", "started_at": "2025-10-16T10:30:00Z"}

# Step 3: Wait for completion
# (Poll every 30 seconds)

# Step 4: Get results from Supabase
# Query test_runs table with test_run_id
# Download artifacts from qualgent-upload bucket
```

### Workflow 2: Scaling Emulator Pool

```bash
# Check current status
./QualGent-Emulator/scripts/manage-production.sh status

# Scale to 10 instances
./QualGent-Emulator/scripts/manage-production.sh scale 10

# Wait 5-10 minutes for instances to start

# Verify availability
curl https://emulator-manager.qualgent.com/emulator/stats

# Returns: {"total": 10, "available": 10, "allocated": 0}
```

### Workflow 3: Deploying a Hotfix

```bash
# QualGent3-Service hotfix example

# 1. Fix code locally
cd QualGent3-Service
# Make changes...

# 2. Test locally
pytest tests/ -v

# 3. Commit and push
git add .
git commit -m "Fix: Connection pool exhaustion under high load"
git push origin main

# 4. Build new Docker image
docker build -t backend-service:v1.0.1 .
docker tag backend-service:v1.0.1 \
  us-east1-docker.pkg.dev/YOUR_PROJECT/qualgent3/backend-service:v1.0.1
docker push us-east1-docker.pkg.dev/YOUR_PROJECT/qualgent3/backend-service:v1.0.1

# 5. Update k8s deployment
# Edit k8s/deployment.yaml: image tag to v1.0.1
kubectl apply -f k8s/deployment.yaml

# 6. Monitor rollout
kubectl rollout status deployment/backend-service -n qualgent3-service

# 7. Verify health
kubectl get pods -n qualgent3-service
curl https://service.qualgent.com/health

# 8. Monitor errors in Sentry
# Check for new errors in next 30 minutes

# 9. If issues, rollback
kubectl rollout undo deployment/backend-service -n qualgent3-service
```

### Workflow 4: Adding a New Test Case

```bash
# 1. Create test case in Supabase
# Insert into test_cases table with:
# - test_case_id (UUID)
# - name
# - instructions (natural language)
# - client_id
# - app_type (android/ios)

# 2. Upload APK/IPA to Supabase storage
# Upload to qualgent-upload bucket
# Note app_file_id (UUID)

# 3. Submit test via API
curl -X POST https://service.qualgent.com/job/start \
  -H "Content-Type: application/json" \
  -d '{
    "test_run_id": "new-uuid",
    "test_case_id": "test-case-uuid",
    "app_file_id": "app-file-uuid",
    "client_id": "client-uuid",
    "user_id": "user-uuid"
  }'

# 4. Monitor execution in real-time
# Query test_case_developer_logs table with test_run_id
# See step-by-step execution logs

# 5. Review results
# Check test_runs table for final result
# Download screenshots/videos from storage
# Read verification report
```

### Workflow 5: Investigating a Failed Test

```bash
# 1. Get test_run_id from failure notification

# 2. Query logs
gcloud logging read "jsonPayload.test_run_id=\"test-run-uuid\"" --limit 500

# 3. Check test_case_developer_logs in Supabase
# See step-by-step execution

# 4. Download screenshots
# From qualgent-upload bucket:
# {client_id}/{user_id}/{test_run_id}/screenshots/

# 5. Watch video
# Download from:
# {client_id}/{user_id}/{test_run_id}/videos/browserstack_{session_id}.mp4

# 6. Read verification report
# {client_id}/{user_id}/{test_run_id}/logs/verification_report.txt

# 7. Check bug detection
# Look for bugs in steps.json
# Check severity: CRITICAL, HIGH, MEDIUM, LOW

# 8. Reproduce locally (if needed)
cd AppiumCUA/android
pytest -s new_cua_android.py \
  --use-local-emulator \
  --test-run-id "test-run-uuid" \
  --no-reset-emulator \
  -k test_name
```

---

## Troubleshooting Guide

### Common Issues

#### Issue 1: "No available emulators"

**Symptoms**:
- Jobs stuck in queue
- `GET /emulator/stats` shows 0 available

**Diagnosis**:
```bash
# Check emulator status
curl https://emulator-manager.qualgent.com/emulator/stats

# Check GCE instances
gcloud compute instances list --filter="name~android-emulator"

# Check database
# Query emulators table in Supabase
```

**Solutions**:
1. Scale up emulator pool:
   ```bash
   ./QualGent-Emulator/scripts/manage-production.sh scale 10
   ```

2. Release stuck emulators:
   ```bash
   # In Supabase SQL Editor:
   SELECT * FROM free_emulator_from_job(NULL);  -- Force release all
   ```

3. Restart unhealthy instances:
   ```bash
   gcloud compute instances reset android-emulator-1 --zone=us-central1-a
   ```

#### Issue 2: "Job timeout"

**Symptoms**:
- Job status: "failed"
- Error: "Timeout after 3600 seconds"

**Diagnosis**:
```bash
# Check job logs
gcloud logging read "jsonPayload.job_id=\"job-uuid\"" --limit 100

# Check worker logs
kubectl logs -l app=backend-service -n qualgent3-service --tail=500
```

**Solutions**:
1. Increase timeout in config.py:
   ```python
   WORKER_TIMEOUT = 7200  # 2 hours
   ```

2. Optimize test case (reduce steps)

3. Check emulator performance:
   ```bash
   # SSH to emulator instance
   gcloud compute ssh android-emulator-1 --zone=us-central1-a

   # Check resources
   top
   docker stats
   ```

#### Issue 3: "Database connection pool exhausted"

**Symptoms**:
- Error: "Could not acquire connection from pool"
- Slow API responses

**Diagnosis**:
```bash
# Check Supabase dashboard for active connections
# Should be < 96 (pool size)

# Check per-process limits in logs
grep "connection throttling" logs.txt
```

**Solutions**:
1. Increase pool size in Supabase dashboard

2. Adjust per-process limits in config.py:
   ```python
   SB_DEFAULT_POOL_SIZE = 128
   SB_NUM_PROCESSES = 20
   ```

3. Check for connection leaks:
   ```bash
   # Look for unclosed connections
   grep "connection not returned" logs.txt
   ```

#### Issue 4: "AI API rate limit exceeded"

**Symptoms**:
- Error: "Rate limit exceeded for Claude API"
- Tests failing with 429 errors

**Diagnosis**:
```bash
# Check Sentry for rate limit errors
# Check Anthropic dashboard for usage

# Count API calls
grep "anthropic.messages.create" logs.txt | wc -l
```

**Solutions**:
1. Implement request queuing:
   ```python
   # Add rate limiter in agent_loop.py
   from tenacity import retry, wait_exponential

   @retry(wait=wait_exponential(multiplier=1, min=4, max=60))
   def call_claude_api(...):
       ...
   ```

2. Request rate limit increase from Anthropic

3. Use context compaction to reduce calls:
   ```bash
   # In .env
   CONTEXT_COMPACTION_ENABLED=true
   ```

#### Issue 5: "Emulator stuck at boot"

**Symptoms**:
- Emulator status: RUNNING
- Appium connection fails
- VNC shows Android boot animation

**Diagnosis**:
```bash
# SSH to instance
gcloud compute ssh android-emulator-1 --zone=us-central1-a

# Check emulator logs
sudo docker logs android-emulator | grep "emulator"

# Check if ADB sees device
adb devices
```

**Solutions**:
1. Restart emulator container:
   ```bash
   sudo docker restart android-emulator
   ```

2. Increase boot timeout:
   ```bash
   # In docker/start-emulator.sh
   # Wait 120 seconds instead of 60
   ```

3. Check hardware acceleration:
   ```bash
   # Verify KVM available
   ls -la /dev/kvm
   # Should show crw-rw----
   ```

4. Rebuild with clean state:
   ```bash
   sudo docker stop android-emulator
   sudo docker rm android-emulator
   sudo docker compose up -d
   ```

#### Issue 6: "Test verification failed"

**Symptoms**:
- Test marked as "failed" even though it looks correct
- Verification report shows low accuracy

**Diagnosis**:
```bash
# Review verification report
# {client_id}/{user_id}/{test_run_id}/logs/verification_report.txt

# Check verification JSON
# {client_id}/{user_id}/{test_run_id}/json/verification_analysis.json

# Watch video manually
# {client_id}/{user_id}/{test_run_id}/videos/browserstack_{session_id}.mp4
```

**Solutions**:
1. Adjust verification prompt (if too strict):
   ```python
   # In cua/tools/verification_providers/gemini_provider.py
   # Modify verification instructions
   ```

2. Re-run verification only:
   ```bash
   cd AppiumCUA
   python -c "from cua.tools.verification_providers import verify_test; verify_test('test-run-uuid')"
   ```

3. Manual override in Supabase:
   ```sql
   UPDATE test_runs
   SET result = 'passed',
       verification_override = true
   WHERE test_run_id = 'test-run-uuid';
   ```

### Debugging Tools

**View Service Logs**:
```bash
# QualGent3-Service (GKE)
kubectl logs -f deployment/backend-service -n qualgent3-service

# Emulator-Manager (Cloud Run)
gcloud run services logs read emulator-manager --region us-central1

# Emulator (GCE)
gcloud compute ssh android-emulator-1 --zone=us-central1-a
sudo docker logs -f android-emulator
```

**Interactive Debugging**:
```bash
# SSH to GKE pod
kubectl exec -it backend-service-xxx -n qualgent3-service -- /bin/bash

# SSH to GCE instance
gcloud compute ssh android-emulator-1 --zone=us-central1-a

# Access emulator via ADB
adb connect EMULATOR_IP:5555
adb shell
```

**Database Queries** (Supabase SQL Editor):
```sql
-- View all jobs
SELECT * FROM jobs ORDER BY created_at DESC LIMIT 50;

-- View queued jobs
SELECT * FROM jobs WHERE status = 'queued';

-- View emulator status
SELECT emulator_name, emulator_ip, status, availability, assigned_job_id
FROM emulators;

-- View test runs
SELECT * FROM test_runs ORDER BY created_at DESC LIMIT 50;

-- Find failed tests
SELECT * FROM test_runs WHERE result = 'failed' ORDER BY created_at DESC;
```

---

## Key Contacts & Resources

### Team Contacts

**Engineering**:
- Lead Engineer: [Name] - [Email]
- Backend Team: backend@qualgent.com
- DevOps Team: devops@qualgent.com

**On-Call**:
- Primary: [Name] - [Phone]
- Secondary: [Name] - [Phone]
- Escalation: [Name] - [Phone]

### External Vendors

**Cloud Providers**:
- Google Cloud Platform: https://cloud.google.com/support
- Supabase: support@supabase.io

**AI Providers**:
- Anthropic: support@anthropic.com
- Google AI: https://cloud.google.com/ai/contact

**Testing Services**:
- BrowserStack: support@browserstack.com

**Monitoring**:
- Sentry: support@sentry.io

### Documentation Links

**Internal**:
- Architecture Diagrams: [Confluence/Wiki URL]
- Runbook: [URL]
- Incident Response: [URL]
- On-Call Schedule: [PagerDuty URL]

**External**:
- FastAPI: https://fastapi.tiangolo.com/
- Appium: https://appium.io/docs/en/latest/
- Anthropic Claude: https://docs.anthropic.com/
- Google Cloud: https://cloud.google.com/docs
- Supabase: https://supabase.com/docs
- Terraform: https://www.terraform.io/docs

### GitHub Repositories

- QualGent3-Service: https://github.com/QualGent/QualGent3-Service
- AppiumCUA: https://github.com/QualGent/AppiumCUA
- QualGent-Emulator: https://github.com/QualGent/QualGent-Emulator
- QualGent-Emulator-Manager: https://github.com/QualGent/QualGent-Emulator-Manager

### Communication Channels

**Slack Channels**:
- #engineering: General engineering discussion
- #qualgent-alerts: Automated alerts
- #qualgent-deployments: Deployment notifications
- #incidents: Incident response
- #on-call: On-call engineer coordination

**Meetings**:
- Engineering All-Hands: Weekly, Mondays 10am
- Platform Sync: Daily standup, 9am
- On-Call Handoff: Weekly, Fridays 4pm

---

## Appendix: Quick Reference

### Essential Commands

**Service Health**:
```bash
# All services
curl https://service.qualgent.com/health
curl https://emulator-manager.qualgent.com/health

# GKE status
kubectl get pods -n qualgent3-service

# GCE instances
gcloud compute instances list

# Cloud Run services
gcloud run services list
```

**Deployment**:
```bash
# QualGent3-Service
kubectl apply -f k8s/deployment.yaml
kubectl rollout status deployment/backend-service -n qualgent3-service

# Emulator
cd QualGent-Emulator
./scripts/deploy-production.sh

# Emulator-Manager
cd QualGent-Emulator-Manager
./deploy_to_cloud_run.sh
```

**Scaling**:
```bash
# Service replicas
kubectl scale deployment backend-service --replicas=5 -n qualgent3-service

# Emulator pool
./QualGent-Emulator/scripts/manage-production.sh scale 10
```

**Logs**:
```bash
# Service logs
kubectl logs -f deployment/backend-service -n qualgent3-service

# Emulator logs
gcloud compute ssh android-emulator-1 -- sudo docker logs -f android-emulator

# Cloud logs
gcloud logging read "severity=ERROR" --limit 50
```

### Environment Variables Cheat Sheet

**QualGent3-Service**:
```
SUPABASE_URL, SUPABASE_KEY, SUPABASE_SERVICE_ROLE_KEY
BROWSERSTACK_USERNAME, BROWSERSTACK_ACCESS_KEY
VERTEX_AI_PROJECT_ID, VERTEX_AI_REGION
MAX_WORKERS=20, WORKER_TIMEOUT=3600, JOB_QUEUE_SIZE=1000
SENTRY_DSN
```

**AppiumCUA**:
```
ANTHROPIC_API_KEY
GOOGLE_API_KEY
BROWSERSTACK_USERNAME, BROWSERSTACK_ACCESS_KEY
SUPABASE_URL, SUPABASE_KEY, SUPABASE_SERVICE_ROLE_KEY
CLAUDE_MODEL_VERSION=4
AI_PROVIDER=vertex
```

**QualGent-Emulator**:
```
DEVICE=Samsung Galaxy S10
SCREEN_WIDTH=1080, SCREEN_HEIGHT=1920
VNC_PASSWORD=android
EMULATOR_MEMORY=6096, EMULATOR_CORES=4
APPIUM_PORT=4723
```

**QualGent-Emulator-Manager**:
```
SUPABASE_URL, SUPABASE_KEY, SUPABASE_SERVICE_ROLE_KEY
GOOGLE_APPLICATION_CREDENTIALS
SCALE_UP_COUNT=5, SCALING_THRESHOLD=20, MAX_WAIT_TIME=300
HOST=0.0.0.0, PORT=8000
```

### Port Reference

| Service | Port | Protocol | Purpose |
|---------|------|----------|---------|
| QualGent3-Service | 80/443 | HTTP/HTTPS | API endpoints |
| Emulator (VNC) | 6080 | HTTP | Web-based VNC |
| Emulator (VNC direct) | 5900 | VNC | Direct VNC access |
| Emulator (Appium) | 4723 | HTTP | Appium server |
| Emulator (ADB) | 5555 | TCP | ADB connection |
| Emulator (ADB server) | 5037 | TCP | ADB daemon |
| Emulator (gRPC) | 8554 | gRPC | Emulator gRPC |
| Emulator (WebRTC) | 8080 | HTTP | WebRTC frontend |
| Emulator (Envoy) | 8555 | HTTP | gRPC-Web proxy |
| Emulator-Manager | 8000 | HTTP | API endpoints |

---

## Conclusion

This document provides a comprehensive overview of the QualGent platform architecture, technology stack, and operational procedures. As a new hire, you should:

1. **Week 1**: Set up development environment, run local tests, explore codebases
2. **Week 2**: Deploy services locally, understand data flow, review logs
3. **Week 3**: Deploy to staging/test environments, monitor metrics, practice troubleshooting
4. **Week 4**: Shadow on-call engineer, participate in deployments, contribute first bug fix

**Remember**:
- Always test changes locally before deploying
- Monitor logs and metrics after deployments
- Ask questions in #engineering Slack channel
- Document any issues or improvements you discover

**Welcome to the QualGent team!**

---

**Document Maintenance**:
- Update this document when architecture changes
- Keep environment variables current
- Add new troubleshooting scenarios as discovered
- Review quarterly for accuracy

**Last Reviewed**: October 2025

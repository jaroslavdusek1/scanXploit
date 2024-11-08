# scanXploit

scanXploit 
The goal of this project is to create a security tool that scans devices in a local network, identifies open ports, determines running services and their versions, and checks for known vulnerabilities in the software. The tool will produce a vulnerability report that can be used by network administrators to assess potential risks.

Folder Structure:
```
project-root/
│
├── frontend/                       # React frontend (UI)
│   ├── public/
│   ├── src/
│   │   ├── components/             # Reusable UI components
│   │   ├── pages/                  # Main pages (Dashboard, Reports, etc.)
│   │   ├── utils/                  # Shared utilities (API calls, formatting)
│   │   └── App.tsx                 # Main React component
│   └── package.json                # Frontend dependencies
│
├── backend/                        # Python backend
│   ├── app/
│   │   ├── controllers/            # REST API request handlers
│   │   ├── services/               # Business logic (Nmap, vulnerabilities, Metasploit)
│   │   ├── models/                 # Database models
│   │   ├── tasks/                  # Asynchronous task handling (Celery)
│   │   ├── schemas/                # Input validation schemas
│   │   ├── utils/                  # Shared utility functions
│   │   └── app.py                  # Entry point for FastAPI
│   ├── database/                   # PostgreSQL configuration
│   │   ├── migrations/             # Database migrations
│   │   └── schemas/                # Database table definitions
│   ├── scanner/                    # Scanning logic
│   │   ├── nmap/                   # Nmap integration
│   │   ├── metasploit/             # Metasploit integration
│   │   └── scannerService.py       # Main scanning logic
│   ├── tests/                      # Unit and integration tests
│   └── requirements.txt            # Backend dependencies
│
├── docker/                         # Docker setup
│   └── docker-compose.yml
│
└── README.md                       # Project documentation
```

## Frontend (React + TypeScript)
To provide a clean, user-friendly interface for administrators to manage scans, view results, and generate reports

## Key Features:
Dashboard: Displays discovered devices, open ports, and vulnerabilities<br>Scan Management: Form for entering network details and initiating scans<br>Reports: View and download detailed reports in PDF/CSV format

## Technologies:
React: Component-based structure for a responsive UI<br>
TypeScript: Ensures type safety and maintainable code<br>
Material UI: For a modern, clean look and feel<br>
Axios: For HTTP requests to the backend API<br>

## Backend (Python + FastAPI)
Handles scanning requests, processes results, interacts with external tools like Nmap and Metasploit, and manages data storage

## Key Features
API Endpoints:
POST /scan: Initiates a scan on the specified IP range<br>
GET /scan/results: Retrieves scan results<br>
GET /scan/report: Generates and downloads a vulnerability report<br>
Integration with Nmap: Executes scans, processes results, and detects services<br>
Vulnerability Analysis: Queries external APIs like Vulners or CIRCL CVE to assess risks<br>
Exploit Attempt: Uses Metasploit for exploit testing on vulnerable services<br>
Task Queue: Asynchronous task handling for long-running scans using Celery<br>

## Technologies
FastAPI: Lightweight and high-performance web framework.<br>
Subprocess Module: For executing Nmap and Metasploit commands<br>
Celery: For managing background tasks (e.g., scanning)<br>
Redis: Task queue backend for Celery<br>
SQLAlchemy: ORM for database interactions<br>
FPDF: For generating PDF reports<br>

## Scanner Engine
Purpose:
Performs network discovery, vulnerability scanning, and exploitation testing.

## Components:
Nmap Integration:<br>
Scans IP ranges for devices, open ports, and service versions<br>
Command example: nmap -sS -A <IP_RANGE><br>
Vulnerability Checker:<br>
Matches service versions against vulnerability databases (e.g., Vulners API)<br>
Identifies CVEs, severity, and potential exploits<br>
Metasploit Integration<br>
Executes exploits on detected vulnerabilities<br>
Verifies access using generated payloads<br>
Result Processor:<br>
Formats scan results and enriches them with vulnerability data<br>
Database (PostgreSQL)<br>
Purpose:<br>
Store scan results, device details, and vulnerability data for reporting and analysis

Schema Design:
Devices Table:
id: Primary Key
ip_address: Device IP
os: Detected operating system
mac_address: Device MAC address

Services Table:
id: Primary Key
device_id: Foreign Key (Devices Table)
port: Open port
service_name: Service running on the port
version: Detected service version

Vulnerabilities Table:
id: Primary Key
service_id: Foreign Key (Services Table)
cve_id: CVE identifier
description: Vulnerability description
severity: Severity level (e.g., High, Medium, Low)
Detailed Workflow

Scan Initialization:
The user starts a scan via the React UI, providing an IP range.
The backend creates a Celery task to run the scan asynchronously.

Network Scanning:
Nmap scans the IP range for active devices and open ports.
Detected services are analyzed for version and OS detection.

Vulnerability Lookup:
Each service version is checked against vulnerability databases for known CVEs.
Metadata (CVE ID, severity, and description) is retrieved.

Exploit Testing:
Vulnerable services are tested using Metasploit to verify exploitability.

Report Generation:
A detailed report is generated with device details, vulnerabilities, and recommended fixes.
Reports are available for download as PDF or CSV.

Result Display:
The frontend displays a summary of devices, services, and vulnerabilities in an interactive dashboard.

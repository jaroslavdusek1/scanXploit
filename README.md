# scanXploit

scanXploit 
The goal of this project is to create a security tool that scans devices in a local network, identifies open ports, determines running services and their versions, and checks for known vulnerabilities in the software. The tool will produce a vulnerability report that can be used by network administrators to assess potential risks.

Folder Structure:
```
project-root/
│
├── frontend/                       # React frontend (Optional)
│   ├── public/
│   ├── src/
│   │   ├── components/             # Reusable UI components
│   │   ├── pages/                  # Main pages (Dashboard, Reports, etc.)
│   │   └── App.tsx                 # Main React component
│   └── package.json                # Frontend dependencies
│
├── backend/                        # Node.js backend
│   ├── src/
│   │   ├── controllers/            # API request handlers (e.g., start scan, get results)
│   │   ├── services/               # Business logic for scanning and vulnerability lookup
│   │   ├── models/                 # Database models (for PostgreSQL)
│   │   ├── routes/                 # API routes (e.g., /scan, /results)
│   │   └── server.ts               # Entry point for the backend
│   └── package.json                # Backend dependencies
│
├── scanner/                        # Nmap + Vulnerability scanning engine
│   ├── nmap-scripts/               # Scripts to run Nmap scans
│   ├── vulnerability-checker/      # Queries to Vulners/CIRCL API
│   ├── scanProcessor.ts            # Logic to process scan results and match vulnerabilities
│   └── scannerService.ts           # Service to handle scan lifecycle
│
├── database/                       # PostgreSQL configurations and schema definitions
│   ├── schemas/                    # Table definitions (devices, services, vulnerabilities)
│   └── migrations/                 # Database migrations
│
├── docker/                         # Docker setup files
│   └── docker-compose.yml
└── README.md                       # Project overview and setup instructions
```


High-Level Architecture Overview:
1. Frontend (React + TypeScript) [Optional]:
    * Purpose: Provide a user-friendly web interface where administrators can initiate scans, view scan results, and download reports.
    * Key Features:
        * Dashboard for listing scanned devices, open ports, and vulnerabilities.
        * Report generation and download (in formats like PDF or CSV).
        * Easy navigation for scanning and managing the network. 
2. Backend (Node.js + Express + TypeScript):
    * Purpose: Serve as the core of the system, handling scan requests, processing results, interacting with the scanner engine, and storing scan data.
    * Key Features:
        * API endpoints for controlling the scan process and fetching results.
        * Interacts with Nmap for scanning devices and services.
        * Cross-references service versions with known vulnerabilities using an external vulnerability database API (e.g., Vulners API or CIRCL CVE Search API).
        * Generates vulnerability reports. 
3. Scanner Engine (Nmap + Vulnerability Checker):
    * Purpose: Conduct the actual network scanning and vulnerability assessment.
    * Key Features:
        * Nmap: Detects devices, open ports, and service versions on the network.
        * Vulnerability Checker: Interacts with vulnerability databases (via APIs) to check known issues based on the identified software versions.
        * Nmap Output Processor: Processes the results from Nmap and formats them for the backend. 
4. Data Storage (PostgreSQL):
    * Purpose: Store information about past scans, devices, and vulnerabilities in a relational format.
    * Key Features:
        * Stores scan results (IP addresses, services, ports, versions).
        * Tracks vulnerability data associated with each service version.
        * Enables query and report generation from stored scan data.

Technologies:
1. Frontend (Optional):
    * React: Component-based architecture for a dynamic and responsive user interface.
    * TypeScript: Strong typing for safer and maintainable code.
    * Material UI or Tailwind CSS: To build responsive and modern-looking UI components. 
2. Backend:
    * Node.js: JavaScript runtime for handling asynchronous I/O, which is ideal for a network scanning service.
    * Express.js: Lightweight framework for routing and handling API requests.
    * TypeScript: Provides static type checking, improving reliability.
    * Nmap: A powerful network scanning tool for detecting devices, open ports, services, and service versions.
    * Vulners API or CIRCL CVE Search API: For looking up known vulnerabilities (CVE) based on service versions.
    * PDFKit or jsPDF: For generating downloadable reports in PDF format.
    * CSV parser: For generating downloadable CSV reports. 
3. Database:
    * PostgreSQL: A robust relational database for storing structured data such as devices, services, versions, and vulnerabilities. Efficient querying and complex data relations make it suitable for storing and managing scan results. 
4. Other Tools:
    * Docker: For containerizing the application, making it easier to deploy and run across different environments.
    * Nodemailer: If email reports are needed, you can use this to send scan results directly to the administrator.
 
Detailed Architecture:
Frontend:
* Components:
    * Dashboard: Displays all devices in the network, their open ports, services, and detected vulnerabilities.
    * Scan Initiation Page: A form that allows the user to input network details (e.g., IP range) and start a new scan.
    * Reports Page: Allows users to view and download previous scan reports (PDF/CSV). 
Backend:
* API Routes:
    * POST /scan: Initiates a new network scan.
    * GET /scan/results: Retrieves the results of the latest scan, including details on devices, open ports, services, and vulnerabilities.
    * GET /scan/report: Generates and downloads the scan report (PDF/CSV). 
* Services (Business Logic):
    * Nmap Service: Interacts with the Nmap command-line tool to run network scans and collect data about services and ports.
    * Vulnerability Service: Queries external APIs (e.g., Vulners, CIRCL CVE Search) for known vulnerabilities based on service versions.
    * Scan Processor: Parses and processes raw Nmap output, enriching it with vulnerability information. 
* Controllers (Routes Handlers):
    * Manages the request-response cycle for each API endpoint.
    * Ensures scan requests are sent to the scanner engine and formatted results are sent back to the frontend. 
Scanner Engine:
* Nmap Integration:
    * Uses Nmap to perform a scan of the specified network range (nmap -sV 192.168.0.1/24).
    * Detects IP addresses, open ports, and services running on those ports. 
* Vulnerability Lookup:
    * Takes the detected services and their versions from Nmap and queries the Vulners API or CIRCL CVE Search API for known vulnerabilities (CVE IDs, descriptions, and severity scores). 
* Scan Processor:
    * Combines the data from Nmap with vulnerability information.
    * Prepares this combined data for storage in the PostgreSQL database and returns it to the backend. 
Database (PostgreSQL):
* Tables:
    * Devices: Stores IP addresses, MAC addresses, and other relevant network information for each scanned device.
    * Services: Stores open ports, running services, and detected software versions for each device.
    * Vulnerabilities: Maps CVEs to specific services and versions. Includes vulnerability severity, CVE description, and links to external databases.

User Workflow:
1. Initiating a Scan:
    * A user initiates a scan through the React interface (or directly via API in the case of a terminal-only tool).
    * The backend triggers the Nmap Service, which scans the specified IP range for open ports and services.
2. Processing Results:
    * The scan results are passed to the Vulnerability Service, which queries external APIs to check for known vulnerabilities based on detected software versions.
    * The Scan Processor organizes this data and prepares it for storage.
3. Displaying and Reporting:
    * The results are displayed on the frontend in a structured manner (grouped by device, service, and vulnerability).
    * The user can download a detailed report in PDF or CSV format.


# Bulk-Certificate-Generator-API-
Assignment Submission 


Project Structure

text
bulk-certificate-generator/
│
├── app/
│   ├── __init__.py          # Application factory initialization
│   ├── certificate.py      # PDF/Image certificate generation logic
│   ├── config.py           # Application configurations (threads, DB paths)
│   ├── db.py               # In-memory mock database store for tracking job statuses
│   ├── routes.py           # API endpoints routing definitions
│   ├── services.py         # Business logic layer and background task processing queue
│   └── validation.py       # Input validation schemas and logic
│
├── tests/
│   ├── __init__.py
│   ├── base.py             # Setup for test fixtures and test client configuration
│   ├── test_create_job.py
│   ├── test_failure_handling.py
│   ├── test_generation.py
│   ├── test_job_status.py
│   ├── test_retrieval.py
│   └── test_validation.py
│
├── requirements.txt         # Project dependencies
├── run.py                  # Entrypoint to run the Flask application
└── sample_request.json      # Sample JSON request body payload


How to Set Up the Project

Follow these steps to set up the development environment:
1. Clone the repository and navigate to the project root directory:bash
cd bulk-certificate-generator

2. Create a virtual environment (Python 3.8+ recommended):bash
python -m venv venv

3. Activate the virtual environment:
	• On macOS/Linux:bash
source venv/bin/activate

	• On Windows (Command Prompt):cmd
venv\Scripts\activate

	• On Windows (PowerShell):powershell
.\venv\Scripts\Activate.ps1

4. Install the required dependencies:bash
pip install -r requirements.txt


How to Run the Application

To start the development server, run the run.py script from the root directory:
bash
python run.py
Use code with caution.
The application will spin up a Flask development server and starts the background worker threads. By default, it will be accessible at:
text
http://127.0.0.1:5000


How to Run Tests

The test suite covers validation logic, happy paths, asynchronous processing, job status transitions, and error handling behaviors.
To run all unit and integration tests, execute the following command in the root folder:
bash
pytest
Use code with caution.
To run a specific test file or check for test coverage details:
bash
pytest tests/test_generation.py
Use code with caution.

How to Submit a Certificate Generation Request

To start a new bulk certificate generation process, submit a POST request containing candidate data and template instructions.
• Endpoint: POST /api/v1/certificates/generate
• Headers: Content-Type: application/json
• Sample Payload (sample_request.json):json
{
  "template_id": "template_gold_2026",
  "certificates": [
    {
      "recipient_name": "Jane Doe",
      "recipient_email": "jane.doe@example.com",
      "course_name": "Advanced Software Architecture",
      "issue_date": "2026-10-08"
    },
    {
      "recipient_name": "John Smith",
      "recipient_email": "john.smith@example.com",
      "course_name": "Asynchronous Backend Design",
      "issue_date": "2026-10-08"
    }
  ]
}

• Example Request Using curl:bash
curl -X POST http://127.0.0 \
     -H "Content-Type: application/json" \
     -d @sample_request.json
Use code with caution.
• Expected Response (202 Accepted):json
{
  "status": "success",
  "message": "Certificate generation job successfully initialized.",
  "job_id": "8f2d4e8c-8431-4b71-9f9b-6dbb5aefce76"
}


How to Retrieve Generated Certificates

Because the processing happens asynchronously in the background, you must poll the status endpoint to verify completion before downloading individual artifacts.

1. Check Job Status

• Endpoint: GET /api/v1/certificates/jobs/<job_id>
• Expected Response (While Processing):json
{
  "job_id": "8f2d4e8c-8431-4b71-9f9b-6dbb5aefce76",
  "status": "processing",
  "progress": {
    "total": 2,
    "completed": 1,
    "failed": 0
  }
}

• Expected Response (When Completed):json
{
  "job_id": "8f2d4e8c-8431-4b71-9f9b-6dbb5aefce76",
  "status": "completed",
  "progress": {
    "total": 2,
    "completed": 2,
    "failed": 0
  },
  "generated_certificates": [
    {
      "recipient_email": "jane.doe@example.com",
      "certificate_id": "cert_abc_123",
      "download_url": "/api/v1/certificates/download/cert_abc_123"
    },
    {
      "recipient_email": "john.smith@example.com",
      "certificate_id": "cert_xyz_456",
      "download_url": "/api/v1/certificates/download/cert_xyz_456"
    }
  ]
}

2. Download a Certificate File

Use the specific download_url provided in the status response to fetch the generated file.
• Endpoint: GET /api/v1/certificates/download/<certificate_id>
• Example Request:bash
curl -O http://127.0.0

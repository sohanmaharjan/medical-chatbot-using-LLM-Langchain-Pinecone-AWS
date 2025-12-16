# Medical Chatbot with RAG Architecture

## Project Overview

A medical chatbot application built using Retrieval-Augmented Generation (RAG) architecture, deployed on AWS with CI/CD pipeline integration. The chatbot processes medical documentation to provide accurate, context-aware responses to medical queries.

## Architecture

### RAG Pipeline
```
Medical Book (PDF) → Document Extraction → Chunking → Embedding → Vector Database (Pinecone) → LLM (OpenAI GPT)
```

### Tech Stack

1. **Language**: Python
2. **LLM**: OpenAI GPT
3. **Orchestration**: LangChain
4. **Vector Database**: Pinecone
5. **Backend**: Flask
6. **Deployment**: AWS (EC2, ECR) with CI/CD pipeline

## System Architecture

### RAG Implementation

**Document Processing Flow:**
- Medical book is loaded as PDF files
- Documents are extracted and parsed
- Text is broken into smaller chunks (optimized to be smaller than the model's context window)
- Chunks are converted into vector embeddings
- Embeddings are stored in Pinecone Vector Database for retrieval

**Query Flow:**
- User submits a medical query through Flask frontend
- Query is embedded into vectors
- Similar chunks are retrieved from Pinecone
- Retrieved context is passed to LLM (GPT model)
- LLM generates response based on retrieved medical knowledge

### Deployment Architecture

```
GitHub Repository → GitHub Actions (CI/CD) → Docker Image → AWS ECR → AWS EC2 → Flask Application
```

## Development Process

### Setup Steps

1. **Create GitHub Repository**
   - Initialize project repository
   - Set up version control

2. **Project Template**
   - Create standardized project structure
   - Set up configuration files

3. **Project Setup**
   - Configure virtual environment
   - Install required packages and libraries
   - Set up `.gitignore` for sensitive files

4. **Jupyter Notebook Experimentation**
   - Prototype RAG pipeline
   - Test document processing
   - Validate embedding generation
   - Experiment with LLM responses
   - *Best practice: Always experiment in notebooks before implementing modular code*

5. **Modular Code Development**
   - Refactor notebook code into modules
   - Implement proper error handling
   - Add logging and monitoring

6. **Flask Web Application**
   - Create frontend interface
   - Build API endpoints
   - Integrate backend with RAG pipeline

7. **Deployment**
   - Containerize application with Docker
   - Set up AWS infrastructure
   - Configure CI/CD pipeline

### Why Jupyter Notebooks First?

**Benefits of trials using `ipynb`:**
1. Rapid and experimental development environment
2. Easy data exploration and visualization
3. Iterative model experimentation and development
4. Easy to track what worked and what didn't
5. Quick prototyping before production code

## AWS Deployment Guide

### Prerequisites

- AWS Account with appropriate permissions
- IAM user with specific permissions for EC2 and ECR
- GitHub repository with project code
- Docker installed locally

### Deployment Steps

#### 1. Create IAM User
- Navigate to AWS IAM Console
- Create new IAM user with programmatic access
- Assign specific permissions:
  - **EC2**: Virtual machine access
  - **ECR**: Elastic Container Registry access (to store Docker images)

#### 2. Save AWS Credentials
- Store Access Key ID and Secret Access Key securely
- Never commit credentials to version control

#### 3. Create ECR Repository
- Navigate to AWS ECR Console
- Create new repository for Docker images
- Note the repository URI

#### 4. Create EC2 Instance (Ubuntu)
- Launch EC2 instance with Ubuntu AMI
- Configure security groups:
  - Open port 8080 (or custom port for Flask app)
  - Configure inbound rules for HTTP access
- Save `.pem` key file for SSH access

#### 5. Configure EC2 Instance
- Connect to EC2 via SSH terminal
- Install required dependencies
- Set up deployment configuration

#### 6. Connect ECR and GitHub Repository
- Configure GitHub Actions workflow
- Set up secrets in GitHub:
  - AWS credentials
  - ECR repository details
  - EC2 connection details

#### 7. Add Secrets and Access Keys to GitHub
- Navigate to GitHub repository settings
- Add repository secrets:
  - `AWS_ACCESS_KEY_ID`
  - `AWS_SECRET_ACCESS_KEY`
  - `AWS_REGION`
  - `ECR_REPOSITORY`

#### 8. Create Dockerfile and CI/CD Configuration
- Write `Dockerfile` for application containerization
- Create `.github/workflows/main.yml` for CI/CD pipeline

#### 9. Commit and Sync Changes
- Push all changes to GitHub repository
- CI/CD pipeline triggers automatically

#### 10. Pipeline Execution
- GitHub Actions detects changes
- Builds Docker image
- Pushes image to ECR
- Deploys to EC2 instance

#### 11. Access the Application
- Open EC2 instance public IP address
- Add configured port to security groups
- Access application: `http://<EC2-IP>:8080`

### CI/CD Pipeline Flow

```
Code Push to GitHub → GitHub Actions Triggered → Build Docker Image → Push to ECR → Pull on EC2 → Deploy Flask App → Application Live
```

## Challenges and Solutions

### Issue 1: Conda Environment Not Working in Bash
**Problem**: Conda environment wasn't working when called in bash terminal. Anaconda prompt showed the conda environment properly.

**Root Cause**: The conda path was not defined in the system's environment variables.

**Solution**: 
- Copied the conda path from Anaconda prompt
- Added it to system environment variables
- Created a new conda environment path
- Restarted terminal for changes to take effect

### Issue 2: Pinecone `get_index` Attribute Error
**Problem**: `AttributeError: 'Pinecone' object has no attribute 'get_index'`

**Root Cause**: Method for accessing Pinecone index changed in recent versions (v3.0.0 and later).

**Solution**:
- Updated code to use `Index` instead of `get_index`
- Verified compatibility with Pinecone version
- Tested index access with new method

### Issue 3: EC2 IP Address Not Working
**Problem**: Flask application deployed successfully via CI/CD, but EC2 IP address wasn't accessible. Custom port 8080 was added to security groups but still didn't work.

**Attempted Solutions**:
1. Added port 8080 to security groups (custom TCP)
2. Verified CI/CD pipeline completed successfully
3. Checked deployment logs

**Resolution Steps Taken**:
- Changed local network settings
- Modified firewall settings
- Created new EC2 instance and re-ran CI/CD pipeline multiple times
- *Final outcome: Issue persisted despite multiple attempts*

### Issue 4: Continuous Integration Failed
**Problem**: AWS region didn't match, container couldn't store Docker image.

**Solution**: Continuous deployment was successfully integrated and deployed after fixing region mismatch.

### Issue 5: CI/CD Pipeline Naming Convention
**Problem**: Pipeline failed due to incorrect file naming.

**Root Cause**: Dockerfile was named incorrectly (used lowercase 'dockerfile' instead of 'Dockerfile').

**Solution**: Renamed file to 'Dockerfile' with capital 'D'.

### Issue 6: Project Worked Locally but Not on EC2
**Problem**: Application ran successfully on localhost but didn't work when deployed to EC2 instance IP address.

**Debugging Attempts**:
- Changed local network settings
- Tried modifying firewall settings
- No resolution found

## Project Structure

```
medical-chatbot/
├── .github/
│   └── workflows/
│       └── main.yml          # CI/CD pipeline configuration
├── src/
│   ├── __init__.py
│   ├── helper.py             # Utility functions
│   ├── prompt.py             # LLM prompt templates
│   └── model.py              # LLM and embedding models
├── notebooks/
│   └── experiments.ipynb     # Prototyping and testing
├── data/
│   └── medical_book.pdf      # Source medical documentation
├── static/                   # Flask static files
├── templates/                # Flask HTML templates
├── app.py                    # Flask application
├── Dockerfile                # Docker configuration
├── requirements.txt          # Python dependencies
├── .env.example              # Environment variables template
└── README.md                 # Project documentation
```

## Environment Variables

Create a `.env` file with the following variables:

```
OPENAI_API_KEY=your_openai_api_key
PINECONE_API_KEY=your_pinecone_api_key
```

## Installation and Local Setup

### Prerequisites
- Python 3.8+
- pip
- Virtual environment tool (venv or conda)

### Setup Instructions

1. **Clone the repository**
```bash
git clone <repository-url>
cd medical-chatbot
```

2. **Create virtual environment**
```bash
conda activate medical-chatbot
```

3. **Install dependencies**
```bash
pip install -r requirements.txt
```

4. **Set up environment variables**
```bash
cp .env.example .env
# Edit .env with your API keys
```

5. **Run the application**
```bash
python app.py
```

6. **Access the application**
- Open browser and navigate to `http://localhost:8000`

## Usage

1. Access the web interface
2. Enter your medical query in the text field
3. Click submit to get AI-generated response
4. The chatbot retrieves relevant information from the medical knowledge base
5. Response is generated using GPT model with retrieved context

## Future Improvements

1. **Deployment**: Resolve EC2 IP address accessibility issue
2. **Security**: Implement proper authentication and authorization
3. **Scalability**: Add load balancing and auto-scaling
4. **Monitoring**: Integrate CloudWatch for application monitoring
5. **Database**: Add user query history and feedback system
6. **UI/UX**: Enhance frontend interface with modern design
7. **Testing**: Add comprehensive unit and integration tests
8. **Documentation**: Add API documentation with Swagger/OpenAPI

## Lessons Learned

1. **Environment Setup**: Always verify environment paths and system variables
2. **Version Compatibility**: Keep track of library version changes and update code accordingly
3. **Deployment Testing**: Test deployment in staging environment before production
4. **Network Configuration**: Properly configure security groups and firewall rules
5. **Naming Conventions**: Follow standard naming conventions (e.g., Dockerfile vs dockerfile)
6. **Experimentation First**: Use Jupyter notebooks for rapid prototyping before production code
7. **Documentation**: Document issues and solutions for future reference

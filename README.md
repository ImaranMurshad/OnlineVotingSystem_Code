# 🗳️ Online Voting System — DevOpsSec

![Python](https://img.shields.io/badge/Python-3.x-3776AB?style=for-the-badge&logo=python&logoColor=white)
![Django](https://img.shields.io/badge/Django-6.0.3-092E20?style=for-the-badge&logo=django&logoColor=white)
![AWS EC2](https://img.shields.io/badge/AWS-EC2-FF9900?style=for-the-badge&logo=amazonaws&logoColor=white)
![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-CI%2FCD-2088FF?style=for-the-badge&logo=githubactions&logoColor=white)
![SQLite](https://img.shields.io/badge/SQLite-003B57?style=for-the-badge&logo=sqlite&logoColor=white)
![Nginx](https://img.shields.io/badge/Nginx-009639?style=for-the-badge&logo=nginx&logoColor=white)

> A secure, role-based online voting platform built with Django, deployed on AWS EC2 with a fully automated GitHub Actions CI/CD pipeline — developed as part of the **DevOpsSec** module.

---

## 📋 Table of Contents

- [Overview](#-overview)
- [Features](#-features)
- [Tech Stack](#-tech-stack)
- [Project Structure](#-project-structure)
- [User Roles](#-user-roles)
- [Getting Started](#-getting-started)
- [Environment Setup](#-environment-setup)
- [Running the Project](#-running-the-project)
- [CI/CD Pipeline](#-cicd-pipeline)
- [Security Features](#-security-features)
- [Security Scan Reports](#-security-scan-reports)
- [Deployment](#-deployment)
- [Known Issues & Future Work](#-known-issues--future-work)

---

## 🔍 Overview

The **Online Voting System** is a full-stack web application that allows organisations to conduct elections digitally. It supports three user roles — **Admin**, **Election Host**, and **Voter** — each with distinct permissions and workflows.

The system is built with security and automation at its core:
- **Django** handles routing, authentication, CSRF protection, and ORM-based database access
- **GitHub Actions** automatically deploys every commit to **AWS EC2**
- **Bandit**, **Flake8**, and **Pylint** enforce security and code quality standards throughout development

---

## ✨ Features

- 🔐 **Role-Based Access Control (RBAC)** — Admin, Host, and Voter roles with enforced permissions
- 🗳️ **Secure Ballot Casting** — One vote per election enforced at the database level (`unique_together`)
- 👥 **Multi-Stage Approval Workflow** — Voters must be approved by a Host before they can log in
- 🏛️ **Election Management** — Hosts can create, edit, and manage elections and candidates
- 📊 **Live Results** — Election results with vote counts and winner highlighted
- 🚀 **Automated Deployment** — GitHub Actions deploys to AWS EC2 on every push to `main`
- 🔒 **Security Hardened** — CSRF tokens, PBKDF2-SHA256 password hashing, session management
- 🛡️ **Static Security Scanning** — Bandit, Flake8, and Pylint integrated into the workflow

---

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| **Language** | Python 3.x |
| **Framework** | Django 6.0.3 |
| **Database** | SQLite3 (via Django ORM) |
| **App Server** | Gunicorn 25.3.0 |
| **Web Server** | Nginx (Reverse Proxy) |
| **Cloud Host** | AWS EC2 (eu-north-1) |
| **CI/CD** | GitHub Actions |
| **IDE** | Visual Studio Code |
| **Security Scan** | Bandit |
| **Linting** | Flake8, Pylint |

---

## 📁 Project Structure

OnlineVotingSystem_Code/
│
├── .github/
│   └── workflows/
│       └── deploy.yml          # GitHub Actions CI/CD pipeline
│
├── online_voting_system/       # Django project root
│   ├── accounts/               # User auth, registration, RBAC, approval
│   │   ├── models.py           # Custom User model (role, status, host FK)
│   │   ├── views.py            # Login, register, dashboard, approval views
│   │   ├── urls.py             # accounts URL patterns
│   │   └── templates/accounts/ # Login, register, dashboard templates
│   │
│   ├── elections/              # Election lifecycle management
│   │   ├── models.py           # Election model (host, title, dates)
│   │   ├── views.py            # Create, edit, list elections
│   │   └── templates/elections/
│   │
│   ├── voting/                 # Ballot casting and results
│   │   ├── models.py           # Candidate + Vote models
│   │   ├── views.py            # Vote, add candidate, results views
│   │   └── templates/voting/
│   │
│   ├── reports/                # Audit log module (future)
│   ├── core/                   # Shared utilities
│   ├── templates/              # Global base templates
│   ├── online_voting_system/   # Project settings, URLs, WSGI
│   │   ├── settings.py
│   │   ├── urls.py
│   │   └── wsgi.py
│   ├── manage.py
│   └── db.sqlite3
│
├── venv/                       # Virtual environment (not committed)
├── .bandit                     # Bandit security scan config
├── .flake8                     # Flake8 linting config
├── .pylintrc                   # Pylint config
├── .gitignore
├── requirements.txt
├── bandit-report.txt           # Latest Bandit security scan output
├── flake8-report.txt           # Latest Flake8 linting output
├── pylint-report.txt           # Latest Pylint output
└── pip-audit-report.txt        # Dependency vulnerability audit


---

## 👥 User Roles

### 🔴 Administrator
- Created via `python manage.py createsuperuser`
- Full access to Django Admin at `/admin/`
- Can approve/reject all users and manage all data
- Protected by `@staff_member_required`

### 🔵 Election Host
- Registers at `/register/host/` — **auto-approved** (`status=1`)
- Creates and manages elections and candidates
- Approves or rejects voter registrations
- Views election results

### 🟢 Voter
- Registers at `/register/voter/` — **pending approval** (`status=0`)
- Must be approved by an assigned Host before logging in
- Casts **one ballot per election** (enforced by `unique_together` constraint)
- Views results after voting

> **Login Gate:** Only users with `status = 1` can authenticate. Pending (`0`) and rejected (`2`) users are blocked.

---

## 🚀 Getting Started

### Prerequisites

- Python 3.x
- pip
- Git
- (Optional) VS Code with Python extension

### Clone the Repository

bash
git clone https://github.com/ImaranMurshad/OnlineVotingSystem_Code.git
cd OnlineVotingSystem_Code
---

## ⚙️ Environment Setup

```bash
# Create and activate virtual environment
python3 -m venv venv
source venv/bin/activate        # macOS/Linux
venv\Scripts\activate           # Windows

# Install dependencies
pip install -r requirements.txt
```

---

## ▶️ Running the Project

```bash
cd online_voting_system

# Apply database migrations
python manage.py migrate

# Create a superuser (Admin)
python manage.py createsuperuser

# Collect static files
python manage.py collectstatic --noinput

# Start the development server
python manage.py runserver
```

Visit: **http://127.0.0.1:8000**

---

## 🔄 CI/CD Pipeline

Every push to the `main` branch triggers the GitHub Actions workflow (`.github/workflows/deploy.yml`), which automatically deploys the latest code to AWS EC2.

```
git push → GitHub Actions → SSH into EC2 → git pull → pip install
        → migrate → collectstatic → restart Gunicorn → restart Nginx
```

### Pipeline Steps

| Step | Action | Command |
|---|---|---|
| 1 | Code checkout | `actions/checkout@v4` |
| 2 | SSH agent setup | `webfactory/ssh-agent@v0.9.0` |
| 3 | Connect to EC2 | `ssh -o StrictHostKeyChecking=no` |
| 4 | Pull latest code | `git pull origin main` |
| 5 | Install dependencies | `pip install -r requirements.txt` |
| 6 | Run migrations | `python manage.py migrate` |
| 7 | Collect static files | `python manage.py collectstatic --noinput` |
| 8 | Restart app server | `sudo systemctl restart gunicorn` |
| 9 | Restart web server | `sudo systemctl restart nginx` |

### GitHub Secrets Required

| Secret | Description |
|---|---|
| `EC2_SSH_KEY` | Private SSH key for EC2 access |
| `EC2_USER` | EC2 OS username (e.g. `ubuntu`) |
| `EC2_HOST` | EC2 public IP or hostname |
| `PROJECT_PATH` | Absolute path to project on EC2 |

---

## 🔒 Security Features

| Feature | Implementation |
|---|---|
| **CSRF Protection** | `CsrfViewMiddleware` — tokens validated on all POST requests |
| **Password Hashing** | PBKDF2-SHA256 via `AbstractUser.set_password()` |
| **Session Security** | HttpOnly signed cookies, invalidated on logout |
| **Ballot Integrity** | `unique_together = ('user', 'election')` at DB level |
| **SQL Injection Safe** | Django ORM parameterised queries — no raw SQL |
| **Access Control** | `@login_required`, `@staff_member_required` decorators |
| **Clickjacking** | `XFrameOptionsMiddleware` — `X-Frame-Options: DENY` |

### Middleware Stack (in order)

```python
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',        # 1. Security headers
    'django.contrib.sessions.middleware.SessionMiddleware', # 2. Session management
    'django.middleware.common.CommonMiddleware',            # 3. URL normalisation
    'django.middleware.csrf.CsrfViewMiddleware',           # 4. CSRF protection
    'django.contrib.auth.middleware.AuthenticationMiddleware', # 5. Auth
    'django.contrib.messages.middleware.MessageMiddleware', # 6. Flash messages
    'django.middleware.clickjacking.XFrameOptionsMiddleware', # 7. Clickjacking
]
```

---

## 🛡️ Security Scan Reports

The following tools are configured and scan reports are committed to the repository:

```bash
# Run Bandit security scan
bandit -r online_voting_system/ -o bandit-report.txt

# Run Flake8 linting
flake8 online_voting_system/ --output-file=flake8-report.txt

# Run Pylint
pylint online_voting_system/ > pylint-report.txt

# Run pip dependency audit
pip-audit > pip-audit-report.txt
```

| Tool | Config File | Purpose |
|---|---|---|
| Bandit | `.bandit` | AST-based Python security vulnerability scanning |
| Flake8 | `.flake8` | PEP 8 code style enforcement (max-line=100) |
| Pylint | `.pylintrc` | Static code quality analysis |

---

## ☁️ Deployment

The application is deployed on **AWS EC2 (eu-north-1 — Stockholm)**.

### Production Stack

```
Internet → Nginx (Port 80) → Gunicorn (Port 8000) → Django App → SQLite3
```

| Component | Role |
|---|---|
| **Nginx** | Reverse proxy, serves static files |
| **Gunicorn** | WSGI application server |
| **Django** | Application logic, ORM, authentication |
| **SQLite3** | Persistent data storage |

### Key URL Endpoints

| URL | View | Access |
|---|---|---|
| `/` | Home | Public |
| `/register/host/` | Host registration | Public |
| `/register/voter/` | Voter registration | Public |
| `/login/` | Login | Public |
| `/host/dashboard/` | Host dashboard | Host |
| `/voter/dashboard/` | Voter dashboard | Voter |
| `/elections/` | My elections | Host |
| `/elections/create/` | Create election | Host |
| `/voting/vote/<id>/` | Cast ballot | Voter |
| `/voting/results/<id>/` | Election results | Host |
| `/host/approve-voters/` | Voter approvals | Host |
| `/admin/` | Django Admin | Admin |

---

---

## ⚠️ Known Issues & Future Work

### Security Improvements Needed (Before Production)

- [ ] **Move `SECRET_KEY` out of `settings.py`** — use environment variables or AWS Secrets Manager
- [ ] **Set `DEBUG = False`** in production and configure custom error pages
- [ ] **Enable HTTPS/TLS** — configure SSL certificate via Let's Encrypt/Certbot on Nginx
- [ ] **Migrate to PostgreSQL** — SQLite is not recommended for production concurrent workloads

### Feature Roadmap

- [ ] Add login rate-limiting to prevent brute-force attacks (`django-axes`)
- [ ] Implement Multi-Factor Authentication (TOTP via `django-otp`)
- [ ] Complete the `reports` module with full audit logging
- [ ] Add automated test suite with CI gate (unit + integration tests)
- [ ] Containerise with Docker and orchestrate with Kubernetes
- [ ] Add Bandit HIGH severity findings as a CI/CD build gate

---

## 📦 Dependencies

Django==6.0.3
gunicorn==25.3.0
asgiref==3.11.1
sqlparse==0.5.5
packaging==26.0

Install all dependencies:
bash
pip install -r requirements.txt

---

## 👨‍💻 Author

Imaran Murshad**
DevOpsSec Module — National College of Ireland(NCI)

---

## 📄 License

This project is for academic purposes as part of the DevOpsSec module at National College of Ireland(NCI).

---

> **DevOpsSec** — Building secure systems through automated pipelines, continuous scanning, and security-first engineering practices.

# Patra

![Python](https://img.shields.io/badge/Python-3.x-blue)
![Django](https://img.shields.io/badge/Django-5.x-green)
![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)

Patra is a Django-based web application for guiding users through the early stages of preparing patent registration documents.

The platform combines OTP-based authentication, user profile management, project tracking, an AI-assisted patent chatbot, dynamic patent form generation, chat history, and Word document export.

Patra was developed as a 5-member team project, based on a real client requirement, and is currently structured as a local development version.

---

## Table of Contents

* [Overview](#overview)
* [Core Features](#core-features)
* [Patent Form Content](#patent-form-content)
* [Tech Stack](#tech-stack)
* [Project Structure](#project-structure)
* [Getting Started](#getting-started)
* [AI Prompt Configuration](#ai-prompt-configuration)
* [Environment Variables](#environment-variables)
* [Main Workflow](#main-workflow)
* [Current Scope](#current-scope)
* [Security Notes](#security-notes)
* [Team](#team)
* [License](#license)

---

## Overview

Preparing patent registration documents usually requires collecting detailed technical information in a structured way. Patra approaches this process through a guided conversation instead of asking the user to complete a static form from the beginning.

The user selects the invention type, chooses the preferred level of AI assistance, interacts with the chatbot, provides the required information step by step, and receives a generated patent form based on the conversation.

The system also keeps previous chats and projects available, so users can review, continue, search, and manage their patent-related conversations.

This repository is intended for local development and portfolio review. It does not depend on a public deployment or a specific production domain.

---

## Core Features

### Authentication and User Flow

* OTP-based login
* User profile completion and editing
* Automatic redirection for returning users
* Home page after login
* Toolbar navigation for profile, projects, history, and chat

### Project and Profile Management

* Display of project cards
* Project status display
* User profile editing
* Project title management
* Ability to edit project/chat titles

### AI-Assisted Patent Chatbot

* Step-by-step patent registration chatbot
* Sending and receiving messages
* Invention type selection by the user
* Support for different invention types:

  * Process
  * Product
  * Hybrid
* Chatbot behavior changes based on the selected invention type

### AI Involvement Control

Patra allows the user to choose how much the AI should be involved in completing the patent form.

The user can decide whether the AI should only organize and record the provided information, or whether it should also help improve and rewrite the descriptions.

Based on this choice, the backend loads a different system prompt and adjusts the chatbot behavior.

### Dynamic Patent Form Generation

* Automatic form generation based on the selected invention type
* Smart form generation from the information provided during chat
* Pre-filling form sections based on user answers
* Avoiding unnecessary fields when they are not relevant to the selected invention type
* Saving the latest generated form for each conversation

### Chat History

* Chat history section available from the interface
* List of previous chats and projects
* Conversations ordered by creation/update time
* Ability to reopen and continue previous chats
* Search among projects and chats by title

### Word Document Export

After the conversation is completed, the generated patent content can be exported as Word documents.

The export flow is designed to produce structured outputs such as:

* Invention summary
* Invention description
* Claims document

---

## Patent Form Content

The generated patent forms are designed to organize invention-related information into structured sections.

Depending on the selected invention type and the information collected during the chat, the form may include sections such as:

* Invention title
* Technical field
* Background or problem statement
* Summary of the invention
* Detailed description
* Key components or process steps
* Claims
* Additional invention-specific details

The exact form structure can change based on whether the invention is a process, product, or hybrid invention.

---

## Tech Stack

* Python
* Django
* Django REST Framework
* Django Templates
* SQLite for local development
* PostgreSQL-compatible driver included through `psycopg2-binary`
* OpenAI API integration
* Kavenegar package for OTP/SMS workflow
* `python-docx` for Word document generation
* `python-dotenv` and `django-environ` for environment configuration
* HTML and CSS

---

## Project Structure

```text
Patra/
├── chat/                  # Chatbot, conversations, form generation, exports
│   ├── Services/           # Word document export utilities
│   ├── models.py
│   ├── service.py
│   ├── urls.py
│   └── views.py
│
├── users/                 # Authentication, OTP, profiles, user flow
│   ├── middleware/
│   ├── models.py
│   ├── urls.py
│   └── views.py
│
├── mysite/                # Django project configuration
│   ├── settings.py
│   └── urls.py
│
├── templates/             # HTML templates
├── media/                 # Generated or uploaded files
├── manage.py
├── requirements.txt
├── README.md
└── LICENSE
```

---

## Getting Started

Follow these steps to run Patra locally.

### 1. Clone the Repository

```bash
git clone https://github.com/PatraTeam/Patra.git
cd Patra
```

### 2. Create a Virtual Environment

```bash
python -m venv .venv
```

### 3. Activate the Virtual Environment

On Windows CMD:

```bash
.venv\Scripts\activate
```

On Windows PowerShell:

```bash
.venv\Scripts\Activate.ps1
```

On macOS/Linux:

```bash
source .venv/bin/activate
```

### 4. Install Dependencies

```bash
pip install -r requirements.txt
```

### 5. Apply Database Migrations

```bash
python manage.py migrate
```

### 6. Run the Development Server

```bash
python manage.py runserver
```

Open the project in your browser:

```text
http://127.0.0.1:8000/
```

---

## AI Prompt Configuration

Patra uses external system prompt files to control the behavior of the AI assistant.

These prompt files are intentionally excluded from the repository and must be created locally on each developer machine or server. This does not mean the AI layer is missing; it means the actual prompt content is treated as environment-specific project logic.

At the project root, create a directory named:

```text
prompts/
```

Expected structure:

```text
Patra/
├── manage.py
├── prompts/
│   ├── process_generative.txt
│   ├── process_non_generative.txt
│   ├── product_generative.txt
│   ├── product_non_generative.txt
│   ├── hybrid_generative.txt
│   └── hybrid_non_generative.txt
```

Each file should contain the complete system-level instructions for the AI assistant.

### Prompt Selection Logic

The backend selects the prompt file based on two values:

* `invention_type`
* `details`

| Invention Type | Details | Loaded Prompt File           |
| -------------- | ------: | ---------------------------- |
| process        |   false | `process_non_generative.txt` |
| process        |    true | `process_generative.txt`     |
| product        |   false | `product_non_generative.txt` |
| product        |    true | `product_generative.txt`     |
| hybrid         |   false | `hybrid_non_generative.txt`  |
| hybrid         |    true | `hybrid_generative.txt`      |

This structure keeps the AI behavior modular and allows the project to support different levels of AI involvement.

For public repositories, keeping the main prompt files private can be a safer approach because prompts may contain internal product logic, conversation strategy, or client-specific instructions.

---

## Environment Variables

Create a `.env` file in the project root, at the same level as `manage.py`.

Example:

```env
SECRET_KEY=your-django-secret-key
DEBUG=True
ALLOWED_HOSTS=127.0.0.1,localhost

USE_MOCK=True
OPENAI_API_KEY=
```

For local testing without connecting to the OpenAI API:

```env
USE_MOCK=True
```

For API-connected testing:

```env
USE_MOCK=False
OPENAI_API_KEY=your-openai-api-key
```

Do not commit `.env` files or real API keys to the repository.

---

## Main Workflow

```text
OTP Login
   ↓
Profile Completion
   ↓
Home / Dashboard
   ↓
Select Invention Type
   ↓
Choose AI Assistance Level
   ↓
Start AI-Guided Chat
   ↓
Generate Patent Form
   ↓
Review or Continue Previous Projects
   ↓
Export Word Documents
```

---

## Current Scope

Patra is currently designed as a local development version.

Important notes about the current scope:

* The project is not tied to a public production domain.
* External services such as OpenAI and SMS/OTP providers require proper environment configuration.
* AI prompt files are intentionally excluded from the repository and must be created locally.
* Generated patent content should be reviewed by a human expert before real submission.
* The project helps organize patent documentation, but it is not a replacement for legal or professional patent advice.

---

## Security Notes

Before using this repository in a public or portfolio setting, make sure that:

* No real API keys are committed.
* `.env` files are kept out of Git.
* Local database files such as `db.sqlite3` are not committed.
* Prompt files are kept private if they contain internal system instructions.
* Any previously exposed API key is rotated.
* Generated media files are reviewed before being committed.

---

## Team

Patra was developed by a 5-member team using an Agile/Scrum-based workflow and was shaped around a real client use case.

| Name               | GitHub                                                   | Main Contribution                                                        |
| ------------------ | -------------------------------------------------------- | ------------------------------------------------------------------------ |
| Saba Zarifi        | [@armilazarifi](https://github.com/armilazarifi)         | Scrum Master, AI Lead, chatbot logic, and dynamic prompt selection       |
| Yasaman Banaei     | [@Banaeiyasman-bit](https://github.com/Banaeiyasman-bit) | Product Manager, user flow design, and UI/UX design in Figma             |
| Yasmina Hajizadeh  | [@yasi1383](https://github.com/yasi1383)                 | Frontend implementation and Django template development                  |
| Roya Mohammadi     | [@royamhmdi](https://github.com/royamhmdi)               | Backend development, database structure, and data models                 |
| Soroush Beyranvand | [@Ox50R0U5H](https://github.com/Ox50R0U5H)               | Backend development, application logic, and frontend–backend integration |

---

## License

This project is licensed under the MIT License.

See the [LICENSE](LICENSE) file for more information.

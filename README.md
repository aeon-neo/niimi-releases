# Niimi

**Sentient AI** - A relationship-focused personal AI assistant that helps you live better, not just work faster.

Niimi is a privacy-first AI companion that runs locally on your machine. Named after Mneme, the Greek muse of memory, it learns your preferences, remembers important details, and adapts its communication style as your relationship deepens.

## Features

- **Memory & Learning** - Tracks preferences, facts, goals, and relationships
- **Calendar & Tasks** - Natural language scheduling and task management
- **Document Processing** - Ingest PDFs with AI-powered search
- **Multimodal** - Video chat with emotion detection, image analysis, audio transcription
- **Knowledge Graph** - Entity extraction and semantic search
- **Privacy-First** - Runs entirely on your local machine, except for AI calls

## Requirements

- PostgreSQL 13+ with pgvector extension
- Anthropic API key
- OpenAI API key (for embeddings)
- Hume API key (for voice emotion analysis & TTS (text to speech))

## Status

Niimi is currently in **private beta**. To join the waitlist for public access, contact **niimi@niimi.ai**

## Links

- Website: https://www.niimi.ai/
- Contact: niimi@niimi.ai

# Installation

This guide is for users installing Niimi from pre-built binaries.

## System Requirements

- **Linux**: Ubuntu 20.04+ or similar (x64)
- **Windows**: Windows 10/11 with Ubuntu via Windows Terminal (x64)
- **macOS**: No pre-built binaries available (see "macOS Installation" section for source build)
- **Memory**: 4GB RAM minimum
- **Disk Space**: 2GB free space

## Choose Your Installation Path

- **Linux Users**: Follow "Linux Installation" section below
- **Windows Users**: Follow "Windows Installation" section below
- **macOS Users**: Follow "macOS Installation" section below (requires building from source)

---

# Linux Installation

Complete installation guide for native Linux (Ubuntu).

## 1. Download Niimi

```bash
# Install git if not already installed
sudo apt update
sudo apt install git

# Clone the releases repository
git clone https://github.com/aeon-neo/niimi-releases.git
cd niimi-releases

# Extract the binary
tar -xzf niimi-3.0.0-linux.tar.gz
cd niimi-3.0.0-linux
```

## 2. Install Dependencies

```bash
# Install PostgreSQL and ffmpeg
sudo apt update
sudo apt install postgresql postgresql-contrib ffmpeg

# Install pgvector extension
sudo apt install postgresql-16-pgvector

# Start PostgreSQL
sudo systemctl start postgresql
sudo systemctl enable postgresql
```

## 3. Create Database

```bash
# Connect to PostgreSQL as superuser
sudo -u postgres psql

# In psql prompt, run these commands:
CREATE DATABASE niimi_db;
CREATE USER niimi_user WITH PASSWORD 'your_secure_password';
GRANT ALL PRIVILEGES ON DATABASE niimi_db TO niimi_user;

# Connect to the new database and enable pgvector extension
\c niimi_db
CREATE EXTENSION vector;
GRANT ALL ON SCHEMA public TO niimi_user;
\q
```

**Important:** The `CREATE EXTENSION vector` command must be run as the postgres superuser before the install script will work.

## 4. Run Database Setup Script

```bash
psql -h localhost -U niimi_user -d niimi_db -f database/install.sql
```

Enter your password when prompted. This creates 22 tables required by Niimi.

## 5. Configure Niimi

Create `.env` file from the example:

```bash
cp .env.example .env
nano .env  # or use your preferred editor
```

Edit `.env` with your settings:

```bash
# Required API Keys
ANTHROPIC_API_KEY=sk-ant-your-key-here
OPENAI_API_KEY=sk-your-openai-key-here

# Optional: For video chat with voice (uncomment to enable)
# HUME_API_KEY=your-hume-api-key
# HUME_SECRET_KEY=your-hume-secret-key

# PostgreSQL Configuration
POSTGRES_HOST=localhost
POSTGRES_PORT=5432
POSTGRES_DB=niimi_db
POSTGRES_USER=niimi_user
POSTGRES_PASSWORD=your_secure_password
```

**Get your API keys:**

| Provider  | Purpose                     | URL                                    |
| --------- | --------------------------- | -------------------------------------- |
| Anthropic | Claude LLM and Vision       | https://console.anthropic.com/         |
| OpenAI    | Embeddings for RAG search   | https://platform.openai.com/api-keys   |
| Hume AI   | Video chat voice (optional) | https://platform.hume.ai/settings/keys |

## 6. Create Documents Directory

```bash
mkdir -p ~/niimi-documents
```

## 7. Run Niimi

```bash
chmod +x niimi
./niimi
```

On first run, Niimi will:

1. Connect to PostgreSQL database
2. Start the web server on port 5443 (configurable via `VIDEO_PORT` in .env)

Open your browser and navigate to: **http://localhost:5443/video-chat.html**

To use a different port, add `VIDEO_PORT=8080` to your .env file.

Press Ctrl+C in the terminal to stop the server.

---

# Windows Installation

Complete installation guide for Windows using Windows Terminal with Ubuntu.

## 1. Install Windows Terminal

Windows Terminal is the modern terminal for Windows that supports multiple profiles including Ubuntu.

1. Open **Microsoft Store** (search "Microsoft Store" in Start menu)
2. Search for **"Windows Terminal"**
3. Click **"Get"** to install
4. Wait for installation to complete

## 2. Install Ubuntu

Ubuntu runs inside Windows via WSL2 (Windows Subsystem for Linux).

1. Open **Microsoft Store**
2. Search for **"Ubuntu"** (or "Ubuntu 24.04 LTS" for the latest)
3. Click **"Get"** to install
4. Wait for installation to complete (may take a few minutes)
5. **Launch Ubuntu** from Start menu to complete first-time setup
6. Create a username and password when prompted (remember these!)

If this is your first time using WSL, Windows may need to enable it. If you see an error:

1. Open **PowerShell as Administrator** (right-click Start, select "Windows Terminal (Admin)")
2. Run: `wsl --install`
3. Restart your computer
4. Launch Ubuntu again from Start menu

## 3. Configure Windows Terminal

1. Open **Windows Terminal**
2. Click the down arrow next to the + tab button
3. Select **"Settings"** (or press Ctrl+,)
4. Under **"Startup"** set **"Default profile"** to **"Ubuntu"**
5. Click **"Save"**
6. Close and reopen Windows Terminal

Windows Terminal will now open Ubuntu by default.

**Optional - Appearance Settings:**

- Click **"Ubuntu"** in the left sidebar
- Under **"Appearance"**:
  - **Color scheme**: "One Half Dark" (good contrast)
  - **Font face**: "Lucida Console"

## 4. Download Niimi

Open Windows Terminal (which now opens Ubuntu). Run:

```bash
# Clone the releases repository
git clone https://github.com/aeon-neo/niimi-releases.git
cd niimi-releases

# Extract the binary
tar -xzf niimi-3.0.0-linux.tar.gz
cd niimi-3.0.0-linux
```

## 5. Install Dependencies

```bash
# Install PostgreSQL and ffmpeg
sudo apt update
sudo apt install postgresql postgresql-contrib ffmpeg

# Install pgvector extension
sudo apt install postgresql-16-pgvector

# Start PostgreSQL
sudo service postgresql start
```

Check if it's running:

```bash
sudo service postgresql status
```

**Auto-start PostgreSQL:**

Create/edit `/etc/wsl.conf`:

```bash
sudo nano /etc/wsl.conf
```

Add these lines:

```ini
[boot]
command="service postgresql start"
```

Save (Ctrl+X, Y, Enter). Then restart: close all Windows Terminal windows, open PowerShell, run `wsl --shutdown`, then reopen Windows Terminal.

## 6. Create Database

```bash
# Connect to PostgreSQL as superuser
sudo -u postgres psql

# In psql prompt, run these commands:
CREATE DATABASE niimi_db;
CREATE USER niimi_user WITH PASSWORD 'your_secure_password';
GRANT ALL PRIVILEGES ON DATABASE niimi_db TO niimi_user;

# Connect to the new database and enable pgvector extension
\c niimi_db
CREATE EXTENSION vector;
GRANT ALL ON SCHEMA public TO niimi_user;
\q
```

**Important:** The `CREATE EXTENSION vector` command must be run as the postgres superuser before the install script will work.

## 7. Run Database Setup Script

```bash
psql -U niimi_user -h localhost -d niimi_db -f database/install.sql
```

Enter your password when prompted. This creates 22 tables required by Niimi.

## 8. Configure Niimi

Create `.env` file from the example:

```bash
cp .env.example .env
nano .env  # or use your preferred editor
```

Edit `.env` with your settings:

```bash
# Required API Keys
ANTHROPIC_API_KEY=sk-ant-your-key-here
OPENAI_API_KEY=sk-your-openai-key-here

# Optional: For video chat with voice (uncomment to enable)
# HUME_API_KEY=your-hume-api-key
# HUME_SECRET_KEY=your-hume-secret-key

# PostgreSQL Configuration
POSTGRES_HOST=localhost
POSTGRES_PORT=5432
POSTGRES_DB=niimi_db
POSTGRES_USER=niimi_user
POSTGRES_PASSWORD=your_secure_password
```

**Get your API keys:**

| Provider  | Purpose                     | URL                                    |
| --------- | --------------------------- | -------------------------------------- |
| Anthropic | Claude LLM and Vision       | https://console.anthropic.com/         |
| OpenAI    | Embeddings for RAG search   | https://platform.openai.com/api-keys   |
| Hume AI   | Video chat voice (optional) | https://platform.hume.ai/settings/keys |

## 9. Create Documents Directory

```bash
mkdir -p ~/niimi-documents
```

## 10. Run Niimi

```bash
chmod +x niimi
./niimi
```

On first run, Niimi will:

1. Connect to PostgreSQL database
2. Start the web server on port 5443 (configurable via `VIDEO_PORT` in .env)

Open your browser (Chrome, Edge, Firefox) and navigate to: **http://localhost:5443/video-chat.html**

To use a different port, add `VIDEO_PORT=8080` to your .env file.

Press Ctrl+C in the terminal to stop the server.

---

# macOS Installation

Pre-built binaries are not available for macOS. macOS users with access to the source code can build and run from source using the included Makefile.

## Prerequisites

- Node.js 18+ (`brew install node@18`)
- PostgreSQL 16 with pgvector (`brew install postgresql@16 pgvector`)

## Quick Start

```bash
cd niimi
make help      # View available commands
make setup     # Install dependencies
make setup-db  # Initialize database
make run       # Start Niimi
```

Open your browser and navigate to: **http://localhost:5443/video-chat.html**

To use a different port, add `VIDEO_PORT=8080` to your .env file.

See the Makefile for detailed build and setup commands.

---

# Usage

## Web Interface

After starting Niimi, open your browser to **http://localhost:5443/video-chat.html** (or your custom VIDEO_PORT)

The interface provides:

- Text chat with Niimi
- Voice input (microphone button)
- Voice output (Hume AI TTS if configured)
- Video camera support for visual context

## Basic Chat

Talk naturally to Niimi:

```
"What's on my calendar today?"
"Remember that I prefer tea over coffee"
"Set a reminder to call mom tomorrow at 3pm"
```

Niimi can:

- Answer questions using her knowledge base
- Remember conversations and extract facts
- Manage your calendar and tasks
- Analyze images, videos, and audio files
- Create and export documents

## Document Ingestion

Place PDF files in your documents folder: `~/niimi-documents/`

Then tell Niimi:

```
"Can you ingest the maths PDF?"
```

She'll process it in the background and notify you when complete.

## Calendar Management

```
"Schedule a meeting with Sarah tomorrow at 2pm"
"What's on my calendar this week?"
"Move the dentist appointment to Friday"
```

## Task Management

```
"Add a task to review the proposal by Friday"
"What are my pending tasks?"
"Mark the report task as completed"
```

## Media Analysis

```
"Analyze the screenshot I saved"
"Transcribe the meeting recording"
```

Place files in your documents folder first.

---

# Troubleshooting

## Database Connection Errors

**Linux:**

```bash
# Check if PostgreSQL is running
sudo systemctl status postgresql

# Test connection
psql -h localhost -U niimi_user -d niimi_db
```

**Windows (Ubuntu via Windows Terminal):**

```bash
# Check if PostgreSQL is running
sudo service postgresql status

# Start PostgreSQL if not running
sudo service postgresql start

# Test connection
psql -h localhost -U niimi_user -d niimi_db
```

## Missing pgvector Extension

If you see "extension vector does not exist":

```sql
# Connect to database
psql -h localhost -U postgres -d niimi_db

# Enable extension
CREATE EXTENSION IF NOT EXISTS vector;
```

## Permission Errors (Linux)

```bash
# Make binary executable
chmod +x niimi

# Fix database permissions
sudo -u postgres psql -c "GRANT ALL PRIVILEGES ON DATABASE niimi_db TO niimi_user;"
```

## Port Already in Use

If you see "Failed to start server. Is port 5443 in use?":

```bash
# Option 1: Use a different port
# Add to .env: VIDEO_PORT=8080

# Option 2: Find what's using the port and kill it
lsof -i :5443
kill PID  # Replace PID with the actual process ID
```

## API Key Errors

If you see "Invalid API key":

1. Check `.env` file exists in same directory as binary
2. Verify `ANTHROPIC_API_KEY` is set correctly (no quotes needed)
3. Ensure no extra spaces around the key

## Change Database Password

If you need to change the PostgreSQL password (e.g., you accidentally used the example password):

```bash
# Change the password
sudo -u postgres psql -c "ALTER USER niimi_user WITH PASSWORD 'your_new_password';"

# Update your .env file to match
nano .env
```

## Reset Database (Failed Installation)

If the install script fails partway through (e.g., "trigger already exists" or other errors from a partial install), you need to drop and recreate the database:

```bash
# Connect as superuser
sudo -u postgres psql

# Drop and recreate database
DROP DATABASE niimi_db;
CREATE DATABASE niimi_db;
GRANT ALL PRIVILEGES ON DATABASE niimi_db TO niimi_user;
\c niimi_db
CREATE EXTENSION vector;
GRANT ALL ON SCHEMA public TO niimi_user;
\q
```

Then run the install script again:

```bash
psql -h localhost -U niimi_user -d niimi_db -f database/install.sql
```

---

# File Locations

**Linux and Windows (Ubuntu via Windows Terminal):**

- Binary: current directory (`./niimi`)
- Configuration: `.env` in same directory as binary
- Documents: `~/niimi-documents/`
- Web UI: `public/` folder in same directory as binary

---

# Updating

To update to a new version:

```bash
# Stop Niimi (Ctrl+C in the terminal running it)

# Backup your .env file
cd ~/niimi-releases/niimi-3.0.0-linux
cp .env ~/.env.niimi.backup

# Pull latest release
cd ~/niimi-releases
git pull

# Extract new version
rm -rf niimi-3.0.0-linux
tar -xzf niimi-3.0.0-linux.tar.gz
cd niimi-3.0.0-linux

# Restore your .env
cp ~/.env.niimi.backup .env

# Restart
./niimi
```

Your database and documents are preserved between updates.

---

# Uninstalling

**Linux and Windows (Ubuntu via Windows Terminal):**

```bash
# Remove the niimi folder
cd
rm -rf ~/niimi-releases

# Remove database (WARNING: deletes all data)
sudo -u postgres psql -c "DROP DATABASE niimi_db;"

# Remove documents
rm -rf ~/niimi-documents/
```

---

# Privacy & Security

- All data stored locally in your PostgreSQL database
- No telemetry or external data collection
- **External API calls**:
  - Anthropic Claude (LLM responses, vision analysis)
  - Hume AI (voice emotion analysis and TTS in video chat - optional)
  - OpenAI (embeddings for RAG search, Whisper for audio transcription)
- API keys stored in local `.env` file
- Keep your `.env` file secure and never share it

---

# Support

For issues:

1. Check troubleshooting section above
2. Ensure all prerequisites are installed correctly
3. Verify `.env` configuration is correct

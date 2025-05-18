---
title: "Part 3: Opensource Stack - Tools I'm Using"
date: 2025-05-18
---
![Part3 banner image.]({{ site.baseurl }}/assets/images/part-3-banner.png)
*Build Your Own USSD APP with Opensource Power*

Welcome to Part 3 of our *Build Your Own USSD App* series! In Part 2, we explored a dental appointment app designed for Papua New Guinea’s mobile users, enabling bookings on basic phones without internet. Now, we’re ready to set up the free, open-source tools that make this possible—no large budget required. This is ideal for individuals or small teams in PNG looking to address local challenges like healthcare access through mobile technology.

Imagine helping someone in Gaire village book a dental visit to UPNG Dental School Clinic with a simple USSD code. That’s the goal, and we’ll use customizable tools, including a few tailored adjustments, to get there. Let’s walk through the installation process step by step.

## Why Choose Free, Open-Source Tools?
Open-source tools offer significant advantages:

* **No Cost**: Free to use, perfect for budget-conscious projects in PNG.
* **Customizable**: You can modify the code to suit your needs.
* **Community Support**: A global network is available to assist with issues.
* **Sustainable**: Built for long-term use in community-driven initiatives.
Let’s set up the essential tools to start your project.

## Your Toolbox – Installation Steps
Here’s how to install the core components—Python, a virtual environment with virtualenv, environment variable management with direnv, a modified ussd_engine with Redis for session management, a custom ussd-simulator, and a data keeper (PostgreSQL or SQLite). We’ll cover steps for both Windows and Linux (Ubuntu, your preferred system).

## Python – The Foundation of Your App
Python is a beginner-friendly language that forms the base of our USSD app. Here’s how to install it:

*   **For Windows**:
    1. Visit python.org and download the latest version (e.g., 3.12).
    2. Run the installer, ensure “Add Python to PATH” is checked, and click Next.
    3. Open Command Prompt (search “cmd” in the Start menu), type `python --version`, and press Enter. You should see something like “Python 3.12.3.” If not, verify the PATH setting.
* **For Linux (Ubuntu)**:
    1. Open your terminal (Ctrl+Alt+T).
    2. Update your system: `sudo apt update && sudo apt upgrade -y`.
    3. Install Python and pip: `sudo apt install python3 python3-pip -y`.
    4. Verify the installation: Type `python3 --version` (e.g., “Python 3.12.3”) and `pip3 --version`. If it fails, check your internet connection.

Python’s simplicity shines here:
```python
def say_hello(name): 
    return f"Welcome, {name}!"
print(say_hello("John")) # Output: Welcome, John!
```
## virtualenv – Creating a Clean Workspace
Using `virtualenv` sets up an isolated environment for your project, preventing conflicts with other software. Think of it as a dedicated workspace for your app.

* **For Both Windows and Linux (Ubuntu)**:
    1. Install virtualenv: pip3 install virtualenv (or pip on Windows).
    2. Create a project folder: mkdir ussd_dental_app && cd ussd_dental_app.
    3. Set up the virtual environment: virtualenv venv.
    4. Activate it:
        * Windows:
            `venv\Scripts\activate` (you’ll see (venv) in your prompt).
        * Ubuntu: 
            `source venv/bin/activate` (look for (venv)).
    5. Deactivate later with: `deactivate` (keep it active for now).
All installations will now stay within this environment.

## direnv – Managing Environment Variables
`direnv` automates the loading of environment variables (e.g., database passwords or Redis settings), making your setup more secure and efficient.

* **For Linux (Ubuntu)**:
    1. Install direnv: `sudo apt install direnv -y`.
    2. Add it to your shell: Edit `~/.bashrc` with `nano ~/.bashrc`, add `eval "$(direnv hook bash)"` at the end, save, and exit.
    3. Reload your shell: `source ~/.bashrc`.
    4. In your project folder (ussd_dental_app), create a .envrc file: `echo "export MY_APP_ENV=development" > .envrc`.
    If you are using the starter project templated, navigate to the root folder of the starter project (`ussd_app`) and execute the above command
    5. Allow it: `direnv allow .`. Variables will load automatically when you cd into the folder.
* **For Windows**:
`direnv` is less straightforward on Windows. For now, set variables manually in Command Prompt with `set MY_APP_ENV=development` (or use PowerShell). If you use WSL, follow the Ubuntu steps for a smoother experience.

## Redis – Session Management for ussd_engine
Redis is a fast, in-memory database we’ll use for managing USSD session data with ussd_engine, ensuring quick access to user interactions. Install it:

* **For Linux (Ubuntu)**:
    1. Install Redis: `sudo apt install redis-server -y`.
    2.  Start the Redis server: `sudo systemctl enable redis` and `sudo systemctl start redis`.
    3. Verify it’s running: `redis-cli ping`. You should see “PONG.”
    4. Add the Redis connection details to your `.envrc`: `echo "export REDIS_URL=redis://localhost:6379/0" >> .envrc` and run `direnv allow .` .
* **For Windows:**
Redis doesn’t have an official Windows build, but you can use WSL or a precompiled binary:
    1. Download a Redis binary for Windows (e.g., from github.com/microsoftarchive/redis).
    2. Extract and run `redis-server.exe`.
    3. Verify: Open another Command Prompt, run `redis-cli.exe ping`, and expect “PONG.”
    4. Set the variable manually: set `alcoolREDIS_URL=redis://localhost:6379/0`. If using WSL, follow the Ubuntu steps.

## ussd_engine – A Customized USSD Engine
This tool, originally by Francis Mwangi (thank you!), drives our USSD flow and uses Redis for session management. I forked his ussd_engine repository and adjusted it for Python 3 compatibility. Install my version:

1. With your virtual environment active, run:
`pip install git+https://github.com/jayskar/ussd_engine.git` .
2. Install the Redis Python client:
pip install redis.
3. Verify: `pip list | grep ussd`. You should see the following.
`ussd_airflow_engine 0.3.4.dev8+g0263868`

Here’s a sample menu:
```yaml
authenticated_menu:
  type: menu_screen
  text: |
    Welcome.
  options:
    - text: Check Appointments
      next_screen: check_appointments
    - text: Book New Appointment
      next_screen: book_appointment
    - text: Cancel Appointment
      next_screen: cancel_appointment
    - text: Exit
      next_screen: end
```
## ussd-simulator – A Tailored Testing Tool
We’re using a simulator from Anthony-cloud-1’s repository (github.com/Anthony-cloud-1/ussd-simulator). I forked and adjusted it to fit our django app’s needs. Set it up:

1. Clone my fork:
`git clone https://github.com/jayskar/ussd-simulator.git` (use my GitHub handle).
2. Navigate:
`cd ussd-simulator/ussd-mock`.
3. Install dependencies:
`npm install`.
4. Run it with `npm run serve` (details in Part 4). This lets you simulate USSD calls like *123#.

## PostgreSQL (or SQLite) – Your Data Storage
SQLite is included with Python for testing, while PostgreSQL is recommended for production. Install it:

*   **For Both Windows and Linux (Ubuntu)**:
    1.  Download from postgresql.org.
        * **Windows**: Run the installer, set a password, and note port 5432.
        * **Ubuntu**:
            `sudo apt install postgresql postgresql-contrib -y`, then
            `sudo service postgresql start`.
            Verify with
            `sudo -u postgres psql -c "SELECT version();”` (expect a version string).
    2.  Connection details will be covered next time.
        Use `direnv` to set your database password: Add export `DB_PASSWORD=yourpassword` to `.envrc` and run `direnv allow .` .

## Extra Helpers (Optional)
Consider Ngrok from ngrok.com to test api calls.

## The Blunt Truth: Watch Out for Snags
Installation can be tricky—miss “Add to PATH” on Windows, forget sudo on Ubuntu, or misconfigure direnv, and you’ll hit roadblocks. If Redis doesn’t start, check your port (6379) isn’t blocked. If my fork URLs fail, check my GitHub handle in the comments—I might need to update access. I’ve skipped Django setup (it’s too much here); look for my next article with Cookiecutter. Let’s keep it straightforward for now.

## What’s Coming Next? Building the App
Part 4 will guide you to build and test the dental app flow using this setup and our custom simulator. Catch up on Part 1 & Part 2 if you missed them. Tried this yet? Share your successes or challenges below—I’m here to assist!

#USSD #DigitalInclusion #PapuaNewGuinea #OpenSource #MobileTech #TechForGood
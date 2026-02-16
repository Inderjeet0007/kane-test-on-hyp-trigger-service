# Kane AI Test Run on HyperExecute Orchestrator 🚀

A robust Node.js automation orchestrator designed to integrate **TestMu AI Kane AI** into your CI/CD pipelines. This tool automates the end-to-end testing workflow by dynamically fetching test cases, mapping them to environments, creating test runs, and triggering execution via the HyperExecute API.

## 🌟 Key Features

* **Dynamic Test Retrieval:** Fetches test case IDs automatically based on readable titles (no hardcoding IDs).
* **Multi-Environment Support:** Runs a single test case across multiple OS/Browser configurations simultaneously (e.g., MacOS Chrome + Windows Edge).
* **Smart Payload Generation:** Automatically handles the complex "Create Shell -> Populate Instances" API logic required by TestMu AI.
* **CI/CD Integration:** Built-in polling mechanism and artifact handling for GitLab CI pipelines.
* **Secure:** Uses environment variables for all sensitive credentials.

---

## 🛠️ Prerequisites

* **Node.js**: v18 or higher
* **TestMu AI Account**: You need a valid Username and Access Key.
* **Project ID**: The ID of your project in TestMu AI Test Manager.

---

## ⚙️ Setup & Installation

### 1. Clone the Repository
```bash
git clone https://gitlab.com/Inderjeet0007/kane-test-on-hyp-trigger-service.git
cd kane-test-on-hyp-trigger-service
```
### 2. Install Dependencies
```bash
npm install
```
### 3. Local Configuration (.env)
Create a `.env` file in the root directory for local testing. Do not commit this file.
``` bash
# .env
LT_USERNAME=your_username
LT_ACCESS_KEY=your_access_key
LT_PROJECT_ID=your_project_id

# Test Configuration
TARGET_TITLE="Change Notification Settings"
TARGET_ENV_NAMES="Win10_Chrome,Mac_Sonoma_Safari"
```
---

## 🚀 Usage

### Running Locally
To test the flow from your local machine:

    node flow.js

* **Success:** You will see logs indicating the Test Run ID created and the Job ID triggered.
* **Output:** A file named `kane_job.env` will be generated containing the Job ID.

---

## 🔗 GitLab CI/CD Integration

This repository includes a `.gitlab-ci.yml` file ready for production.

### Required CI/CD Variables
Go to **Settings > CI/CD > Variables** in your GitLab project and add the following:

| Variable | Description | Masked? | Protected? |
| :--- | :--- | :--- | :--- |
| `LT_USERNAME` | Your TestMu AI Username | No | No |
| `LT_ACCESS_KEY` | Your TestMu AI Access Key | **Yes** | **Yes** (if using protected branch) |
| `LT_PROJECT_ID` | Project ID from TestMu AI URL | No | No |
| `TMA_BASE64_AUTH`| Base64 encoded `user:key` for polling | **Yes** | **Yes** |
| `TARGET_TITLE` | Title of the test case to run | No | No |
| `TARGET_ENV_NAMES` | Comma-separated list of env names | No | No |

**⚠️ Important:**
* **`TARGET_ENV_NAMES`** should look like: `Win10_Chrome,Mac_Sonoma_Safari` (No spaces after commas).
* **Uncheck "Mask variable"** for `TARGET_TITLE` if it contains spaces.

### Pipeline Workflow
1.  **Trigger Stage:**
    * Runs `node flow.js` to create the test run.
    * Saves the `KANE_JOB_ID` to a `kane_job.env` artifact.
2.  **Polling:**
    * Uses `curl` to check the status of the job every 30 seconds.
    * Passes the pipeline if the test status is `completed` or `passed`.
    * Fails the pipeline if the test is `failed` or `error`.

---

## 📂 Project Structure

    kane-ai-orchestrator/
    ├── flow.js              # 🧠 Main logic script (The Brain)
    ├── .gitlab-ci.yml       # 🤖 CI/CD Pipeline definition
    ├── package.json         # 📦 Dependencies (axios)
    ├── .gitignore           # 🙈 Ignores node_modules & .env
    └── README.md            # 📖 This documentation


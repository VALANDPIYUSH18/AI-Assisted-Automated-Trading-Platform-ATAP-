# 📋 How to Copy ATAP Issues to GitHub

## 🚀 Quick Start - 3 Methods

---

## **METHOD 1: GitHub CLI (Fastest) ⚡**

### Prerequisites
- Install GitHub CLI: https://cli.github.com
- Authenticate: `gh auth login`
- Clone the shell script provided

### Steps

1. **Create your GitHub repository:**
   ```bash
   # Go to https://github.com/new
   # Create repo: `atap` (or any name)
   # Clone it locally
   git clone https://github.com/YOUR-USERNAME/atap.git
   cd atap
   ```

2. **Download the shell script:**
   ```bash
   # Option A: Copy the content from github_create_issues.sh and paste into a file
   # Option B: Use curl
   curl -O https://raw.githubusercontent.com/YOUR-REPO/main/github_create_issues.sh
   ```

3. **Edit the script (important!):**
   ```bash
   # Open github_create_issues.sh in a text editor
   # Change line: REPO="your-username/atap"
   # To your actual repo, e.g.: REPO="rajkumar/atap"
   ```

4. **Make executable and run:**
   ```bash
   chmod +x github_create_issues.sh
   ./github_create_issues.sh
   ```

5. **Wait for all 39 issues to be created** ✅
   - Should take 2-3 minutes

### Result
- ✅ All 39 issues created with titles, descriptions, labels
- ✅ Organized by Sprint 1-5
- ✅ Labeled with priority (P0, P1, P2)
- ✅ Linked to milestones

---

## **METHOD 2: Manual Copy-Paste (If No CLI) 📝**

If you don't want to use GitHub CLI, you can manually create issues. Here's how:

### Steps

1. **Go to your GitHub repo** → Issues tab → "New Issue"

2. **For each issue, copy-paste from the table below:**
   - Title
   - Description (expand from markdown file)
   - Labels (click "Labels" and select)
   - Milestone (select Sprint 1, 2, 3, etc.)

3. **Create Milestones First:**
   - Go to Issues → Milestones → "New Milestone"
   - Create: Sprint 1, Sprint 2, Sprint 3, Sprint 4, Sprint 5
   - Leave description and due date blank for now

### Quick Copy Format

For each issue:
```
Title: [Copy from list below]
Description: [Copy full description]
Labels: [sprint-X, category, P0/P1/P2]
Milestone: [Sprint X]
```

**Issue Quick List:**

| Issue | Title | Labels |
|-------|-------|--------|
| ATAP-001 | Project Setup & Infrastructure | sprint-1, infrastructure, P0 |
| ATAP-002 | Database Schema & Models | sprint-1, backend, database, P0 |
| ATAP-003 | User Auth - Registration | sprint-1, backend, auth, P0 |
| ATAP-004 | User Auth - Login & JWT | sprint-1, backend, auth, P0 |
| ATAP-005 | Two-Factor Authentication (2FA) | sprint-1, backend, auth, P1 |
| ATAP-006 | User Profile Management | sprint-1, backend, auth, P1 |
| ATAP-007 | User Subscription Tier | sprint-1, backend, billing, P1 |
| ATAP-008 | Error Handling & Logging | sprint-1, backend, infrastructure, P1 |
| ATAP-009 | Broker - Angel One | sprint-2, backend, brokers, P0 |
| ATAP-010 | Broker - Zerodha | sprint-2, backend, brokers, P0 |
| ATAP-011 | Strategy - EMA Crossover | sprint-2, backend, strategies, P0 |
| ATAP-012 | Strategy - RSI Momentum | sprint-2, backend, strategies, P1 |
| ATAP-013 | Strategy - Breakout | sprint-2, backend, strategies, P1 |
| ATAP-014 | Backtest Engine - Core | sprint-2, backend, backtesting, P0 |
| ATAP-015 | Backtest Engine - Metrics | sprint-2, backend, backtesting, P1 |
| ATAP-016 | Backtest Results UI | sprint-2, frontend, backtesting, P1 |
| ATAP-017 | Indicator Calculations | sprint-2, backend, indicators, P1 |
| ATAP-018 | Order Execution | sprint-3, backend, trading, P0 |
| ATAP-019 | Position Dashboard | sprint-3, backend, frontend, trading, P0 |
| ATAP-020 | Risk Management - Sizing/SL/TP | sprint-3, backend, risk, P0 |
| ATAP-021 | Strategy Deployment | sprint-3, backend, trading, P0 |
| ATAP-022 | Manual Position Management | sprint-3, backend, trading, P1 |
| ATAP-023 | Trade History & Audit Log | sprint-3, backend, trading, P1 |
| ATAP-024 | Email Notifications | sprint-3, backend, notifications, P1 |
| ATAP-025 | In-App Notifications | sprint-3, backend, frontend, notifications, P1 |
| ATAP-026 | End-to-End Testing | sprint-3, testing, QA, P1 |
| ATAP-027 | Portfolio Analytics | sprint-4, frontend, analytics, P1 |
| ATAP-028 | Risk - Max Positions & Leverage | sprint-4, backend, risk, P1 |
| ATAP-029 | MT5 Connector | sprint-4, backend, brokers, integration, P0 |
| ATAP-030 | Advanced Strategy Types | sprint-4, backend, strategies, P2 |
| ATAP-031 | Marketplace - Strategy Sharing | sprint-4, backend, frontend, marketplace, P2 |
| ATAP-032 | Creator Revenue Dashboard | sprint-4, frontend, marketplace, P2 |
| ATAP-033 | Security Audit | sprint-5, security, P0 |
| ATAP-034 | Performance Optimization | sprint-5, infrastructure, performance, P1 |
| ATAP-035 | Documentation | sprint-5, documentation, P1 |
| ATAP-036 | AWS Deployment | sprint-5, infrastructure, deployment, P0 |
| ATAP-037 | Testing & Bug Fixes | sprint-5, testing, QA, P1 |
| ATAP-038 | Beta Launch | sprint-5, launch, marketing, P1 |
| ATAP-039 | Post-Launch Monitoring | sprint-5, operations, support, P1 |

---

## **METHOD 3: CSV Import (If GitHub Supports) 📊**

Some GitHub integrations support CSV import. Steps:

1. **Create CSV file** (atap_issues.csv):
   ```csv
   Title,Description,Labels,Milestone
   "ATAP-001: Project Setup","Description here...","sprint-1,infrastructure,P0","Sprint 1"
   "ATAP-002: Database Schema","Description here...","sprint-1,backend,database,P0","Sprint 1"
   ...
   ```

2. **Use GitHub Integration Tool:**
   - https://github.com-issue-importer.com (3rd party, use with caution)
   - Or use GitHub API directly

3. **Alternative: Use octokit.js**
   ```javascript
   // Simple Node.js script to create issues from CSV
   const Octokit = require("@octokit/rest");
   const fs = require("fs");
   const csv = require("csv-parser");
   
   const octokit = new Octokit({
     auth: "YOUR_GITHUB_TOKEN"
   });
   
   fs.createReadStream("atap_issues.csv")
     .pipe(csv())
     .on("data", async (row) => {
       await octokit.issues.create({
         owner: "YOUR-USERNAME",
         repo: "atap",
         title: row.Title,
         body: row.Description,
         labels: row.Labels.split(","),
         milestone: row.Milestone
       });
     });
   ```

---

## ✅ After Creating Issues

### 1. **Create Milestones**
Go to Issues → Milestones → Create 5 milestones:
- [ ] Sprint 1 (Due: 2 weeks from now)
- [ ] Sprint 2 (Due: 4 weeks from now)
- [ ] Sprint 3 (Due: 6 weeks from now)
- [ ] Sprint 4 (Due: 8 weeks from now)
- [ ] Sprint 5 (Due: 10 weeks from now)

### 2. **Create Project Board**
Go to Projects → New Project → Select "Table" template
- **Columns:**
  - Backlog
  - Ready
  - In Progress
  - In Review
  - Done

### 3. **Assign to Team**
For each issue:
- Click issue
- Click "Assignees" → add your team members
- Click "Projects" → add to project board

### 4. **Create Labels (Optional)**
Go to Issues → Labels → Create custom labels:
- `sprint-1`, `sprint-2`, `sprint-3`, `sprint-4`, `sprint-5`
- `backend`, `frontend`, `infrastructure`
- `P0`, `P1`, `P2` (Priority)
- `auth`, `trading`, `strategies`, `notifications`, etc.

---

## 🎯 Sample GitHub Board After Setup

```
BACKLOG:
├─ ATAP-027: Portfolio Analytics
├─ ATAP-028: Risk Management
└─ ATAP-029: MT5 Connector

READY (Sprint 1 - Next up):
├─ ATAP-001: Project Setup
├─ ATAP-002: Database Schema
├─ ATAP-003: User Auth
├─ ATAP-004: Login & JWT
├─ ATAP-005: 2FA
├─ ATAP-006: Profile
├─ ATAP-007: Subscription
└─ ATAP-008: Logging

IN PROGRESS (Currently working):
├─ [assigned to person 1]
├─ [assigned to person 2]
└─ [assigned to person 3]

IN REVIEW:
├─ [PR #12 - User Auth]
└─ [PR #13 - Database]

DONE:
└─ [Completed tasks from previous sprint]
```

---

## 🚨 Troubleshooting

### Problem: GitHub CLI not found
```bash
# Install GitHub CLI:
# macOS:
brew install gh

# Linux:
curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg

# Windows:
choco install gh
```

### Problem: Authentication failed
```bash
# Re-authenticate:
gh auth logout
gh auth login
# Follow prompts
```

### Problem: Script permission denied
```bash
chmod +x github_create_issues.sh
./github_create_issues.sh
```

### Problem: Issues created with wrong labels/milestone
- Manually edit each issue (click issue → click "..." → "Edit")
- Or delete and recreate using the script

---

## 📊 Final Checklist

After all issues are created:

- [ ] All 39 issues appear in GitHub Issues tab
- [ ] Issues are labeled with Sprint 1-5
- [ ] Issues have Priority labels (P0, P1, P2)
- [ ] Milestones created (Sprint 1-5)
- [ ] Project board created (Backlog, Ready, In Progress, In Review, Done)
- [ ] Team members assigned to Sprint 1 issues
- [ ] README.md updated with link to project
- [ ] GitHub Actions CI/CD stubbed out

---

## 🚀 Ready to Start?

Once all issues are in GitHub:

1. **Print this for your team:**
   ```
   Sprint 1 (Weeks 1-2): 28 story points
   - ATAP-001 to ATAP-008
   - Focus: Foundation & Authentication
   - Target: Complete all 8 issues
   ```

2. **Assign issues to team members**

3. **Start with ATAP-001**

4. **Update issue status** as you progress

---

## 📞 Need Help?

If you get stuck:

1. Check GitHub's issue documentation: https://docs.github.com/en/issues
2. Review the shell script: github_create_issues.sh
3. Check GitHub CLI docs: https://cli.github.com/manual

Good luck! 🚀

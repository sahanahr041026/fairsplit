# FairSplit: Split Smart. Settle Easy. Stay Fair.

## 1. Team Name

[Team number:32 / name: Team Casade] — **FairSplit**

## 2. Problem Statement & Solution Description

**Problem Statement:**

Shared expenses in groups such as flatmates, trips and friends cause disputes. Most split apps let a debtor mark a debt as "paid" without the receiver agreeing. This leaves balances unreliable, and it is hard to see who still owes whom and what the smallest set of payments to settle up would be.

**Proposed Solution:**

FairSplit is a group expense tracker where **a payment only counts once the receiver approves it**. Members add expenses with an equal or custom split. A debtor records a payment and it waits for the receiver's confirmation, while the debt stays unchanged. Approval settles the oldest shares first, and partial payments are supported. A debt-simplification helper called Budget Baba suggests the fewest payments needed to settle a group. XP, levels and achievement badges reward on-time settling. The prototype ships with seeded demo users, groups and expenses. All money is stored as `Decimal`, and no API keys are needed.

## 3. Selected Features

### Feature 1: Receiver-Approved Payment Workflow

A FastAPI backend with SQLAlchemy and SQLite stores expenses as per-person shares. A payment moves to "Awaiting Receiver Confirmation" and only the receiver can approve or reject it. Approval settles the oldest shares first, supports partial payments, awards XP and badges, and notifies both sides. Rejection needs a reason, and the debtor can resubmit. Each payment can be decided exactly once, even with double-clicks or two open tabs. You can never have more awaiting than you actually owe that person. Leftover paise in a split go to the first person, so shares always add up exactly.

### Feature 2: Balances, Budget Baba and Rewards

A React + Vite frontend shows a dashboard, group pages, "who owes whom" balances, approvals, notifications, history and a weekly summary. **Budget Baba** simplifies debts into suggested payments (suggestions only, never applied automatically). Every approved payment gives the payer +20 XP, and clearing all debts gives +10 XP more. Level is `XP ÷ 100 + 1`. Six achievement badges can be unlocked: First Settlement, Five Payments Completed, Debt-Free Hero, Weekend Warrior, Consistent Settler and Group Champion. A group can only be left when you owe nothing, nobody owes you, and none of your payments is awaiting.

> **Planned (not in the code yet):** *Transaction badges.* Ambani goes to the person with the most to receive, Vijay Mallya to the person deepest in debt, and Nirmala Sitharaman to the middleman of a chain (A owes B, B owes C, A owes C → B). They would be per group, based on settled balances only, and would never change any amount.


Weekly Batch: Ambani, Mallya & Nirmala of the Week

Every week the group is "batched" and each member can earn a title based on how money actually moved (approved payments only):

Title	Who gets it	Meaning
👑 Ambani of the Week	The person who made the most payments during the week	The group's top payer
🏴‍☠️ Vijay Mallya of the Week	The person who made no payment at all while owing money	The one who vanished with the dues
🧾 Nirmala of the Week	The middle person in a chain of debts	Settles things without paying a rupee

How Nirmala works: if A owes B and B owes C, B does not need to pay anyone. Budget Baba's debt simplification collapses the chain into a single payment, A pays C directly, and B, who only sat in the middle, becomes the Nirmala of the payment. (If the loop closes, with C also owing A, the debts cancel out and nobody has to pay.)

Titles are shown on the Weekly page, and Ambani and Mallya feed the existing XP, badge and "Ghost Baba watchlist" system.
## 4. Setup & Running Instructions

Requirements: Python 3.10+ and Node.js 18+ with npm. No internet is needed after installing dependencies, and no API keys are used.

Start the backend in terminal 1:

```bash
cd backend
pip install -r requirements.txt
uvicorn main:app --reload      # http://127.0.0.1:8000
```

Start the frontend in terminal 2:

```bash
cd frontend
npm install
npm run dev                    # http://localhost:5173
```

Open http://localhost:5173. The Vite dev server proxies `/api` to the backend. Backend API documentation is at http://127.0.0.1:8000/docs.

The SQLite file (`backend/fairsplit.db`) is created and seeded on first start. Delete it to reset. A random signing secret is generated once into `backend/.secret` (or set `FAIRSPLIT_SECRET`). Sessions last 7 days.

Validation:

```bash
# backend
cd backend && pip install httpx && python test_api.py   # uses a throw-away database

# frontend
cd frontend && npm run build
```

The frontend build and the 165 backend API checks passed during verification.

## 5. Feature Demonstration

Demo logins (password `demo123`): aarav@demo.com · meera@demo.com · rohan@demo.com · isha@demo.com. You can also tap a demo chip on the login page.

### Feature 1: Receiver-Approved Payment Workflow

1. Sign in as **Aarav** and open **Approvals**. Isha's ₹500 payment is waiting.
2. Approve it. It is a partial payment, so the debt reduces but stays open, and Isha earns +20 XP.
3. Sign out and sign in as **Rohan**. Approve Meera's rent payment.
4. To test the safeguards, add an expense, record a payment as the debtor, then try to pay more than you owe, or approve the same payment in two tabs.

**Expected outcome:** debt changes only after the receiver approves. Both people are notified, the oldest shares settle first, and a payment can be decided only once.

### Feature 2: Balances, Budget Baba and Rewards

1. Open a group page and the **Who owes whom** balances. Check the Budget Baba suggestions for the simplest way to settle.
2. Open **Achievements** as Isha or Meera to see XP, level, streak and unlocked badges.
3. Check **Notifications**, **Weekly summary** and **History** for the full activity trail.
4. Try leaving a group while you still owe money. It is blocked until you are settled.

**Expected outcome:** balances, suggestions, XP and badges stay consistent with the approved payments.

## Project Layout

`backend/main.py` (models, API, logic) · `backend/seed.py` · `backend/test_api.py` · `frontend/src/App.jsx` (all pages) · `frontend/src/styles.css`

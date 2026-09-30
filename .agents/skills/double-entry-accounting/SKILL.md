---
name: double-entry-accounting
description: >-
  Implement and validate a double-entry ledger, chart of accounts, journals, trial balance, P&L and balance sheet.
---

# Double-Entry Accounting

- Separate from customer wallet ledger (§22). Debits == credits always.
- Core accounts (Assets/Liabilities/Equity/Revenue/Expenses) per §22.
- Prepaid flow: top-up Debit Clearing / Credit Wallet Liability; usage Debit Liability / Credit Revenue; provider cost Debit COGS / Credit Payable; settlement Debit Bank + Fee / Credit Clearing.
- Journal entries and lines are append-only; never cascade-delete financial history.
- Finance agent must refuse to fabricate missing data (label UNRECONCILED).
- All rules configurable + accountant-reviewable before statutory use.

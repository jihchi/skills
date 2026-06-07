---
name: plaintext-accounting-ledger
description: Practice plain text accounting with Ledger CLI. Use when the user wants an agent to inspect, edit, validate, or report on Ledger-compatible plain text accounting journals.
---

# Plain Text Accounting with Ledger CLI

Coach/operator for **Ledger CLI only**: maintain human-readable double-entry journals, run reports, and make safe edits. Do not offer hledger, Beancount, CSV/bank-statement imports, or tax/accounting advice. Brief educational explanations are OK.

## References

Local refs in `references/`: `README.md`, `ledger3.txt`, `ledger.1.md`, `ledger-mode.txt`, `what-is-plain-text-accounting.html`.

Read `references/README.md` during normal use. Read full refs only for unfamiliar commands/arguments, edge cases, or workflows: lots, prices, virtual postings, budgeting, forecasting, archiving, aliases, strictness, ledger-mode behavior, `ledger print` formatting.

## Principles

- Ledger CLI reports from journals; it normally does not mutate input. Edit deliberately.
- Never invent facts: dates, amounts, payees, accounts, balances, commodities, prices, tax treatment. Use only the person, source material, or strong ledger precedent.
- Preserve local conventions: account names, date style, commodities, transaction-state meaning, metadata, layout, insertion order.
- Ask only for meaningful ambiguity affecting accounting meaning, file placement, or destructive/reformatting behavior.
- Write explicit posting amounts; do not rely on amount elision.
- Do not modify user-global Ledger config (`~/.ledgerrc`).
- Be VCS-neutral: do not require/manage/reason about git/VCS workflows.
- Prefer report-based validation over routine balance assertions.

## Discover the ledger before editing

1. Find Ledger Root: prefer local `.ledgerrc`; else roots like `index.ledger`, `index.txt`, `journal.ledger`, `main.ledger`, or a single obvious ledger file. If multiple plausible roots, ask.
2. Inspect includes and nearby journals.
3. Detect organization (single/year/month/account/include-root), date style, account families/vocabulary, commodity declarations/formatting, metadata/tag conventions, cleared/pending/uncleared usage, aliases, virtual postings, budgets, price DB.
4. Baseline parse:

```sh
ledger --file <root> source
```

If invalid, stop and report the pre-existing issue before edits. Use `--strict`/`--pedantic` only when the ledger already declares accounts/commodities/tags or the person asks.

## Editing rules

- **Where:** Do not append transactions to an include-only root unless existing practice. Choose target journal by organization (year/month/account/single file). Ask if ambiguous.
- **Insertion/format:** Preserve insertion order (chronological, append-only, or unsorted); do not broadly reorder. Preserve dominant date style; for brand-new ledgers use `YYYY-MM-DD`. State every posting amount. Preserve local formatting unless cleanup is requested.
- **Categorization:** Search prior same/similar payee first. Use `ledger xact`/`ledger entry` only with good precedent; otherwise write manually. If no strong precedent, ask. Do not silently create `Expenses:Unknown` unless placeholders are explicitly allowed.
- **State:** Infer only from evidence/local convention: imported/statement-proven may be cleared; planned/manual usually uncleared; uncertain may be pending. Ask if state meaning is unclear.
- **Metadata/docs:** Reuse metadata/tag conventions. Avoid adding metadata if unused unless asked. Record receipt/invoice/statement/document refs only if convention exists or the person provides the ref.

## Validation after edits

Always run:

```sh
ledger --file <root> source
```

Then run the smallest proving report, e.g.:

```sh
ledger --file <root> reg <account-or-payee-query>
ledger --file <root> bal <account-query>
ledger --file <root> accounts
ledger --file <root> payees
ledger --file <root> commodities
ledger --file <root> stats
```

Summarize changes and passed validation.

## Core Ledger toolkit

Defaults: `source` parse validation; `balance`/`bal` balances; `register`/`reg` history/running totals; `print` canonical Ledger-readable output (only for intentional formatting/cleanup); `accounts`, `payees`, `commodities`, `tags`, `stats` introspection; `xact`/`entry` drafts from precedent; `prices`/`pricedb` price history for commodities/investments.

Do not use XML, Lisp, Python extension, Org Babel, plotting, or developer commands unless explicitly asked or genuinely required.

## Common reports

Use `<root>` for Ledger Root.

```sh
ledger --file <root> bal                                # overall balances
ledger --file <root> bal ^Assets ^Liabilities           # net worth
ledger --file <root> bal ^Income ^Expenses              # cash flow
ledger --file <root> reg <account>                      # account register
ledger --file <root> reg @<payee>                       # payee history
ledger --file <root> -p "this month" bal ^Expenses      # this month's expenses
ledger --file <root> -M --period-sort "amount" reg ^Expenses # monthly expenses
ledger --file <root> accounts                           # accounts inventory
ledger --file <root> payees                             # payees inventory
ledger --file <root> commodities                        # commodities inventory
ledger --file <root> stats                              # journal summary
```

Use normal local output for correctness. Anonymize/redact examples, shared diagnostics, or external reports.

## New-ledger setup

Create only when explicitly asked. Ask for: root path/file organization; preferred currencies/commodities; opening date; opening balances for assets/liabilities; desired account families/names. Recommend common families (`Assets`, `Liabilities`, `Income`, `Expenses`, `Equity`) but adapt.

Declare only named commodities, e.g.:

```ledger
commodity $
    format $1,000.00
    alias USD
```

Opening balance pattern:

```ledger
2026-01-01 * Opening Balance
    Assets:Bank:Checking          $1,000.00
    Liabilities:Credit Card        $-50.00
    Equity:Opening Balances        $-950.00
```

Validate with `source`, then show `bal`.

## Transaction examples

Use local date style and commodity formatting in real edits.

```ledger
; Expense paid from asset
2026-01-15 Grocery Store
    Expenses:Food:Groceries         $45.67
    Assets:Bank:Checking           $-45.67

; Income received
2026-01-31 Employer
    Assets:Bank:Checking         $3,000.00
    Income:Salary               $-3,000.00

; Transfer between assets
2026-02-01 Transfer
    Assets:Bank:Savings            $500.00
    Assets:Bank:Checking          $-500.00

; Credit card purchase
2026-02-05 Restaurant
    Expenses:Food:Dining            $32.10
    Liabilities:Credit Card        $-32.10

; Credit card payment
2026-02-20 Credit card payment
    Liabilities:Credit Card        $250.00
    Assets:Bank:Checking          $-250.00
```

Reimbursement as asset owed back: ask whether the person wants this or ordinary expense treatment.

```ledger
2026-03-01 Office Supplies
    Assets:Reimbursements:Employer  $80.00
    Assets:Bank:Checking           $-80.00

2026-03-15 Employer reimbursement
    Assets:Bank:Checking            $80.00
    Assets:Reimbursements:Employer $-80.00
```

Detailed cash purchase: do not use petty-cash simplification by default; record real category.

```ledger
2026-04-02 Bakery
    Expenses:Food:Snacks             $6.50
    Assets:Cash                     $-6.50
```

Investment/multi-commodity purchase: treat investments, crypto, foreign currency, lots, costs, and price DBs as advanced commodity workflows; ask for explicit confirmation.

```ledger
2026-05-10 Broker
    Assets:Brokerage                 10 AAPL @ $180.00
    Assets:Brokerage:Cash        $-1,800.00
```

## Special workflows

- Budgeting/forecasting: use periodic transactions and `--budget`/`--forecast` only when explicitly asked.
- Virtual postings: use only if existing ledger already uses them or the person explicitly asks for a workflow needing non-real postings.
- Aliases: use account/payee aliases only if existing ledger already uses aliases or the person asks for normalization rules.
- `ledger print`: may format when formatting cleanup is the task; do not treat normalized output as source of truth during ordinary edits.
- Archiving old years: only when explicitly asked. Consult manual, then carefully use documented `print` and `equity` workflow.

## Communication style

Be concise. Explain accounting concepts briefly when useful. State assumptions and validation commands. Ask one clear question when meaningful ambiguity blocks safe work.

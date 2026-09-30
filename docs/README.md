# Student Account Management System

## Purpose

This COBOL sample provides a console menu for viewing and changing a single student account balance. The account is held in memory for the duration of the program; the sample does not load or save account data to an external system.

## COBOL Files

| File | Purpose | Key logic |
| --- | --- | --- |
| `src/cobol/main.cob` | Console entry point and menu loop. | Displays the account options, accepts a choice, and calls `Operations` to view the balance, credit the account, or debit it. Choice 4 exits; other choices display an error. |
| `src/cobol/operations.cob` | Handles account operations and user interaction for amounts. | Reads and displays the balance for `TOTAL`; prompts for an amount, updates the balance, and displays the result for `CREDIT`; checks available funds before processing `DEBIT`. |
| `src/cobol/data.cob` | Stores and retrieves the current balance. | `READ` copies the stored balance to the caller; `WRITE` copies the caller's balance into storage. The stored balance is initialized to 1000.00. |

## Account Business Rules

- The single account starts with a balance of **1000.00**.
- A credit adds the entered amount to the current balance.
- A debit is processed only when the current balance is greater than or equal to the requested amount. A debit equal to the full balance is allowed; a larger debit is rejected with an insufficient-funds message and does not change the balance.
- The program operates on one in-memory balance. It does not identify individual students, manage multiple accounts, or persist changes after the process ends.
- The code does not define additional validation rules for entered amounts, such as a positive-only check or explicit range/error handling.

## Operation Flow

`MainProgram` dispatches menu selections to `Operations`. `Operations` uses `DataProgram` to read the current balance before displaying or changing it, and writes the new balance after a successful credit or debit.

## Sequence Diagram

```mermaid
sequenceDiagram
actor User
participant Main as MainProgram
participant Ops as Operations
participant Data as DataProgram

loop Until the user exits
Main->>User: Display menu and prompt
User->>Main: Enter menu choice
alt View balance (1)
Main->>Ops: TOTAL
Ops->>Data: READ balance
Data-->>Ops: Current balance
Ops->>User: Display current balance
else Credit account (2)
Main->>Ops: CREDIT
Ops->>User: Prompt for credit amount
User->>Ops: Enter amount
Ops->>Data: READ balance
Data-->>Ops: Current balance
Ops->>Ops: Add amount to balance
Ops->>Data: WRITE updated balance
Data-->>Ops: Balance stored
Ops->>User: Display updated balance
else Debit account (3)
Main->>Ops: DEBIT
Ops->>User: Prompt for debit amount
User->>Ops: Enter amount
Ops->>Data: READ balance
Data-->>Ops: Current balance
alt Balance is sufficient
Ops->>Ops: Subtract amount from balance
Ops->>Data: WRITE updated balance
Data-->>Ops: Balance stored
Ops->>User: Display updated balance
else Insufficient funds
Ops->>User: Display insufficient-funds message
end
else Exit (4)
Main->>Main: Set continue flag to NO
else Invalid choice
Main->>User: Display invalid-choice message
end
end
Main->>User: Display goodbye message
Main->>Main: STOP RUN
```

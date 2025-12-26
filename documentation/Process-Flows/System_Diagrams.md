# System Diagrams

## 1. Transaction Status Lifecycle
This state machine illustrates the lifecycle of a remittance transaction from initiation to final payout.

```mermaid
stateDiagram-v2
    [*] --> Initiated: SendTransaction()
    Initiated --> PendingCommit: Success Response
    
    state "Wait for Commit" as PendingCommit
    PendingCommit --> Paid: CommitTransaction()
    PendingCommit --> Expired: No Commit > 30 mins
    
    Paid --> [*]
    Expired --> [*]
    
    Paid --> Cancelled: CancelTransaction() (If not yet picked up)
    Cancelled --> [*]
```

## 2. Partner Onboarding Process Flow
A high-level view of how we bring new partners live.

```mermaid
flowchart LR
    A[Partner Request] --> B{Biz Review}
    B -- Yes --> C[Sign NDA]
    B -- No --> D[Reject]
    
    C --> E[Share API Docs]
    E --> F[UAT Integration]
    F --> G{UAT Sign-off?}
    
    G -- No --> F
    G -- Yes --> H[Prod Keys Config]
    H --> I[Go Live]
```

# Business Rules & Compliance Guidelines

As the Business Analyst for this integration, I defined the following rules to ensure compliance with the **Nepal Rastra Bank (Central Bank of Nepal)** regulations and our internal risk policies.

## 1. KYC (Know Your Customer) Requirements
All transactions require full Sender KYC. Missing fields will result in a `400 Bad Request`.

### Mandatory Sender Fields
| Field | Description | Validation Rule |
|-------|-------------|-----------------|
| `senderFirstName` | First Name | Min 2 chars, No special chars. |
| `senderIdType` | ID Document Type | Must use standard codes (01=Passport, 02=National ID). |
| `senderIdNumber` | ID Number | Must limit-check against ID type format. |
| `senderOccupation` | Job/Role | Required for AML monitoring. |
| `senderSourceOfFund`| Origin of money | Must match the profile (e.g., Salary). |

> **BA Decision**: We enforced `senderDateOfBirth` validation (Must be > 18 years) at the API level to reduce manual compliance rejections later in the queue.

## 2. Transaction Limits
- **Per Transaction**: Max 1,000,000 NPR (approx $7,500 USD).
- **Per Day (Sender)**: Max 5 transactions.
- **Account Deposit**: Any amount > 100,000 NPR requires enhanced due diligence (EDD).

## 3. purpose of Remittance Codes
Partners must map their internal codes to our standard list:

| Code | Meaning | Use Case |
|------|---------|----------|
| `1` | Family Maintenance | Standard sending to relatives. |
| `2` | Savings | Sending to own account. |
| `3` | Education | Tuition fees (requires student ID proof). |
| `4` | Gift/Donation | Low limits apply. |

## 4. Payout Modes
The `paymentMode` field dictates the flow:
- **`B` (Bank Deposit)**: Instant credit to 50+ Nepali banks. Requires `bankAccountNumber`.
- **`W` (Wallet Load)**: Real-time top-up (e.g., eSewa, Khalti). Requires `receiverMobile`.
- **`C` (Cash Pickup)**: Beneficiary picks up cash at 10,000+ locations. Requires `pinNumber` generation.

## 5. Cancellation Policy
- **Before Payout**: Transactions can be cancelled via `/CancelTransaction`.
- **After Payout**: Irreversible. Partners must raise a manual "Recall Request" via the operations portal.

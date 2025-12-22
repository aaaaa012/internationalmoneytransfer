# API Integration Guide

This document outlines the technical steps required to integrate with the Remittance API. It is written for the developer teams of our sending partners, based on the requirements I gathered during the solution design phase.

## 1. Authentication & Security
We use a custom **HMAC-SHA256** signature scheme to ensure message integrity.

### Header Requirements
Every request must include the following header:
`Authorization: hmacauth [API_KEY]:[SIGNATURE]:[NONCE]:[TIMESTAMP]`

### Signature Generation Logic
The signature is a Base64-encoded HMAC-SHA256 hash of a concatenated string.
**Data to Sign Structure:**
`[API_KEY] + [METHOD] + [URL_PATH] + [TIMESTAMP] + [NONCE] + [BODY_HASH]`

- **METHOD**: Uppercase (e.g., `POST`)
- **BODY_HASH**: MD5 hash of the raw JSON body (Base64 encoded).
- **TIMESTAMP**: Unix epoch.

> **BA Note:** This strict signature requirement was introduced to prevent "replay attacks" and ensure that no payload is tampered with during transit.

---

## 2. Transaction Flow (The 'Happy Path')

### Step 1: Connectivity Check
**Endpoint**: `/GetEcho`
Before starting daily batched or real-time sends, initiate a heartbeat check.
- **Input**: `agentSessionId`
- **Success**: Returns the same ID.

### Step 2: Validation (Recommended)
**Endpoint**: `/AccountValidation`
Reduce failure rates by verifying the bank account *before* deducting funds from your customer.
- **Critical Fields**: `bankAccountNumber`, `locationId` (Bank Code).
- **Business Rule**: If this returns "Invalid", do **not** proceed to Send.

### Step 3: Initiate Transaction
**Endpoint**: `/SendTransaction`
This action performs KYC checks and reserves the transaction amount.
- **Request**: Full Sender/Receiver details.
- **Response**: Returns a `confirmationId`.
- **Status**: Transaction is now in **HOLD** state. It is NOT yet paid out.

### Step 4: Finalize
**Endpoint**: `/CommitTransaction`
The final handshake. Without this call, the transaction in Step 3 will auto-expire after 30 minutes.
- **Input**: `confirmationId` (from Step 3 response).
- **Action**: Funds are released to the beneficiary.

---

## 3. Best Practices
1.  **Idempotency**: Always use a unique `agentTxnId` for every send request.
2.  **Timeouts**: If you don't receive a response for `SendTransaction`, call `QueryTXNStatus` with your `agentTxnId` before retrying. **Do not just retry**, as this may create duplicates.
3.  **Currency**: `payoutCurrency` is fixed to `NPR` for this corridor. `remitCurrency` can be USD, EUR, etc.

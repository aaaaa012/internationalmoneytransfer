# Error Code Reference

This document helps partner developers handle API responses correctly. I designed these codes to separate **Technical Errors** (retriable) from **Business Errors** (non-retriable).

## Response Structure
```json
{
  "code": "1001",
  "message": "Invalid Account Number",
  "status": "FAILED"
}
```

## Code List

| Code | Type | Message | Action Required |
|------|------|---------|-----------------|
| `0` | Success | Success | Store the `pinNumber` or `refId` and show success to user. |
| `1001` | Business | Invalid Account | Ask user to check the account number. **Do not retry.** |
| `1002` | Business | Limit Exceeded | User sent too much. Block request until tomorrow. |
| `1003` | Business | Invalid Bank Code | Update your bank list via `/GetCatalogue`. |
| `1004` | Compliance | Restricted Country | Sender/Receiver country is on sanctions list. |
| `9001` | Technical | Signature Mismatch | Check your HMAC logic or timestamp sync. |
| `9002` | Technical | System Timeout | **Retry** using `QueryTXNStatus` first. |
| `9003` | Technical | Database Lock | Wait 5 seconds and retry. |

 **Note**: If you receive `9002` (Timeout), never assume the transaction failed. Always query the status to prevent double-charging your customer.

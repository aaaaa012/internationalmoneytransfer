# Partner Onboarding Checklist

I used this tracker to manage the integration lifecycle for new money transfer partners.

## Phase 1: Kick-off & Credentials
- [ ] NDA Signed
- [ ] Commercial Agreement Finalized (Exchange Rate margins defined)
- [ ] IP Whitelisting Form received
- [ ] UAT Credentials (`api-key`, `api-secret`) shared securely

## Phase 2: Development & Integration
- [ ] Connectivity Test (`/GetEcho`) successful
- [ ] Static Data Ingest (`/GetCatalogue`) implemented
- [ ] HMAC Signature generation verified
- [ ] **Milestone**: First successful UAT transaction (Cash Mode)
- [ ] **Milestone**: First successful UAT transaction (Bank Mode)
- [ ] **Milestone**: First successful UAT transaction (Wallet Mode)


## Phase 3: UAT Verification (BA Sign-off)
- [ ] **Scenario 1**: Valid Transaction (Happy Path)
- [ ] **Scenario 2**: Invalid Account Number (Error Handling)
- [ ] **Scenario 3**: Transaction > Limit (Compliance Check)
- [ ] **Scenario 4**: Timeout Simulation (Idempotency Check)

## Phase 4: Production Go-Live
- [ ] Production Keys Generated
- [ ] Production IPs Whitelisted
- [ ] Penny Test (Real money, small amount) confirmed
- [ ] **GO LIVE** 

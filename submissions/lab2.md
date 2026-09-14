# Lab 2 - Threat Modeling: STRIDE on Juice Shop with Threagile

## Task 1

### Severity table

| Severity | Count |
|---|---|
| elevated | 4 |
| medium | 14 |
| low | 5 |
| total | 23 |



| Severity | Id | Where | Stride | Explanation |
|---|---|---|---|---|
| elevated      | unencrypted-communication     | user-browser   | i  | unencrypted means exposed info
| elevated      | unencrypted-communication     | reverse-proxy  | i  | unencrypted means exposed info
| elevated      | cross-site-scripting          | juice-shop     | t  | allows script interjection, thats tampering
| elevated      | missing-authentication        | juice-shop     | s  | improper auth allows impersonation, that's spoofing
| medium        | cross-site-request-forgery    | juice-shop     | s  | tricks users to do what they didnt want to, using their account privileges

## Task 2

|Typre | Baseline | Secure | Delta |
|---|---|---|---|
| elevated | 4 | 1 | -3 |
| medium | 14 | 12 | -2 |
| low | 5 | 5 | 0
| total | 23 | 18 | -5 |

| Gone | Fields |
|---|---|
| missing-authentication    | Proxy-to-application HTTPS with mutual TLS authentication                                                 |
| unencrypted-asset         | Browser to reverse proxy over HTTPS on port 443 and Direct browser access to the application over HTTPS   |
| unencrypted-communication | Encryption: data-with-symmetric-shared-key for Juice Shop Application and Persistent Storage.             |

| Left | Reason|
|---|---|
| cross-site-scripting | TLS, mTLS & encryption at rest dont change input processing |
| server-side-request-forgery | Juice Shop → Webhook Endpoint outbound |

### Deployment
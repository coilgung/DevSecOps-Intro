# Lab 1 — Deploy OWASP Juice Shop & Course Workflow

## Triage report

### Asset

Image tag: `bkimminich/juice-shop:v20.0.0`

Image digest: `bkimminich/juice-shop@sha256:fd58bdc9745416afce8184ee0666278a436574633ea7880365153a63bfd418b0`

Host OS: Linux Mint 22

Docker version: Docker version 29.1.3, build 29.1.3-0ubuntu3~24.04.2

### Deployment

Run command: `docker run -d --name juice-shop -p 127.0.0.1:3000:3000 bkimminich/juice-shop:v20.0.0`

Access URL: http://127.0.0.1:3000

Bound to local host only: yea, because its unsecure by design

Restart policy: none

### Health
```
NAMES        STATUS          PORTS
juice-shop   Up 10 minutes   127.0.0.1:3000->3000/tcp
HTTP 200
{"version":"20.0.0"}46
bkimminich/juice-shop@sha256:fd58bdc9745416afce8184ee0666278a436574633ea7880365153a63bfd418b0
```
### Surface

#### the login and registration forms (Account menu, top right), 
the register does not check the email address at all, even a@a is valid, the length check is 5-40, 0-20 and at least 8, the other password advice is verbose
the forms in the login have no checks register has, just if the spaces were filled, if the email is just ' it will return [object Object], unaccounted error

#### the product list, 
so in the product list the objects get out of stock before even bought? the stock is seemingly subtracted on adding to the basket, not on actual purchase, which is weird. clicking any of the items like juices and merch shows description with price and bonuses, also reviews from users, with their emails public 

#### any admin or account area you can discover, 
so with ' or 1=1; you can get into admin account, 
http://127.0.0.1:3000/#/administration give out a whole admin panel,
shows the email of the account (not the one entered, the real one)

#### Errors in the DevTools console
when entering the accout page: Content-Security-Policy: The page’s settings blocked an inline script (script-src-elem) from being executed because it violates the following directive: “script-src 'self' 'unsafe-eval'”. Consider using a hash ('sha256-Yf13RWm+wAWNdF7OxoRECVYHDdO7Cgo6L25nvblOvew=') or a nonce.

#### anything pre-populated in Application → Local Storage or cookies. 
the local storage and cookies show the last email used to enter the accout (the injection too), all the dialogues with the ai assistant (which save between users which is a massive blunder), site language, some lstdxprt, none of it is secure



#### In the Network tab click one product and watch the requests (/api/Products/<id>/reviews and similar): do they need authentication?
no authentification:
```
scheme http
host 127.0.0.1:3000
filename /rest/products/24/reviews
Address 127.0.0.1:3000
Status 200
OK
Version HTTP/1.1
Transferred 414 B (30 B size)
Referrer Policy strict-origin-when-cross-origin
Request Priority Highest
DNS Resolution System
```


### Headers

```
HTTP/1.1 200 OK
Access-Control-Allow-Origin: *
X-Content-Type-Options: nosniff
X-Frame-Options: SAMEORIGIN
Feature-Policy: payment 'self'
X-Recruiting: /#/jobs
Accept-Ranges: bytes
Cache-Control: public, max-age=0
Last-Modified: Sun, 13 Sep 2026 13:59:25 GMT
ETag: W/"26af-1a09b10ced3"
Content-Type: text/html; charset=UTF-8
Content-Length: 9903
Vary: Accept-Encoding
Date: Sun, 13 Sep 2026 14:21:59 GMT
Connection: keep-alive
Keep-Alive: timeout=5
```

Missing: Content-Security-Policy, Strict-Transport_police

### Top 3 risks

Broken Access Control (A01) Admin functionality and user data can be accessed without proper authorization.

Security Misconfiguration (A02) Missing CSP/HSTS and overly permissive Access-Control-Allow-Origin: *.

Injection (A05) SQL injection allows login/admin bypass using ' or 1=1;.

## PR Template

Path: `.github/PULL_REQUEST_TEMPLATE.md`

Sections: `Goal`, `Changes`, `Testing`, `Artifacts & Screenshots`, and `Checklist`.

Checklist items: PR title follows `feat(labN): <topic>`; no secrets or large temporary files are committed; `submissions/labN.md` exists.

Auto-fill evidence: 




## Github community

Giving a project a star demmonstrates appreciation and recommends to others. Following instructors, TAs, and classmates is networking


## Smoke test

Workflow path: `.github/workflows/lab1-smoke.yml`.

The workflow starts on pull requests to `main`, has only `contents: read`, launches `bkimminich/juice-shop:v20.0.0`, and checks the version endpoint every five seconds for up to 60 seconds. This gives the application enough time to initialise instead of failing the job too early.


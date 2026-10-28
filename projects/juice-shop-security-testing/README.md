# OWASP Juice Shop — Web Application Security Testing

## 1. Project Overview

This project documents my practical web application security testing exercises using OWASP Juice Shop, an intentionally vulnerable web application designed for security training.

The tests were performed in my own isolated Docker based lab using Kali Linux. The purpose was to understand network reconnaissance, HTTP traffic analysis, input handling, and access control vulnerabilities.

## 2. Objectives

- Practise identifying open ports and services.
- Understand HTTP requests and responses.
- Use Burp Suite to inspect web application traffic.
- Investigate application responses to different search inputs.
- Test access control using separate lab accounts.
- Document security observations and recommend mitigations.

## 3. Lab Environment

| Component | Details |
|---|---|
| Operating System | Kali Linux |
| Lab Platform | Docker |
| Target Application | OWASP Juice Shop |
| Target Address | 172.29.10.10 |
| Target Port | 3000 |
| Tools | Nmap, Burp Suite Community Edition, cURL, jq |

**Scope:** All testing was conducted against my own local training application.

## 4. Tools and Activities

### 4.1 Network Reconnaissance

**Tool:** Nmap

I used Nmap to scan the local training application and identify open ports and running services.

Example command:

```bash
nmap -sV -p 22,80,443,3000 172.29.10.10
```

**Purpose:** To practise identifying accessible network services on the lab target.

### 4.2 HTTP Traffic Analysis

**Tools:** Burp Suite and cURL

I inspected HTTP requests and responses to understand how the browser communicates with the web application.

Activities included:
- Reviewing HTTP methods and response status codes.
- Inspecting request headers and response bodies.
- Using Burp Suite HTTP History to review application traffic.
- Sending controlled requests for further analysis.

### 4.3 Search Input Testing

**Target endpoint:**

`/rest/products/search?q=apple`

I compared application responses for different search inputs. I observed differences in the returned data and an SQLite syntax error when testing malformed input.

**Observation:** The error suggests that the application encountered an input-related database query problem.

**Limitation:** An error message alone does not prove that SQL injection was successfully exploited. Further controlled testing would be needed to establish exploitability and impact.

## 5. Security Finding: Cross-User Basket Data Disclosure

**Category:** Broken Access Control  
**Related vulnerability class:** IDOR / Broken Object Level Authorization (BOLA)  
**Environment:** Local OWASP Juice Shop training application

### Description

During an access control exercise, I used two self-created lab accounts to test basket access.

A request authenticated as one test user returned shopping basket data associated with the other test user. The response included the other user's basket information.

This behaviour is consistent with an IDOR/BOLA-style access control weakness because the application returned an object that did not belong to the authenticated user.

### Expected Behaviour

An authenticated user should only be able to access baskets that they are authorised to view.

### Observed Behaviour

A request made with one test account's authentication was able to retrieve another test account's basket data.

### Potential Impact

If the same weakness existed in a real application, an unauthorised user might be able to view another user's shopping basket information.

The actual impact would depend on the data exposed and the application's other security controls.

### Recommended Remediation

1. Verify basket ownership on the server for every request.
2. Derive the authenticated user's identity from the validated session or token.
3. Reject requests when the requested basket does not belong to the authenticated user.
4. Apply object-level authorisation checks consistently across relevant API endpoints.
5. Add automated tests to verify that one user cannot access another user's basket.

### Testing Limitations

The exercise demonstrated cross-user data disclosure in an intentionally vulnerable local training application. I tested data access only; I did not test modifying or deleting another user's basket.

This finding does not establish that a real company's application has the same vulnerability.

## 6. Evidence to Add

The following sanitised screenshots will be added as supporting evidence:

- Nmap scan results.
- Burp Suite HTTP request and response.
- Search input testing and response comparison.
- Cross-user basket access test, showing the relevant request and response.

Authentication tokens, session cookies, email addresses, password hashes, and other sensitive information must be redacted before publication.

## 7. Learning Outcomes

Through this project, I practised:

- Basic network reconnaissance.
- HTTP request and response analysis.
- Web application input testing.
- Authentication and object-level access control testing.
- Identifying and documenting potential security vulnerabilities.
- Writing remediation recommendations.

## 8. Conclusion

This project helped me develop practical experience with web application security testing in an isolated training environment. It also improved my understanding of access control weaknesses and the importance of verifying object ownership on the server.

I will continue expanding this portfolio with additional lab exercises, evidence, and technical reports.

---

**Project type:** Personal cybersecurity lab  
**Target:** Intentionally vulnerable training application  
**Testing environment:** Local Docker lab

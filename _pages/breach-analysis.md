---
author_profile: true
tags: Incident Analysis Data Breach
toc: true
comments: true
permalink: /breach-analysis/
---

# Breach Analysis

Data Breach avoidance is the fist principle goal of most cyber security teams. But security breaches happen all the time. Can we learn something from them? Let's look at some of the incidents, unravel what happened and how it could have been avoided.

Organizations might not be very transparent with sharing the details of how the compromise occurred because it might expose their lack of best practices. As such, details need to be pulled form unverified sources. So take the "How" information below with a pinch of salt.

## Tangerine Telecom - 2024

Australian ISP Tangerine was breached on February 18, 2024, with over 200,000 records stolen by hackers. Full personal information (names, date of birth, phone numbers, and email addresses) was taken; bank and password details were not.

### How

- Contractor had access to legacy data of customers

### Missing controls

> Assuming that contractor's device was compromised by a credential stealer providing access to their saved credentials and browser sessions to the attacker

- ✋ BYOD policy must mandate EDR, patching, etc. This can be implemented by tools like Duo that run device checks and integrate with SSO.
- ✋ Principle of least privilege - The contractor did not likely require access to all the legacy customer data. Limit access to DB tables. Sensitive data can also be masked to prevent exposure in case a user has access to the table due to some access misconfiguration.
- 🔍 Control, Track, Monitory and Audit sensitive data such as customer PII

### Links

- [Tangerine Statement](https://www.tangerinetelecom.com.au/news/media-statement-tangerine-cyber-incident)

## Opus Telecom - 2022

Optus, a Telecom in Australia, was breach in 2022. Information which may have been exposed includes customers’ names, dates of birth, phone numbers, email addresses, and, for a subset of customers, addresses, ID document numbers such as driver's license or passport numbers.

### How

Unprotected and publicly exposed API

### Missing controls

- ✋ Public API endpoint without authentication and authorization
- 🔒 Having incremental user ids allowed the attacker to query all users
- 🔍 Automated scanning of API endpoints using DAST, EASM, vulnerability scanning tools could have detected this issue

### Links

- [Optus Statement](https://www.optus.com.au/about/media-centre/media-releases/2022/09/optus-notifies-customers-of-cyberattack)
- [UpGard - How did the Optus Data Breach Happen?](https://www.upguard.com/blog/how-did-the-optus-data-breach-happen)
- [Wiki](https://en.wikipedia.org/wiki/2022_Optus_data_breach)

## Capital One 2019

2019 data breach of Capital One, the fifth largest consumer bank in the U.S., exposed 100 million customers’ accounts and credit card applications. In addition to the reputation damage, the company expects to incur between $100 million and $150 million in costs related to the hack.

### How

Server Side Request Forgery (SSRF) was used to exfiltrate AWS credentials since IMDSv1 was enabled on the EC2 instance. This allowed the attacker to access S3 buckets with the data.

### Missing controls

- ✋ Disable IMDSv1 and enable IMDSv2 on EC2 instances
- ✋ Run SAST scans and Code Review to identify SSRF vulnerabilities in code. This does not apply if, as some reports stated, the WAF had a misconfiguration that allowed SSRF.
- 🔒 Implement the principal of least privilege to limit permissions of the role in the instance profile. The assigned role likely did not require list bucket and sync permissions to the S3 buckets with user data.
- 🔍 Monitor access to sensitive information such as PII by monitoring S3 bucket access logs and report anomalies.

### Links

- [Capital One Statement](https://www.capitalone.com/digital/facts2019/)
- [ACM Consolidated Article](https://dl.acm.org/doi/10.1145/3546068)
- [MIT Case Study](https://cams.mit.edu/wp-content/uploads/capitalonedatapaper.pdf)

## Control Icons

| Syntax                           | Description |
| -------------------------------- | ----------- |
| preventive control               | ✋           |
| secure design & defence in depth | 🔒           |
| detective control                | 🔍           |

## Template

```
## BreachName

### How

### Missing controls

### Links
```

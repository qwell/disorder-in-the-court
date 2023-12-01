# Disorder in the Court

## Description

Insufficient permission check vulnerabilities in public court record platforms from multiple vendors allowed unauthorized public access to sealed, confidential, unredacted, and/or otherwise restricted case documents. Affected documents include witness lists and testimony, mental health evaluations, child custody agreements, detailed allegations of abuse, corporate trade secrets, jury forms, and much more.

## Details

- Five platforms used by individual courts in Florida -- [Brevard County](#brevard-county-florida), [Hillsborough County](#hillsborough-county-florida), [Lee County](#lee-county-florida), [Monroe County](#monroe-county-florida), and [Sarasota County](#sarasota-county-florida) -- are each presumed to be developed "in-house"[^2] by the county court.

While all of the platforms allowed unintended public access to restricted documents, the severity varied based on the levels of restrictions that could be bypassed and the discoverability of document IDs. The methods used to exploit each of the vulnerabilities also varied, but could all be performed by an unauthenticated attacker using only a browser's developer tools.

## Platforms

### Brevard County, Florida

Document URLs contain a version of the document ID that is encrypted using a method that includes an expiry mechanism. Although every docket entry displays the associated document ID, the encrypted form -- which is required to view documents -- is only provided for documents that the user has access to. This would be a great method that enables sharing of documents for a limited time, but for one fatal flaw.

Along with the encrypted document ID, the URLs also contain the query parameters `theIV=` and `theKey=`, which an astute observer might recognize as AES. Using the IV and key from the URL, an attacker can encrypt a document ID and use it to view a restricted document. Additionally, URLs include the parameter `isRedacted=` which, as the name suggests, accepts the encrypted form of the strings "Yes" or "No" to view unredacted copies of documents.

### Hillsborough County, Florida

Session cookies are used to determine which cases and documents a user is viewing. When a case or document is requested, a request is sent to the API, which associates the data with the user's session cookie and returns the results. The API endpoint for obtaining document information returns a list that includes the document ID, several values that specify the security level required to view the document, and the user's applied access level. The frontend chooses whether to display a document link or one of the restriction type indicators based on the applied access level. The backend assumes that if an attacker is able to request a document, they must have access to it. An attacker can view restricted documents by changing the security level to a more permissive value.

### Lee County, Florida

Session cookies are used to determine which cases and documents a user was viewing. When a case or document is requested, a request is sent to the API, which associates the data with the user's session cookie and returns the results. For most types of cases, document IDs are available in the pre-rendered HTML. Restricted documents could be viewed by executing the `pushDataAndShow()` function from the site's JavaScript source code.

### Monroe County, Florida

A similar vulnerability was discovered initially, however their attempted fix introduced a new vulnerability. Much like Hillsborough County, the frontend chooses whether to display a document link based on the security level of the document and the backend incorrectly assumes that all requests should be trusted. After fixing the first vulnerability, the developers helpfully left a debugger statement immediately before the security level was checked, which paused code execution and gave an attacker a chance to adjust the security level and exploit this vulnerability. The debugger statement has since been removed and the backend no longer returns valid document IDs for restricted documents, however the backend still accepts restricted document IDs.

### Sarasota County, Florida

In what is certainly the most egregious of the Florida county vulnerabilities, document URLs contained nothing more than a numeric document ID. An attacker could view restricted documents by simply incrementing the document ID in the URL. The only protection was a CAPTCHA on the landing page, which could be bypassed.

Given the ease with which this vulnerability could be discovered and exploited, it is reasonable to assume that every document was compromised the moment it was filed.

In defense of Sarasota County, they were the first to attempt to fix their issue. Unfortunately, their first attempt at a fix was not sufficient and a new vulnerability was discovered. Worse yet, while searching for a new method, a third vulnerability (or a second consequence of the second vulnerability) was discovered that allowed an unauthenticated attacker to view restricted cases.

## Timeline

- 2023-06-21 - Vulnerability #1 discovered in Monroe County.
- 2023-07-04 - Report #1 sent to Monroe County - _no response_.
- 2023-07-14 - Report #2 sent to Monroe County - _no response_.
- 2023-08-?? - **Vulnerability #1 confirmed fixed in Monroe County.**
- 2023-08-?? - Vulnerability #2 discovered in Monroe County.
- 2023-09-14 - Report for Monroe County vulnerability sent to [Jason Parker](https://ꩰ.com/@north) at [Jeltz](https://jeltz.org) by "Eli", for further research.
- 2023-09-15 - Vulnerability discovered in Lee County.
- 2023-09-15 - Vulnerability #1 discovered in Sarasota County.
- 2023-09-16 - Vulnerability discovered in Hillsborough County.
- 2023-09-18 - Vulnerability discovered in Brevard County.
- 2023-10-02 - Report for all vulnerabilities sent to [CERT Coordination Center](https://www.kb.cert.org/vuls/) (CERT/CC).
- 2023-10-03 - Report for all Florida court vulnerabilities sent to Florida's Office of the State Courts Administrator (OSCA).
- 2023-10-04 - Report for all vulnerabilities sent to [Cybersecurity and Infrastructure Security Agency](https://cisa.gov) (CISA) by CERT/CC.
- 2023-10-06 - Response from Florida OSCA.
- 2023-10-06 - Report sent to Florida Court Clerks & Comptrollers by Florida OSCA.
- 2023-10-11 - **Vulnerability #1 confirmed fixed in Sarasota County.**
- 2023-10-11 - Vulnerability #2 discovered in Sarasota County.
- 2023-10-27 - **Vulnerability #2 confirmed fixed in Sarasota County.**
- 2023-11-28 - **Vulnerability #2 confirmed partially fixed in Monroe County.**
- 2023-11-29 - **Vulnerability confirmed fixed in Lee County.**
- 2023-11-30 - Disclosure published.

## Overview by Platform

| Vendor              | Platform | IDs Available | Access | Fix Date   |
| ------------------- | -------- | ------------- | ------ | ---------- |
| Brevard County      |          | Yes           | RU     |            |
| Hillsborough County |          | Limited       | R      |            |
| Lee County          |          | Limited       | RZ     | 2023-11-29 |
| Monroe County       |          | Yes           | R      | 2023-11-28 |
| Sarasota County     |          | No            | R      | 2023-10-27 |

**Key**:

- _IDs Available_:
  - Whether restricted document IDs are available to an attacker.
- _Access_:
  - _R_ - Sealed, Confidential, Pending Redaction, and/or VOR (Viewable on Request).
  - _U_ - Unredacted.
  - _Z_ - Attackers can view partial details of cases marked as restricted.

## Acknowledgements

- "Eli", who discovered the first set of court vulnerabilities and has been a major contributor throughout the process. _[Is this how most adults find and make friends?]_
- Andrew Crocker and Hannah Zhao from the [Electronic Frontier Foundation](https://eff.org), who were there in a time of need.
- [Josh Renaud](https://www.joshrenaud.com/) from [St. Louis Post-Dispatch](https://www.stltoday.com/), who provided much needed early guidance and previously fought the fight that made it so I didn't have to, earning him a [Press Freedom Award](https://www.youtube.com/watch?v=DhflQv1rJ1A).
- [Jaku](https://twitter.com/Jaku) founder of [Crowd Control](https://crowdcontrol.live/), who frequently offered his cybersecurity wisdom and experience.
- [Zack Whittaker](https://techcrunch.com/author/zack-whittaker/) from [TechCrunch](https://techcrunch.com), who immediately recognized the severity and jumped at the chance to learn more[^3].
- [judyrecords.com](https://judyrecords.com), who handled the first round of fallout from court security issues.
- The Arkansas [Administrative Office of the Courts](https://arcourts.gov/), who allowed me to present my findings in an effort to avoid the same pitfalls when building their own court platform.
- Dedications to the Fediverse furries, who provided plenty of amusement after my Bluesky / AT Protocol vulnerability publications. I see you.
- The [CISA CVD Team](https://www.cisa.gov/coordinated-vulnerability-disclosure-process) assisted with the coordination of these vulnerabilities[^4].

## Contact

[Jason Parker](https://linktr.ee/northantara)

- Email: [north@ꩰ.com](mailto:north@ꩰ.com)
- Press: [press@jeltz.org](mailto:press@jeltz.org)
- Mastodon: [@north@ꩰ.com](https://ꩰ.com/@north)

## Sponsorship

- If you enjoy my work, consider becoming a sponsor on [Patreon](https://patreon.com/northantara) or [GitHub](https://github.com/sponsors/qwell/), and/or consider donating to the [Electronic Frontier Foundation](https://eff.org/donate) or [St. Jude](https://www.stjude.org/donate). Several hundred hours of unpaid labor have been put into researching and disclosing these vulnerabilities; no vendors have provided or offered any bounties.

## Definitions

- **Enumeration**: The process of systematically probing a system to discover valuable information, such as document names or user accounts, by incrementing values in a URL or input field.
- **Brute Force Attack**: A method of trial-and-error used to obtain information such as a password or PIN. In this case, it refers to repeatedly trying different document IDs to find restricted documents.
- **Obfuscation**: The practice of making something difficult, but not impossible, to understand. In a security context, obfuscation might be used to make files or code less readable, thereby hiding information from unauthorized users.
- **Developer Tools**: A set of tools included in most web browsers that allow developers to inspect the underlying code of a web page, monitor network requests, and test live JavaScript code among other functionalities.
- **Query Parameter**: A way to pass data in a URL, typically used in GET requests to web servers, which can be manipulated for purposes such as accessing unauthorized data.
- **Cache Directory**: A temporary storage location on a computer where frequently accessed data is kept to speed up retrieval.
- **AES**: A specification for the encryption of electronic data, standing for Advanced Encryption Standard.
- **Session Cookie**: A small piece of data sent from a website and stored by the user's web browser while the user is browsing, used to remember stateful information for the duration of the browsing session or some other expiry timeout.

## Footnotes

[^2]: _This is very uncommon in other states. A guess as to why Florida does things so differently is that Florida's extensive open records or "sunshine" laws (see also "Florida Man") spawned platforms before commercial vendors began to enter the market. Unlike many states where court records are made available through a single state website, Florida generally allows each county to make decisions about which platforms they use, as long as they follow Florida's [Standards for Access to Electronic Court Records](https://web.archive.org/web/20230917165659/https://www.flcourts.gov/content/download/850949/file/standards-for-access-to-electronic-court-records-september-2022.pdf) and [Access Security Matrix](https://web.archive.org/web/20231005211829/https://www.flcourts.gov/content/download/858675/file/access-security-matrix-v13-january%202023.pdf)._
[^3]: _TechCrunch: [Security flaws in court record systems used in five US states exposed sensitive legal documents](https://techcrunch.com/2023/11/30/us-court-records-systems-vulnerabilities-exposed-sealed-documents/)_
[^4]: _[Multiple Vulnerabilities Affecting Web-Based Court Case and Document Management Systems](https://www.cisa.gov/news-events/alerts/2023/11/30/multiple-vulnerabilities-affecting-web-based-court-case-and-document-management-systems)_

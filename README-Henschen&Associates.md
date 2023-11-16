# Disorder in the Court

## Description

Insufficient permission check vulnerabilities in public court record platforms from multiple vendors allowed attackers to view sealed, confidential, unredacted, and/or otherwise restricted case documents. Affected documents include witness lists and testimony, mental health evaluations, child custody agreements, detailed allegations of abuse, corporate trade secrets, jury forms, and much more.

## Details

Each of the platforms are developed by separate entities.

- **[Henschen & Associates](https://henschen.com/)' CaseLook(?)** is used in Ohio. Henschen & Associates _did not respond after multiple reports_ and **the vulnerability remains unresolved**.

While all of the platforms allowed unintended public access to restricted documents, the severity varied due to the levels of restrictions that could be bypassed and the discoverability of document IDs. The methods used to exploit each of the vulnerabilities also varied, but could all be performed by an unauthenticated attacker using only a browser's developer tools.

## Platforms

### Henschen & Associates – CaseLook

Document URLs are obfuscated using a bizarre format that interposes parts of the case number with a hexadecimal docket ID that starts at zero and increments for every document in the case, the length of the docket ID in hexadecimal, the size of the file in hexadecimal, and the length in hexadecimal of the size of the file in hexadecimal. The only information an attacker wouldn't know is the size of the file. A brute force is possible, however, the enumerable space grows with each page in the document.

The bigger problem is the way documents are served to the user. When a user requests the obfuscated document URL, a copy of the file is placed into a cache directory before being served to the user. Files in the cache directory are stored with incrementing numeric filenames that range from 0 to 32,767 (for reference: the Super Nintendo, released in 1991, can count to 65,535). The counter increments in an unknown way over time and is also incremented by 8 each time a new document is requested. If an attacker were to scan those filenames, they would eventually discover documents, including those with restrictions.

## Timeline

- 2023-10-10 - Vulnerability discovered in Henschen & Associates' CaseLook.
- 2023-10-11 - Report #1 for CaseLook sent to Henschen & Associates – _no response_.
- 2023-10-13 - Report #2 for CaseLook sent to Henschen & Associates – _no response_.
- 2023-10-16 - Report #3 for CaseLook sent to Henschen & Associates – _no response_.
- 2023-10-17 - Report #4 for CaseLook sent to Henschen & Associates – _no response_.
- 2023-11-13 - Report #5 for CaseLook sent to Henschen & Associates - _no response_.
- 2023-11-13 - Report for CaseLook sent to Ohio State CISO and Madison County, Ohio Court Clerk - _no response_.

## Overview by Platform

| Vendor                | Platform | IDs Available | Access | Fix Date |
| --------------------- | -------- | ------------- | ------ | -------- |
| Henschen & Associates | CaseLook | No            | R      |          |

**Key**:

- _IDs Available_:
  - Whether restricted document IDs are available to an attacker.
- _Access_:
  - _R_ - Sealed, Confidential, Pending Redaction, and/or VOR (Viewable on Request).
  - _U_ - Unredacted.
  - _Z_ - Attackers can view partial details of cases marked as restricted.

## Acknowledgements

- "Eli", who discovered the first set of court vulnerabilities and has been a major contributor throughout. _(Is this how most adults find and make friends?)_
- Andrew Crocker and Hannah Zhao from the [Electronic Frontier Foundation](https://eff.org), who were there when I (and many, many others) needed them most.
- [Josh Renaud](https://www.joshrenaud.com/) from [St. Louis Post-Dispatch](https://www.stltoday.com/), who fought this fight before me, earning him a [Press Freedom Award](https://www.youtube.com/watch?v=DhflQv1rJ1A).
- [Jaku](https://twitter.com/Jaku) founder of [Crowd Control](https://crowdcontrol.live/), who frequently offered his cybersecurity wisdom and experience.
- [Zack Whitaker](https://techcrunch.com/author/zack-whittaker/) from [TechCrunch](https://techcrunch.com), who immediately recognized the severity and jumped at the chance to help.
- The [CISA CVD Team](https://www.cisa.gov/coordinated-vulnerability-disclosure-process) assisted with the coordination of these vulnerabilities.
- [judyrecords.com](https://judyrecords.com), who handled the first round of blowback from court security issues.
- The State of Arkansas, who allowed me to present my findings in an effort to avoid the same pitfalls when building their own court platform.

## Footnotes

## Definitions

- **Enumeration**: The process of systematically probing a system to discover valuable information, such as document names or user accounts, by incrementing values in a URL or input field.
- **Brute Force Attack**: A method of trial-and-error used to obtain information such as a password or PIN. In this case, it refers to repeatedly trying different document IDs to find restricted documents.
- **Obfuscation**: The practice of making something difficult, but not impossible, to understand. In a security context, obfuscation might be used to make files or code less readable, thereby hiding information from unauthorized users.
- **Developer Tools**: A set of tools included in most web browsers that allow developers to inspect the underlying code of a web page, monitor network requests, and test live JavaScript code among other functionalities.
- **Query Parameter**: A way to pass data in a URL, typically used in GET requests to web servers, which can be manipulated for purposes such as accessing unauthorized data.
- **Cache Directory**: A temporary storage location on a computer where frequently accessed data is kept to speed up retrieval.
- **AES128**: A specification for the encryption of electronic data, standing for Advanced Encryption Standard with a key size of 128 bits.
- **Session Cookie**: A small piece of data sent from a website and stored by the user's web browser while the user is browsing, used to remember stateful information for the duration of the browsing session or some other expiry timeout.

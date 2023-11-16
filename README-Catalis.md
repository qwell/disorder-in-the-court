# Disorder in the Court

## Description

Insufficient permission check vulnerabilities in public court record platforms from multiple vendors allowed attackers to view sealed, confidential, unredacted, and/or otherwise restricted case documents. Affected documents include witness lists and testimony, mental health evaluations, child custody agreements, detailed allegations of abuse, corporate trade secrets, jury forms, and much more.

## Details

Each of the platforms are developed by separate entities.

- **[Catalis](https://catalisgov.com/)' CMS360** is used in Georgia, Mississippi, Ohio, and Tennessee. Catalis is a "government solutions" company that provides a wide array[^1] of public record, payment, and regulatory/compliance platforms.

While all of the platforms allowed unintended public access to restricted documents, the severity varied due to the levels of restrictions that could be bypassed and the discoverability of document IDs. The methods used to exploit each of the vulnerabilities also varied, but could all be performed by an unauthenticated attacker using only a browser's developer tools.

## Platforms

### Catalis – CMS360

To view documents, URLs with numeric document and case IDs were used. This allowed an unskilled attacker to stumble upon restricted documents by simply incrementing the document ID in the document URL.

Many courts configured CMS360 to disallow document viewing altogether, making it very difficult to guess document IDs, so it is unclear whether documents could be viewed if document IDs were discovered. Many courts also require a login to view cases, so it is not known whether those courts allowed viewing images.

## Timeline

- 2023-07-17 - Vulnerability discovered in Catalis' CMS360.
- 2023-09-14 - Report for all vulnerabilities sent to [Jason Parker](https://ꩰ.com/@north) at [Jeltz](https://jeltz.org) by "Eli".
- 2023-09-30 - Report #1 for CMS360 sent to Catalis – _no response_.
- 2023-10-02 - Report #2 for CMS360 sent to Catalis – _no response_.
- 2023-10-02 - Report for all vulnerabilities sent to [CERT Coordination Center](https://www.kb.cert.org/vuls/) (CERT/CC).
- 2023-10-03 - Report #3 for CMS360 sent to Catalis, detailing report to CERT/CC – _no response_.
- 2023-10-04 - Report for all vulnerabilities sent to [Cybersecurity and Infrastructure Security Agency](https://cisa.gov) (CISA) by CERT/CC.
- 2023-10-06 - Report #4 for CMS360 sent to Catalis, with disclosure timeline and CISA hand-off details – _no response_.
- 2023-11-01 - Response from Catalis, after discussion with CEO Scott Roza.
- 2023-11-03 - **Vulnerability in CMS360 fixed by Catalis.**

## Overview by Platform

| Vendor                  | Platform | IDs Available | Access | Fix Date   |
| ----------------------- | -------- | ------------- | ------ | ---------- |
| Catalis / ICON Software | CMS360   | No            | R      | 2023-11-03 |

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

## Definitions

- **Enumeration**: The process of systematically probing a system to discover valuable information, such as document names or user accounts, by incrementing values in a URL or input field.
- **Brute Force Attack**: A method of trial-and-error used to obtain information such as a password or PIN. In this case, it refers to repeatedly trying different document IDs to find restricted documents.
- **Obfuscation**: The practice of making something difficult, but not impossible, to understand. In a security context, obfuscation might be used to make files or code less readable, thereby hiding information from unauthorized users.
- **Developer Tools**: A set of tools included in most web browsers that allow developers to inspect the underlying code of a web page, monitor network requests, and test live JavaScript code among other functionalities.
- **Query Parameter**: A way to pass data in a URL, typically used in GET requests to web servers, which can be manipulated for purposes such as accessing unauthorized data.
- **Cache Directory**: A temporary storage location on a computer where frequently accessed data is kept to speed up retrieval.
- **AES128**: A specification for the encryption of electronic data, standing for Advanced Encryption Standard with a key size of 128 bits.
- **Session Cookie**: A small piece of data sent from a website and stored by the user's web browser while the user is browsing, used to remember stateful information for the duration of the browsing session or some other expiry timeout.

## Footnotes

[^1]: _Catalis [states on their website](https://web.archive.org/web/20231005221308/https://catalisgov.com/our-story/) that "in less than five years [we have] strategically acquired and combined more than 30 public sector software companies". Learning and merging infrastructure after an acquisition takes a large amount of effort. Juggling 30 acquisitions would be a monumental undertaking that reduces focus on other necessary areas of business (e.g. securing legacy platforms)._

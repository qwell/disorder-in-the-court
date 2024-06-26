Canonical URL has changed.  See [here](https://github.com/qwell/disclosures/blob/main/README-2023-11-30-disorder-in-the-court.md) for updates.

# Disorder in the Court

## Description

Insufficient permission check vulnerabilities in public court record platforms from multiple vendors allowed unauthorized public access to sealed, confidential, unredacted, and/or otherwise restricted case documents. Affected documents include witness lists and testimony, mental health evaluations, child custody agreements, detailed allegations of abuse, corporate trade secrets, jury forms, and much more.

## Details

- **[Henschen & Associates - CaseLook](#henschen--associates---caselook)** is used in Ohio. Henschen & Associates did not respond after multiple reports, however the vulnerability has been fixed.

While all of the platforms allowed unintended public access to restricted documents, the severity varied based on the levels of restrictions that could be bypassed and the discoverability of document IDs. The methods used to exploit each of the vulnerabilities also varied, but could all be performed by an unauthenticated attacker using only a browser's developer tools.

## Platforms

### [Henschen & Associates](https://henschen.com/) - CaseLook

Document URLs were obfuscated using a bizarre format that interposed parts of the case number with a docket ID that started at zero and incremented for every document in the case, the length of the docket ID, the size of the file, and the length of the size of the file. The only information an attacker wouldn't know is the size of the file. A brute force was possible, however, the enumerable space grew with each page in the document.

The bigger problem was the way documents were served to the user. When a user requests a document URL, a copy of the file is placed into a cache directory before being served to the user. Files in the cache directory were stored with incrementing numeric filenames that ranged from 0 to 32,767 (for reference: the Super Nintendo, released in 1991, can count to 65,535). The counter incremented in an unknown way over time and was also incremented by 8 each time a new document was requested. If an attacker were to scan those filenames, they would have eventually discover documents, including those with restrictions.

Although Henschen & Associates eventually fixed the vulnerability, they did not ever respond to reports. This type of behavior is disrespectful to reporters of vulnerabilities and should give customers pause; if no response is received, future reporters may instead decide to sell, exploit, or immediately publish their discoveries.

- [CVE-2023-6376](https://nvd.nist.gov/vuln/detail/CVE-2023-6376): Henschen & Associates court document management software does not sufficiently randomize file names of cached documents, allowing a remote, unauthenticated attacker to access restricted documents.

## Timeline

- 2023-10-10 - Vulnerability discovered in Henschen & Associates' CaseLook.
- 2023-10-11 - Report #1 for CaseLook sent to Henschen & Associates - _no response_.
- 2023-10-13 - Report #2 for CaseLook sent to Henschen & Associates - _no response_.
- 2023-10-16 - Report #3 for CaseLook sent to Henschen & Associates - _no response_.
- 2023-10-17 - Report #4 for CaseLook sent to Henschen & Associates - _no response_.
- 2023-11-13 - Report #5 for CaseLook sent to Henschen & Associates - _no response_.
- 2023-11-13 - Report for CaseLook sent to Ohio State CISO and Madison County, Ohio Court Clerk - _no response_.
- 2023-11-22 - **Vulnerability confirmed fixed in CaseLook.**
- 2023-11-30 - Disclosure published.

## Overview by Platform

| Vendor                | Platform | IDs Available | Access | Fix Date   |
| --------------------- | -------- | ------------- | ------ | ---------- |
| Henschen & Associates | CaseLook | No            | R      | 2023-11-22 |

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

[^3]: _TechCrunch: [Security flaws in court record systems used in five US states exposed sensitive legal documents](https://techcrunch.com/2023/11/30/us-court-records-systems-vulnerabilities-exposed-sealed-documents/)_
[^4]: _[Multiple Vulnerabilities Affecting Web-Based Court Case and Document Management Systems](https://www.cisa.gov/news-events/alerts/2023/11/30/multiple-vulnerabilities-affecting-web-based-court-case-and-document-management-systems)_

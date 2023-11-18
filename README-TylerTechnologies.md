# Disorder in the Court

## Description

Insufficient permission check vulnerabilities in public court record platforms from multiple vendors allowed unauthorized public access to sealed, confidential, unredacted, and/or otherwise restricted case documents. Affected documents include witness lists and testimony, mental health evaluations, child custody agreements, detailed allegations of abuse, corporate trade secrets, jury forms, and much more.

## Details

Each of the eight platforms are developed by separate entities.

- **[Tyler Technologies](https://www.tylertech.com/)' Court Case Management Plus** is used in Georgia. In February, 2022 a different Tyler Technologies court records platform had a similar vulnerability that allowed the website [judyrecords.com](judyrecords.com) to accidentally scrape sensitive data.

While all of the platforms allowed unintended public access to restricted documents, the severity varied due to the levels of restrictions that could be bypassed and the discoverability of document IDs. The methods used to exploit each of the vulnerabilities also varied, but could all be performed by an unauthenticated attacker using only a browser's developer tools.

## Platforms

### Tyler Technologies – Court Case Management Plus

Multiple vulnerabilities were found in Court Case Management Plus.

The first vulnerability was a mechanism that allowed an automatic passwordless login for any user, which was triggered by adding the query parameter `xyzldk=<username>` to the login URL; this was discovered through a simple Google search.

The second is a pair of vulnerabilities due to the use of a component from [Aquaforest](https://www.aquaforest.com/) called TIFFServer. The first of the pair allows an attacker to enumerate the name of every file in a directory by adding the query parameter `PN=<number>` to `tssp.aspx`; if the file was a document, it could also be viewed. The second of the pair allows an attacker to enumerate every directory on the system, and even more concerning, the entire network, by using `tiffserver.aspx` and either `te003.aspx` or `te004.aspx`. Combining this pair of vulnerabilities gave an attacker an extraordinary level of access to files far beyond those in a court case. In a number of instances, the enumeration led to the discovery of directly downloadable files, including as-yet-unfiled warrant applications and backups of source code for nine separate Tyler Technologies platforms.

In 2019, a similar vulnerability was discovered in TIFFServer ([CVE-2020-9323](https://nvd.nist.gov/vuln/detail/CVE-2020-9323)) by Quentin (paragonsec) Rhoads-Herrera of TEAMARES that allowed an attacker to know how many files were in a directory. A second vulnerability ([CVE-2020-9325](https://nvd.nist.gov/vuln/detail/CVE-2020-9325)) disclosed at the same time allowed an attacker to download arbitrary files. While it's fortunate that Court Case Management Plus was not affected by the latter vulnerability, it was only because the version being used was released an astonishing _14 years earlier_, in 2006.

## Timeline

- 2023-10-06 - Vulnerability #1 discovered in Tyler Technologies' Court Case Management Plus.
- 2023-10-07 - Vulnerabilities #2 and #3 discovered in Court Case Management Plus.
- 2023-10-07 - Report for Court Case Management Plus sent to Tyler Technologies.
- 2023-10-08 - Response from Tyler Technologies.
- 2023-10-10 - Report for Court Case Management Plus sent to CISA.
- 2023-11-01 - **Vulnerabilities #1, #2, and #3 in Court Case Management Plus fixed by Tyler Technologies.**

## Overview by Platform

| Vendor             | Platform                   | IDs Available | Access | Fix Date   |
| ------------------ | -------------------------- | ------------- | ------ | ---------- |
| Tyler Technologies | Court Case Management Plus | Yes           | RUZ    | 2023-11-01 |

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
- The furries, who provided me with plenty of amusement after I published my Bluesky / AT Protocol vulnerabilities. I see you.

## Contact

- Email: [north@ꩰ.com](mailto:north@ꩰ.com)
- Press: [press@jeltz.org](mailto:press@jeltz.org)
- Mastodon: [@north@ꩰ.com](https://ꩰ.com/@north)
- Twitch: [NorthAntara](https://twitch.tv/northantara)
- YouTube: [NorthAntara](https://youtube.com/northantara)
- Twitter: [NorthAntara](https://twitter.com/northantara)

## Sponsorship

- If you enjoy my work, consider becoming a sponsor on [Patreon](https://patreon.com/northantara) or [GitHub](https://github.com/sponsors/qwell/), and/or consider donating to the [Electronic Frontier Foundation](https://eff.org/donate) or [St. Jude](https://www.stjude.org/donate).

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

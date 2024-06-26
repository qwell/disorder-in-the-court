Canonical URL has changed.  See [here](https://github.com/qwell/disclosures/blob/main/README-2023-11-30-disorder-in-the-court.md) for updates.

# Disorder in the Court

## Description

Insufficient permission check vulnerabilities in public court record platforms from multiple vendors allowed unauthorized public access to sealed, confidential, unredacted, and/or otherwise restricted case documents. Affected documents include witness lists and testimony, mental health evaluations, child custody agreements, detailed allegations of abuse, corporate trade secrets, jury forms, and much more.

## Details

- **[Tyler Technologies - Court Case Management Plus](#tyler-technologies---court-case-management-plus)** is used in Georgia. In February 2022, a different Tyler Technologies court records platform had a similar vulnerability that allowed the website [judyrecords.com](judyrecords.com) to accidentally scrape sensitive data.

While all of the platforms allowed unintended public access to restricted documents, the severity varied based on the levels of restrictions that could be bypassed and the discoverability of document IDs. The methods used to exploit each of the vulnerabilities also varied, but could all be performed by an unauthenticated attacker using only a browser's developer tools.

## Platforms

### [Tyler Technologies](https://www.tylertech.com/) - Court Case Management Plus

Multiple vulnerabilities were found in Court Case Management Plus.

The first vulnerability was a mechanism that enabled automatic passwordless login for any user, which was triggered by adding the query parameter `xyzldk=<username>` to the login URL; this was discovered through a simple Google search.

The second was a pair of vulnerabilities related to the use and misconfiguration of a very old, end-of-life component from [Aquaforest](https://www.aquaforest.com/) called TIFF Server. The first of the pair allowed an attacker to enumerate the name of every file in a directory by adding the query parameter `PN=<number>` to `tssp.aspx`; if the file was a document, it could also be viewed. The second of the pair allowed an attacker to enumerate every directory on the system, and even more concerning, the entire network, by using `tiffserver.aspx` and either `te003.aspx` or `te004.aspx`. Combining this pair of vulnerabilities gave an attacker an extraordinary level of access to files far beyond those in a court case. In a number of instances, the enumeration led to the discovery of directly downloadable files, including as-yet-unfiled warrant applications and backups of source code for nine separate Tyler Technologies platforms.

In 2019, a similar vulnerability in TIFFServer ([CVE-2020-9323](https://nvd.nist.gov/vuln/detail/CVE-2020-9323)) was discovered by Quentin (paragonsec) Rhoads-Herrera of TEAMARES that allowed an attacker to know how many files were in a directory. A second vulnerability ([CVE-2020-9325](https://nvd.nist.gov/vuln/detail/CVE-2020-9325)) disclosed at the same time allowed an attacker to download arbitrary files. Court Case Management Plus was not affected by the latter vulnerability as the version being used was released an astonishing _14 years earlier_, in 2006. After being notified of the vulnerabilities, Aquaforest [wrote a guide](https://www.aquaforest.com/blog/tiff-server-security-update) on how TIFF Server configurations can be made more secure.

- [CVE-2023-6342](https://nvd.nist.gov/vuln/detail/CVE-2023-6342): Tyler Technologies Court Case Management Plus allows a remote attacker to authenticate as any user by manipulating at least the 'CmWebSearchPfp/Login.aspx?xyzldk=' and 'payforprint_CM/Redirector.ashx?userid=' parameters. The vulnerable "pay for print" feature was removed on or around 2023-11-01.
- [CVE-2023-6343](https://nvd.nist.gov/vuln/detail/CVE-2023-6343): Tyler Technologies Court Case Management Plus allows a remote, unauthenticated attacker to enumerate and access sensitive files using the tiffserver/tssp.aspx 'FN' and 'PN' parameters. This behavior is related to the use of a deprecated version of Aquaforest TIFF Server, possibly 2.x. The vulnerable Aquaforest TIFF Server feature was removed on or around 2023-11-01.
- [CVE-2023-6344](https://nvd.nist.gov/vuln/detail/CVE-2023-6344): Tyler Technologies Court Case Management Plus allows a remote, unauthenticated attacker to enumerate directories using the tiffserver/te003.aspx or te004.aspx 'ifolder' parameter. This behavior is related to the use of a deprecated version of Aquaforest TIFF Server, possibly 2.x. The vulnerable Aquaforest TIFF Server feature was removed on or around 2023-11-01.
- [CVE-2023-6353](https://nvd.nist.gov/vuln/detail/CVE-2023-6353): Tyler Technologies Civil and Criminal Electronic Filing allows an unauthenticated, remote attacker to upload, delete, and view files by manipulating the Upload.aspx 'enky' parameter.
- [CVE-2023-6354](https://nvd.nist.gov/vuln/detail/CVE-2023-6354): Tyler Technologies Magistrate Court Case Management Plus allows an unauthenticated, remote attacker to upload, delete, and view files by manipulating the PDFViewer.aspx 'filename' parameter.
- [CVE-2023-6375](https://nvd.nist.gov/vuln/detail/CVE-2023-6375): Tyler Technologies Court Case Management Plus may store backups in a location that can be accessed by a remote, unauthenticated attacker. Backups may contain sensitive information such as database credentials.
- [CVE-2023-6352](https://nvd.nist.gov/vuln/detail/CVE-2023-6352): The default configuration of Aquaforest TIFF Server allows access to arbitrary file paths, subject to any restrictions imposed by Internet Information Services (IIS) or Microsoft Windows. Depending on how a web application uses and configures TIFF Server, a remote attacker may be able to enumerate files or directories, traverse directories, bypass authentication, or access restricted files.

## Timeline

- 2023-10-06 - Vulnerability #1 discovered in Tyler Technologies' Court Case Management Plus.
- 2023-10-07 - Vulnerabilities #2 and #3 discovered in Court Case Management Plus.
- 2023-10-07 - Report for Court Case Management Plus sent to Tyler Technologies.
- 2023-10-08 - Response from Tyler Technologies.
- 2023-10-10 - Report for Court Case Management Plus sent to CISA.
- 2023-11-01 - **Vulnerabilities #1, #2, and #3 confirmed fixed in Court Case Management Plus.**
- 2023-11-30 - Disclosure published.

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

# Disorder in the Court

## Description

Insufficient permission check vulnerabilities in public court record platforms from multiple vendors allowed unauthorized public access to sealed, confidential, unredacted, and/or otherwise restricted case documents. Affected documents include witness lists and testimony, mental health evaluations, child custody agreements, detailed allegations of abuse, corporate trade secrets, jury forms, and much more.

## Details

Each of the platforms are developed by separate entities.

- **[Catalis](https://catalisgov.com/)' CMS360** is used in Georgia, Mississippi, Ohio, and Tennessee. Catalis is a "government solutions" company that provides a wide array[^1] of public record, payment, and regulatory/compliance platforms.
- **[Tyler Technologies](https://www.tylertech.com/)' Court Case Management Plus** is used in Georgia. In February, 2022 a different Tyler Technologies court records platform had a similar vulnerability that allowed the website [judyrecords.com](judyrecords.com) to accidentally scrape sensitive data.
- **[Henschen & Associates](https://henschen.com/)' CaseLook(?)** is used in Ohio. Henschen & Associates did not respond after multiple reports, however the vulnerability has been fixed.
- Five platforms are each presumed to be developed "in-house"[^2] by individual Florida county courts.

While all of the platforms allowed unintended public access to restricted documents, the severity varied due to the levels of restrictions that could be bypassed and the discoverability of document IDs. The methods used to exploit each of the vulnerabilities also varied, but could all be performed by an unauthenticated attacker using only a browser's developer tools.

## Platforms

### Catalis – CMS360

To view documents, URLs with numeric document and case IDs were used. This allowed an unskilled attacker to stumble upon restricted documents by simply incrementing the document ID in the document URL.

Many courts configured CMS360 to disallow document viewing altogether, making it very difficult to guess document IDs, so it is unclear whether documents could be viewed if document IDs were discovered. Many courts also require a login to view cases, so it is not known whether those courts allowed viewing images.

### Tyler Technologies – Court Case Management Plus

Multiple vulnerabilities were found in Court Case Management Plus.

The first vulnerability was a mechanism that allowed an automatic passwordless login for any user, which was triggered by adding the query parameter `xyzldk=<username>` to the login URL; this was discovered through a simple Google search.

The second is a pair of vulnerabilities due to the use of a component from [Aquaforest](https://www.aquaforest.com/) called TIFFServer. The first of the pair allows an attacker to enumerate the name of every file in a directory by adding the query parameter `PN=<number>` to `tssp.aspx`; if the file was a document, it could also be viewed. The second of the pair allows an attacker to enumerate every directory on the system, and even more concerning, the entire network, by using `tiffserver.aspx` and either `te003.aspx` or `te004.aspx`. Combining this pair of vulnerabilities gave an attacker an extraordinary level of access to files far beyond those in a court case. In a number of instances, the enumeration led to the discovery of directly downloadable files, including as-yet-unfiled warrant applications and backups of source code for nine separate Tyler Technologies platforms.

In 2019, a similar vulnerability was discovered in TIFFServer ([CVE-2020-9323](https://nvd.nist.gov/vuln/detail/CVE-2020-9323)) by Quentin (paragonsec) Rhoads-Herrera of TEAMARES that allowed an attacker to know how many files were in a directory. A second vulnerability ([CVE-2020-9325](https://nvd.nist.gov/vuln/detail/CVE-2020-9325)) disclosed at the same time allowed an attacker to download arbitrary files. While it's fortunate that Court Case Management Plus was not affected by the latter vulnerability, it was only because the version being used was released an astonishing _14 years earlier_, in 2006.

### Henschen & Associates – CaseLook

Document URLs were obfuscated using a bizarre format that interposed parts of the case number with a hexadecimal docket ID that started at zero and incremented for every document in the case, the length of the docket ID in hexadecimal, the size of the file in hexadecimal, and the length in hexadecimal of the size of the file in hexadecimal. The only information an attacker wouldn't know is the size of the file. A brute force was possible, however, the enumerable space grew with each page in the document.

The bigger problem was the way documents were served to the user. When a user requests a document URL, a copy of the file is placed into a cache directory before being served to the user. Files in the cache directory were stored with incrementing numeric filenames that ranged from 0 to 32,767 (for reference: the Super Nintendo, released in 1991, can count to 65,535). The counter incremented in an unknown way over time and was also incremented by 8 each time a new document was requested. If an attacker were to scan those filenames, they would have eventually discover documents, including those with restrictions.

Although Henschen & Associates eventually fixed the vulnerability, they did not ever respond to reports. This type of behavior is disrespectful to reporters of vulnerabilities and should give customers pause; if no response is received, future reporters may instead decide to sell, exploit, or immediately publish their discoveries.

### Brevard County

Document URLs contain a version of the document ID that is encrypted using a method that includes an expiry mechanism. Although every docket entry displays the associated document ID, the encrypted form -- which is required to view documents -- is only provided for documents that the user has access to. This would be a great method that enables sharing of documents for a limited time, but for one fatal flaw.

Along with the encrypted document ID, the URLs also contained the query parameters `theIV=` and `theKey=`, which an astute observer might recognize as AES128. Using the IV and key from the URL, an attacker can encrypt a document ID and use it to view a restricted document. Additionally, URLs included the parameter `isRedacted=` which, as the name suggests, accepted the encrypted form of the strings "Yes" or "No" to view unredacted copies of documents.

### Hillsborough County

Session cookies were used to determine which cases and documents a user was viewing. When a case or document was requested, a request was sent to the API, which associated the data with the user's session cookie and returned the results. The API endpoint for obtaining document information returned a list that included the document ID, several values that specify the security level required to view the document, and the user's applied access level. The frontend chose whether to display a document link or one of the restriction type indicators based on the applied access level. The backend assumed that if an attacker is able to request a document, they must have access to it.

### Lee County

Session cookies were used in a manner similar to Hillsborough County. For most types of cases, document IDs were available in the pre-rendered HTML. Documents could be viewed by executing a single function in the site's JavaScript source code.

### Monroe County

A similar vulnerability was discovered initially, but their change introduced a new vulnerability. Much like Hillsborough County, the frontend chose whether to display a document link based on the security level of the document and the backend made a bad assumption. After fixing the first vulnerability, the developers helpfully left a debugger statement one line before these checks are made, which pauses code execution and gave the attacker a chance to adjust the security level.

### Sarasota County

In what is certainly the most egregious of the Florida county vulnerabilities, document URLs contained nothing more than a numeric document ID. The only protection was a CAPTCHA on the landing page, which could be bypassed.

Due to the triviality of discovering this vulnerability and the ease with which it could be exploited on a mass scale, it is reasonable to assume that every document became compromised the moment it was filed.

In defense of Sarasota County, they were the first to attempt to fix their issue. Unfortunately, their first attempt at a fix was not sufficient and a new vulnerability was discovered. Worst yet, while searching for a new method, a third vulnerability (or a second consequence of the second vulnerability) was discovered that allowed an unauthenticated attacker to view restricted cases.

## Timeline

- 2023-06-21 - Vulnerability #1 discovered in Monroe County.
- 2023-07-04 - Report #1 sent to Monroe County – _no response_.
- 2023-07-14 - Report #2 sent to Monroe County – _no response_.
- 2023-07-17 - Vulnerability discovered in Catalis' CMS360.
- 2023-08-?? - **Vulnerability #1 confirmed fixed in Monroe County.**
- 2023-08-?? - Vulnerability #2 discovered in Monroe County.
- 2023-09-14 - Report for all vulnerabilities sent to [Jason Parker](https://ꩰ.com/@north) at [Jeltz](https://jeltz.org) by "Eli".
- 2023-09-15 - Vulnerability discovered in Lee County.
- 2023-09-15 - Vulnerability #1 discovered in Sarasota County.
- 2023-09-16 - Vulnerability discovered in Hillsborough County.
- 2023-09-18 - Vulnerability discovered in Brevard County.
- 2023-09-30 - Report #1 for CMS360 sent to Catalis – _no response_.
- 2023-10-02 - Report #2 for CMS360 sent to Catalis – _no response_.
- 2023-10-02 - Report for all vulnerabilities sent to [CERT Coordination Center](https://www.kb.cert.org/vuls/) (CERT/CC).
- 2023-10-03 - Report for all Florida court vulnerabilities sent to Florida's Office of the State Courts Administrator (OSCA).
- 2023-10-03 - Report #3 for CMS360 sent to Catalis, detailing report to CERT/CC – _no response_.
- 2023-10-04 - Report for all vulnerabilities sent to [Cybersecurity and Infrastructure Security Agency](https://cisa.gov) (CISA) by CERT/CC.
- 2023-10-06 - Report #4 for CMS360 sent to Catalis, with disclosure timeline and CISA hand-off details – _no response_.
- 2023-10-06 - Vulnerability #1 discovered in Tyler Technologies' Court Case Management Plus.
- 2023-10-06 - Response from Florida OSCA.
- 2023-10-06 - Report sent to Florida Court Clerks & Comptrollers by Florida OSCA.
- 2023-10-07 - Vulnerabilities #2 and #3 discovered in Court Case Management Plus.
- 2023-10-07 - Report for Court Case Management Plus sent to Tyler Technologies.
- 2023-10-08 - Response from Tyler Technologies.
- 2023-10-10 - Report for Court Case Management Plus sent to CISA.
- 2023-10-10 - Vulnerability discovered in Henschen & Associates' CaseLook.
- 2023-10-11 - Report #1 for CaseLook sent to Henschen & Associates – _no response_.
- 2023-10-11 - **Vulnerability #1 confirmed fixed by Sarasota County.**
- 2023-10-11 - Vulnerability #2 discovered in Sarasota County.
- 2023-10-13 - Report #2 for CaseLook sent to Henschen & Associates – _no response_.
- 2023-10-16 - Report #3 for CaseLook sent to Henschen & Associates – _no response_.
- 2023-10-17 - Report #4 for CaseLook sent to Henschen & Associates – _no response_.
- 2023-10-27 - **Vulnerability #2 confirmed fixed by Sarasota County.**
- 2023-11-01 - **Vulnerabilities #1, #2, and #3 in Court Case Management Plus confirmed fixed by Tyler Technologies.**
- 2023-11-01 - Response from Catalis, after discussion with CEO Scott Roza.
- 2023-11-03 - **Vulnerability in CMS360 confirmed fixed by Catalis.**
- 2023-11-13 - Report #5 for CaseLook sent to Henschen & Associates - _no response_.
- 2023-11-13 - Report for CaseLook sent to Ohio State CISO and Madison County, Ohio Court Clerk - _no response_.
- 2023-11-22 - **Vulnerability in CaseLook confirmed fixed by Henschen & Associates.**

## Overview by Platform

| Vendor                  | Platform                   | IDs Available | Access | Fix Date   |
| ----------------------- | -------------------------- | ------------- | ------ | ---------- |
| Catalis / ICON Software | CMS360                     | No            | R      | 2023-11-03 |
| Tyler Technologies      | Court Case Management Plus | Yes           | RUZ    | 2023-11-01 |
| Henschen & Associates   | CaseLook                   | No            | R      | 2023-11-22 |
| Brevard County          |                            | Yes           | RU     |            |
| Hillsborough County     |                            | Limited       | R      |            |
| Lee County              |                            | Limited       | RZ     |            |
| Monroe County           |                            | Yes           | R      |            |
| Sarasota County         |                            | No            | R      | 2023-10-27 |

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
- [Zack Whitaker](https://techcrunch.com/author/zack-whittaker/) from [TechCrunch](https://techcrunch.com), who immediately recognized the severity and jumped at the chance to learn more.
- [judyrecords.com](https://judyrecords.com), who handled the first round of fallout from court security issues.
- The Arkansas [Administrative Office of the Courts](https://arcourts.gov/), who allowed me to present my findings in an effort to avoid the same pitfalls when building their own court platform.
- The [CISA CVD Team](https://www.cisa.gov/coordinated-vulnerability-disclosure-process) assisted with the coordination of these vulnerabilities.
- Dedications to the Fediverse furries, who provided plenty of amusement after my Bluesky / AT Protocol vulnerability publications. I see you.

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

[^1]: _Catalis [states on their website](https://web.archive.org/web/20231005221308/https://catalisgov.com/our-story/) that "in less than five years [we have] strategically acquired and combined more than 30 public sector software companies". Learning and merging infrastructure after an acquisition takes a large amount of effort. Juggling 30 acquisitions would be a monumental undertaking that reduces focus on other necessary areas of business (e.g. securing legacy platforms)._
[^2]: _This is very uncommon in other states. A guess as to why Florida does things so differently is that Florida's extensive open records or "sunshine" laws (see also "Florida Man") spawned platforms before commercial vendors began to enter the market. Unlike many states where court records are made available through a single state website, Florida generally allows each county to make decisions about which platforms they use, as long as they follow Florida's [Standards for Access to Electronic Court Records](https://web.archive.org/web/20230917165659/https://www.flcourts.gov/content/download/850949/file/standards-for-access-to-electronic-court-records-september-2022.pdf) and [Access Security Matrix](https://web.archive.org/web/20231005211829/https://www.flcourts.gov/content/download/858675/file/access-security-matrix-v13-january%202023.pdf)._

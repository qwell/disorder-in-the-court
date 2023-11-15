# Disorder in the Court

## Description

Insufficient permission check vulnerabilities in eight public court record platforms used in five states allowed attackers to view sealed, confidential, unredacted, and/or otherwise restricted case documents. Affected documents include witness lists and testimony, mental health evaluations, child custody agreements, detailed allegations of abuse, corporate trade secrets, and much more.

## Details

Each of the eight platforms are developed by separate entities. The first platform, used in Georgia, Mississippi, Ohio, and Tennessee, is [Catalis](https://catalisgov.com/)’ CMS360. Catalis is a "government solutions" company that provides a wide array[^1] of public record, payment, and regulatory/compliance platforms. The second platform, used in Georgia, is [Tyler Technologies](https://www.tylertech.com/)’ Court Case Management Plus; in February, 2022 a different Tyler Technologies court records platform had a similar vulnerability that allowed the website [judyrecords.com](judyrecords.com) to accidentally scrape sensitive data. The third platform, used in Ohio, is [Henschen & Associates](https://henschen.com/)’ CaseLook(?). Henschen & Associates has not responded after multiple reports and the vulnerability remains unresolved. The remaining five platforms are each presumed to be developed "in-house"[^2] by individual Florida county courts.

While all of the platforms allowed unintended public access to restricted documents, the severity varied due to the levels of restrictions that can be bypassed and the discoverability of document IDs. The methods used to exploit each of the vulnerabilities also varied, but they could all be performed by an unauthenticated attacker using only a browser’s developer tools.

## Platforms

### Catalis – CMS360

To view documents, URLs with numeric document and case IDs were used. This allowed an unskilled attacker to stumble upon restricted documents by simply incrementing the document ID in the document URL.

Many courts configured CMS360 to disallow document viewing altogether, making it very difficult to guess document IDs, so it is unclear whether documents could be viewed if document IDs were discovered. Many courts also require a login to view cases, so it is not known whether those courts allowed viewing images.

### Tyler Technologies – Court Case Management Plus

Multiple vulnerabilities were found in Court Case Management Plus.

The first vulnerability was a mechanism that allowed an automatic passwordless login for any user, which was triggered by adding the query parameter `xyzldk=<username>` to the login URL; this was discovered through a simple Google search.

The second is a pair of vulnerabilities stemming from the usage of a component from [Aquaforest](https://www.aquaforest.com/) called TIFFServer. The first allows an attacker to enumerate the name of every file in a directory by adding the query parameter `PN=<number>` to `tssp.aspx`; if the file was a document, it could also be viewed. The second allows an attacker to enumerate every directory on the system, and even more concerning, the entire network, by using `te003.aspx` / `te004.aspx` and `tiffserver.aspx`. Combining these vulnerabilities gave an attacker an extraordinary level of access to files far beyond those in a court case. In a number of instances, the enumeration led to the discovery of directly downloadable files, including as-yet-unfiled warrant applications and backups of source code for 9 separate Tyler Technologies platforms.

In 2019, a similar vulnerability was discovered in TIFFServer ([CVE-2020-9323](https://nvd.nist.gov/vuln/detail/CVE-2020-9323)) by Quentin (paragonsec) Rhoads-Herrera of TEAMARES that allowed an attacker to know how many files were in a directory. A second vulnerability ([CVE-2020-9325](https://nvd.nist.gov/vuln/detail/CVE-2020-9325)) disclosed at the same time also allowed an attacker to download arbitrary files. While it’s fortunate that Court Case Management Plus was not affected by the latter vulnerability, it was only because the version being used was released an astonishing _14 years earlier_, in 2006.

### Henschen & Associates – CaseLook

Document URLs are obfuscated using a bizarre format that interposes parts of the case number with a hexadecimal docket ID that starts at zero and increments for every document in the case, the length of the docket ID, the size of the file in hexadecimal, and the length of the size of the file. The only information an attacker wouldn’t know is the size of the file; a brute force would be possible; however, the enumerable space grows with each page in the document.

The bigger problem is the way documents are served to the user. When a user requests the obfuscated document URL, a copy of the file is placed into a cache directory before being served to the user. Files in the cache directory are stored with incrementing numeric filenames that range from 0 to 32,767 (for reference: the Super Nintendo, released in 1991, can count to 65,535); the counter increments in an unknown way over time and is also incremented by 8 each time a new document is requested. If an attacker were to scan those filenames, they would eventually discover restricted documents.

### Brevard County

If awards were being given, Brevard County would easily win the "You Tried!" award (that’s a good thing).

Document URLs contain a version of the document ID that is encrypted using a method that includes an expiry mechanism. Although each docket entry displays the associated document ID, the encrypted form was only provided for documents that the user should have access to. This would be a great method that enables sharing of documents for a limited time, but for one fatal _[and heartbreaking - Ed.]_ flaw.

Along with the encrypted document ID, the URLs also contained the query parameters `theIV=` and `theKey=`, which an astute observer might recognize as AES128. Using the IV and key from the URL, an attacker can encrypt a document ID and use it to view a restricted document. Additionally, URLs included the parameter `isRedacted=` which, as the name suggests, accepted the encrypted form of the strings "Yes" or "No" to view unredacted copies of documents.

### Hillsborough County

Session cookies were used to determine which cases and documents a user was viewing. When a case or document was requested, a request was sent to the API, which associated the data with the user’s session cookie and returned the results. The API endpoint for obtaining document information returned a list that included the document ID, several values that specify the security level required to view the document, and the user’s applied access level. The frontend chose whether to display a document link or one of the restriction type indicators based on the applied access level. The backend assumed that if an attacker is able to request a document, they must have access to it.

### Lee County

Session cookies were used in a manner similar to Hillsborough County. For most types of cases, document IDs were available in the pre-rendered HTML. Documents could be viewed by executing a single function call.

### Monroe County

A similar vulnerability was discovered initially, but their change introduced a new vulnerability. Much like Hillsborough County, the frontend chose whether to display a document link based on the security level of the document and the backend made a bad assumption. After fixing the first vulnerability, the developers helpfully left a debugger statement one line before these checks are made, which pauses code execution and gave the attacker a chance to adjust the security level.

### Sarasota County

In what is certainly the most egregious of the Florida county vulnerabilities, document URLs contained nothing more than a numeric document ID. The only protection was a CAPTCHA on the landing page, which could be bypassed.
Due to the triviality of discovering this vulnerability and the ease with which it could be exploited on a mass scale, it is reasonable to assume that every document became compromised the moment they were filed.
In defense of Sarasota County, they were the first to attempt to fix their issue. Unfortunately, their first attempt at a fix was not sufficient and a new vulnerability was discovered. Worst yet, while searching for a new method, a third vulnerability (or a second consequence of the second vulnerability) was discovered that allowed an unauthenticated attacker to view restricted cases.

## Timeline

- 2023-06-21 - Vulnerability discovered in Monroe County.
- 2023-07-04 - First report to Monroe County – no response.
- 2023-07-14 - Second report to Monroe County – no response.
- 2023-07-17 - Vulnerability discovered in Catalis CMS360.
- 2023-08-?? - Vulnerability fixed in Monroe County.
- 2023-08-?? - Second vulnerability discovered in Monroe County.
- 2023-09-14 - "Eli" provides the above information to [Jason Parker](https://ꩰ.com/@north) at [Jeltz](https://jeltz.org).
- 2023-09-15 - Vulnerability discovered in Lee County.
- 2023-09-15 - Vulnerability discovered in Sarasota County.
- 2023-09-16 - Vulnerability discovered in Hillsborough County.
- 2023-09-18 - Vulnerability discovered in Brevard County.
- 2023-09-30 - First report sent to Catalis – no response.
- 2023-10-02 - Second report sent to Catalis – no response.
- 2023-10-02 - All known vulnerabilities reported to CERT/CC for coordination.
- 2023-10-03 - Report sent to Florida’s Office of the State Courts Administrator (OSCA).
- 2023-10-03 - Third report sent to Catalis detailing report to CERT/CC – no response.
- 2023-10-04 - [Cybersecurity and Infrastructure Security Agency](https://cisa.gov) (CISA) takes over from CERT/CC.
- 2023-10-06 - Fourth report sent to Catalis with disclosure timeline and CISA hand-off – no response.
- 2023-10-06 - Vulnerability discovered in Tyler Technologies Court Case Management Plus.
- 2023-10-06 - Florida OSCA responds to report.
- 2023-10-06 - Florida OSCA sends report to Florida Court Clerks & Comptrollers.
- 2023-10-07 - Two additional vulnerabilities discovered in Court Case Management Plus.
- 2023-10-07 - Report sent to Tyler Technologies.
- 2023-10-08 - Tyler Technologies responds to report.
- 2023-10-10 - Vulnerabilities in Court Case Management Plus reported to CISA.
- 2023-10-10 - Vulnerability discovered in Henschen & Associates’ CaseLook.
- 2023-10-11 - First report sent to Henschen & Associates – no response.
- 2023-10-11 - Sarasota County fixes vulnerability.
- 2023-10-11 - Second vulnerability discovered in Sarasota County.
- 2023-10-13 - Second report sent to Henschen & Associates – no response.
- 2023-10-16 - Third report sent to Henschen & Associates – no response.
- 2023-10-17 - Fourth report sent to Henschen & Associates – no response.
- 2023-10-27 - Sarasota County fixes second vulnerability
- 2023-11-01 - Tyler Technologies fixes vulnerabilities in Court Case Management Plus.
- 2023-11-01 - Catalis responds to report after discussion with CEO Scott Roza.
- 2023-11-03 - Catalis fixes vulnerability in CMS360.

## Overview by Platform

| Vendor                  | Platform                   | IDs Available | Access | Fix Date   |
| ----------------------- | -------------------------- | ------------- | ------ | ---------- |
| Catalis / ICON Software | CMS360                     | No            | R      | 2023-11-03 |
| Tyler Technologies      | Court Case Management Plus | Yes           | RUZ    | 2023-11-01 |
| Henschen                | CaseLook                   | No            | R      |            |
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

### Acknowledgements

- "Eli", who discovered the first set of court vulnerabilities and has been a major contributor throughout. _(Is this how most adults find and make friends?)_
- Andrew Crocker and Hannah Zhao from the [Electronic Frontier Foundation](https://eff.org), who were there when I (and many, many others) needed them most.
- [Josh Renaud](https://www.joshrenaud.com/) from [St. Louis Post-Dispatch](https://www.stltoday.com/), who fought this fight before me, earning him a [Press Freedom Award](https://www.youtube.com/watch?v=DhflQv1rJ1A).
- [Jaku](https://twitter.com/Jaku) founder of [Crowd Control](https://crowdcontrol.live/), who frequently offered his cybersecurity wisdom and experience.
- [Zack Whitaker](https://techcrunch.com/author/zack-whittaker/) from [TechCrunch](https://techcrunch.com), who immediately recognized the severity and jumped at the chance to help.
- The [CISA CVD Team](https://www.cisa.gov/coordinated-vulnerability-disclosure-process) assisted with the coordination of these vulnerabilities.
- [judyrecords.com](https://judyrecords.com), who handled the first round of blowback from court security issues.
- The State of Arkansas, who allowed me to present my findings in an effort to avoid the same pitfalls when building their own court platform.

### Footnotes

- [^1]: _Catalis [states on their website](https://web.archive.org/web/20231005221308/https://catalisgov.com/our-story/) that "in less than five years [we have] strategically acquired and combined more than 30 public sector software companies". Learning and merging infrastructure after an acquisition takes a large amount of effort. Juggling 30 acquisitions would be a monumental undertaking that reduces focus on other necessary areas of business (e.g. securing legacy platforms)._
- [^2]: _This is very uncommon in other states. A guess as to why Florida does things so differently is that Florida’s extensive open records or "sunshine" laws (see also "Florida Man") spawned platforms before commercial vendors began to enter the market. Unlike many states where court records are made available through a single state website, Florida generally allows each county to make decisions about which platforms they use, as long as they follow Florida’s [Standards for Access to Electronic Court Records](https://web.archive.org/web/20230917165659/https://www.flcourts.gov/content/download/850949/file/standards-for-access-to-electronic-court-records-september-2022.pdf) and [Access Security Matrix](https://web.archive.org/web/20231005211829/https://www.flcourts.gov/content/download/858675/file/access-security-matrix-v13-january%202023.pdf)._

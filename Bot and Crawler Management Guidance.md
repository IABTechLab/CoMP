# Bot and Crawler Management Guidance

## Table of Contents

- [Background and Objective](#background-and-objective)
- [Summary](#summary)
  - [Key Questions](#key-questions)
    - [Identity & Transparency](#identity--transparency)
    - [Granular Control](#granular-control)
    - [Gray Zone & Challenges](#gray-zone--challenges)
    - [Future-Proofing](#future-proofing)
- [Building Your Crawler Management Capability](#building-your-crawler-management-capability)
  - [Establish a Review Cadence](#establish-a-review-cadence)
  - [Documenting Your Decisions](#documenting-your-decisions)
  - [Communicating Your Policy](#communicating-your-policy)
- [Evaluating Crawlers](#evaluating-crawlers)
  - [Traffic Return Value](#traffic-return-value)
  - [Content Use Value](#content-use-value)
  - [Bandwidth and Infrastructure Cost](#bandwidth-and-infrastructure-cost)
  - [Reputational and Brand Value](#reputational-and-brand-value)
  - [Strategic Value](#strategic-value)
  - [Assessment Matrix](#assessment-matrix)
  - [Handling the Gray Zone](#handling-the-gray-zone)
- [Decision Framework](#decision-framework)
- [Conclusion](#conclusion)
- [Supplemental Information](#supplemental-information)
  - [Common Types of Bots and Crawlers](#common-types-of-bots-and-crawlers)
  - [How Crawlers Identify Themselves](#how-crawlers-identify-themselves)
    - [User-Agent Strings](#user-agent-strings)
    - [IP Address and ASN Verification](#ip-address-and-asn-verification)
    - [robots.txt Declarations](#robotstxt-declarations)
  - [Known Crawler Databases](#known-crawler-databases)
  - [How to Identify Who Is Really There](#how-to-identify-who-is-really-there)
    - [ASN Lookup for Operator Identification](#asn-lookup-for-operator-identification)
    - [Behavioral Fingerprinting](#behavioral-fingerprinting)
  - [The Unknown Bot Problem](#the-unknown-bot-problem)
  - [Existing Controls and Their Limitations](#existing-controls-and-their-limitations)
    - [robots.txt](#robotstxt)
    - [IP Blocking and Rate Limiting](#ip-blocking-and-rate-limiting)
    - [CDN Bot Management Products](#cdn-bot-management-products)
    - [Web Application Firewalls (WAF)](#web-application-firewalls-waf)
  - [The Gap in Existing Tools](#the-gap-in-existing-tools)
  - [Emerging Licensing Declarations](#emerging-licensing-declarations)

---

## Background and Objective

As the use of LLMs and Agents explode, AI Systems need more and more content to inform their models. This has sent large language model crawlers to ransack Content Owners' content with little recourse. While this does threaten to upend the economic foundation of the open internet, it affects all companies with intellectual property in any format.

That said, the problem is not the existence of crawling. The problem is **invisibility**. Most Content Owners have little to no understanding of how much it is costing their business in Operating Cost to deliver their content — to say nothing of how their content is being accessed, interpreted, or used after it's been crawled.

This guide is a practical framework for gaining that understanding and making informed decisions. It does not recommend blocking or allowing any specific crawler. It provides the tools to reason about the decision yourself.

---

## Summary

This guide is primarily intended for business decision makers who are responsible for the commercial value of their organization's intellectual property.

While technical teams handle the implementation, the business leads are the owners of the strategy. This document aims to help them:

- **Define the Strategy:** Determine which bots and crawlers are "allies" vs. "extractors."
- **Govern the Intellectual Property:** Align access with the company's broader data licensing and copyright stance.
- **Direct the Vendors:** Move from accepting default settings to demanding high-transparency bot management that serves business goals.

Taking an all-on or all-off approach is not appropriate. Leaving all on will result in unfettered access with no recourse. Turning all off will break systems that directly contribute to revenue — such as disabling ads.txt verification, resulting in very little to no programmatic revenue.

This guide aims to provide a practical framework for non-technical teams to create a consistent strategy to manage non-human traffic. It is not exhaustive, and alternative vectors for unapproved crawling still exist, but it will provide an outline for what business teams need to understand before engaging with technical teams.

### Key Questions

These questions will help inform conversations between business teams and internal IT teams, or directly with vendors like Content Delivery Networks (e.g., Cloudflare, Akamai, Fastly) or a Bot Management vendor.

#### Identity & Transparency

- How much money am I spending delivering my content to bots and crawlers?
- Your dashboard shows "verified bots," but how are you verifying them beyond just the User-Agent? Are you doing automated verification of the origin of the IP addresses you're seeing? *(See: How Bots Identify Themselves)*
- Can you provide a report of "Unknown" traffic volume compared to "Verified" traffic? I need to see the size of our "invisible" audience. *(See: How to Identify Who Is Really There)*
- How quickly are new AI crawlers (like those from emerging LLM startups) added to your "Good Bot" or "Bad Bot" categories?

#### Granular Control

- We want to allow the operator of Search Engine X for discovery but block them from using our content for AI training. Can your tool distinguish between different bot functions from the *same* operator?
- Can we implement rate limiting specifically for AI crawlers without affecting our SEO crawlers? I want to keep the door open but slow down the extraction rate.

#### Gray Zone & Challenges

- When you gate-challenge a bot, what actually happens?
- What does that challenge look like? Does it match the look and feel of my page?
- Are we inadvertently blocking legitimate research or specialized partners because they can't solve a CAPTCHA?
- How granular can I be when setting up rules for when to issue a challenge? Am I able to set a range of confidence for when to issue a CAPTCHA?
- Can we set up a "monitoring-only" mode for specific companies so we can collect data on a bot's behavior before we decide to block it?
- Do you surface new traffic sources for me to review and decide on the appropriate action?
- What is your approach to bots acting on behalf of human users?

#### Future-Proofing

- How does your bot management integrate with our analytics so I can see the amount of traffic each bot is actually delivering?
- Do your tools allow me to monitor changes in traffic that is being blocked so I can reassess my blocking rules? *(See: The Unknown Bot Problem)*
- Do you have or plan to build a marketplace of your own for content licensing?
- Do your tools allow for nuanced decision making? If so, how granular are they?
- Do you have or plan to handle payments or build clearing-house functionality?

Each of these purposes has different implications for the content owner. The value of a search engine crawler to an SEO-dependent publication is self-evident. The value of a training data crawler that collects content — especially where there isn't a commercial agreement in place — is a different question entirely.

---

## Building Your Crawler Management Capability

The sequence matters. Content owners who act first (block/allow) without visibility often make decisions based on incomplete information — for example, blocking a crawler that drives significant referral traffic, or allowing one that consumes disproportionate resources for no benefit.

**The recommended sequence:**
![](https://github.com/IABTechLab/CoMP/blob/6688ff6dc28a7e9ef360e68b5a01f7df46005686/assets/sequence.png)

### Establish a Review Cadence

Bot and crawler traffic is not static. New crawlers appear regularly; known crawlers change behavior; operators launch new products. A crawler management practice requires periodic review:

- **Weekly:** Check for new unknown high-volume bots
- **Monthly:** Review bandwidth consumption by operator; review decision queue (bots pending evaluation)
- **Quarterly:** Full value matrix review; update robots.txt and middleware rules; check for new RSL/CoMP developments

### Documenting Your Decisions

For each significant crawler, maintain a record of:

| Field | Description |
|---|---|
| **Decision** | Allow / Allow with conditions / Require licensing / Block |
| **Decision date** | When the decision was made |
| **Rationale** | Why this decision was made (value assessment summary) |
| **Conditions (if any)** | Rate limits, path restrictions, citation requirements |
| **Review date** | When this decision should be revisited |

This documentation protects you if a decision is questioned, enables consistent policy across your team, and makes quarterly reviews faster.

### Communicating Your Policy

Once you have a policy, consider publishing it:

- A human-readable `crawler-policy.md` or dedicated page on your site
- A machine-readable `robots.txt` file
- A contact address for licensing inquiries

Operators running legitimate crawlers often want to understand content owner policies and will engage commercially if a clear path exists. Making your policy visible and your contact reachable turns a passive block into a potential relationship.

---

## Evaluating Crawlers

Once you know who a crawler is, the next question is: **what is this relationship worth to me?** This is not a binary question. The value of a crawler exists on multiple dimensions simultaneously.

### Traffic Return Value

Does this crawler drive readers back to your site? Search engine crawlers have a clear, measurable traffic return value — you can observe it in referral analytics. AI inference crawlers may drive some traffic if they cite sources; training crawlers typically return none.

*Assessment questions:*

- Can I see referral traffic attributed to this operator in my analytics?
- Does this operator's product surface my content in ways that lead users to my site?
- Is there evidence that users of this service visit my site after seeing my content there?

### Content Use Value

How is your content being used? Being indexed for a general search engine serves discovery. Being used to train a proprietary AI model creates a commercial product without your participation.

*Assessment questions:*

- Is the crawled content used to create a product that competes with mine (e.g., AI-generated summaries replacing my articles)?
- Is the crawled content attributed to me or anonymized into a training dataset?
- Does the operator's product enhance or diminish the perceived value of reading the original?

### Bandwidth and Infrastructure Cost

High-volume crawlers consume server resources and bandwidth. At scale, this has a real cost.

*Assessment questions:*

- What percentage of my total server requests is this crawler?
- What percentage of outbound bandwidth is attributable to this crawler?
- Is the crawl rate respectful (uses Crawl-delay, crawls during off-peak hours)?

### Reputational and Brand Value

Some crawlers represent associations you may or may not want. Being well-indexed by a reputable search engine is valuable. Being scraped by a disreputable data broker is not.

*Assessment questions:*

- What is the public reputation of the operator?
- Would my audience or sponsors have concerns about knowing my content feeds this service?

### Strategic Value

Some crawlers represent relationships that have longer-term strategic value beyond immediate traffic — partnerships, data exchange agreements, distribution deals.

### Assessment Matrix

Some of these categories are easily quantifiable. For example, "how much referral traffic is being sent by the AI System?" or "How much is it costing my company to deliver my content to this bot?" will have numeric answers. When evaluating reputation, questions like "Does this company adhere to robots.txt?" and "Is this company doing academic research?" can be helpful. Where there is an existing license or partnership in place, understanding how the commercial agreement informs the cadence and use of content is paramount.

For each crawler identified in your traffic, score each dimension on a simple 1–5 scale *(scores are for illustrative purposes only and not a recommendation or endorsement)*:

| Crawler | Traffic Return | Content Use | Bandwidth Cost | Reputation | Strategic |
|---|---|---|---|---|---|
| ExampleBot1 | 5 | 4 (discovery) | 2 (moderate) | 5 | 5 |
| AISystem1Bot | 1 (none) | 2 (training) | 3 | 3 | 2 |
| AISystem2Bot | 2 | 3 (inference) | 2 | 4 | 3 |
| CCBot | 1 (none) | — | 4 | 3 | 1 |
| AhrefsBot | 1 | — | 2 | 4 | 2 |
| Unknown Bot | 1 | 1 (unknown) | 3 | 1 | 1 |

> *See: [Common Types of Bots and Crawlers](#common-types-of-bots-and-crawlers)*

No scoring framework produces the right answer for every content owner. A content owner whose business model depends on exclusive data will weigh content use more heavily. A content owner exploring licensing revenues will weigh strategic value differently. The matrix is a tool for making your reasoning explicit — it is not a formula for an automatic decision.

### Handling the Gray Zone

Many crawlers fall into a gray zone where the value case is ambiguous. Some principles for navigating ambiguity:

**Default to observation before action.** One month of logged, attributed crawler data will reveal patterns you cannot anticipate in advance. Make major decisions — especially blocks — based on data, not assumptions.

**Evaluate at a crawler level.** A company may operate multiple crawlers with different purposes. Where one crawler provides search indexing, another from the same company may provide AI training. Evaluate each crawler on its own terms.

**Recognize that decisions can be reversed.** A block is not permanent. Start conservative, observe, and open access when the value case becomes clear. It is easier to unblock a crawler that turns out to be valuable than to recollect data that has already been crawled.

**Consider the implications.** Blocking crawlers may carry reputational or relationship implications. Consider whether a licensing conversation is more valuable than a block.

---

## Decision Framework

Once you understand who is crawling your site and what value each crawler represents, you have four broad options for each one. Go category by category and then, within categories your company identifies as high-risk, evaluate each crawler individually.

| Decision | Description |
|---|---|
| **Allow (Unrestricted)** | The crawler provides sufficient value (traffic return, discovery, archival) to justify free access with no restrictions. |
| **Allow with Conditions (Managed Access)** | The crawler has value, but you want to shape how it accesses your content — rate limits, content scope restrictions, citation requirements, or a formal licensing agreement. |
| **Require Licensing (Monetize)** | The crawler extracts value from your content without returning commensurate value. You are willing to allow access but only under a formal license with payment terms. |
| **Block** | The crawler provides no value, creates cost, or represents a threat. You do not want to grant access under any terms. |

![](https://github.com/IABTechLab/CoMP/blob/6688ff6dc28a7e9ef360e68b5a01f7df46005686/assets/example%20decision%20tree.png)

As with any system, Content Owners will need to validate that terms are being adhered to at some regular cadence using processes not addressed in this document.

---

## Conclusion

To navigate the evolving landscape of crawling by AI Systems, Content Owners must have clear visibility into all non-human traffic. You need to know which commercial entities are accessing your content, how often, and for what purpose. Once qualified, teams are able to evaluate crawlers across multiple dimensions and organizations are empowered with the information they need to create an informed strategy.

This framework is meant to help create informed strategies and make conversations across business and engineering teams more productive. Ultimately, a proactive management practice ensures that intellectual property is protected while maintaining the systems that drive discovery and revenue.

---

## Supplemental Information

### Common Types of Bots and Crawlers

A **bot** is any software program that makes automated HTTP requests to a web server. A **crawler** (also called a spider or web robot) is a specific type of bot that systematically traverses the web by following links, typically to build an index or dataset of content.

The distinction matters because not all bots crawl. A monitoring bot checks whether your site is up and returns; it doesn't follow links or collect content. A crawler arrives, reads a page, discovers links to other pages, and continues methodically across your site.

| Bot Type | Purpose | Notes |
|---|---|---|
| **Search Engines** | Index content for discovery in search engines. Their core function is to bring referral traffic back to content owners. Blocking them means reduced search visibility. | The crawler reads your content; in exchange, the search engine refers readers to you. |
| **SEO and Analytics** | Operated by services used by businesses to analyze website structure, backlinks, and keyword rankings. | Often not malicious, but may represent competitive intelligence gathering. |
| **AI Training** | Collect large quantities of content to train machine learning models. | At time of publication, largely done without commercial agreements — content owners receive no guarantees of traffic, citation, or payment by default. |
| **AI Inference** | Fetch content in real time, often in response to a user's query, to provide up-to-date answers. | May send some referral traffic if they cite sources, but often result in zero-click search. |
| **Archival and Research** | Operated by institutions preserving the web for posterity or studying its structure for academic purposes. | Generally non-commercial; may present a content leakage vector. |
| **Scrapers/Aggregators** | Collect headlines, summaries, or full articles for redistribution. | Impact depends on the content owner's distribution strategy. |
| **Security and Infrastructure** | Probe servers for technical information, often for cybersecurity use cases. | May represent reconnaissance for malicious activity; monitor closely. |
| **Performance and Uptime** | Sporadically check websites and apps to determine if content is available. | Usually low traffic volume; may be operated by the content owner or their hosting provider. |
| **Price and Inventory Monitoring** | Track e-commerce data for comparison engines or aggregators. | — |
| **Interoperability** | Unfurls social links, ensures referral links are live. | — |
| **Bots on Behalf of Humans** | Crawling content on behalf of a human user. | — |

### How Crawlers Identify Themselves

At minimum, every automated request should produce a log entry containing:

| Field | Purpose |
|---|---|
| Timestamp | Enables time-series analysis and rate calculation |
| Source IP | Required for ASN lookup and identity verification |
| ASN | Identifies the operating organization |
| User-Agent string | Primary crawler identifier |
| HTTP method | GET/POST/HEAD distinguish crawl behavior |
| Requested path | Reveals what content is being targeted |
| HTTP response code | Confirms access was granted or denied |
| Response bytes | Quantifies bandwidth consumed |
| Referrer header | Reveals how the crawler discovered the URL |
| Request headers (anonymized) | Fingerprinting; distinguishes bot from human |

#### User-Agent Strings

The primary way a crawler announces itself is via the HTTP `User-Agent` header, sent with every request. Typical examples:

```
GPTBot/1.0 (+https://openai.com/gptbot)
Mozilla/5.0 (compatible; Googlebot/2.1; +http://www.google.com/bot.html)
ClaudeBot/1.0; +https://anthropic.com/claude-web
```

The UA string typically contains a product name and version, and a URL pointing to documentation about the crawler.

**Limitations of UA strings:**

- **They are voluntary** — any bot can claim to be any other bot
- **They can be spoofed** — a scraper can set `User-Agent: Googlebot/2.1` and appear legitimate
- **They can be absent** — some bots send no UA or a generic string like `python-requests/2.28.0`
- **They change** — crawlers update version numbers; pattern lists can become stale

#### IP Address and ASN Verification

A more reliable signal than the UA string is the IP address of the requesting server. Every IP address belongs to an Autonomous System Number (ASN), which maps to a registered organization.

Google's crawlers, for example, will always originate from ASNs registered to Google LLC (AS15169). If a request claims to be Googlebot but originates from a residential ISP or anonymous VPN ASN, it is almost certainly not Google.

Content owners can verify crawler identity by:

- Looking up the ASN of the source IP (using tools like `whois`, `ipinfo.io`, or `bgp.tools`)
- Checking whether the ASN matches the declared operator
- For major search engines: performing a reverse DNS lookup on the IP (e.g., `dns.google.com` format for Google)

#### robots.txt Declarations

Some crawlers declare themselves in the context of `robots.txt` interactions — they read the file, honor `Disallow` directives, and identify their `User-agent` group. This is a signal of cooperative intent but not a guarantee of compliance.

### Known Crawler Databases

Several maintained databases map UA strings and IP ranges to operators and declared purposes:

- **[IAB TechLab Spiders and Bots List](https://iabtechlab.com/)** — List and classification of bots related to IVT determinations for advertising
- **[Known Agents](https://knownagents.com)** (knownagents.com) — A curated, frequently updated database of AI agents and their declared purposes, with robots.txt snippet generators
- **[user-agents.net](https://user-agents.net)** — Broad database of UA strings with classification
- **[crawleragents.net](https://crawleragents.net)** — Crawler-specific registry
- **Search engine documentation** — Google, Bing, and others publish their own crawler IP ranges and verification methods

> No single database is complete. A robust identification system layers multiple sources.

### How to Identify Who Is Really There

Building a complete picture of automated traffic requires combining multiple signals. Sample identification workflow:

```
Request arrives
     │
     ├── User-Agent string
     │    └── Match against known crawler database
     │         → Operator name, declared purpose, documentation URL
     │
     ├── IP Address
     │    ├── ASN lookup → Registered organization
     │    ├── Reverse DNS → Hostname pattern (e.g., *.googlebot.com)
     │    └── IP reputation → Known datacenter, VPN, residential, etc.
     │
     ├── Behavioral signals
     │    ├── Request rate and pattern
     │    ├── URLs targeted (sitemap? deep pages? API endpoints?)
     │    ├── Headers present (browser-typical vs bot-typical)
     │    └── Crawl depth and breadth
     │
     └── Combined confidence score
          → Known operator (high confidence)
          → Likely operator (medium confidence, needs verification)
          → Unknown bot (low confidence, requires investigation)
```

#### ASN Lookup for Operator Identification

Even without a UA match, the ASN of a request's source IP often reveals the operating company.

An unrecognized UA originating from AS15169 is likely still a Google service. A UA claiming to be Googlebot originating from AS14618 (AWS) is almost certainly not.

#### Behavioral Fingerprinting

Beyond declared identity, what a bot *does* reveals more about its purpose:

| Behavior | Likely Purpose |
|---|---|
| Follows robots.txt Disallow | Well-behaved, likely legitimate operator |
| Ignores robots.txt | Aggressive scraper or malicious bot |
| Fetches only HTML, ignores images/CSS | Text collection (training data or indexing) |
| Targets specific content sections (e.g., `/research/`, `/analysis/`) | Targeted scraping or high-value content collection |
| Very high request rate with no delay | Aggressive scraping; not crawl-polite |
| Crawls at consistent intervals (e.g., every 24hr) | Content freshness monitoring or news aggregation |
| Hits only `/sitemap.xml` then follows URLs | Organized, systematic crawl — likely legitimate indexer |
| Sends no Accept-Language or Cookie headers | Almost certainly automated |
| Requests non-public URLs (admin paths, API endpoints) | Security scanner or reconnaissance bot |

### The Unknown Bot Problem

A meaningful percentage of bot traffic does not match any known crawler signature. This traffic may be:

- New crawlers that have not yet been catalogued
- Crawlers deliberately obscuring their identity (UA spoofing)
- Scrapers operated by businesses or individuals without public disclosure
- Malicious bots (content theft, credential stuffing, ad fraud infrastructure)

The inability to identify a bot should be treated as information in itself, not as a reason to ignore it. High-volume unidentified traffic deserves investigation.

### Existing Controls and Their Limitations

#### robots.txt

`robots.txt` is a text file at `https://yourdomain.com/robots.txt` that uses a standard syntax to instruct crawlers which paths to access and which to avoid.

**Example:**

```
User-agent: AiCobot
Disallow: /

User-agent: Searchbot
Allow: /
Disallow: /private/

User-agent: *
Crawl-delay: 10
```

**What robots.txt does well:**

- Communicates access policy to cooperative crawlers
- Scopes crawl depth and rate for compliant bots
- Universally understood by legitimate search engines and most AI crawlers

**Limitations:**

- **Entirely voluntary.** There is no technical enforcement mechanism. A crawler that ignores robots.txt will not be stopped by it.
- **Binary at path level.** You can allow or disallow paths, but you cannot express *why* or under *what terms*. There is no native mechanism for conditional access, licensing, or payment.
- **No operator identity verification.** Any bot can claim to be any User-agent group.
- **Static.** Changes to robots.txt take effect only when the crawler re-reads the file, which may take days or weeks.

> The RSL 1.0 standard extends robots.txt with a `License:` directive that links to a machine-readable license document, partially addressing the conditional access limitation.

#### IP Blocking and Rate Limiting

IP-based blocks can stop a known crawler from accessing your site. Rate limiting (e.g., max N requests per second per IP) can throttle aggressive crawlers.

**What it does well:**

- Technically enforced — a blocked IP cannot access content
- CDN-level rate limits can protect origin infrastructure

**Limitations:**

- **Crawlers rotate IPs.** A well-resourced crawler can operate across thousands of IP addresses, making IP-based blocks a persistent game of whack-a-mole.
- **False positives.** Large shared IP ranges (cloud providers, CDNs) may serve both crawler and human traffic on the same IP.
- **No identity.** Blocking an IP range does not leave a record of why the access was blocked or what terms would make it acceptable.

#### CDN Bot Management Products

Many CDN providers offer managed bot detection and blocking products (Cloudflare Bot Management, Akamai Bot Manager, Fastly Bot Detection, etc.).

**What they do well:**

- Sophisticated detection using TLS fingerprinting, behavioral analysis, and reputation databases
- Challenge mechanisms that require JavaScript execution (which crawlers typically cannot perform)
- Managed lists of known good and bad bots

**Limitations:**

- **Vendor lock-in.** A CDN bot management product typically only works on that CDN. Switching CDN providers requires reconfiguring your entire bot policy.
- **Black box.** You may not have full visibility into *why* a request was blocked or *who* was blocked.
- **No commercial layer.** CDN bot management can block or allow but cannot issue a license, capture payment terms, or generate invoices.
- **Not universal.** A tool built on top of CDN bot management is not applicable to content owners on a different CDN.

#### Web Application Firewalls (WAF)

WAF rules can block requests based on patterns (UA strings, IP ranges, request characteristics). Many CDN providers include a WAF.

**Limitations:** Same as IP blocking — technically enforced but brittle against determined crawlers, and provides no commercial or licensing layer.

### The Gap in Existing Tools

A common thread across all existing controls: they are **enforcement tools without a citation or evaluation layer**. They can block or allow, but they do not tell you:

- Who is responsible for the automated traffic you are seeing
- What they do with what they collect
- Whether the relationship has commercial value worth pursuing
- What your full automated traffic picture looks like over time

An additional gap in current tools is the analysis of bot behavior (time on site, number of links processed, etc.) similar to human user behavior patterns. This is the gap that a dedicated tracking and evaluation tool addresses.

### Emerging Licensing Declarations

The [RSL 1.0](https://rslstandard.org/) standard (Really Simple Licensing) is a machine-readable standard designed to help content creators specify exactly how AI models and web crawlers can use their data. Adoption is early but growing.

[WebBotAuth](https://datatracker.ietf.org/wg/webbotauth/about/), used by Cloudflare and supported by OpenAI, AWS Bedrock, and others, is another emerging mechanism in this space.

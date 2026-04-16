# Content Metadata Marketplace Supply Specification (MVP)

## Table of Contents

- [Content Metadata Marketplace Supply Specification (MVP)](#content-metadata-marketplace-supply-specification-mvp)
- [Purpose](#purpose)
    - [In scope](#in-scope)
    - [Out of scope](#out-of-scope)
- [AI System Request for Content](#ai-system-request-for-content)
  - [Object: AISystem](#object-aisystem)
  - [Object: AISystemUse](#object-aisystemuse)
- [Content Owner or Marketplace Response](#content-owner-or-marketplace-response)
  - [Object: Package](#object-package)
  - [Object: Scope](#object-scope)
  - [Object: Text](#object-text)
  - [Object: Video](#object-video)
  - [Object: Image](#object-image)
  - [Object: Audio](#object-audio)
  - [Object: Retrieval](#object-retrieval)
- [Lists](#lists)
  - [List: AI Authentication Methods](#list-aiauth)
  - [List: Content Scope](#list-contentscope)
  - [List: Intended Use Function](#list-function)
  - [List: Sub-Function](#list-subfn)
  - [List: Content Type](#list-ctype)
  - [List: Publication Status](#list-published)
  - [List: Creation Source](#list-sourcetype)
  - [List: Authorization Type](#list-auth)
  - [List: Endpoint / Delivery Format](#list-retrieval-type)
  - [List: Function Definitions](#list-function-definitions)
  - [List: Sub-Function Definitions](#list-subfn-definitions)
  - [List: Access Format Type Definitions](#list-access-format-types)
- [Implementation Guidance](#implementation-guidance)
    - [Example 1: Unauthorized Request](#example-1-unauthorized-request)
    - [Example 2: Authorized Request](#example-2-authorized-request)
    - [Example 3: Curated Selection of Multiple Asset Types](#example-3-curated-selection-of-multiple-asset-types)
    - [Example 4: Image Creation](#example-4-image-creation)
    - [Example 5: Podcast Full Feed Authorized](#example-5-podcast-full-feed-authorized)
    - [Example 6: Podcast Published After a Date](#example-6-podcast-published-after-a-date)
    - [Example 7: Video Creation](#example-7-video-creation)
    - [Example 8: Agent Actions](#example-8-agent-actions)

# Purpose
Define the object model and transport format for describing content assets offered by Inventory Owners and/or Content Marketplace to an AI System. It aims to build the foundation for a marketplace that supports Content Owners while providing enough flexibility to future proof for new use cases.

### In scope
This API will standardize a language that allows inventory owners to communicate metadata about the content on offer, where the terms to use that content are, and how to access the content. It also allows AI Systems to tell Content owners and/or Marketplaces the action they would like to perform using the content package

### Out of scope
- <b>Bot Blocking</b> - while Content Owners are strongly encouraged to block crawling until and unless some commercial agreement is in place, Content Owners must work with their partners to block crawling from AI Systems at the level they deem appropriate for their business
- <b>Licensing Terms</b> - Commercial terms between Content Owners, Marketplaces, and AI Systems must be negotiated a priori to this API
- <b>Clearing House Functionality</b> - This API can be used to communicate where AI Systems can find terms to license and access content, but token issuance, counting, and payment are out of scope.
- <b>Supply Discovery</b> - It is assumed that AI Systems have an understanding of the content they'd like to license. Where discovery is required, AI Systems should work directly with Marketplaces and Content Owners.
- <b>Reporting</b> - The CoMP API is a communication protocol only, as such, it does not explicitly support reporting, but it is strongly recommended that the Content Owner receives reporting from the AI System to track and monitor usage against terms laid out in the license.

# Specification
# AI System Request for Content

## Object: AISystem
Information about the AI System making the request to scrape the content

| Attribute | Type | Description |
| --- | --- | --- |
| name | string, required | Canonical domain of the AI System requesting access to the content |
| ua | string, recommended | User agent of the AI System requesting access to the content |
| id | string | ID of the AI system, as registered by Tech Lab Agent Registry |
| aisysuse | object, required | Information about the Content being requested, and how the AI system will utilize the content after it has been crawled. <br><br>See [Object: AISystemUse](#object-aisystemuse) for additional information |
| ext | object | Placeholder for implementer specific extensions |

## Object: AISystemUse
Information about the Content being requested, and how the AI system will utilize the content after it has been crawled

| Attribute | Type | Description |
| --- | --- | --- |
| lid | string, required | License ID - Unique license reference for the content package the AI System would like to crawl<br><br>Assumes the AI system has a license to perform the function a priori. If the AI System has not been issued a token, it should review terms listed in `package.licenseurl`. |
| aiauth | int, required | Information about how the AI system plans to authenticate itself based on how access has been permissioned by the content owner.<br><br>See: [List: AI Authentication Methods](#list-aiauth) |
| uri | string, array | Uniform Resource Identifier (URI) that the AI System is requesting to crawl. |
| scope | int | General information about the content the AI System is asking for.<br><br>See: [List: Content Scope](#list-contentscope)<br><br>If blank, the scope will be determined by the information in [Object: Scope](#object-scope). |
| function | int, array | Function(s) that the AI System is stating it will use the content in this package for.<br><br>See: [List: Intended Use Function](#list-function) for values.<br><br>If function is not known <i>a priori</i>, implementers should use `all`. 
| subfn | int, array | Additional information about the function(s) the AI System is stating it will use the content for.<br><br>See: [List: Sub-Function](#list-subfn) for values. |
| resdis | int | Indicates if the results will be displayed to a human user where 0 = No and 1= Yes |
| ext | object | Placeholder for implementer specific extensions |

# Content Owner or Marketplace Response

## Object: Package
High-level information about the package of inventory to be included in the crawl by the AI System

| Attribute | Type | Description |
| --- | --- | --- |
| id | string, required | Unique package identifier defined by the Content Owner or Marketplace |
| title | string | Title of the package of content to be included in the crawl |
| seller | string | Canonical domain of the business entity offering the content package to be crawled. |
| packager | string | Canonical domain of the business entity that did the packaging of content, if different than the seller of the package. |
| licenseurl | string | URL for AI system to find the License(s) required to access content. <br><br>It is strongly recommended that the Content Owner receives reporting from the AI System to track and monitor usage against terms laid out in the License. |
| citation | int | Indicates if citation of the Content Owner is required to use this package where 0 = No and 1 = Yes. Strongly recommended if `aisystem.resdis` has a value of 0.|
| reporturl | string | URL for AI system to send usage reporting to the Content Owner or Marketplace. |
| scope | object | Information about the scope of content included in the package. <br><br>See [Object: Scope](#object-scope) for additional detail |
| retrieval | object | Information about how the AI System may access the Content once a token has been issued<br><br>See [Object: Retrieval](#object-retrieval) for additional detail. |
| ext | object | Placeholder for implementer specific extensions |

## Object: Scope
Information about the scope of the elements included in the package

| Attribute | Type | Description |
| --- | --- | --- |
| scope | int | Information about how much of the Content Owner's corpus is available in this package.<br><br>See: [List: Content Scope](#list-contentscope) |
| max | int | Indicates if there is an upper limit on how much the AI system may crawl where 0 =  No — AI system has unlimited access and 1 = Yes — there is a maximum . Where this value is 1, AI Systems should reference the license to determine what the upper limit is and what it applies to. |
| ctype | int, array | Type of content available in this package.<br><br>See: [List: Content Type](#list-ctype) |
| text | object, array | Information about text based assets included in this package. There should be one object per each distinct text-based asset.<br><br>See [Object: Text](#object-text) for additional detail |
| video | object, array | Information about Video assets included in this package. There should be one object per each distinct video asset.<br><br>See [Object: Video](#object-video) for additional detail |
| image | object, array | Information about image assets included in this package. There should be one object per each distinct image asset.<br><br>See [Object: Image](#object-image) for additional detail |
| audio | object, array | Information about audio assets included in this package. There should be one object per each distinct audio asset.<br><br>See [Object: Audio](#object-audio) for additional detail |
| ext | object | Placeholder for implementer specific extensions |

## Object: Text
Information about each text-based asset included in the package. One object per individual text-based asset should be sent.

| Attribute | Type | Description |
| --- | --- | --- |
| title | string | Title of the Text asset |
| cattax | int, default 9 | The taxonomy in use. Refer to the [AdCOM List: Category Taxonomies](https://github.com/InteractiveAdvertisingBureau/AdCOM/blob/main/AdCOM%20v1.0%20FINAL.md#list--category-taxonomies-) for values. If no `cattax` field is supplied IAB Content Category Taxonomy 3.1 is assumed.|
| cat | int, array | Array of IAB Tech Lab content categories of the content. The taxonomy to be used is defined by the `cattax` field.|
| language | int, array | Content language using ISO-639-1-alpha-2.|
| wordcount | int, array | Count of whitespace-delimited words in main body |
| pubdate | string | Original publication date/time in ISO-8601 |
| published | int, default 1 | Denotes if the content is publicly available.<br><br>See: [List: Publication Status](#list-published) |
| update | string | Most recent substantive update date/time in ISO-8601 |
| author | string, array | Name of the Author(s) |
| sourcetype | int | Source of the creation of the asset.<br><br>See: [List: Creation Source](#list-sourcetype) |
| provenance | int | Indicates if provenance is available for the text asset, where 0 = No and 1 = Yes. |
| provent | string | Canonical domain of the entity providing the provenance (e.g. C2PA)<br><br>Required if `provenance` = 1 |
| ext | object | Placeholder for implementer specific extensions |

## Object: Video
Information about each video asset included in the package. One object per individual video asset should be sent.

| Attribute | Type | Description |
| --- | --- | --- |
| title | string, array | Title of the Video asset included in the package |
| cattax | int, default 9 | The taxonomy in use. Refer to the [AdCOM List: Category Taxonomies](https://github.com/InteractiveAdvertisingBureau/AdCOM/blob/main/AdCOM%20v1.0%20FINAL.md#list--category-taxonomies-) for values. If no `cattax` field is supplied IAB Content Category Taxonomy 3.1 is assumed.|
| cat | int, array | Array of IAB Tech Lab content categories of the content. The taxonomy to be used is defined by the `cattax` field.|
| language | int, array | Content language using ISO-639-1-alpha-2.|
| dur | int, array | Duration of the Video asset included in the package |
| clip | int | Denotes if the full length asset, or a clip derived from a longer video, where 0 = full length and 1 = Clip (derived from a longer video)|
| wordcount | int, array | Count of whitespace-delimited words in main body |
| transcript | int | Transcription of the video asset is available, where 0 = No and 1 = Yes|
| pubdate | string | Original publication date/time in ISO-8601 |
| published | int, default 0 | Denotes if the content is publicly available.<br><br>See: [List: Publication Status](#list-published) |
| update | string | Most recent substantive update date/time in ISO-8601 |
| author | string, array | Name of the Author(s) |
| sourcetype | int | Source of the creation of the asset.<br><br>See: [List: Creation Source](#list-sourcetype) |
| provenance | int | Indicates if provenance is available for the video asset, where 0 = No and 1 = Yes. |
| provent | string | Canonical domain of the entity providing the provenance (e.g. C2PA)<br><br>Required if `provenance` = 1 |
| ext | object | Placeholder for implementer specific extensions |

## Object: Image
Information about each image asset included in the package. One object per individual image asset should be sent.

| Attribute | Type | Description |
| --- | --- | --- |
| title | string, array | Title of the Image asset included in the package |
| cattax | int, default 9 | The taxonomy in use. Refer to the [AdCOM List: Category Taxonomies](https://github.com/InteractiveAdvertisingBureau/AdCOM/blob/main/AdCOM%20v1.0%20FINAL.md#list--category-taxonomies-) for values. If no `cattax` field is supplied IAB Content Category Taxonomy 3.1 is assumed.|
| cat | int, array | Array of IAB Tech Lab content categories of the content. The taxonomy to be used is defined by the `cattax` field.|
| language | int, array | Content language using ISO-639-1-alpha-2.|
| pubdate | string | Original publication date/time in ISO-8601 |
| published | int, default 0 | Denotes if the content is publicly available.<br><br>See: [List: Publication Status](#list-published) |
| update | string | Most recent substantive update date/time in ISO-8601 |
| author | string, array | Name of the Author(s) or photographer who has photo credit |
| sourcetype | int | Source of the creation of the asset.<br><br>See: [List: Creation Source](#list-sourcetype) |
| provenance | int | Indicates if provenance is available for the image asset, where 0 = No and 1 = Yes. |
| provent | string | Canonical domain of the entity providing the provenance (e.g. C2PA)<br><br>Required if `provenance` = 1 |
| ext | object | Placeholder for implementer specific extensions |

## Object: Audio
Information about each audio asset included in the package. One object per individual audio asset should be sent.

| Attribute | Type | Description |
| --- | --- | --- |
| title | string, array | Title of the Audio asset included in the package |
| cattax | int, default 9 | The taxonomy in use. Refer to the [AdCOM List: Category Taxonomies](https://github.com/InteractiveAdvertisingBureau/AdCOM/blob/main/AdCOM%20v1.0%20FINAL.md#list--category-taxonomies-) for values. If no `cattax` field is supplied IAB Content Category Taxonomy 3.1 is assumed.|
| cat | int, array | Array of IAB Tech Lab content categories of the content. The taxonomy to be used is defined by the `cattax` field.|
| language | int, array | Content language using ISO-639-1-alpha-2.|
| dur | int, array | Duration of the Audio asset included in the package |
| wordcount | int, array | Count of words in the audio transcript |
| transcript | int | Transcription of the audio asset is available, where 0 = No and 1 = Yes |
| pubdate | string | Original publication date/time in ISO-8601 |
| published | int, default 0 | Denotes if the content is publicly available.<br><br>See: [List: Publication Status](#list-published) |
| update | string | Most recent substantive update date/time in ISO-8601 |
| author | string, array | Name of the Author(s) |
| sourcetype | int | Source of the creation of the asset.<br><br>See: [List: Creation Source](#list-sourcetype) |
| provenance | int | Indicates if provenance is available for the audio asset, where 0 = No and 1 = Yes. |
| provent | string | Canonical domain of the entity providing the provenance (e.g. C2PA)<br><br>Required if `provenance` = 1 |
| ext | object | Placeholder for implementer specific extensions |

## Object: Retrieval
Information for the AI System to access the content once a token has been issued

| Attribute | Type | Description |
| --- | --- | --- |
| auth | int | Type of authorization required to access the content package.<br><br>See: [List: Authorization Type](#list-auth)<br><br>It is strongly recommended that some authorization mechanism is utilized. |
| endpoint | string | Entry point / feed URI to access the content package after access has been granted |
| type | int, array | Available endpoint types where the content can be retrieved.<br><br>See: [List: Endpoint / Delivery Format](#list-retrieval-type) for values. |
| ext | object | Placeholder for implementer specific extensions |

## Lists

### List: AI Authentication Methods <a name="list-aiauth"></a>

| Value | Label |
|-------|-------|
| 0 | UserAgent |
| 1 | IP range |
| 2 | Token |
| 3 | WebBot Auth |
| 4 | Agent ID from TechLab Registry |
| 5 | Other |

### List: Content Scope <a name="list-contentscope"></a>

| Value | Label |
|-------|-------|
| 0 | Full corpus (e.g. website.com) |
| 1 | Section (e.g. website.com/entertainment) |
| 2 | Date range (e.g. content published after YYYYMMDD) |
| 3 | Genre (as enumerated in Content Taxonomy 3.1) |
| 4 | Topic (e.g. all available content within a given topic, likely based on keywords) |
| 5 | Curated selection (subset determined by Content Owner or Marketplace) |
| 6 | Other |

### List: Intended Use Function <a name="list-function"></a>

| Value | Label |
|-------|-------|
| 0 | all |
| 1 | ai-all |
| 2 | ai-train |
| 3 | ai-input |
| 4 | ai-index |
| 5 | search |

### List: Sub-Function <a name="list-subfn"></a>

| Value | Label |
|-------|-------|
| 0 | training |
| 1 | rag |
| 2 | grounding |
| 3 | agent-view |
| 4 | agent-actions |
| 5 | other |

### List: Content Type <a name="list-ctype"></a>

| Value | Label |
|-------|-------|
| 0 | Text |
| 1 | Video |
| 2 | Image |
| 3 | Audio |
| 4 | All |
| 5 | Other |

### List: Publication Status <a name="list-published"></a>

| Value | Label |
|-------|-------|
| 0 | Not specified (Default) |
| 1 | Previously published content |
| 2 | Unpublished / archival material (e.g. b-roll, outtakes) |


### List: Creation Source <a name="list-sourcetype"></a>

| Value | Label |
|-------|-------|
| 0 | Human |
| 1 | AI |
| 2 | Hybrid |

### List: Authorization Type <a name="list-auth"></a>

| Value | Label |
|-------|-------|
| 0 | None |
| 1 | api_key |
| 2 | oauth2 |
| 3 | SSL |
| 4 | Other |

### List: Endpoint / Delivery Format <a name="list-retrieval-type"></a>

| Value | Label |
|-------|-------|
| 0 | HTML |
| 1 | RSS |
| 2 | API |
| 3 | MCP |
| 4 | NLWeb |
| 5 | XML |
| 6 | NewsML |
| 7 | Other |

### List: Function Definitions <a name="list-function-definitions"></a>

| Value | Name | Definition |
|-------|------|------------|
| 0 | all | Any automated processing of content, inclusive of all values below. |
| 1 | ai-all | Any use by AI systems, including training, inference, indexing, retrieval, generation, grounding, or model evaluation. This term explicitly includes the ai-train, ai-input, and ai-index usage types defined below, as well as other AI-related uses not yet enumerated. |
| 2 | ai-train | Training or fine-tuning AI models. |
| 3 | ai-input | Input into AI models, including retrieval-augmented generation, grounding, or other use of the content to produce generative AI responses or search summaries. |
| 4 | ai-index | Inclusion in an AI system's internal index or retrieval database. |
| 5 | search | Building a search index and providing search results (e.g., returning hyperlinks and short excerpts from your website's contents). Search does not include providing AI-generated search summaries (see ai-input). |

### List: Sub-Function Definitions <a name="list-subfn-definitions"></a>

| Value | Name | Definition |
|-------|------|------------|
| 0 | training | AI System trains on content. |
| 1 | rag | AI System pulls in external, up-to-date, or specialized information snippets from an index of documents at query time (Retrieval), adds these snippets to the AI System's context window (Augmentation), and feeds it into the AI System to get an improved response (Generation). |
| 2 | grounding | Utilization of a non-zero snippet from a piece of content in the AI System's context window that is not done at query or crawl time. |
| 3 | agent-view | AI System is training to take some future action. (For example, a person's Agent crawling an airline's website for dates, times and cost with the expectation of future booking.) |
| 4 | agent-actions | AI System is taking an action on behalf of a person or business. For example, an Agent crawling a restaurant reservation site to make a reservation. |
| 5 | other | — |

### List: Access Format Type Definitions <a name="list-access-format-types"></a>

| Value | Format | Description |
|-------|--------|-------------|
| 0 | HTML | Content is delivered via headless content management systems (CMS) to retrieve and deliver digital content (like text, images, and data) to websites or mobile applications, often via a global Content Delivery Network (CDN). |
| 1 | RSS | XML-formatted feeds consumed via specialized readers or aggregators, which display new content in reverse chronological order as soon as it is published. |
| 2 | API | Content is delivered via headless content management systems (CMS) to retrieve and deliver digital content (like text, images, and data) to websites or mobile applications, often via a global Content Delivery Network (CDN). |
| 3 | MCP | AI System client connects to Servers (specialized connectors) that expose specific data or functionality for the AI System's use. |
| 4 | NLWeb | Similar to HTML, but content is delivered via a conversational interface allowing interactions using natural language. |
| 5 | XML | Back-end data exchange requiring interoperability between Content Owner or Marketplace and AI Systems, delivered in a structured, machine-readable format separate from its eventual presentation. |
| 6 | NewsML | Machine-readable, structured, and rich content distribution that does not require human readability. |

# Implementation Guidance

### Example 1: Unauthorized Request

The AI System identifies itself and its intent but indicates it does not yet have an issued token.

**Request:**

```json
{
  "aisystem": {
    "name": "open-learner-ai.org",
    "ua": "OpenLearnerBot/2.1",
    "id": "TL-REG-445566",
    "aisysuse": {
      "lid": "PENDING_REVIEW",
      "aiauth": 0,
      "uri": [
        "https://primary-publisher.com"
      ],
      "scope": 0,
      "function": [2],
      "subfn": [0],
      "resdis": 0
    }
  }
}
```

**Data Points from the Specification**

- `aisystem.name`: The canonical domain of the AI System requesting access.
- `aisysuse.lid`: Since a token has not been issued, the system should review terms in `package.licenseurl`.
- [`aiauth (0)`](#list-aiauth): The AI system plans to authenticate via UserAgent because it lacks a specific token or IP range permissioning.
- [`scope (0)`](#list-contentscope): Indicates a request for the full corpus (e.g., the entire website).
- [`function (2)`](#list-function): Explicitly states the function is ai-train.
- [`subfn (0)`](#list-subfn): Further specifies the sub-function as training.
- [`resdis (0)`](#list-resdis): Confirms that results will not be displayed to a human user.

**Relevant Implementation Notes**

- **Bot Blocking:** The specification notes that Content Owners are strongly encouraged to block crawling until a commercial agreement is in place.
- **Licensing:** Commercial terms must be negotiated a priori to the use of this API.
- **Clearing House:** While this API communicates intent, it does not handle the actual payment or token issuance.

In this scenario, the Content Owner responds to the unauthorized request by providing a Package object that redirects the AI System to the required licensing terms. The response specifies that no authorization is currently granted (auth 0), but points the requester to a licenseurl to negotiate terms a priori to accessing the full corpus.

**Content Owner Response: Redirect to Licensing**

```json
{
  "package": {
    "id": "PKG-REF-99-PENDING",
    "title": "Full Corpus Access - Licensing Required",
    "seller": "primary-publisher.com",
    "licenseurl": "https://primary-publisher.com/licensing/ai-terms",
    "reporturl": "https://primary-publisher.com/reporting/ai-usage",
    "citation": 1,
    "scope": {
      "scope": 0,
      "max": 1,
      "ctype": [
        4
      ]
    },
    "retrieval": {
      "auth": 0,
      "endpoint": "https://api.primary-publisher.com/v1/unauthorized",
      "type": [
        2
      ]
    }
  }
}
```

**Key Object Details from the Specification**

- `package.licenseurl`: Used to communicate where the AI System can find terms to license and access content, as token issuance and payment are out of scope for this API.
- [`package.citation (1)`](#list-citation): Indicates that citation of the Content Owner is required if the content is eventually used.
- [`scope.scope (0)`](#list-contentscope): Confirms the response acknowledges a request for the full corpus.
- [`scope.max (1)`](#list-max): Indicates there is an upper limit on how much the AI system may crawl, and the system should reference the license to determine that limit.
- [`scope.ctype (4)`](#list-ctype): Denotes that "all" content types (text, video, image, audio) are included in this corpus definition.
- [`retrieval.auth (0)`](#list-auth): Specifies that currently "none" (no authorization) is active for this requester at this endpoint.
- [`retrieval.type (2)`](#list-retrieval-type): Informs the AI system that content is typically delivered via API once authorized.


### Example 2: Authorized Request

The AI System has a pre-existing commercial agreement (indicated by a valid License ID) and specifies that it will authenticate using its IP range, a common method for server-to-server crawling permissions. The AI System global-news-aggregator.com is requesting access to a specific section of content for grounding and RAG (Retrieval-Augmented Generation) purposes.

**AI System Request (AISystem)**

```json
{
  "aisystem": {
    "name": "global-news-aggregator.com",
    "ua": "GlobalNewsBot/5.0",
    "id": "TL-REG-882211",
    "aisysuse": {
      "lid": "LIC-998-ALPHA-2026",
      "aiauth": 1,
      "uri": [
        "https://fin-times.com/markets/commodities"
      ],
      "scope": 1,
      "function": [3],
      "subfn": [1, 2],
      "resdis": 1
    }
  }
}
```

**Breakdown of Metadata**

- [`aiauth (1)`](#list-aiauth): The AI system explicitly states it plans to authenticate via IP range.
- `lid`: A unique license reference is provided, assuming the system has a license to perform the function a priori.
- [`scope (1)`](#list-contentscope): The request is limited to a specific section of the site.
- [`function (3)`](#list-function): The intent is ai-input, which includes RAG and grounding.
- [`subfn (1, 2)`](#list-subfn): Specifically defines the use cases as RAG and grounding.
- [`resdis (1)`](#list-resdis): Indicates that the results will be displayed to a human user.

**Content Owner Response (Package)**

The Content Owner acknowledges the License ID and confirms that the content is available for retrieval via a NewsML feed, which is structured for machine-readable distribution.

```json
{
  "package": {
    "id": "PKG-FIN-COMM-001",
    "title": "Commodities Market Deep-Dive",
    "seller": "fin-times.com",
    "licenseurl": "https://fin-times.com/legal/terms/LIC-998",
    "reporturl": "https://fin-times.com/reporting/ai-usage",
    "citation": 1,
    "scope": {
      "scope": 1,
      "max": 0,
      "ctype": [
        0
      ],
      "text": [
        {
          "title": "Gold Prices Surge Amid Volatility",
          "cattax": 9,
          "cat": [
            272
          ],
          "wordcount": [
            1200
          ],
          "pubdate": "2026-02-26T14:30:00Z",
          "author": [
            "Alex Sterling"
          ],
          "sourcetype": 0,
          "provenance": 1,
          "provent": "c2pa.org"
        }
      ]
    },
    "retrieval": {
      "auth": 0,
      "endpoint": "https://newsml.fin-times.com/v3/commodities",
      "type": [
        6
      ]
    }
  }
}
```

**Key Response Elements**

- [`max (0)`](#list-max): The AI system has unlimited access within this scope based on the license.
- [`citation (1)`](#list-citation): Citation is required, which aligns with the AI system's intent to display results to humans (resdis: 1).
- [`retrieval.auth (0)`](#list-auth): Listed as "none" because the authentication is handled at the network level via the IP range specified in the request, rather than an API key or OAuth.
- [`retrieval.type (6)`](#list-retrieval-type): Delivery is via NewsML, a structured format that does not require human readability for the initial transfer.
- `package.reporturl`: Provides the endpoint where the AI System should send usage reporting — `https://fin-times.com/reporting/ai-usage`.
- `cattax (9)`: Specifies IAB Tech Lab Content Taxonomy 3.1 is in use for categorization.
- `cat (52)`: Business & Finance content categorized using IAB Content Taxonomy 3.1.


### Example 3: Curated Selection of Multiple Asset Types

An AI System is looking for specific visual content to help an "Agent" perform tasks (e.g., identifying products or locations) based on a curated list provided by a Marketplace. The AI System requests a specific Curated Selection of high-quality multimedia assets. The Content Owner responds with a package containing a specific Image and Video asset, providing detailed metadata for each to ensure the AI System understands the source and length of the content.

**AI System Request (AISystem)**

```json
{
  "aisystem": {
    "name": "vision-agent.ai",
    "ua": "VisionAgentBot/1.2",
    "aisysuse": {
      "lid": "LIC-CURATED-777",
      "aiauth": 2,
      "scope": 5,
      "function": [1],
      "subfn": [4],
      "resdis": 1
    }
  }
}
```

- [`scope (5)`](#list-contentscope): Requesting a curated selection determined by the Content Owner or Marketplace.
- [`function (1)`](#list-function): Using ai-all, which covers inference and generation.
- [`subfn (4)`](#list-subfn): Specifically for agent-actions, where the AI is taking action on behalf of a user.

**Content Owner Response (Package with Image & Video)**

This response includes one image and one video asset, specifically detailing the sourcetype (Human vs. AI) and provenance.

```json
{
  "package": {
    "id": "PKG-MEDIA-PREMIUM",
    "title": "Verified Travel Selection 2026",
    "seller": "premium-travel-pics.com",
    "licenseurl": "https://premium-travel-pics.com/license",
    "reporturl": "https://premium-travel-pics.com/reporting/ai-usage",
    "citation": 1,
    "scope": {
      "scope": 5,
      "ctype": [
        1,
        2
      ],
      "image": [
        {
          "title": [
            "Grand Canyon Sunset"
          ],
          "cattax": 9,
          "cat": [
            653, 655, 660
          ],
          "pubdate": "2026-01-10T18:00:00Z",
          "published": 1,
          "author": [
            "Jane Photographer"
          ],
          "sourcetype": 0,
          "provenance": 1,
          "provent": "c2pa.org"
        }
      ],
      "video": [
        {
          "title": [
            "Grand Canyon South Rim Walkthrough"
          ],
          "cattax": 9,
          "cat": [
            653, 655, 660
          ],
          "dur": [
            45
          ],
          "clip": 1,
          "transcript": 0,
          "sourcetype": 0,
          "provenance": 1,
          "provent": "c2pa.org"
        }
      ]
    },
    "retrieval": {
      "auth": 1,
      "endpoint": "https://media-api.premium-travel.com/v1/download",
      "type": [
        2
      ]
    }
  }
}
```

**Key Object Metadata**

- [`image.sourcetype (0)`](#list-sourcetype): Indicates the image was created by a human.
- [`video.clip (1)`](#list-clip): Denotes this is a clip derived from a longer video.
- `video.dur (45)`: The duration of the video asset is 45 seconds.
- [`provenance (1)`](#list-provenance): Confirms that provenance information is available for these assets.
- `provent`: Identifies C2PA as the entity providing the provenance.
- [`retrieval.auth (1)`](#list-auth): Access requires an api_key.
- `package.reporturl`: Provides the endpoint where the AI System should send usage reporting — `https://premium-travel-pics.com/reporting/ai-usage`.
- `cattax (9)`: Specifies IAB Tech Lab Content Taxonomy 3.1 is in use for categorization.
- `cat (653, 655, 660)`: Travel content categorized using IAB Content Taxonomy 3.1.


### Example 4: Image Creation

The AI system is asking to crawl an application with a lot of images in response to a user query that would like to create an image in the style of a well known artist. The AI System uses its issued token to request images related to a specific artist's style for generative use.

**AI System Request (AISystem)**

```json
{
  "aisystem": {
    "name": "creative-gen.ai",
    "ua": "CreativeGenBot/2.0",
    "id": "TL-REG-554433",
    "aisysuse": {
      "lid": "TOKEN-STYLE-99-ALPHA",
      "aiauth": 2,
      "uri": [
        "https://digital-archive-museum.org/collections/artist-style-ref"
      ],
      "scope": 4,
      "function": [3],
      "subfn": [5],
      "resdis": 1
    }
  }
}
```

- [`aiauth (2)`](#list-aiauth): Authenticating using a Token.
- [`scope (4)`](#list-contentscope): Targeting by topic (the specific artist's name or movement).
- [`function (3)`](#list-function): Identified as ai-input, which includes use for generative AI responses.
- [`subfn (5)`](#list-subfn): Categorized as other for style-transfer.
- [`resdis (1)`](#list-resdis): Indicates the generated image will be displayed to a human.

**Content Owner Response: Curated Style Reference Package**

This response acknowledges the AI system's token-based request and delivers a curated selection of images—some with verified C2PA provenance and some without—along with instructions for retrieval.

```json
{
  "package": {
    "id": "PKG-ARTIST-COLLECTION-02",
    "title": "Impressionist Style Reference Set - Mixed Provenance",
    "seller": "digital-archive-museum.org",
    "licenseurl": "https://digital-archive-museum.org/licensing/style-transfer",
    "reporturl": "https://digital-archive-museum.org/reporting/ai-usage",
    "citation": 1,
    "scope": {
      "scope": 5,
      "max": 1,
      "ctype": [
        2
      ],
      "image": [
        {
          "title": [
            "Starry Night Interpretation"
          ],
          "cattax": 9,
          "cat": [
            43
          ],
          "pubdate": "2026-01-05T12:00:00Z",
          "published": 1,
          "author": [
            "Original Artist Name"
          ],
          "sourcetype": 0,
          "provenance": 1,
          "provent": "c2pa.org"
        },
        {
          "title": [
            "Abstract Study in Blue"
          ],
          "cattax": 9,
          "cat": [
            43
          ],
          "pubdate": "2026-01-10T14:20:00Z",
          "published": 1,
          "author": [
            "Unknown Artist"
          ],
          "sourcetype": 0,
          "provenance": 0
        }
      ]
    },
    "retrieval": {
      "auth": 2,
      "endpoint": "https://api.digital-archive-museum.org/v1/assets",
      "type": [
        2
      ]
    }
  }
}
```

**Metadata Breakdown**

- [`package.citation (1)`](#list-citation): Since the AI system indicated it would display results to a human user (resdis: 1), the content owner here explicitly requires citation.
- [`scope.scope (5)`](#list-contentscope): The package is defined as a curated selection.
- [`scope.max (1)`](#list-max): Indicates an upper limit on the crawl; the AI system should reference the license for the specific cap.
- **Image 1 (Provenance Available):** Marked as human-created (`sourcetype: 0`) with `provenance: 1` and a `provent` (Provenance Entity) of `c2pa.org`.
- **Image 2 (No Provenance):** Also human-created, but marked with `provenance: 0`. Per the spec, the `provent` field is omitted because it is only required when `provenance` is 1.
- [`retrieval.auth (2)`](#list-auth): Specifies that OAuth2 is required to access the content once a token has been issued.
- [`retrieval.type (2)`](#list-retrieval-type): Informs the AI system that the content is accessible via an API.
- `package.reporturl`: Provides the endpoint where the AI System should send usage reporting — `https://digital-archive-museum.org/reporting/ai-usage`.
- `cattax (9)`: Specifies IAB Tech Lab Content Taxonomy 3.1 is in use for categorization.
- `cat (43)`: Art and Photography content categorized using IAB Content Taxonomy 3.1.


### Example 5: Podcast Full Feed Authorized

The AI System would like to crawl an entire news podcast feed to train and ground its model.

**AI System Request (AISystem)**

```json
{
  "aisystem": {
    "name": "audio-intel.ai",
    "ua": "AudioIntelBot/3.0",
    "id": "TL-REG-223344",
    "aisysuse": {
      "lid": "LIC-POD-PRO-2026",
      "aiauth": 2,
      "uri": [
        "https://news-podcasts.com/daily-brief"
      ],
      "scope": 0,
      "function": [2, 3],
      "subfn": [0, 2],
      "resdis": 0
    }
  }
}
```

- [`scope (0)`](#list-contentscope): Requests the full corpus of the feed.
- [`function (2, 3)`](#list-function): Declares use for ai-train and ai-input.
- [`subfn (0, 2)`](#list-subfn): Specifically targets training and grounding.
- [`resdis (0)`](#list-resdis): Indicates results will not be displayed to a human user.

**Content Owner Response (Package)**

The Content Owner provides the package details, including a specific Audio object that highlights the availability of a transcript—crucial for grounding tasks.

```json
{
  "package": {
    "id": "PKG-DAILY-BRIEF-FULL",
    "title": "Daily Brief Podcast Archive",
    "seller": "news-podcasts.com",
    "licenseurl": "https://news-podcasts.com/licensing",
    "reporturl": "https://news-podcasts.com/reporting/ai-usage",
    "citation": 1,
    "scope": {
      "scope": 0,
      "max": 0,
      "ctype": [
        3
      ],
      "audio": [
        {
          "title": [
            "Global Markets Update - Feb 27"
          ],
          "cattax": 9,
          "cat": [
            392
          ],
          "dur": [
            1800
          ],
          "wordcount": [
            4500
          ],
          "transcript": 1,
          "pubdate": "2026-02-27T06:00:00Z",
          "published": 1,
          "author": [
            "Host Name",
            "Guest Expert"
          ],
          "sourcetype": 0,
          "provenance": 0
        }
      ]
    },
    "retrieval": {
      "auth": 2,
      "endpoint": "https://api.news-podcasts.com/v1/rss",
      "type": [
        1
      ]
    }
  }
}
```

**Key Metadata Elements**

- `audio.dur (1800)`: Indicates the duration is 1800 seconds (30 minutes).
- [`audio.transcript (1)`](#list-transcript): Per the specification table for Audio, 1 = Yes, a transcription is available.
- `audio.wordcount (4500)`: Provides the count of words in the audio transcript.
- [`audio.sourcetype (0)`](#list-sourcetype): Confirms the audio is human-created.
- [`retrieval.type (1)`](#list-retrieval-type): The content is delivered via an RSS feed.
- [`retrieval.auth (2)`](#list-auth): Requires OAuth2 for access.
- `package.reporturl`: Provides the endpoint where the AI System should send usage reporting — `https://news-podcasts.com/reporting/ai-usage`.
- `cattax (9)`: Specifies IAB Tech Lab Content Taxonomy 3.1 is in use for categorization.
- `cat (392)`: Personal Finance content categorized using IAB Content Taxonomy 3.1.


### Example 6: Podcast Published After a Date

The AI System would like to crawl just episodes of the news podcast feed published after January 1, 2026 to answer a user query about a specific news headline. The AI system requests content published after the specified date for RAG (Retrieval-Augmented Generation).

**AI System Request (AISystem)**

```json
{
  "aisystem": {
    "name": "current-events-agent.ai",
    "ua": "CurrentEventsBot/1.5",
    "id": "TL-REG-112233",
    "aisysuse": {
      "lid": "LIC-RAG-2026-X",
      "aiauth": 2,
      "uri": [
        "https://news-podcasts.com/daily-brief"
      ],
      "scope": 2,
      "function": [3],
      "subfn": [1],
      "resdis": 1
    }
  }
}
```

- [`scope (2)`](#list-contentscope): Specific to a date range filter (e.g., content published after YYYYMMDD).
- [`function (3)`](#list-function): Identified as ai-input, which includes RAG and grounding.
- [`subfn (1)`](#list-subfn): Specifically defines the use case as RAG.
- [`resdis (1)`](#list-resdis): Indicates that results will be displayed to a human user.

**Content Owner Response (Package)**

The Content Owner responds with a package containing assets published within that window. For RAG purposes, providing the transcript (1) is essential.

```json
{
  "package": {
    "id": "PKG-POD-2026-Q1",
    "title": "Daily Brief - 2026 Post-Jan Archive",
    "seller": "news-podcasts.com",
    "licenseurl": "https://news-podcasts.com/licensing/rag-terms",
    "reporturl": "https://news-podcasts.com/reporting/ai-usage",
    "citation": 1,
    "scope": {
      "scope": 2,
      "max": 0,
      "ctype": [
        3
      ],
      "audio": [
        {
          "title": [
            "Economic Outlook: February 2026"
          ],
          "cattax": 9,
          "cat": [
            392
          ],
          "dur": [
            1500
          ],
          "wordcount": [
            3800
          ],
          "transcript": 1,
          "pubdate": "2026-02-15T08:00:00Z",
          "published": 1,
          "author": [
            "Casey Jordan"
          ],
          "sourcetype": 0,
          "provenance": 0
        }
      ]
    },
    "retrieval": {
      "auth": 2,
      "endpoint": "https://api.news-podcasts.com/v1/feed",
      "type": [
        2
      ]
    }
  }
}
```

**Key Object Details**

- [`package.citation (1)`](#list-citation): Required, as the AI system intends to display the result to a human.
- [`scope.scope (2)`](#list-contentscope): Acknowledges the date range scope.
- [`audio.transcript (1)`](#list-transcript): Per the Audio object specification, 1 = Yes, a transcript is available.
- `audio.pubdate`: "2026-02-15T08:00:00Z" confirms the asset meets the "after January 1st" requirement.
- [`audio.provenance (0)`](#list-provenance): Indicates no provenance is available for this specific audio asset.
- `package.reporturl`: Provides the endpoint where the AI System should send usage reporting — `https://news-podcasts.com/reporting/ai-usage`.
- `cattax (9)`: Specifies IAB Tech Lab Content Taxonomy 3.1 is in use for categorization.
- `cat (392)`: Personal Finance content categorized using IAB Content Taxonomy 3.0.


### Example 7: Video Creation

The AI System is responding to a user query to create a video in Steampunk style. The AI system requests content filtered by topic (scope 4) to support a generative user query.

**AI System Request (AISystem)**

```json
{
  "aisystem": {
    "name": "creative-gen.ai",
    "ua": "CreativeGenBot/2.0",
    "id": "TL-REG-554433",
    "aisysuse": {
      "lid": "LIC-TOPIC-SP-2026",
      "aiauth": 2,
      "uri": [
        "https://video-archive.com/steampunk-search"
      ],
      "scope": 4,
      "function": [3],
      "subfn": [1, 2],
      "resdis": 1
    }
  }
}
```

- [`scope (4)`](#list-contentscope): Filters the request specifically to the "steampunk" topic.
- [`function (3)`](#list-function): Identifies the use as ai-input for generative responses.
- [`subfn (1, 2)`](#list-subfn): Specifies the sub-functions of RAG and grounding.
- [`resdis (1)`](#list-resdis): Indicates the final output will be seen by a human.

**Content Owner Response (Package)**

The Content Owner provides a curated collection of videos with varying provenance and creation sources.

```json
{
  "package": {
    "id": "PKG-STEAMPUNK-001",
    "title": "Steampunk Visual Archives",
    "seller": "video-archive.com",
    "licenseurl": "https://video-archive.com/legal/ai-terms",
    "reporturl": "https://video-archive.com/reporting/ai-usage",
    "citation": 1,
    "scope": {
      "scope": 4,
      "max": 1,
      "ctype": [
        1
      ],
      "video": [
        {
          "title": [
            "History of Steampunk Aesthetics"
          ],
          "cattax": 9,
          "cat": [
            205
          ],
          "dur": [
            600
          ],
          "clip": 0,
          "transcript": 1,
          "pubdate": "2026-01-20T10:00:00Z",
          "author": [
            "Gearheart Media"
          ],
          "sourcetype": 0,
          "provenance": 1,
          "provent": "c2pa.org"
        },
        {
          "title": [
            "AI-Generated Steampunk Landscape"
          ],
          "cattax": 9,
          "cat": [
            205
          ],
          "dur": [
            60
          ],
          "clip": 1,
          "transcript": 0,
          "pubdate": "2026-02-15T14:30:00Z",
          "author": [
            "StyleBot-V1"
          ],
          "sourcetype": 1,
          "provenance": 0
        }
      ]
    },
    "retrieval": {
      "auth": 2,
      "endpoint": "https://api.video-archive.com/v1/media",
      "type": [
        2
      ]
    }
  }
}
```

**Metadata Highlights**

- [`citation (1)`](#list-citation): Requires the AI System to cite the Content Owner.
- **Video 1 (Human + Provenance):**
  - `sourcetype (0)`: Indicates the video was created by a human.
  - [`provenance (1)`](#list-provenance): Confirms that technical provenance is available via c2pa.org.
  - `transcript (1)`: Confirms a transcription is available for this asset.
- **Video 2 (AI + No Provenance):**
  - `sourcetype (1)`: Explicitly identifies the asset as AI-generated.
  - [`provenance (0)`](#list-provenance): Indicates no technical provenance is available for this specific file.
  - `clip (1)`: Denotes that this asset is a clip rather than full-length content.
- [`retrieval.type (2)`](#list-retrieval-type): Informs the AI system the content is delivered via API.
- `package.reporturl`: Provides the endpoint where the AI System should send usage reporting — `https://video-archive.com/reporting/ai-usage`.
- `cattax (9)`: Specifies IAB Tech Lab Content Taxonomy 3.1 is in use for categorization.
- `cat (205)`: Digital Arts content categorized using IAB Content Taxonomy 3.1.


### Example 8: Agent Actions

This scenario explores the Agent-View and Agent-Actions sub-functions. This is designed for AI "Agents" that don't just consume content for knowledge, but crawl data to perform tasks like booking travel or making reservations. The AI System, acting as a personal travel agent, requests access to a restaurant's reservation site to check for availability and perform a booking on behalf of a user.

**AI System Request (AISystem)**

```json
{
  "aisystem": {
    "name": "concierge-bot.ai",
    "ua": "ConciergeBot/1.0",
    "id": "TL-REG-889900",
    "aisysuse": {
      "lid": "LIC-AGENT-ACT-2026",
      "aiauth": 2,
      "uri": [
        "https://bistro-reserve.com/api/availability"
      ],
      "scope": 5,
      "function": [1],
      "subfn": [3, 4],
      "resdis": 1
    }
  }
}
```

- [`scope (5)`](#list-contentscope): Requesting a curated selection (the specific reservation data).
- [`function (1)`](#list-function): Using ai-all, which includes model evaluation and retrieval.
- [`subfn (3, 4)`](#list-subfn): Combining agent-view (gathering data for future action) and agent-actions (taking the action).
- [`resdis (1)`](#list-resdis): The outcome of the action will be shown to a human user.

**Content Owner Response (Package)**

The Marketplace or Content Owner provides the retrieval endpoint for the Agent, typically utilizing the Model Context Protocol (MCP) or an API for structured interaction.

```json
{
  "package": {
    "id": "PKG-RESERVE-TOOL-01",
    "title": "BistroReserve Agent Interface",
    "seller": "bistro-reserve.com",
    "licenseurl": "https://bistro-reserve.com/legal/agent-terms",
    "reporturl": "https://bistro-reserve.com/reporting/ai-usage",
    "citation": 0,
    "scope": {
      "scope": 5,
      "max": 1,
      "ctype": [
        0
      ],
      "text": [
        {
          "title": "Real-time Table Availability",
          "cattax": 9,
          "cat": [
            653
          ],
          "published": 1,
          "sourcetype": 0,
          "provenance": 0
        }
      ]
    },
    "retrieval": {
      "auth": 2,
      "endpoint": "https://mcp.bistro-reserve.com/v1/connect",
      "type": [
        3
      ]
    }
  }
}
```

**Key Metadata Elements**

- [`scope.scope (5)`](#list-contentscope): Confirms the response is for a curated selection of reservation data.
- [`text.sourcetype (0)`](#list-sourcetype): Confirms the data provided (availability, descriptions) is human-created.
- [`retrieval.type (3)`](#list-retrieval-type): Specifies MCP (Model Context Protocol), where the AI System client connects to specialized connectors to expose functionality.
- [`retrieval.auth (2)`](#list-auth): Requires OAuth2 for the Agent to securely interact with the booking system.
- [`package.citation (0)`](#list-citation): Citation is not required for this functional data exchange.
- `package.reporturl`: Provides the endpoint where the AI System should send usage reporting — `https://bistro-reserve.com/reporting/ai-usage`.
- `cattax (9)`: Specifies Tech Lab IAB Content Taxonomy 3.0 is in use for categorization.
- `cat (653)`: Travel content categorized using IAB Tech Lab Content Taxonomy 3.1.

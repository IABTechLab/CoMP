
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
- [Implementation Guidance](#implementation-guidance)
  - [Function Definitions](#function-definitions)
  - [Sub-Function Definitions](#sub-function-definitions)
  - [Access Format Type Definitions](#access-format-type-definitions)
  - [JSON Examples](#json-examples)
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
- <b>Supply Discovery</b> - It is assumed that AI Systems have an understanding of the content they’d like to license. Where discovery is required, AI Systems should work directly with Marketplaces and Content Owners.
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
| lid | string, required | License ID - Unique license reference for the content package the AI System would like to crawl<br><br>Assumes the AI system has a license to perform the function a priori . If the AI System has not been issued a token, it should review terms listed in `package.licenseurl`. |
| aiauth | int, required | Information how the ai system plans to authenticate itself based on how access has been permissioned by the content owner where<br><br>0 = UserAgent<br>1 = IP range<br>2 = Token<br>3 = WebBot Auth <br>4 = Agent ID from TechLab Registry <br>5 = Other |
| uri | string, array | Uniform Resource Identifier (URI) that the AI System is requesting to crawl. |
| scope | int | General information about the content the AI System is asking for<br><br>0 = full corpus (e.g. website.com)<br>1 = section (e.g. website.com/entertainment)<br>2 = date range (e.g. content published after YYYYMMDD)<br>3 = genre (e.g. all available content that falls within a given genre as enumerated in [Content Taxonomy 3.1](https://github.com/InteractiveAdvertisingBureau/Taxonomies/blob/main/Content%20Taxonomies/Content%20Taxonomy%203.1.tsv))<br>4 = topic (e.g. all available content that falls within a given topic, likely based on keywords)<br>5 = curated selection (e.g. some subset of content determined by the Content Owner or a Marketplace to be the most relevant to the AI System)<br>6 = other<br><br>If blank, the scope will be determined by the information in [Object: Package](#object-scope). |
| function | int, array | Function(s) that the AI System is stating it will use the content in this package for where<br><br>0 = all <br>1 = ai-all <br>2 = ai-train<br>3 = ai-input<br>4 = ai-index<br>5 = search<br><br>If function is not known a priori, implementers should use all<br><br>See [Function Definitions section of Implementation Guidance](#function-definitions) for additional detail |
| subfn | int, array | Additional information about the function(s) the AI System is stating it will use the content where<br><br>0 = training<br>1 = rag<br>2 = grounding<br>3 = agent-view<br>4 = agent-actions<br>5 = other<br><br>See [Sub-Function Definitions section of Implementation Guidance](#sub-function-definitions) for additional Detail |
| resdis | int | Indicates in the results will be displayed to a human user where <br><br>0 = no<br>1 = yes |
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
| citation | int | Indicates if citation of the Content Owner is required to use this package. <br><br>0 = no<br>1 = yes<br><br>Strongly recommended if `aisystem.resdis` has a value of 0. |
| scope | object | Information about the scope of content included in the package. <br><br>See [Object: Scope](#object-scope) for additional detail |
| retrieval | object | Information about how the AI System may access the Content once a token has been issued<br><br>See [Object: Retrieval](#object-retrieval) for additional detail |
| ext | object | Placeholder for implementer specific extensions |

## Object: Scope
Information about the scope of the elements included in the package

| Attribute | Type | Description |
| --- | --- | --- |
| scope | int | Information about how much of the Content Owners corpus is available in this package<br><br>0 = full corpus<br>1 = section<br>2 = date range<br>3 = genre<br>4 = topic<br>5 = curated selection<br>6 = other<br> |
| max |  | Indicates if there is an upper limit on how much the AI system may crawl where <br><br>0 = no, the AI system has unlimited access <br>1 = yes, there is a maximum<br><br>Where this value is 1, AI Systems should reference the license to determine what the upper limit is and what it applies to. |
| ctype | int, array | Type of content available in this package where<br><br>0 = text<br>1 = video<br>2 = image<br>3 = audio<br>4 = all<br>5 = other <br> |
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
| wordcount | int, array | Count of whitespace-delimited words in main body |
| pubdate | string | Original publication date/time in ISO-8601 |
| published | int, default 1 | Denotes if the content is publicly available where<br><br>0 = not specified (Default)<br>1 = previously published content<br>2 = unpublished / archival material (e.g., b-roll, outtakes) |
| update | string | Most recent substantive update date/time in ISO-8601 |
| author | string, array | Name of the Author(s) |
| sourcetype | int | Source of the creation of the asset<br><br>0 = human<br>1 = ai<br>2 = hybrid |
| provenance | int | Indicates if provenance is available for the text asset where<br><br>0 = no<br>1 = yes |
| provent | string | Canonical domain of the entity providing the provenance (e.g. C2PA)<br><br>Required if `provenance`= 1 |
| ext | object | Placeholder for implementer specific extensions |

## Object: Video
Information about each video asset included in the package. One object per individual video asset should be sent.

| Attribute | Type | Description |
| --- | --- | --- |
| title | string, array | Title of the Video asset included in the package |
| dur | int, array | Duration of the Video asset included in the package |
| clip | int | Denotes if the full length asset, or a clip derived from a longer video where<br><br>0 = full length<br>1 = clip |
| wordcount | int, array | Count of whitespace-delimited words in main body |
| transcript | int | Transcription of the video asset is available where <br>0 = no<br>1 = yes |
| pubdate | string | Original publication date/time in ISO-8601 |
| published | int, default 0 | Denotes if the content is publicly available where<br><br>0 = not specified (Default)<br>1 = previously published content<br>2 = unpublished / archival material (e.g., b-roll, outtakes) |
| update | string | Most recent substantive update date/time in ISO-8601 |
| author | string, array | Name of the Author(s) |
| sourcetype | int | Source of the creation of the asset<br><br>0 = human<br>1 = ai<br>2 = hybrid |
| provenance | int | Indicates if provenance is available for the video asset where<br><br>0 = no<br>1 = yes |
| provent | string | Canonical domain of the entity providing the provenance (e.g. C2PA)<br><br>Required if `provenance`=1 |
| ext | object | Placeholder for implementer specific extensions |

## Object: Image
Information about each image asset included in the package. One object per individual image asset should be sent.

| Attribute | Type | Description |
| --- | --- | --- |
| title | string, array | Title of the Image asset included in the package |
| pubdate | string | Original publication date/time in ISO-8601 |
| published | int, default 0 | Denotes if the content is publicly available where<br><br>0 = not specified (Default)<br>1 = previously published content<br>2 = unpublished / archival material (e.g., b-roll, outtakes) |
| update | string | Most recent substantive update date/time in ISO-8601 |
| author | string, array | Name of the Author(s) or photographer who has photo credit |
| sourcetype | int | Source of the creation of the asset<br><br>0 = human<br>1 = ai<br>2 = hybrid |
| provenance | int | Indicates if provenance is available for the image asset where<br><br>0 = no<br>1 = yes |
| provent | string | Canonical domain of the entity providing the provenance (e.g. C2PA)<br><br>Required if `provenance`=1 |
| ext | object | Placeholder for implementer specific extensions |

## Object: Audio
Information about each audio asset included in the package. One object per individual audio asset should be sent.

| Attribute | Type | Description |
| --- | --- | --- |
| title | string, array | Title of the Audio asset included in the package |
| dur | int, array | Duration of the Audio asset included in the package |
| wordcount | int, array | Count of words in the audio transcript |
| transcript | int | Transcription of the audio asset is available where 0 = no, 1 = yes |
| pubdate | string | Original publication date/time in ISO-8601 |
| published | int, default 0 | Denotes if the content is publicly available where<br><br>0 = not specified (Default)<br>1 = previously published content<br>2 = unpublished / archival material (e.g., b-roll, outtakes) |
| update | string | Most recent substantive update date/time in ISO-8601 |
| author | string, array | Name of the Author(s) |
| sourcetype | int | Source of the creation of the asset<br><br>0 = human<br>1 = ai<br>2 = hybrid |
| provenance | int | Indicates if provenance is available for the audio asset where<br><br>0 = no<br>1 = yes |
| provent | string | Canonical domain of the entity providing the provenance (e.g. C2PA)<br><br>Required if `provenance`= 1 |
| ext | object | Placeholder for implementer specific extensions |

## Object: Retrieval
Information for the AI System to access the content once a token has been issued

| Attribute | Type | Description |
| --- | --- | --- |
| auth | int | Type of authorization required to access the content package where<br><br>0 = none<br>1 = api_key<br>2 = oauth2<br>3 = SSL<br><br>It is strongly recommended that some authorization mechanism is utilized. |
| endpoint | string | Entry point / feed URI to access the content package after access has been granted |
| type | Int, array | Available endpoint types where the content can be retrieved where<br><br>0 = HTML <br>1 = RSS<br>2 = API<br>3 = MCP<br>4 = NLWeb<br>5 = XML<br>6 = NewsML<br>7 = Other<br><br>See [Implementation Guidance](#implementation-guidance) for additional detail |
| ext | object | Placeholder for implementer specific extensions |

# Implementation Guidance

## Function Definitions

| Name | Definition |
| --- | --- |
| all | Any automated processing of content, inclusive of all values below. |
| ai-all | Any use by AI systems, including training, inference, indexing, retrieval, generation, grounding, or model evaluation. This term explicitly includes the ai-train, ai-input, and ai-index usage types defined below, as well as other AI-related uses not yet enumerated. |
| ai-train | Training or fine-tuning AI models. |
| ai-input | Input into AI models, including retrieval-augmented generation, grounding, or other use of the content to produce generative AI responses or search summaries. |
| ai-index | Inclusion in an AI system’s internal index or retrieval database. |
| search | Building a search index and providing search results (e.g., returning hyperlinks and short excerpts from your website's contents). Search does not include providing AI-generated search summaries (see ai-input). |

## Sub-Function Definitions

| Name | Definition |
| --- | --- |
| training | AI System trains on content |
| rag | AI System pulls in external, up-to-date, or specialized information snippets from an index of documents at query time (Retrieval), adds these snippets to the AI System’s context window (Augmentation), and feeds it into the AI System to get an improved response (Generation) |
| grounding | Utilization of a non-zero snippet from a piece of content in the AI Systems context window that is not done at query or crawl time. |
| agent-view | AI System is training to take some future action. (For example, a person's Agent crawling an airlines website for dates, times and cost with the expectation of future booking.) |
| agent-actions | AI System is taking an action on behalf of a person or business. For example, an Agent crawling a restaurant reservation site to make a reservation. |
| other |  |

## Access Format Type Definitions

| HTML | Content is delivered via headless content management systems (CMS) to retrieve and deliver digital content (like text, images, and data) to websites or mobile applications, often via a global Content Delivery Network (CDN). |
| --- | --- |
| RSS | XML-formatted feeds consumed via specialized readers or aggregators, which display new content in reverse chronological order as soon as it is published |
| API | Content is delivered via headless content management systems (CMS) to retrieve and deliver digital content (like text, images, and data) to websites or mobile applications, often via a global Content Delivery Network (CDN). |
| MCP | AI System client connects to Servers (specialized connectors) that expose specific data or functionality for the AI System’s use. |
| NLWeb | Similar to HTML, but content is delivered via a conversational interface allowing interactions using natural language. |
| XML | Back-end data exchange requiring interoperability between Content Owner or Marketplace and AI Systems, delivered in a structured, machine-readable format separate from its eventual presentation |
| NewsML | Machine-readable, structured, and rich content distribution that does not require human readability |

# JSON Examples

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
- `aiauth (0)`: The AI system plans to authenticate via UserAgent because it lacks a specific token or IP range permissioning.
- `scope (0)`: Indicates a request for the full corpus (e.g., the entire website).
- `function (2)`: Explicitly states the function is ai-train.
- `subfn (0)`: Further specifies the sub-function as training.
- `resdis (0)`: Confirms that results will not be displayed to a human user.

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
    "citation": 1,
    "scope": {
      "scope": 0,
      "max": 1,
      "ctype": [4]
    },
    "retrieval": {
      "auth": 0,
      "endpoint": "https://api.primary-publisher.com/v1/unauthorized",
      "type": [2]
    }
  }
}
```

**Key Object Details from the Specification**

- `package.licenseurl`: Used to communicate where the AI System can find terms to license and access content, as token issuance and payment are out of scope for this API.
- `package.citation (1)`: Indicates that citation of the Content Owner is required if the content is eventually used.
- `scope.scope (0)`: Confirms the response acknowledges a request for the full corpus.
- `scope.max (1)`: Indicates there is an upper limit on how much the AI system may crawl, and the system should reference the license to determine that limit.
- `scope.ctype (4)`: Denotes that "all" content types (text, video, image, audio) are included in this corpus definition.
- `retrieval.auth (0)`: Specifies that currently "none" (no authorization) is active for this requester at this endpoint.
- `retrieval.type (2)`: Informs the AI system that content is typically delivered via API once authorized.

---

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

- `aiauth (1)`: The AI system explicitly states it plans to authenticate via IP range.
- `lid`: A unique license reference is provided, assuming the system has a license to perform the function a priori.
- `scope (1)`: The request is limited to a specific section of the site.
- `function (3)`: The intent is ai-input, which includes RAG and grounding.
- `subfn (1, 2)`: Specifically defines the use cases as RAG and grounding.
- `resdis (1)`: Indicates that the results will be displayed to a human user.

**Content Owner Response (Package)**

The Content Owner acknowledges the License ID and confirms that the content is available for retrieval via a NewsML feed, which is structured for machine-readable distribution.

```json
{
  "package": {
    "id": "PKG-FIN-COMM-001",
    "title": "Commodities Market Deep-Dive",
    "seller": "fin-times.com",
    "licenseurl": "https://fin-times.com/legal/terms/LIC-998",
    "citation": 1,
    "scope": {
      "scope": 1,
      "max": 0,
      "ctype": [0],
      "text": [
        {
          "title": "Gold Prices Surge Amid Volatility",
          "wordcount": [1200],
          "pubdate": "2026-02-26T14:30:00Z",
          "author": ["Alex Sterling"],
          "sourcetype": 0,
          "provenance": 1,
          "provent": "c2pa.org"
        }
      ]
    },
    "retrieval": {
      "auth": 0,
      "endpoint": "https://newsml.fin-times.com/v3/commodities",
      "type": [6]
    }
  }
}
```

**Key Response Elements**

- `max (0)`: The AI system has unlimited access within this scope based on the license.
- `citation (1)`: Citation is required, which aligns with the AI system's intent to display results to humans (resdis: 1).
- `retrieval.auth (0)`: Listed as "none" because the authentication is handled at the network level via the IP range specified in the request, rather than an API key or OAuth.
- `retrieval.type (6)`: Delivery is via NewsML, a structured format that does not require human readability for the initial transfer.

---

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

- `scope (5)`: Requesting a curated selection determined by the Content Owner or Marketplace.
- `function (1)`: Using ai-all, which covers inference and generation.
- `subfn (4)`: Specifically for agent-actions, where the AI is taking action on behalf of a user.

**Content Owner Response (Package with Image & Video)**

This response includes one image and one video asset, specifically detailing the sourcetype (Human vs. AI) and provenance.

```json
{
  "package": {
    "id": "PKG-MEDIA-PREMIUM",
    "title": "Verified Travel Selection 2026",
    "seller": "premium-travel-pics.com",
    "licenseurl": "https://premium-travel-pics.com/license",
    "citation": 1,
    "scope": {
      "scope": 5,
      "ctype": [1, 2],
      "image": [
        {
          "title": ["Grand Canyon Sunset"],
          "pubdate": "2026-01-10T18:00:00Z",
          "published": 1,
          "author": ["Jane Photographer"],
          "sourcetype": 0,
          "provenance": 1,
          "provent": "c2pa.org"
        }
      ],
      "video": [
        {
          "title": ["Grand Canyon South Rim Walkthrough"],
          "dur": [45],
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
      "type": [2]
    }
  }
}
```

**Key Object Metadata**

- `image.sourcetype (0)`: Indicates the image was created by a human.
- `video.clip (1)`: Denotes this is a clip derived from a longer video.
- `video.dur (45)`: The duration of the video asset is 45 seconds.
- `provenance (1)`: Confirms that provenance information is available for these assets.
- `provent`: Identifies C2PA as the entity providing the provenance.
- `retrieval.auth (1)`: Access requires an api_key.

---

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

- `aiauth (2)`: Authenticating using a Token.
- `scope (4)`: Targeting by topic (the specific artist's name or movement).
- `function (3)`: Identified as ai-input, which includes use for generative AI responses.
- `subfn (5)`: Categorized as other for style-transfer.
- `resdis (1)`: Indicates the generated image will be displayed to a human.

**Content Owner Response: Curated Style Reference Package**

This response acknowledges the AI system's token-based request and delivers a curated selection of images—some with verified C2PA provenance and some without—along with instructions for retrieval.

```json
{
  "package": {
    "id": "PKG-ARTIST-COLLECTION-02",
    "title": "Impressionist Style Reference Set - Mixed Provenance",
    "seller": "digital-archive-museum.org",
    "licenseurl": "https://digital-archive-museum.org/licensing/style-transfer",
    "citation": 1,
    "scope": {
      "scope": 5,
      "max": 1,
      "ctype": [2],
      "image": [
        {
          "title": ["Starry Night Interpretation"],
          "pubdate": "2026-01-05T12:00:00Z",
          "published": 1,
          "author": ["Original Artist Name"],
          "sourcetype": 0,
          "provenance": 1,
          "provent": "c2pa.org"
        },
        {
          "title": ["Abstract Study in Blue"],
          "pubdate": "2026-01-10T14:20:00Z",
          "published": 1,
          "author": ["Unknown Artist"],
          "sourcetype": 0,
          "provenance": 0
        }
      ]
    },
    "retrieval": {
      "auth": 2,
      "endpoint": "https://api.digital-archive-museum.org/v1/assets",
      "type": [2]
    }
  }
}
```

**Metadata Breakdown**

- `package.citation (1)`: Since the AI system indicated it would display results to a human user (resdis: 1), the content owner here explicitly requires citation.
- `scope.scope (5)`: The package is defined as a curated selection.
- `scope.max (1)`: Indicates an upper limit on the crawl; the AI system should reference the license for the specific cap.
- **Image 1 (Provenance Available):** Marked as human-created (`sourcetype: 0`) with `provenance: 1` and a `provent` (Provenance Entity) of `c2pa.org`.
- **Image 2 (No Provenance):** Also human-created, but marked with `provenance: 0`. Per the spec, the `provent` field is omitted because it is only required when `provenance` is 1.
- `retrieval.auth (2)`: Specifies that OAuth2 is required to access the content once a token has been issued.
- `retrieval.type (2)`: Informs the AI system that the content is accessible via an API.

---

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

- `scope (0)`: Requests the full corpus of the feed.
- `function (2, 3)`: Declares use for ai-train and ai-input.
- `subfn (0, 2)`: Specifically targets training and grounding.
- `resdis (0)`: Indicates results will not be displayed to a human user.

**Content Owner Response (Package)**

The Content Owner provides the package details, including a specific Audio object that highlights the availability of a transcript—crucial for grounding tasks.

```json
{
  "package": {
    "id": "PKG-DAILY-BRIEF-FULL",
    "title": "Daily Brief Podcast Archive",
    "seller": "news-podcasts.com",
    "licenseurl": "https://news-podcasts.com/licensing",
    "citation": 1,
    "scope": {
      "scope": 0,
      "max": 0,
      "ctype": [3],
      "audio": [
        {
          "title": ["Global Markets Update - Feb 27"],
          "dur": [1800],
          "wordcount": [4500],
          "transcript": 1,
          "pubdate": "2026-02-27T06:00:00Z",
          "published": 1,
          "author": ["Host Name", "Guest Expert"],
          "sourcetype": 0,
          "provenance": 0
        }
      ]
    },
    "retrieval": {
      "auth": 2,
      "endpoint": "https://api.news-podcasts.com/v1/rss",
      "type": [1]
    }
  }
}
```

**Key Metadata Elements**

- `audio.dur (1800)`: Indicates the duration is 1800 seconds (30 minutes).
- `audio.transcript (1)`: Per the specification table for Audio, 1 = Yes, a transcription is available.
- `audio.wordcount (4500)`: Provides the count of words in the audio transcript.
- `audio.sourcetype (0)`: Confirms the audio is human-created.
- `retrieval.type (1)`: The content is delivered via an RSS feed.
- `retrieval.auth (2)`: Requires OAuth2 for access.

---

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

- `scope (2)`: Specific to a date range filter (e.g., content published after YYYYMMDD).
- `function (3)`: Identified as ai-input, which includes RAG and grounding.
- `subfn (1)`: Specifically defines the use case as RAG.
- `resdis (1)`: Indicates that results will be displayed to a human user.

**Content Owner Response (Package)**

The Content Owner responds with a package containing assets published within that window. For RAG purposes, providing the transcript (1) is essential.

```json
{
  "package": {
    "id": "PKG-POD-2026-Q1",
    "title": "Daily Brief - 2026 Post-Jan Archive",
    "seller": "news-podcasts.com",
    "licenseurl": "https://news-podcasts.com/licensing/rag-terms",
    "citation": 1,
    "scope": {
      "scope": 2,
      "max": 0,
      "ctype": [3],
      "audio": [
        {
          "title": ["Economic Outlook: February 2026"],
          "dur": [1500],
          "wordcount": [3800],
          "transcript": 1,
          "pubdate": "2026-02-15T08:00:00Z",
          "published": 1,
          "author": ["Casey Jordan"],
          "sourcetype": 0,
          "provenance": 0
        }
      ]
    },
    "retrieval": {
      "auth": 2,
      "endpoint": "https://api.news-podcasts.com/v1/feed",
      "type": [2]
    }
  }
}
```

**Key Object Details**

- `package.citation (1)`: Required, as the AI system intends to display the result to a human.
- `scope.scope (2)`: Acknowledges the date range scope.
- `audio.transcript (1)`: Per the Audio object specification, 1 = Yes, a transcript is available.
- `audio.pubdate`: "2026-02-15T08:00:00Z" confirms the asset meets the "after January 1st" requirement.
- `audio.provenance (0)`: Indicates no provenance is available for this specific audio asset.

---

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

- `scope (4)`: Filters the request specifically to the "steampunk" topic.
- `function (3)`: Identifies the use as ai-input for generative responses.
- `subfn (1, 2)`: Specifies the sub-functions of RAG and grounding.
- `resdis (1)`: Indicates the final output will be seen by a human.

**Content Owner Response (Package)**

The Content Owner provides a curated collection of videos with varying provenance and creation sources.

```json
{
  "package": {
    "id": "PKG-STEAMPUNK-001",
    "title": "Steampunk Visual Archives",
    "seller": "video-archive.com",
    "licenseurl": "https://video-archive.com/legal/ai-terms",
    "citation": 1,
    "scope": {
      "scope": 4,
      "max": 1,
      "ctype": [1],
      "video": [
        {
          "title": ["History of Steampunk Aesthetics"],
          "dur": [600],
          "clip": 0,
          "transcript": 1,
          "pubdate": "2026-01-20T10:00:00Z",
          "author": ["Gearheart Media"],
          "sourcetype": 0,
          "provenance": 1,
          "provent": "c2pa.org"
        },
        {
          "title": ["AI-Generated Steampunk Landscape"],
          "dur": [60],
          "clip": 1,
          "transcript": 0,
          "pubdate": "2026-02-15T14:30:00Z",
          "author": ["StyleBot-V1"],
          "sourcetype": 1,
          "provenance": 0
        }
      ]
    },
    "retrieval": {
      "auth": 2,
      "endpoint": "https://api.video-archive.com/v1/media",
      "type": [2]
    }
  }
}
```

**Metadata Highlights**

- `citation (1)`: Requires the AI System to cite the Content Owner.
- **Video 1 (Human + Provenance):**
  - `sourcetype (0)`: Indicates the video was created by a human.
  - `provenance (1)`: Confirms that technical provenance is available via c2pa.org.
  - `transcript (1)`: Confirms a transcription is available for this asset.
- **Video 2 (AI + No Provenance):**
  - `sourcetype (1)`: Explicitly identifies the asset as AI-generated.
  - `provenance (0)`: Indicates no technical provenance is available for this specific file.
  - `clip (1)`: Denotes that this asset is a clip rather than full-length content.
- `retrieval.type (2)`: Informs the AI system the content is delivered via API.

---

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

- `scope (5)`: Requesting a curated selection (the specific reservation data).
- `function (1)`: Using ai-all, which includes model evaluation and retrieval.
- `subfn (3, 4)`: Combining agent-view (gathering data for future action) and agent-actions (taking the action).
- `resdis (1)`: The outcome of the action will be shown to a human user.

**Content Owner Response (Package)**

The Marketplace or Content Owner provides the retrieval endpoint for the Agent, typically utilizing the Model Context Protocol (MCP) or an API for structured interaction.

```json
{
  "package": {
    "id": "PKG-RESERVE-TOOL-01",
    "title": "BistroReserve Agent Interface",
    "seller": "bistro-reserve.com",
    "licenseurl": "https://bistro-reserve.com/legal/agent-terms",
    "citation": 0,
    "scope": {
      "scope": 5,
      "max": 1,
      "ctype": [0],
      "text": [
        {
          "title": "Real-time Table Availability",
          "published": 1,
          "sourcetype": 0,
          "provenance": 0
        }
      ]
    },
    "retrieval": {
      "auth": 2,
      "endpoint": "https://mcp.bistro-reserve.com/v1/connect",
      "type": [3]
    }
  }
}
```

**Key Metadata Elements**

- `scope.scope (5)`: Confirms the response is for a curated selection of reservation data.
- `text.sourcetype (0)`: Confirms the data provided (availability, descriptions) is human-created.
- `retrieval.type (3)`: Specifies MCP (Model Context Protocol), where the AI System client connects to specialized connectors to expose functionality.
- `retrieval.auth (2)`: Requires OAuth2 for the Agent to securely interact with the booking system.
- `package.citation (0)`: Citation is not required for this functional data exchange.


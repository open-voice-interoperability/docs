<img src="https://github.com/open-voice-interoperability/artwork/blob/main/horizontal/color/Interoperability_Logo_color.png" width="200">

# Assistant Manifest Specification Version 0.9.0

The Open Voice Network\
Open Voice Interoperability Initiative - LF AI & Data Foundation\
Architecture Work Group

30 April 2024 \
Draft Version 0.9.0

*_Editor-in-Chief: David Attwater_*\
*_Contributors: Emmett Coin, Deborah Dahl, Leah Barnes, Allan Wylie and Diego Gosmar_*

## TABLE OF CONTENTS

### CHAPTER 0. SCOPE AND INTRODUCTION
#### &nbsp; 0.1 Document Scope
#### &nbsp; 0.2 Assistant Manifest Purpose

### CHAPTER 1. SPECIFICATION
#### &nbsp; 1.1 Representation
#### &nbsp; 1.2 Protocol
#### &nbsp; 1.3 AAA & Security
#### &nbsp; 1.4 Nomenclature
#### &nbsp; 1.5 Assistant Manifest Object Structure
#### &nbsp; 1.6 The `identification` object
#### &nbsp; 1.6 The `capabilities` object

### CHAPTER 2. SCHEMA

### CHAPTER 3. REFERENCES

### CHAPTER 4. GLOSSARY OF TERMS

### CHAPTER 5. DECISION LOG
### CHAPTER 5. DOCUMENT CHANGE LOG

*****
### CHAPTER 0. SCOPE AND INTRODUCTION
#### 0.1 Document Scope
This document specifies the object format for the Open Voice Interoperability Initiative  - LF AI & Data Foundation (OVON) Assistant Manifest.  

#### 0.2 Assistant Manifest Purpose
The Assistant Manifest is a structured description of the key characteristics and capabilities of a conversational assistant that is associated with a unique serviceEndpoint.  The manifest can be thought of as the curriculum vitae of the conversational agent and a public record of the services that it offers.  It can be used, for example, by other agents or users to decide whether to invite a particular agent to join a conversation.  In this regard, it is particularly relevant to discovery agents who provide services to other agents to help them find assistants to achieve certain tasks for them.

The Assistant Manifest is intended to be used as a component of other specifications; it is not a stand-alone document.

### CHAPTER 1. SPECIFICATION
#### 1.1 Representation

The Assistant Manifest will be represented as a JSON [[1]](https://www.ecma-international.org/publications-and-standards/standards/ecma-404/) object in string format. This object will be embodied in other objects. It is not expected to stand alone as a document in its own right.

#### 1.2 Protocol

JSON was chosen for the OVON conversation envelope as it is an Open and Human Readable Standard format for Data Exchange that is independent of any particular protocol.

#### 1.3 AAA & Security

Authorization, Authentication, Accounting, and Security specifications are outside the scope of this document and will be defined in separate documents.

#### 1.4 Nomenclature

This specification uses `camelCase` (i.e., no spaces with new words capitalized) for all nominal property names, such as `serviceEndpoint` and `supportedLayers`.  

#### 1.5 Assistant Manifest Object Structure

    {
        "identification":
        {
            "serviceEndpoint": "https://dev.buerokratt.ee/ovonr/conversation",
            "organization": "Government of Estonia",
            "conversationalName": "Buerokratt",
            "serviceName": "Passport Office",
            "role": "Immigration Specialist",
            "synopsis" : "Immigration specialist as part of the Beurocrat system."
        },

        "capabilities": [
            {
                "keyphrases": [
                    "visa",
                    "immigration",
                    "passport",
                    "permanent resident"
                ],
                "languages": [
                    "en-us"
                ],
                "descriptions": [
                    "international travel to and from Estonia"
                ],
                "supportedLayers": [
                    "text"
                ]
            }
        ]
    }

##### _Figure 1. Example Assistant Manifest Object_ #####

Figure 1 shows an example of an assistant manifest object.  It has two mandatory sections, `identification` and `capabilities`, which are described below.

#### 1.6 The `identification` object

The `identification` object publishes key aspects of the conversational assistant's role and identity.  It should not contain specific information about specific services or capabilities that are offered.  This section allows others to know how the agent refers to itself, the organization that it represents, and its role in serving its users.

|Key|Type|Description|Example|Mandatory|
|--|--|--|--|--|
|`serviceEndpoint`|URL|The endpoint of the conversational assistant.  This endpoint will be capable of sending and receiving OVON Conversation Envelopes.|"https://dev.buerokratt.ee/ovonr/conversation"|Mandatory|
|`organization`|string|The name of the organization administering this assistant. If relevant, this is the organization that the agent will introduce itself as acting on behalf of. This will be searchable and vocalizable.|"Government of Estonia"|Mandatory|
|`conversationalName`|string|The 'given name' of the conversational agent. This will be the name by which the agent will introduce themselves to other conversants and the name that it will respond to when addressed. This will be searchable and vocalizable.|"Buerokratt"|Mandatory|
|`serviceName`|string|The organizational function of the assistant. This will be searchable and vocalizable.   |"Passport Office"|Optional|
|`role`|string|The 'job title' or 'role' of the assistant.  This will be how the agent would describe its role in the organization or its relationship to its users.  This will be searchable and vocalizable.|"Immigration Specialist"|Optional|
|`synopsis`|string|A sentence describing the function of the assistant.  This sentence is intended to be spoken to the user and should take less than five seconds to verbalize. In English this would typically be less than 75 characters. |"Immigration specialist as part of the Buerokratt system."|Mandatory|

#### 1.6 The `capabilities` object

The `capabilities` object describes the capabilities offered by the agent. The manifest is keyed to a specific endpoint which in a large organization may support a large number of federated capabilities.  For this reason, the capabilities object is expressed as an array of capability objects.  Each capability object stands alone from the others.  It represents a set of characteristics that work together independently of the other capability objects. For example, if one capability supports the Welsh language for banking functions and another supports the Inuit language for sports information, this does not mean that the endpoint can provide banking services in the Inuit language.  

The features of each capability object are shown below.

|Key|Type|Description|Example|Mandatory|
|--|--|--|--|--|
|`keyphrases`|string array|A list of searchable key phrases, in no particular order, that represent the key features of the service provided or topics about which questions can be answered by the assistant.  These can be words or phrases and will typically be simple noun or verb phrases, suitable, for example, for use in simple text searches.  There is no limit to the length of the list or the length of the phrases.|["visa",  "immigration", "passport","permanent resident" ],|Mandatory|
|`languages`|string array|A list of the languages supported by this capability according to [IETF BCP 47 Language Tag [4]](https://www.rfc-editor.org/rfc/rfc5646.txt).|["en-us"]|*Default:* *Undefined*|
`descriptions`|string array|A set of searchable texts, in no particular order, describing the services and capabilities of the assistant. These are expressed in natural language suitable, for example, for inclusion in queries or searches using natural language technologies. There is no limit to the length of the list or the length of the phrases.|["international travel to and from Estonia","permanent and temporary visa requirements when visiting Estonia"]|Mandatory|
`supportedLayers`|string array|A list of the dialogEvent layers supported by this capability. See [[2] Interoperable Dialog Event Specification](https://docs.google.com/document/d/1ld0tbGhQEOcZ4toCi0R4AEIWlIET8PgF1b-xKhtwsm0) and [[3] Interoperable Conversation Envelope Specification Version 0.9.0.](https://github.com/open-voice-interoperability/docs/blob/main/specifications/ConversationEnvelope/0.9.0/InteroperableConvEnvSpec.md ) |["text","ssml","pronun","voice","html"]|*Default:* ["text"]|

### Chapter 2. Schema
##### The structure of a JSON assistant manifest is defined below as a JSON Schema. #####

    {
        "$id": "https://github.com/open-voice-interoperability/docs/tree/main/schemas/assistant-manifest/0.9.0/assistant-manifest-schema.json",
        "$schema": "https://json-schema.org/draft/2020-12/schema",
        "description": "A representation of an 'assistant manifest’ - a standard format for publishing the identity and capability of an OVON agent.",
        "type": "object",
        "required":  [ "identification", "capabilities" ],
        "properties": {
            "identification": {
            "required":  ["serviceEndpoint", "organization", "conversationalName" ],
            "properties": {
                "serviceEndpoint": {
                "$ref": "#/$defs/url",
                "description": "The endpoint of the conversational assistant."
                },
                "organization": {
                "type": "string",
                "description": "The name of the organization administering this assistant."
                },
                "conversationalName": {
                "type": "string",
                "description": "The 'given name' of the conversational agent."
                },
                "serviceName": {
                "type": "string",
                "description": "The organizational function of the assistant."
                },
                "role": {
                "type": "string",
                "description": "The 'job title', or 'role' of the assistant."
                },
                "synopsis": {
                    "type": "string",
                    "description": "A sentence summarizing the function of the assistant."
                }
            }
            },
            "capabilities": {
            "type": "array",
            "description": "A list of capabilities supported by this endpoint.",
            "items": {
                "type": "object",
                "description": "One of the capabilities supported by this endpoint.",
                "required":  ["keyphrases" , "descriptions"],
                "properties" : {
                "keyphrases": {
                    "type": "array",
                    "description": "A list of searchable key phrases.",
                    "items": {
                    "type": "string",
                    "description": "A searchable key phrase."
                    }
                },
                "languages": {
                    "type": "array",
                    "description": "A list of the languages supported by this capability.",
                    "items": {
                    "type": "string",
                    "description": "A language supported by this capability"
                    }
                },
                "descriptions": {
                    "type": "array",
                    "description": "A set of searchable texts, in no particular order, describing the services and capabilities of the assistant.",
                    "items": {
                    "type": "string",
                    "description": "A searchable text describing one of the services or capabilities of the assistant."
                    }
                },
                "supportedLayers": {
                    "type": "array",
                    "description": "A list of the dialogEvent layers supported by this capability.",
                    "items": {
                    "type": "string",
                    "description": "A dialogEvent layer supported by this capability"
                    }
                }
                }
            }
            }
        },

        "$defs": {
            "url": {
            "type": "string",
            "description": "Any valid URL"
            }
        }
    }

### Chapter 3. References

* [1] https://www.ecma-international.org/publications-and-standards/standards/ecma-404/ ECMA-404 The JSON data interchange syntax
* [2] https://docs.google.com/document/d/1ld0tbGhQEOcZ4toCi0R4AEIWlIET8PgF1b-xKhtwsm0 Interoperable Dialog Event Specification (version 1.0.1)
* [3] https://github.com/open-voice-interoperability/docs/blob/main/specifications/ConversationEnvelope/0.9.0/InteroperableConvEnvSpec.md Interoperable Conversation Envelope Specification Version 0.9.0.
* [4] https://www.rfc-editor.org/rfc/rfc5646.txt RFC 5646. BCP 47. Tags for Identifying Languages.
* [5] https://www.ietf.org/rfc/rfc4646.txt RFC 4646 Regarding Best Practice for Tags for Identifying Languages

### Chapter 4. Glossary of Terms


|Term|Definition|
|-|-|
|channeling|A conversational assistant acts as an intermediary between a user and another conversational assistant, passing through requests and returning responses. The intermediary assistant will often make no modifications to the requests or the responses but may do so, for example increasing the speed or volume or even translating between languages.  |
|confidence |A number representing a measure of the confidence that the information contained in the associated value is ‘correct’.|
|conversational assistant|A conversant in a conversation (usually but not necessarily a machine) that is performing the specific role of adopting and meeting the needs of another conversant (usually but not necessarily a human).|
|conversant|A participant in a conversation which might be a human or a machine|
|conversation floor manager|A component that manages which conversants are active in a given conversation and which agent is the current focal agent. |
|delegation|A conversational assistant passes control and management of the dialog to another conversational assistant, along with a negotiated amount of context and dialog history. |
|derived specification|A derivative standard, built upon this specification, which defines a specific way to use a dialog event in a particular context.|
|dialog event |A linguistic event in a spoken or written monologue or dialog between two or more speakers.  |
|dialog event feature |A layer of information of a certain type associated with the dialog event.|
|dialog event object|A JSON object encoding a dialog event as part of an interface to a natural language component in a text or speech processing solution or dialog system.|
|dialog event object id |The unique identifier of the dialog event object|
|dialog event span|Identifies the span of time for the dialog event|
|dialog event speaker id|A unique id of the human or machine associated with content of the dialog event|
|discovery agent|A specific type of conversational agent that provides services to help users and/or other agents to discovery the location, capability and identity of agents to perform certain tasks|
|envelope|Structured data (hierarchical structure of data names and values) used to describe events to be communicated by a speaker or agent in a conversation.  Envelopes can contain multiple ordered events to support simple protocols such as HTTPS where servers can only respond with a single response to a single request.|
|feature mime type|The type of token values in a specific dialog event feature.|
|feature token|A representation of part of the information that makes up a feature value.  |
|focal agent|The agent that is currently tasked with responding to agent input.
|JSON path |An unambiguous reference to part of a JSON object.|
|language code|A code representing the language (e.g., American English, British English, New Norsk, etc.)
mandatory elements|The mandatory elements that are required in the various elements of the envelope in order to be OVON compliant.  These elements are enough to allow basic no-frills interoperation between agents.|
|mediation|A conversational assistant acting as a user, has a conversation with another conversational assistant behind the scenes using dialog – semantic or linguistic – interfaces to achieve a goal and return to the user.|
|stand-off-annotation |A method of feature layering and cross-referencing that permits the different features of an utterance or linguistic event to be kept separate but also linked logically and temporally with each other. |
|token encoding|The specific encoding used to represent text in a token.|
|token link|A link from one token in a feature to part or all of another token in a feature used to implement stand-off annotation.|
|token object|A JSON object representing a feature token that defines the value of the feature and other associated information, such as its span and how it links to other feature tokens.|
|token span|Identifies the span of time for an individual token object|
|user proxy agent|A component that implements converts between a human user interacting via certain media into OVON-compliant dialog envelopes and renders utterances from other conversants back to the human user in the appropriate media.|

### Chapter 5. Decision Log

|Issue|Description|
|--|--|
|Conversant or Assistant Manifest|Question: Is the manifest only for conversants with the role of an 'assistant' or should it be named a 'Conversant Manifest' and be capable of representing the identity and capabilities of all participants of a conversation including a user? </br> Answer: The manifest is intended to describe anything that can be a conversational endpoint. This could be a typical informational assistant, a discovery agent, or a user represented by a user proxy agent.  We decided to keep the term 'assistant' to cover all of these cases because it is sufficiently ambiguous, and the dominant use case will not be to describe human users. |

### Chapter 6. Document Change Log

|Version|Release Date|Description|
|-|-|-|
|0.9.0|23 April 2024|Draft for comments to the community|

***********
---
layout: default
title: Interoperable Dialog Event Object Specification Version 1.0
parent: specs

## 2023.06.09
## Draft Version 1.0.0


**Interoperable Dialog Event Object Specification Version 1.0**  
**The Open Voice Network**   
**Architecture Work Group of the Technical Committee**   
**Editor-in-chief: David Attwater**  
**Contributors: Emmett Coin, Deborah Dahl, Jim Larson**

**June 9, 2023**

# TABLE OF CONTENTS
## CHAPTER 0. SCOPE AND INTRODUCTION 
### 0.1 Document Scope 
### 0.2 Dialog Events
### 0.3 A Foundation for Further Specialization 

## CHAPTER 1. SPECIFICATION 
### 1.1 Representation
### 1.2 Dialog Event Object
### 1.3 Span
### 1.4 Feature Objects
### 1.5 Confidence, Linking and Stand-off Annotation
### 1.6 JSON Path and the substring() extension
### 1.7 Alternates
### 1.8 Nomenclature 

## CHAPTER 2. SCHEMA
## CHAPTER 3. REFERENCES
## CHAPTER 4. GLOSSARY OF TERMS
## CHAPTER 5. DECISION LOG 

	
## Chapter 0. Scope and Introduction


### 0.1 Document Scope


##### This document specifies the format for Open Voice Network (OVON) interoperable dialog events. The requirements for this specification are given in [Interoperable Dialog Packet Requirements [10]](https://openvoicenetwork.org/docs/2832-2/). This specification is generic. As described in the requirements there are many different potential uses of a dialog event. #####


### 0.2 Dialog Events


##### Interoperable conversational systems will need to be able to process linguistic input and generate linguistic output. The components within such systems also need to send and receive such output in a standardized way. #####


##### The purpose of a dialog event is to define a generic standardized data structure that can be used in any component of a dialog system to express a ‘language event’, that is to say, any features associated with a phrase, utterance or part of an utterance. Dialog events span a certain time period and are associated with a single speaker. #####


##### These events are used to represent user inputs and system outputs of various types, primarily linguistic inputs and outputs such as speech or text, but also potential multimodal inputs and outputs, such as selections on a touchscreen or images presented by a system. Dialog events can be used to express whole utterances, phrases, or other slices of time. #####


##### Components of dialog systems may receive an event and add features to it, for example, a natural language interpretation component may receive an event containing a text feature and add a semantic feature to it as an interpretation of the text. #####


##### It is anticipated that in the future, events could be joined together to form streams, for example, to represent continuous input or output of a speech-to-text engine. ##### 


### 0.3 A Foundation for Further Specialization


##### This specification defines a generic format into which dialog features in different formats can be gathered together into a single event. It also describes how such features can reference each other. #####


##### It does not define which formats (mime types) should be present, how features should be named, or which features might be required under what circumstances. This will be left to additional specifications, which we term _derived specifications_, built upon this one. ##### 


##### For example, dialog events could be used to carry prompt and response information for a dialog system, annotate spoken dialog between two human speakers, or be used as part of an interface to a natural language component in a text processing solution. ##### 


##### We are anticipating that a future OVON standard for representing utterances in a dialog system or dialog history will be developed as one example of a derived specification. #####


## Chapter 1. Specification
### 1.1 Representation
##### Dialog event objects will be represented as a JSON [1] object in a string format. The JSON dialog event object is not intended to be a stand-alone document. It is intended to be included in a larger data structure as an object with a re-usable standard structure


### 1.2 Dialog Event Object


##### Each dialog event object has a unique ID, is associated with a single speaker (person or machine), spans a period of time, and contains features representing different inter-related aspects of the event. #####


![diagram1](https://github.com/NSouthernLF/docs/assets/101130471/c55c66e2-79e8-4f16-bc9c-f0e81728821e)


##### _Figure 1. Example of a dialog event container._ #####


##### Figure 1 shows an example of a dialog event object. Each dialog event object has a unique id and relates to just one speaker identified by the speaker-id. The event can optionally be linked to a previous event object from the same speaker using the previous-id. The span object represents the time-span of the event. #####


##### The features: id, speaker-id, span, and features are mandatory. The previous-id object is optional. The features section will typically contain one or many feature objects but may be empty. Each feature object is identified with a key which can have any arbitrary feature name. As dictionary keys, feature names must be unique within a dialog event. #####




### 1.3 Span
##### The _span_ object describes a period of time during which the event occurred. It is this span that distinguishes the dialog event from a visual user-interface event which occurs at a point in time. Span objects must contain either an absolute _start-time_ or a relative _start-offset_ but not both. They can also optionally contain either an _end-time_ or _end-offset_ but not both. Absolute times are represented in [ISO 8601 [2]](https://www.iso.org/iso-8601-date-and-time-format.html) format, more specifically the simpler interoperable variant specified in [IETF RFC 3339 [3]](https://datatracker.ietf.org/doc/html/rfc3339). Relative durations are represented in [ISO 8601 [2]](https://www.iso.org/iso-8601-date-and-time-format.html) duration format. ##### 


##### The top-level span object may contain a _start-offset_ rather than an absolute _start-time_. This _start-offset_ specifies the start time as the time elapsed since an absolute reference time is defined outside of the object. For example, the event might be contained in a dialog history object and this offset might be relative to the start-time of the conversation represented by that object. #####


##### As will be explained below, span objects can also be included in individual tokens within features of the dialog event. #####




### 1.4 Feature Objects
![diagram2](https://github.com/NSouthernLF/docs/assets/101130471/b036994b-44c1-46b7-a8d5-35337c69bbb8)
##### _Figure 2. A dialog event containing an audio feature object and a tokenized text feature object._ ##### 


##### As a minimum, each feature object must contain a _mime-type_ and an array of token objects named _tokens_ ##### 




##### Figure 2 shows a simple example of a tokenized text feature. In this example, the _mime-type_ is plain text and the feature is broken into a sequence of tokenized words. Each word is represented by a _token_ object. ##### 


##### This is just one example of how text can be represented in a feature. The _tokens_ array contains zero or more token objects, and each token object must contain either a value object or a value-url string. The value-url string references an external document that contains the value. The reference is a string in the format of a Universal Resource Locator (URL) as shown in the my-audio-feature in Figure 2. ##### 


##### The _mime-type_ is mandatory and defines the format of the _value_ object or the document referenced by the _value-url_. The format of _mime-type_ is defined in IETF RFC-2231 [6] #####


##### The object _lang_ defines the language of the feature according to [IETF BCP 47 language Tag [4]](https://www.rfc-editor.org/rfc/rfc5646.txt). This value is optional and remains undefined if not included. This value informs how the feature should be interpreted. If the feature contains multilingual content it is recommended that this value remains undefined or is set to the dominant language of the feature.


##### The _encoding_ object defines the text encoding within the _value_ objects. Values should be one of "ISO-8859-1" (See [ISO/IEC 8859-1 [11]](https://www.iso.org/standard/28245.html)) or "UTF-8" (See [RFC3629 [12]](https://datatracker.ietf.org/doc/html/rfc3629)). This value is optional and remains undefined if not included. ##### 


##### The _token-schema_ object is included to allow reference to standard sets of token values or symbols for certain _features_ with specific _mime-types_. For example, it could be used to specify which phonetic pronunciation alphabet is present in a pronunciation feature, or refer to a specific standard set of intents for a given domain in a semantic feature. The format of this object is not mandated and is expected to be defined in derived specifications. This value is optional and remains undefined if not included. Figure 2 shows one example of how the _token-schema_ might be used to reference a specific text tokenizer.##### 


##### Figure 4 shows an example of how _token-schema_ might be used to reference a standard semantic schema. Both examples are illustrative, not normative. ##### 




![Screenshot 2023-06-15 at 6 11 51 PM](https://github.com/NSouthernLF/docs/assets/101130471/083c49b1-4a93-4c98-aef1-fd471d9a95b3)
##### _Figure 3. A feature object containing tokens objects with spans._ #####


##### Each token object can also optionally contain a _span_ object. This indicates the time span of this particular token. As described in section [1.3 Span](https://www.iso.org/standard/28245.html), spans can be defined as absolute times or relative offsets. In a token object _start-offset_ and _end-offset_ are relative to the _start-time_ of the parent dialog event (or the implied dialog event start time defined by its _start-offset_).##### 


##### Figure 3 shows an example where the tokenized words of the text feature for Figure 2 are each given an individual _span._ ##### 


### 1.5 Confidence, Linking, and Stand-Off Annotation
##### Each feature should represent just one aspect of the dialog event. Each token object within each feature can be linked to another token object in the event using the object links. ##### 




![Figure4](https://github.com/NSouthernLF/docs/assets/101130471/20c637f9-28ec-48e8-b17f-c9abfdc3e6be)
##### _Figure 4. A simple example of linking between features with confidence assigned to a feature._ #####




##### Figure 4 shows an example of a semantic feature which links to the text that it represents. #####


##### The optional _links_ object is an array of references to the _value_ (or parts of the _value_) of other token objects using a JSON Path with some extensions. There is no restriction on how many links a feature can have. #####


##### Unlike the text in Figure 2, in this example, the text is not tokenized. The semantic token with the name _"intent"_ is linked to the whole text of the utterance as defined by the JSON path ```$.my-text-feature.tokens[0].value.``` ##### The semantic token with the name _"date"_ however is linked to a sub-string of the source text - the word _tomorrow_ - via the JSON Path ```$.my-text-feature.tokens[0].value.substring (34,41)```. Section 1.6 describes how to use JSON Path references in more detail.
##### The optional _confidence_ object is a number carrying a measure of the confidence that the information contained in the associated value is ‘correct’. Confidence values are expressed as a probability (real number between 0.0 and 1.0). Derivative specifications may override this range for specialized situations.


##### Recall that the format of a given _value_ object is defined by the _mime-type_ and will vary between features. #####


##### Feature layering and cross-referencing are both important parts of the dialog event standard. Together they permit the different features of an utterance or linguistic event to be kept separate but also linked logically and temporally with each other. This approach is termed stand-off-annotation.[9] #####


### 1.6 JSON Path and the substring() extension


##### Individual elements of the linked object are defined as strings containing JSON Paths. JSON Path is a notation for querying and manipulating data stored in JavaScript Object Notation (JSON).
##### At the time of publication, there is not an agreed standard definition for JSON Path. There are a number of implementations based on [JSON Path [7]](https://goessner.net/articles/JsonPath/) that are broadly compatible. There is also an IETF task force [RFC6901 [8]](https://www.rfc-editor.org/rfc/rfc6901) working on a common standard.#####


##### JSON Path expressions in _links_ objects are cross-references from one token object to the _value_, or part of a value, of another token object. For these references, the root document (designated by ‘$’) is the _features_ object in the current dialog event. There is currently no way to specify a link between features that are not contained in the same dialog event. #####


##### Implementations should support one additional non-standard extension to JSON Path - namely the _substring()_ function. This extension is added to allow token objects to reference a specific substring within another token object, for example, a word, phrase, or arbitrary sequence of characters. #####


##### The _substring_ function can be invoked at the tail end of a path. The input to this function is the output of the preceding path expression. The substring function takes two ordered comma-separated parameters, the index of the start and end character in the substring. The first character of the string is at the index _zero_. #####


##### For example the JSON Path: ```$.my-text-feature.tokens[0].value.substring(34,41)``` will return the 34th to the 41st character of string contained within the object referred to by the JSON Path ```$.my-text-feature.tokens[0].value```. If the referred object is not a string, or the substring is out-of-bounds then the resulting behavior is undefined. ##### 


##### Token objects can contain content that is specified externally via a _value-url_ string. If this external content is expressed in JSON format then the JSON path should be interpreted as if the _value_ had been specified locally. This is functionally equivalent to reading the content of the document referenced by value-url and placing it in the equivalent local _value_ object before applying the links reference. #####


### 1.7 Alternates


![Diagram5](https://github.com/NSouthernLF/docs/assets/101130471/b878d7c6-690c-4046-a73e-6a0cd76dd8e9)


##### _Figure 5. Defining alternate values for a feature._ #####


##### Spoken or written language rarely has a single unambiguous interpretation. For this reason, the dialog event object allows for multiple interpretations of a given event. As has already been seen, the _tokens_ object is used to represent the preferred interpretation of a given feature. In addition to this, the optional alternates array can be used to express alternate interpretations of this feature. For example, in Figure 5 it can be seen that the preferred interpretation of the feature named _my-text-feature_ has the _value_ “what is the weather forecast for tomorrow” and a _confidence_ of 0.96. The _alternates_ array contains one additional interpretation of the utterance with the _value_ “what is the weather forecast for thursday” and a _confidence_ of 0.73. #####


##### The _alternates_ object is an array of an array. Each object in the outer array represents an alternative interpretation and each inner array represents the token objects for this interpretation. These inner token objects have the same format as array elements in the _tokens_ object. #####


##### The outer array can contain zero or more interpretations. An empty _alternates_ array implies that there are no alternate interpretations and is equivalent to the _alternates_ object not being present in the event. #####


### 1.8 Nomenclature
##### This specification uses ‘kebab-case’ (i.e. hyphenated lowercase) for all nominal property names, for example, _start-offset_ and _value-url._ User-defined object keys such as the dictionary names used in the _features_ section can use any valid JSON expression. It is recommended but not mandated that any normative key names in derived specifications follow the same kebab-case convention. #####




### Chapter 2. Schema
##### The structure of a JSON dialog event is defined below as a JSON Schema. #####


![Diagram5](https://github.com/NSouthernLF/docs/assets/101130471/20a4a092-6f06-4f9d-8c0d-2390ba15824d)




### Chapter 3. References
* [1] https://www.ecma-international.org/publications-and-standards/standards/ecma-404/ ECMA-404 The JSON data interchange syntax
* [2] https://www.iso.org/iso-8601-date-and-time-format.html ISO 8601 Date and Time Format.
* [3] https://datatracker.ietf.org/doc/html/rfc3339 Newman, Chris; Klyne, Graham (July 2002). Date and Time on the Internet: Timestamps. IETF. doi:10.17487/RFC3339. RFC 3339. Archived 
* [4] https://www.rfc-editor.org/rfc/rfc5646.txt RFC 5646. BCP 47. Tags for Identifying Languages. 
* [5] https://www.ietf.org/rfc/rfc4646.txt RFC 4646 Regarding Best Practice for Tags for Identifying Languages
* [6] https://datatracker.ietf.org/doc/html/rfc2231 MIME Parameter Value and Encoded Word Extensions: Character Sets, Languages, and Continuations
* [7] https://goessner.net/articles/JsonPath/ JSONPath - XPath for JSON. Stefan Goessner.
* [8] https://www.rfc-editor.org/rfc/rfc6901 RFC6901. JavaScript Object Notation (JSON) Pointer 
* [9] Pose-Rodriguez, Javier & Lopez, Patrice & Romany, Laurence. (2014). A Generic Formalism for Encoding Standoff annotations in TEI.
* [10] https://openvoicenetwork.org/docs/2832-2/ Interoperable Dialog Packet Requirement Specification.
* [11] https://www.iso.org/standard/28245.html ISO/IEC 8859-1:1998 Information technology — 8-bit single-byte coded graphic character sets — Part 1: Latin alphabet No. 1
* [12] https://datatracker.ietf.org/doc/html/rfc3629 RFC3629. UTF-8, a transformation format of ISO 10646


### Chapter 4. Glossary of Terms
![graph4](https://github.com/NSouthernLF/docs/assets/101130471/64d076a4-117f-4348-b941-804675d841c1)


### Chapter 5. Decision Log
##### This section documents some of the key design decisions that were made by the team during the development of this specification. It is informative, not normative. #####


![5b](https://github.com/NSouthernLF/docs/assets/101130471/2f1e2256-d826-4fd4-898c-0ca7dd083187)


## OVON Interoperable Event Restrictions ##


![LastDiagram](https://github.com/NSouthernLF/docs/assets/101130471/f102b54b-a431-4bb2-b194-5f054b96390a)






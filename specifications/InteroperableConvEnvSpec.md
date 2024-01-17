# Interoperable Conversation Envelope Specification

The Open Voice Network\
Open Voice Interoperability Initiative - LF AI & Data Foundation\
Architecture Work Group\
16 January 2024\
\
*_Editor-in-Chief: David Attwater_*\
*_Contributors: Emmett Coin,  Deborah Dahl,  Jim Larson, Allan Wylie,  Rainer Türner and Diego Gosmar_*

## TABLE OF CONTENTS
### CHAPTER 0. SCOPE AND INTRODUCTION
#### &nbsp; 0.1 Document Scope
#### &nbsp; 0.2 Dialog Envelopes
#### &nbsp; 0.3 Delegation, Channeling and Mediation
#### &nbsp; 0.4 OVON Event Types
### CHAPTER 1. SPECIFICATION
#### &nbsp; 1.1 Representation
#### &nbsp; 1.2 Protocol
#### &nbsp; 1.3 AAA & Security
#### &nbsp; 1.4 Nomenclature
#### &nbsp; 1.5 Dialog Envelope Object Structure
#### &nbsp; 1.6 Schema Object
#### &nbsp; 1.7 Conversation Object
#### &nbsp; 1.8 Sender Object
#### &nbsp; 1.9 Response Code Object
#### 1.10 Events Object
#### 1.11 Event-Types
#### 1.12 Utterance Events
##### &nbsp; &nbsp; &nbsp;  1.12.1 Utterance Text Feature
#### 1.13 Whisper Events
##### &nbsp; &nbsp; &nbsp;  1.13.1 Whisper Text Feature
#### 1.14 Extensible Dialog Event Features (Informative)
#### 1.15 Invite Event
#### 1.16 Bye Event
### CHAPTER 2. MINIMAL BEHAVIORS
#### &nbsp; 2.1 Minimal Agent Behavior
#### &nbsp; 2.2 Minimal Conversation Floor Manager Behaviors
### CHAPTER 3. JSON Envelope SCHEMA
### CHAPTER 4. REFERENCES
### CHAPTER 5. GLOSSARY OF TERMS
### CHAPTER 6. DECISION LOG

*****
### CHAPTER 0. SCOPE AND INTRODUCTION
#### 0.1 Document Scope
This document specifies the format for Open Voice Interoperability Initiative  - LF AI & Data Foundation (OVON) interoperable dialog envelope. The user stories upon which this specification is based can be found in the OVON white paper Interoperability of Conversational Assistants  [1].  

#### 0.2 Dialog Envelopes
The OVON Dialog Envelope is a universal JSON structure whose purpose is to allow human or automatic agents (assistants) to interoperably participate in a conversation.  When coupled with a specific protocol, such as HTTPS (See section 1.2), a dialog agent that can generate and send Dialog Envelopes is capable of inter-operating with any other OVON-compliant agent, regardless of the technology or architecture on which that other agent is based..


<img width="510" alt="Screenshot 2024-01-16 at 6 20 54 PM" src="https://github.com/open-voice-interoperability/docs/assets/101130471/6b909294-0d00-498e-9aee-ef0edcd916d9">

A conversation can consist of multiple conversants, who may be any arbitrary mixture of human or autonomous agents.  For this draft of the specification, we simplify the problem and say that at any given moment the conversation will comprise two conversants. One conversant will be a human user and the other an autonomous dialog agent.\
\
Figure 1 shows a simple schematic showing a user interacting with three OVON-compliant dialog assistants, via a user proxy agent, and a conversation floor manager.  Let's assume for now that there is a single user who remains constant for the duration of the conversation.  This user engages with a number of dialog agents, one at a time,  during the course of the conversation in a sequential manner.\
\
The proxy agent acts as a media gateway with the user - converting whatever media interface the user is interacting with into standard interoperable messaging.  The conversational floor manager acts as a hub to coordinate the conversation.\
\
In this draft of the specification, we consider the case where the function of the user proxy agent and the conversation manager are combined.   This combined function is termed an 'OVON compliant host browser'.\
\
In its simplest configuration, the user proxy agent might present a text-based chat interface to the user.  For spoken interaction, the proxy agent will likely also contain speech-to-text and text-to-speech facilities but other configurations are anticipated.  The proxy agent may have conversational ability in its own right but in many cases, it may only provide rudimentary capabilities such as wake-word detection.\
\
Each arrow in the diagram denotes a message passing from one agent to another.  Each of these messages will comprise a single dialog envelope.   Each conversant can 'hear' things said to it or 'say' things.  Conversants may also invite other agents to join the conversation and take the conversational floor.


#### 0.3 Delegation, Channeling and Mediation

The conversational floor manager is in control of which conversants are engaged in a conversation at any given moment.\
\
To start a conversation, the proxy agent invites a specific dialog agent (e.g. Dialog Agent A) to take part in a conversation with the user.   Once engaged, a dialog agent receives a dialogue envelope containing each user utterance and sends an utterance back to the user in response.  Once the assistant is done with the interaction they can simply end the interaction or invite another agent to enter the conversation (e.g. Dialogue Agent B in the diagram).   The change of control of a dialog between two agents is called 'delegation'.\
\
Dialog Assistants may also engage the services of another dialog agent behind the scenes to assist with the conversation (e.g. Agent A engages the services of target agent C to support it in its interaction with the user.)    The controlling agent might choose to pass user utterances unaltered to the target agent and may in return pass the target agent's responses unaltered to the user.  This pattern is termed 'channeling'. It is in many ways functionally equivalent to delegating the conversation but the channeling agent is also able to take, intervene or override the contribution of the agent to which the conversation is being channeled.  While channeling does not change the conversational intent of the utterance, it may change how the content is rendered.  For example, increase the volume for the hard of hearing, decrease the speed of presentation for the cognitively challenged or for non-native speakers, change the voice characteristics of the presentation, or change the language, or change visual characteristics if these are present.\
\
Alternatively, the intermediate agent may reformulate the user's utterances and/or the target agent's utterances, holding whole conversations behind the scenes in order to achieve a goal.  This pattern is termed 'mediation'.  The mediation pattern also may be particularly relevant in the case where the target agent is in fact another human user (e.g. where an autonomous agent acts on behalf of a user to book an appointment with a doctor or restaurant).\
\
There is no limit to the depth of a channeling or mediation chain and delegation can happen at any level in such a chain.  Any agent that is hosting a mediated or channeled conversation will need to perform the functions of a conversation floor manager to those agents it is hosting.\
\
The patterns described above allow for conversation between one user and multiple agents where it is clear which agent has the conversational floor at any given moment.  As noted the channeling and mediation pattern do allow for more than one conversational agent to be involved in the conversation at the same time.\
\
It is envisaged that later extensions to this specification will also support simultaneous multi-party conversation where multiple users and agents may be listening to the conversation simultaneously and take turns to speak or even speak over one another.

#### 0.4 OVON Event Types

In order to facilitate the patterns described above each dialog envelope can carry one or more OVON Events.  OVON Events are broken into two categories.\

* utterance events - spoken or written natural language
  * utterance  - An utterance spoken in the conversation itself by a user or agent
  * whisper - An out-of-band linguistic instruction from one agent to another
* agent control events  - structure control messages
  * invite - A user-agent or assistant is invited to join the conversation.
  * bye -  A user-agent or assistant is leaving the conversation
### CHAPTER 1. SPECIFICATION
#### 1.1 Representation

A dialog envelope will be represented as a JSON [1] object in a string format.  The JSON dialog envelope is expected to be a stand-alone document or object but there is no reason that it cannot be part of a larger JSON document.

#### 1.2 Protocol

JSON was chosen for the OVON dialog envelope as it is an Open and Human Readable Standard format for Data Exchange that is independent of any particular protocol.  Supported protocols and the mechanisms by which two agents agree on a protocol to be used are currently outside the scope of this document.\
\
For the sake of simplicity, it is anticipated that OVON implementations will initially use HTTPS as underlying communication protocol, but could include several other ones currently available (i.e. SIP, Websockets, WebRTC, etc) or any future available ones (i.e, HTTP/3, etc).

#### 1.3 AAA & Security

Authorization, Authentication, Accounting, and Security specifications are outside the scope of this document and will be defined in separate documents. 

#### 1.4 Nomenclature

This specification uses ‘camelCase’ (i.e. no spaces with new words being capitalized) for all nominal property names, for example, eventType and replyTo.  

#### 1.5 Dialog Envelope Object Structure

<img width="458" alt="Screenshot 2024-01-16 at 7 53 11 PM" src="https://github.com/open-voice-interoperability/docs/assets/101130471/1472e1d3-205c-4750-8e47-b556f291055b">

##### Figure 2. An example of a conversation envelope #####

Figure 2 shows an example of a dialog envelope.  The envelope is wrapped in an ovon key.   This contains five sections:

* schema - the version of the dialog envelope and a schema to validate it against
* conversation - persistent information related to the current dialog
* sender - details of the sender
* responseCode - a response code if this message is in response to another
* events - a list of OVON 'events'

The responseCode section is optional.  All other sections are mandatory.

#### 1.6 Schema Object


<img width="486" alt="Screenshot 2024-01-16 at 7 56 33 PM" src="https://github.com/open-voice-interoperability/docs/assets/101130471/b3fdc32a-eb9d-4534-becf-4b0c03df73ce">\
<img width="264" alt="Screenshot 2024-01-16 at 8 02 35 PM" src="https://github.com/open-voice-interoperability/docs/assets/101130471/55366975-5af4-4fbf-b15a-bd9cf719e648">

The _schema_ object specifies the format of the message in this OVON envelope.  It is mandatory. It must contain a valid _version_ number for an OVON envelope.  Figure 3 shows the minimal information that must be present in an OVON-compliant envelope.

<img width="518" alt="Screenshot 2024-01-16 at 7 57 58 PM" src="https://github.com/open-voice-interoperability/docs/assets/101130471/b12cdc97-ee6f-4c6f-a9fe-6229a2235a01">\
<img width="338" alt="Screenshot 2024-01-16 at 8 03 33 PM" src="https://github.com/open-voice-interoperability/docs/assets/101130471/58326eaa-7125-460e-8596-3a451ebfb97b">

The schema for the version of the envelope specification can be found in **Chapter 3. Schema**.  Figure 4 shows an optional _url_ that may also be included which should point to the correct version of that JSON schema.

#### 1.7 Conversation Object

<img width="529" alt="Screenshot 2024-01-16 at 8 05 59 PM" src="https://github.com/open-voice-interoperability/docs/assets/101130471/8cb28601-0d2a-4853-b987-6a5ed7c4a498">

As shown in figure 5, the _conversation_ section contains just one piece of mandatory information - the _id_ of the conversation.  The _id_ should be a unique identifier for the current conversation with the user.  Persistent information relating to this current conversation can be keyed to this _id_.   The _id_  can be any arbitrary length character sequence that can be represented as a string in JSON.

#### 1.8 Sender Object

<img width="495" alt="Screenshot 2024-01-16 at 8 08 02 PM" src="https://github.com/open-voice-interoperability/docs/assets/101130471/38943807-86aa-4a53-9007-37d0d8e99ff0">

Figure 7 shows the mandatory elements in the sender object.  The from object is a string and should be a valid URI.  

![Picture2c](https://github.com/open-voice-interoperability/docs/assets/101130471/d4a14bd0-a077-424c-9f5a-3e8ef88247a5)

Figure 8 shows other supported elements in the sender object.   The _replyTo_ object is also a string and must also be a valid URI.  The _replyTo_ address should be the address of an OVON-compliant assistant or host-browser and should be capable of receiving messages in OVON Envelope format.

#### 1.9 Response Code Object

<img width="500" alt="Screenshot 2024-01-16 at 8 27 41 PM" src="https://github.com/open-voice-interoperability/docs/assets/101130471/f124433b-6927-43b5-b202-bcaca9d11c18">

Figure 9 shows the mandatory elements of the optional _responseCode_ object.  This is an integer value and is modeled on the semantics defined in [RFC 9110 HTTP Semantics](https://docs.google.com/document/d/1ld0tbGhQEOcZ4toCi0R4AEIWlIET8PgF1b-xKhtwsm0/edit?userstoinvite=jim42%40larson-tech.com&sharingaction=manageaccess&role=writer#bookmark=id.w6ym7j9punzt).

![Screenshot 2024-01-16 at 8 37 32 PM](https://github.com/open-voice-interoperability/docs/assets/101130471/e85bb618-9df0-4ffc-b601-fe96c3d225a2)\
<img width="348" alt="Screenshot 2024-01-16 at 8 40 21 PM" src="https://github.com/open-voice-interoperability/docs/assets/101130471/9d57be31-a4dc-411c-9501-dbc7f9b29829">

Figure 10 shows Other supported elements in the _responseCode_ object.  An optional description can be added to the response code.  This is a text string. There are no constraints on the content of this string which is intended to convey information for audit and logging purposes.

#### 1.10 Events Object

![Diagram3](https://github.com/open-voice-interoperability/docs/assets/101130471/b07255df-fa8e-44ae-99b6-7b34d1997f57)


Figure 11 shows the structure of the _events) object.  This should be an array of one or more objects which we will call an event object. 

Each event object must have an _eventType_, which is a string, and a parameters object which is a dictionary of parameter objects with standard key names specific to the event-type.

#### 1.11 Event-Types

The event-type string can be one of the following:\

* _utterance_  -  A natural language utterance as a dialog event.
* _whisper_ - An inter-assistant communication as a dialog event.
* _invite_ - A control message to invite another agent-browser or assistant to join the conversation. 
* _bye_  - A control message to indicate that a user-agent or assistant is leaving the conversation.

The following sections define these event objects in more detail.

#### 1.12 Utterance Events

![diagram5](https://github.com/open-voice-interoperability/docs/assets/101130471/8172f675-53d2-45ac-91db-b7086b0cd084)

Figure 12 shows the structure of an event with the _eventType_ of _utterance_.  This object contains just one parameter with the key-name _dialogEvent_.

OVON events of this type are sent whenever a user or an assistant takes a dialog act.  The value of the _dialogEvent_ dictionary key must contain a valid dialog event object as specified in [Interoperable Dialog Event Object Specification Version 1.0](https://docs.google.com/document/d/1ld0tbGhQEOcZ4toCi0R4AEIWlIET8PgF1b-xKhtwsm0/edit?userstoinvite=jim42%40larson-tech.com&sharingaction=manageaccess&role=writer#bookmark=id.mnvmxlp2vaay ).
Figure 12 shows the structure of an event with the eventType of utterance.  This object contains just one parameter with the key-name dialogEvent.

OVON events of this type are sent whenever a user or an assistant takes a dialog act.  The value of the dialogEvent dictionary key must contain a valid dialog event object as specified in Interoperable Dialog Event Object Specification Version 1.0.   

The following sections describe the features that are currently supported in the utterance dialog event.   

Additional keys and mime-types are permitted.  Compliant OVON dialog agents do not need to respond to any unsupported keys in the dialog event..   

The following sections describe the _features_ that are currently supported in the _utterance_ dialog event.   

Additional keys and mime-types are permitted.  Compliant OVON dialog agents do not need to respond to any unsupported keys in the dialog event.

##### 1.12.1 Utterance Text Feature
![chart](https://github.com/open-voice-interoperability/docs/assets/101130471/e2cfc559-3056-4171-beff-d6039040d8cc)

### 1.13 Whisper Events

![diagram6](https://github.com/open-voice-interoperability/docs/assets/101130471/75315823-7e40-4ad7-bbe9-636164dd7809)

Figure 13 shows the structure of an OVON event with the _eventType_ of _whisper_.  This object contains just one parameter with the key-name _dialogEvent_.\
\
OVON events of this type are sent whenever an assistant wants to send a natural language instruction or request to another agent.   _whisperUtterance_ events are identical in format to _utterance_ events but they are not to be directly voiced in the dialog.\
\
This object can contain any valid dialog event objects as specified in [Interoperable Dialog Event Object Specification Version 1.0](https://docs.google.com/document/d/1ld0tbGhQEOcZ4toCi0R4AEIWlIET8PgF1b-xKhtwsm0/edit?userstoinvite=jim42%40larson-tech.com&sharingaction=manageaccess&role=writer#bookmark=id.mnvmxlp2vaay).   

##### 1.13.1 Whisper Text Feature

<img width="496" alt="Screenshot 2024-01-16 at 9 33 34 PM" src="https://github.com/open-voice-interoperability/docs/assets/101130471/c0baef01-80b9-4ed5-ac0d-e9e306509e53">

### 1.14 Extensible Dialog Event Features (informative)

The features in Dialog Events are intentionally intended to be extensible.  This specification does not limit the features that can be put into an utterance event or a whisper event.\
\
The current version of this specification mandates only the text feature in each dialog event.  Future versions are likely to support and standardize additional event features such as:\

* _ssml_ to describe how text should be rendered as speech
* _speech_ to send raw speech for output or input.\
\
There are no limitations on the features that are added to a dialog event.  This enables agents to exchange any media that they wish in addition to the text message.\
\
For example, a video feature intended to represent Video Conversational agent communications (i.e. Avatar communications) could be added as shown in Figure 14.  This example is informative only.


<img width="509" alt="Screenshot 2024-01-16 at 9 38 04 PM" src="https://github.com/open-voice-interoperability/docs/assets/101130471/6c98d65c-f9d2-45fe-b1e4-d212249df0fb">

### 1.15 Invite Event

<img width="515" alt="Screenshot 2024-01-16 at 9 38 31 PM" src="https://github.com/open-voice-interoperability/docs/assets/101130471/11d1890a-91d6-49b8-9115-8b5175b5f85e">

Invite events act as an invitation for the target agent to enter the conversation.  They also invite the target agent to take the conversational floor and respond to all utterances from this point onwards.  This object contains just one parameter with the key-name _to_ which contains a single key _url_. There are no optional parameters for the _invite_ event.\
\
It is possible to invite an agent to a conversation without giving it any other events.  This is termed a bare invite as shown in Figure 15.  The recipient of such a bare invitation is being invited to engage with the user without being given any context.  A suitable response would be to speak a greeting and ask how the agent can help.

![Figure16](https://github.com/open-voice-interoperability/docs/assets/101130471/2bf57386-95d1-454c-b31e-561f6de3f0a0)

Invite events will typically be accompanied by additional events.  Figure 16 shows a dialog envelope where the inviting agent tells the user that they are inviting another agent to speak with them.  Then the invite event issues the invitation, accompanied by a whisper which tells the new bot what it is that is being asked of it, and a bye message to indicate that the inviting agent is withdrawing from the conversation.

### 1.16 Bye Event

![byeevent](https://github.com/open-voice-interoperability/docs/assets/101130471/c415e09b-0e9f-47b2-bde7-7df38e9dd6c9)

When an agent wants to leave the conversation it sends a _bye_ event.  This message indicates that the agent is leaving the dialog, and if it currently has control it also relinquishes the floor.   

As with the _invite_ event, the _bye_ event can be accompanied by other events.  The _bye_ event has no parameters.

![figure17b](https://github.com/open-voice-interoperability/docs/assets/101130471/bc3c5a7e-4d78-4ed5-820d-108b2cd1d907)

## Chapter 2. Minimal Behaviors

#### 2.1 Minimal Agent Behaviors

OVON-compliant dialog agents must support each of the following event types in order to be considered fully compliant.\
\
The minimal behavior expected from an OVON-compliant dialog agent in response to these event types is as follows:

* utterance events - spoken or written natural language
  * _utterance_  -  Answer the speaker with an utterance in return.
  * _whisper_ - Ignore it if the contents cannot be understood.

* agent control events  - structure control messages
  * _invite_ - Say 'hello' and respond to any whisper utterances. 
  * _bye_ - Ignore this message. It is intended for the conversation floor manager.

#### 2.2 Minimal Conversation Floor Manager Behaviors
OVON-compliant conversation floor managers (including host browsers) agents must support each of the following event types in order to be considered fully compliant.\
\
The minimal behavior expected from an OVON-compliant conversation floor manager in response to these event types is as follows:

* _utterance events_ - spoken or written natural language
  * _utterance_  -  Send this _utterance_ to all current conversants apart from the sender. 
  * _whisper_ - Send this _whisper_ to the current focal agent.

* _agent control events_  - structured control messages
  * _invite_ -  Forward the _invite_ to the designated agent and set this agent to be the current focal agent.    
  * _bye_ - Remove this agent from the current register of active conversants.

The conversation floor manager retains ultimate responsibility for deciding which conversants are currently considered to be active in the conversation and which agent is the current focal agent.  This can for example include removing agents from the conversation if they do not respond within an allotted time, inviting trusted agents to the conversation to deal with exceptions, and deciding when to terminate a conversation with the user.  

## Chapter 3. JSON Envelope Schema

The structure of a JSON dialog envelope is defined below as a JSON Schema.    This file is located at [https://github.com/open-voice-interoperability/lib-interop/tree/main/schemas/conversation-envelope/0.9.0/conversation-envelope-schema.json]

![finalgraph](https://github.com/open-voice-interoperability/docs/assets/101130471/ac019dcd-6e0a-4eea-9a7e-64601f8b8045)

## Chapter 4. References

[1] [https://openvoicenetwork.org/docs/interoperability-of-conversational-assistants/] **Interoperability of Conversational Assistants**\
[2] [https://openvoicenetwork.org/docs/interoperable-dialog-event-object-specification-version-1-0/] **Interoperable Dialog Event Object Specification Version 1.0**\
[3] [https://datatracker.ietf.org/doc/html/rfc9110/] **IETF RFC 9110 HTTP Semantics.**

## Chapter 5. Glossary of Terms

<img width="391" alt="Screenshot 2024-01-16 at 10 50 09 PM" src="https://github.com/open-voice-interoperability/docs/assets/101130471/2170eaf8-c6cf-46ea-81d2-9d31dbb23227">\
<img width="389" alt="Screenshot 2024-01-16 at 10 50 30 PM" src="https://github.com/open-voice-interoperability/docs/assets/101130471/7133d03c-db41-4167-b4ad-ad30affba11d">\
<img width="383" alt="Screenshot 2024-01-16 at 10 50 47 PM" src="https://github.com/open-voice-interoperability/docs/assets/101130471/23fb6896-f61f-41fc-b746-3f6ae0abd22f">

## Chapter 6. Decision Log

This section documents some of the key design decisions that were made by the team during the development of this specification.  It is informative, not normative.

<img width="369" alt="Screenshot 2024-01-16 at 10 55 25 PM" src="https://github.com/open-voice-interoperability/docs/assets/101130471/f1d9c095-073a-4a1c-a3eb-29900c29ebb6">\
<img width="369" alt="Screenshot 2024-01-16 at 10 55 47 PM" src="https://github.com/open-voice-interoperability/docs/assets/101130471/8daca28e-2c35-47ea-8db9-e14a62a8c201">\
<img width="369" alt="Screenshot 2024-01-16 at 10 56 25 PM" src="https://github.com/open-voice-interoperability/docs/assets/101130471/c86a5bfa-7615-4df6-8c14-528b6da20b3b">\
<img width="369" alt="Screenshot 2024-01-16 at 10 56 56 PM" src="https://github.com/open-voice-interoperability/docs/assets/101130471/f7c16143-d0c7-47e8-ae83-976b5d373125">





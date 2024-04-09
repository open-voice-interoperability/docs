<img src="https://github.com/open-voice-interoperability/artwork/blob/main/horizontal/color/Interoperability_Logo_color.png" width="200">

# Interoperable Conversation Envelope Specification Version 0.9.1

The Open Voice Network\
Open Voice Interoperability Initiative - LF AI & Data Foundation\
Architecture Work Group

09 April 2024 \
Draft Version 0.9.1

*_Editor-in-Chief: David Attwater_*\
*_Contributors: Emmett Coin,  Deborah Dahl,  Jim Larson, Allan Wylie,  Rainer Türner and Diego Gosmar_*

## TABLE OF CONTENTS
### CHAPTER 0. SCOPE AND INTRODUCTION
#### &nbsp; 0.1 Document Scope
#### &nbsp; 0.2 Conversation envelopes
#### &nbsp; 0.3 Delegation, Channeling and Mediation
#### &nbsp; 0.4 OVON Event Types
### CHAPTER 1. SPECIFICATION
#### &nbsp; 1.1 Representation
#### &nbsp; 1.2 Protocol
#### &nbsp; 1.3 AAA & Security
#### &nbsp; 1.4 Nomenclature
#### &nbsp; 1.5 Conversation Envelope Object Structure
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
### CHAPTER 7. DOCUMENT CHANGE LOG

*****
### CHAPTER 0. SCOPE AND INTRODUCTION
#### 0.1 Document Scope
This document specifies the format for Open Voice Interoperability Initiative  - LF AI & Data Foundation (OVON) interoperable conversation envelope. The user stories upon which this specification is based can be found in the OVON white paper Interoperability of Conversational Assistants  [1].  

#### 0.2 Conversation Envelopes
The OVON Conversation Envelope is a universal JSON structure whose purpose is to allow human or automatic agents (assistants) to interoperably participate in a conversation.  When coupled with a specific protocol, such as HTTPS (See section 1.2), a dialog agent that can generate and send Conversation Envelopes is capable of inter-operating with any other OVON-compliant agent, regardless of the technology or architecture on which that other agent is based.


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
Each arrow in the diagram denotes a message passing from one agent to another.  Each of these messages will comprise a single conversation envelope.   Each conversant can 'hear' things said to it or 'say' things.  Conversants may also invite other agents to join the conversation and take the conversational floor.


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

In order to facilitate the patterns described above each conversation envelope can carry one or more OVON Events.  OVON Events are broken into two categories.\

* utterance events - spoken or written natural language
  * utterance  - An utterance spoken in the conversation itself by a user or agent
  * whisper - An out-of-band linguistic instruction from one agent to another
* agent control events  - structure control messages
  * invite - A user-agent or assistant is invited to join the conversation.
  * bye -  A user-agent or assistant is leaving the conversation
### CHAPTER 1. SPECIFICATION
#### 1.1 Representation

A conversation envelope will be represented as a JSON [1] object in a string format.  The JSON conversation envelope is expected to be a stand-alone document or object but there is no reason that it cannot be part of a larger JSON document.

#### 1.2 Protocol

JSON was chosen for the OVON conversation envelope as it is an Open and Human Readable Standard format for Data Exchange that is independent of any particular protocol.  Supported protocols and the mechanisms by which two agents agree on a protocol to be used are currently outside the scope of this document.\
\
For the sake of simplicity, it is anticipated that OVON implementations will initially use HTTPS as underlying communication protocol, but could include several other ones currently available (i.e. SIP, Websockets, WebRTC, etc) or any future available ones (i.e, HTTP/3, etc).

#### 1.3 AAA & Security

Authorization, Authentication, Accounting, and Security specifications are outside the scope of this document and will be defined in separate documents. 

#### 1.4 Nomenclature

This specification uses ‘camelCase’ (i.e. no spaces with new words being capitalized) for all nominal property names, for example, eventType and replyTo.  

#### 1.5 Conversation Envelope Object Structure

    {
      "ovon": {

          "schema": {
              "version": "0.9.0"      
              "url": "https://github.com/open-voice-interoperability/lib-interop/tree/main/schemas/conversation-envelope/0.9.0/conversation-envelope-schema.json"
          },

          "conversation": {
              "id": "31050879662407560061859425913208"
          },
    
          "sender": {
              "from": "https://example.com/message-from",
              "reply-to": "https://example.com/reply-message-to"
          },

          "responseCode" : {
              "code": 200
              "description": "Invite request accepted."
          }  
    
          "events": [
              {
                  "eventType": "event type A",
                  "parameters": {
                    "parameter 1" : { parameter 1 values }  
                    … 
                    "parameter n" : { parameter 1 values }  
                  }
              },
              {
                  "eventType": "event type B",
                  "parameters": {
                    "parameter 1" : { parameter N values }
                    … 
                    "parameter n" : { parameter N values }   
                  }
              },
          ]
      }
    }

##### Figure 2. An example of a conversation envelope #####

Figure 2 shows an example of a conversation envelope.  The envelope is wrapped in an ovon key.   This contains five sections:

* schema - the version of the conversation envelope and a schema to validate it against
* conversation - persistent information related to the current dialog
* sender - details of the sender
* responseCode - a response code if this message is in response to another
* events - a list of OVON 'events'

The responseCode section is optional.  All other sections are mandatory.

#### 1.6 Schema Object

    {
      "ovon": {
        ..
        "schema": { "version": "0.9.0" }
        ..
      }
    }

##### Figure 3. Mandatory elements of the _schema_ object.

The _schema_ object specifies the format of the message in this OVON envelope.  It is mandatory. It must contain a valid _version_ number for an OVON envelope.  Figure 3 shows the minimal information that must be present in an OVON-compliant envelope.

    {
      "ovon": {
        ..
        "schema": {
          "version": "0.9.0"      
          "url": "https://github.com/open-voice-interoperability/lib-interop/tree/main/schemas/0.9.0/conversation-envelope-schema.json"
        }
        ..
      }
    }

##### Figure 4. Other optional elements in the _schema_ object.

The schema for the version of the envelope specification can be found in **Chapter 3. Schema**.  Figure 4 shows an optional _url_ that may also be included which should point to the correct version of that JSON schema.

#### 1.7 Conversation Object

    {
      "ovon": {
        …        
        "conversation": { "id": "jk31050879662407560061859425913208" },
        … 
      }
    }

##### Figure 5. Mandatory elements of the _conversation_ object.

The conversation object carries persistent information related to the current conversation.  It should be passed on from agent to agent.

As shown in figure 5, the conversation section contains just one piece of mandatory information - the id of the conversation.  The id  should be a unique identifier for the current conversation with the user.  Persistent information relating to this current conversation can be keyed to this id.   The id  can be any arbitrary length character sequence that can be represented as a string in JSON.

    {
      "ovon": {
        …        
        "conversation": { 
          "id": "jk31050879662407560061859425913208"


          "persistentState": {
            "uniqueKey1": { .. object .. },
            "uniqueKey2": { .. object .. } 
          }
        },
        … 
      }
    }

##### Figure 6. Optional elements in the _conversation_ object.

Figure 6 shows other additional elements in the conversation object. The _persistentState_ is optional and consists of key-value pairs where the values can be any arbitrary JSON object.  The purpose of this is to enable agents to persist information that is important to maintaining internal state in the conversation.  Any message sender can add a new unique key-value pair.  All message handlers should persist the data in this section when replying to a message.  Agents are encouraged to remove key-value pairs from the message that are specific to them when sending a 'bye' event.    There are currently no restrictions currently placed on the content of persistent objects.   Privacy and security issues apply here. It is suggested that the data in these sections is encrypted but this is not mandatory.  Consideration should also be given to the size of any objects in this section as this might affect the downstream performance of the remaining conversation.

#### 1.8 Sender Object

    {
      "ovon": {
        …        
        "sender": {
          "from": "https://example.com/message-from"
        },
        … 
      }
    }

##### Figure 7. Mandatory elements of the _sender_ object

Figure 7 shows the mandatory elements in the sender object.  The from object is a string and should be a valid URI.  

    {
      "ovon": {
        …        
        "sender": {
          "from": "https://example.com/message-from",
          "replyTo": "https://example.com/reply-message-to"
        },
        … 
      }
    }

##### Figure 8. Optional elements in the _sender_ object.

Figure 8 shows other supported elements in the sender object.   The _replyTo_ object is also a string and must also be a valid URI.  The _replyTo_ address should be the address of an OVON-compliant assistant or host-browser and should be capable of receiving messages in OVON Envelope format.

#### 1.9 Response Code Object

    {
      "ovon": {
        …        
        "responseCode": {
          "code": 200
        }
        … 
      }
    }

##### Figure 9. Mandatory elements of the optional _responseCode_ object.

Figure 9 shows the mandatory elements of the optional _responseCode_ object.  This is an integer value and is modeled on the semantics defined in [RFC 9110 HTTP Semantics](https://docs.google.com/document/d/1ld0tbGhQEOcZ4toCi0R4AEIWlIET8PgF1b-xKhtwsm0/edit?userstoinvite=jim42%40larson-tech.com&sharingaction=manageaccess&role=writer#bookmark=id.w6ym7j9punzt).

    {
      "ovon": {
        …        
        "responseCode": {
          "code": 200
          "description": "Invite request accepted."
        }
        … 
      }
    }

##### Figure 10.  Other supported elements in the responseCode object.

Figure 10 shows Other supported elements in the _responseCode_ object.  An optional description can be added to the response code.  This is a text string. There are no constraints on the content of this string which is intended to convey information for audit and logging purposes.

#### 1.10 Events Object

    {
      ovon {
        ..
        "events": [
          {
            "eventType": "event type A",
            "parameters": {
              "parameter 1" : { parameter 1 values }  
              … 
              "parameter n" : { parameter 1 values } 
            } 
          },
          {
            "eventType": "event type B",
            "parameters": {
              "parameter 1" : { parameter N values }
              … 
              "parameter n" : { parameter N values }   
            }
          },
        ]
      }
    }

##### Figure 11. The _events_ object

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

    {
      "ovon" {
        ..
        "events": [
          {
            "eventType": "utterance",
            "parameters": {
              "dialogEvent": {
                "speakerId": "b5y09lky5KU5",
                "span": { "startTime": "2023-06-14 02:06:07+00:00" },
                "features": {
                  "text": {
                    "mimeType": "text/plain",
                    "tokens": [ { "value": "I need my repeat medication" } ]
                  }
                }
              }
            }
          },
          ..
        ]
      }
    }

Figure 12. Mandatory elements of the OVON _utterance_ event.

Figure 12 shows the structure of an event with the _eventType_ of _utterance_.  This object contains just one parameter with the key-name _dialogEvent_.

OVON events of this type are sent whenever a user or an assistant takes a dialog act.  The value of the _dialogEvent_ dictionary key must contain a valid dialog event object as specified in [Interoperable Dialog Event Object Specification Version 1.0](https://docs.google.com/document/d/1ld0tbGhQEOcZ4toCi0R4AEIWlIET8PgF1b-xKhtwsm0/edit?userstoinvite=jim42%40larson-tech.com&sharingaction=manageaccess&role=writer#bookmark=id.mnvmxlp2vaay ).
Figure 12 shows the structure of an event with the eventType of utterance.  This object contains just one parameter with the key-name dialogEvent.

OVON events of this type are sent whenever a user or an assistant takes a dialog act.  The value of the dialogEvent dictionary key must contain a valid dialog event object as specified in Interoperable Dialog Event Object Specification Version 1.0.   

The following sections describe the features that are currently supported in the utterance dialog event.   

Additional keys and mime-types are permitted.  Compliant OVON dialog agents do not need to respond to any unsupported keys in the dialog event..   

The following sections describe the _features_ that are currently supported in the _utterance_ dialog event.   

Additional keys and mime-types are permitted.  Compliant OVON dialog agents do not need to respond to any unsupported keys in the dialog event.

##### 1.12.1 Utterance Text Feature

The _text_ feature is **mandatory** in all _utterance_ dialog events.

|parameter|Description|
|-|-|
|_mimeType_|text/plain
|_speakerId_|The _speakerId_ should be a unique identifier to the speaker that was or will be perceived as the social actor in the conversation.  Agents that are channeling utterances from other agents or speakers should keep the _speakerId_ of the original speaker.   This is one of the main distinctions between channeling and mediation.  It is the responsibility of the agent that generates the event to decide which _speakerId_ to attach to the event.
|_value_|Any number of values are allowed as strings in the _tokens_ section.  When concatenated together the _tokens_ should represent the orthographic representation of the utterance.
|_valueUrl_|Any number of value URLs are allowed in the tokens section.  These URLs should locate content of type 'text/plain' and when downloaded and concatenated together the _tokens_ should represent the orthographic representation of the utterance.

### 1.13 Whisper Events

    {
      "ovon" {
        ..
        "events": [
          {
            "eventType": "whisper",
            "parameters": {
              "dialogEvent": {
                "speakerId": "agent08kkmy6gt",
                "span": { "start-time": "2023-06-19 03:09:07+00:00" },
                "features": {
                  "text": {
                    "mimeType": "text/plain",
                    "tokens": [ { "value": "explain the side effects of citalopram in less than 200 words" } ]
                  }
                }
              }
            }
          },
          ..
        ]
      }
    }

##### Figure 13. Mandatory elements of the OVON _whisper_ event.

Figure 13 shows the structure of an OVON event with the _eventType_ of _whisper_.  This object contains just one parameter with the key-name _dialogEvent_.\
\
OVON events of this type are sent whenever an assistant wants to send a natural language instruction or request to another agent.   _whisperUtterance_ events are identical in format to _utterance_ events but they are not to be directly voiced in the dialog.\
\
This object can contain any valid dialog event objects as specified in [Interoperable Dialog Event Object Specification Version 1.0](https://docs.google.com/document/d/1ld0tbGhQEOcZ4toCi0R4AEIWlIET8PgF1b-xKhtwsm0/edit?userstoinvite=jim42%40larson-tech.com&sharingaction=manageaccess&role=writer#bookmark=id.mnvmxlp2vaay).   

##### 1.13.1 Whisper Text Feature

The _text_ feature is **mandatory** in all _whisper_ dialog events.

|parameter|Description|
|-|-|
|_mimeType_|text/plain
|_speakerId_|The _speakerId_ should be a unique identifier of the agent or speaker that generated the request.  In the case where the event is simply a forwarded version of a user utterance, this event should be given the _speakerId_ of the forwarding agent not the original speaker.
|_value_|Any number of values are allowed as strings in the _tokens_ section.  When concatenated together the _tokens_ should represent the orthographic representation of the utterance.
|_valueUrl_|Any number of value URLs are allowed in the tokens section.  These URLs should locate content of type 'text/plan' and when downloaded and concatenated together the _tokens_ should represent the orthographic representation of the utterance.


### 1.14 Extensible Dialog Event Features (informative)

The features in Dialog Events are intentionally intended to be extensible.  This specification does not limit the features that can be put into an utterance event or a whisper event.

The current version of this specification mandates only the text feature in each dialog event.  Future versions are likely to support and standardize additional event features such as:

* _ssml_ to describe how text should be rendered as speech
* _speech_ to send raw speech for output or input.

There are no limitations on the features that are added to a dialog event.  This enables agents to exchange any media that they wish in addition to the text message.  For example, a video feature intended to represent Video Conversational agent communications (i.e. Avatar communications) could be added as shown in Figure 14.  This example is informative only

      "features": {
      ...
      "video": {
      "mimeType": “video/mpeg”,
      "tokens": [
        {
          "valueUrl": http://localhost/xyz1234.m4a
        }
      ]
      },
      ...
      }

#### Figure 14. Example video feature, which at present would be considered a custom feature.

### 1.15 Invite Event

    "ovon": {
        "schema": {
          "version": "0.9.0"      
        },
        "conversation": {
          "id": "someUniqueIdCreatedByTheFirstParticipant"
        },
        "sender": {
            "from": "https://someBotThatOfferedTheInvite.com"
        },
        "responseCode" : { "code": 200 },
        "events": [
            {
                "eventType": "invite",
                "parameters": {
                    "to": {
                        "url": "https://someBotThatIsBeingInvited.com"
                    }
                }
            }
        ]
    }


##### Figure 15. Mandatory elements of the _invite_ object shown as a 'bare invite'

Invite events act as an invitation for the target agent to enter the conversation.  They also invite the target agent to take the conversational floor and respond to all utterances from this point onwards.  This object contains just one parameter with the key-name _to_ which contains a single key _url_. There are no optional parameters for the _invite_ event.\
\
It is possible to invite an agent to a conversation without giving it any other events.  This is termed a bare invite as shown in Figure 15.  The recipient of such a bare invitation is being invited to engage with the user without being given any context.  A suitable response would be to speak a greeting and ask how the agent can help.

    {
      "ovon": {
        "schema": {
          "version": "0.9.0"      
        },
        "conversation": {
          "id": "31050879662407560061859425913208"
        },
        "sender": {
          "from": "https://someBotThatOfferedTheInvite.com"
        },
        "responseCode" : { "code": 200 },
        "events": [
          {
            "eventType": "utterance",
            "parameters": {
              "dialogEvent": {
                "speakerId": "humanOrAssistantID",
                "span": { "startTime": "2023-06-14 02:06:07+00:00" }
                "features": {                         
                  "text": {
                    "mimeType": "text/plain",
                    "tokens": [ { "value": "I'll pass you over to my -weather."  } ]
                  }
                }
              }
            }
          },
          {
            "eventType": "invite",
            "parameters": {
              "to": { "url": "https://my-weather-bot.com" }
            }
          },
          {
            "eventType": "whisper",
            "parameters": {
              "dialogEvent": {
                "speakerId": "humanOrAssistantID",
                "span": { "startTime": "2023-06-14 02:06:07+00:00" },
                "features": {
                  "text": {
                    "mimeType": "text/plain",
                    "tokens": [ { "value": "What is the weather in Detroit Michigan?" } ] 
                  }
                }
              }
            }
          },
          {
            "eventType" : "bye"
          }
        ]
      }
    }

##### Figure 16. A typical dialog envelope for a delegation invite, including a voiced transfer prompt.

Invite events will typically be accompanied by additional events.  Figure 16 shows a conversation envelope where the inviting agent tells the user that they are inviting another agent to speak with them.  Then the invite event issues the invitation, accompanied by a whisper which tells the new bot what it is that is being asked of it, and a bye message to indicate that the inviting agent is withdrawing from the conversation.

### 1.16 Bye Event

    {
      "ovon": {
      "schema": {
        "version": "0.9.0"      
      },
      "conversation": {
          "id": "31050879662407560061859425913208"
        },
        "sender": {
          "from": "https://someBotThatOfferedTheBye.com"
        },
        "responseCode" : { "code": 200 },
        "events": [
          {
            "eventType" : "bye"
          }
        ]
      }
    }

Figure 17A. A minimal _bye_ envelope detaching an agent from a conversation.

When an agent wants to leave the conversation it sends a _bye_ event.  This message indicates that the agent is leaving the dialog, and if it currently has control it also relinquishes the floor.   

As with the _invite_ event, the _bye_ event can be accompanied by other events.  The _bye_ event has no parameters.

    "ovon": {
      "schema": {
        "version": "0.9.0"      
      },
      "conversation": {
        "id": "31050879662407560061859425913208"
      },
      "sender": {
        "from": "https://someBotThatOfferedTheBye.com"
      },
      "responseCode" : 200,
      "events": [
        {
          "eventType": "utterance",
          "parameters": {
            "dialogEvent": {
              "speakerId": "AssistantID",
                "span": { "startTime": "2023-06-14 02:06:07+00:00" },
                  "features": {
                    "text": {
                      "mimeType": "text/plain",
                      "tokens": [ { "value": "Thank you! I am glad I could help."  } ]
                    }
                  }
                }
              }
            }
          },
          {
            "eventType" : "bye",
          }
        }
      ]
    }

Figure 17B. A _bye_ event with a voiced farewell.

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

The structure of a JSON conversation envelope is defined below as a JSON Schema.    This file is located at [https://github.com/open-voice-interoperability/lib-interop/tree/main/schemas/conversation-envelope/0.9.0/conversation-envelope-schema.json]

    {
      "$id": "https://github.com/open-voice-interoperability/lib-interop/tree/main/schemas/conversation-envelope/0.9.0/conversation-envelope-schema.json",
      "$schema": "https://openvoicenetwork.org/schema",
      "description": "A representation of a 'dialogue envelope’ - the universal data exchange format for an OVON compliant interoperable agent.",
      "type": "object",
      "required":  [ "ovon" ],
      "properties": {
        "ovon": {
          "required":  ["schema" , "conversation", "sender", "events" ],
          "properties": {


            "schema": {
              "type": "object",
              "required":  ["version" ],
              "properties": {
                "version": {
                  "type": "string",
                  "description": "The version of the dialog envelope specification matching this envelope."
                },
                "url": {
                  "$ref": "#/$defs/url"
                }
              }
            },


            "conversation": {
              "required":  ["id" ],
              "properties": {
                  "id": {
                    "type": "string",
                    "description": "Unique identifier for the current conversation with the user."
                  },
                  "persistentState": {
                      "type": "object",
                      "description": "Container for an arbitrary set of key value pairs to be persisted.",
                      "additionalProperties" : {
                          "type" : "object"
                      }
                  }
              }
            },


            "sender": {
              "type": "object",
              "required":  ["from"],
              "properties" : {
                "from": {
                  "$ref": "#/$defs/uri",
                  "description": "The URI of the sender of the envelope."
                },
                "replyTo": {
                  "$ref": "#/$defs/uri",
                  "description": "The URI to direct responses to the envelope."
                }
              }


            },


            "responseCode": {  
              "type": "object",
              "required":  ["code"],
              "properties" : {
                "code" : {
                  "type" : "integer"
                },
                "description" : {
                  "type" : "string"
                }            
              }


            },


            "events": {  
              "type": "array",
              "items" : {
                "type" : "object",
                "properties" : {
                  "eventType": {
                    "type" : "string",
                    "enum": ["utterance", "whisper", "invite", "bye"]
                  }
                },
                "allOf": [
                  {
                    "if": {
                      "properties": {
                        "eventType": { "const": "utterance" }
                      },
                      "required": ["eventType"]
                    },
                    "then" : {
                      "properties": {
                        "parameters": {
                          "description": "Parameters for the utterance event.",
                          "type" : "object",
                          "properties" : {
                            "dialogEvent": {
                              "ref": "https://github.com/open-voice-interoperability/lib-interop/tree/main/schemas/dialog-event/1.0.1/dialog-event-schema.json"
                            }
                          },  
                          "additionalProperties": false
                        }
                      }
                    }
                  },


                  {
                    "if": {
                      "properties": {
                        "eventType": { "const": "whisper" }
                      },
                      "required": ["eventType"]
                    },
                    "then" : {
                      "properties": {
                        "parameters": {
                          "description": "Parameters for the whisper event.",
                          "type" : "object",
                          "properties" : {
                            "dialogEvent": {
                              "ref": "https://github.com/open-voice-interoperability/lib-interop/tree/main/schemas/dialog-event/1.0.1/dialog-event-schema.json"
                            }
                          },
                          "additionalProperties": false
                        }
                      }
                    }
                  },


                  {
                    "if": {
                      "properties": {
                        "eventType": { "const": "invite" }
                      },
                      "required": ["eventType"]
                    },
                    "then" : {
                      "properties": {
                        "parameters" : {
                          "description": "Parameters for the invite event.",
                          "type": "object",
                          "properties": {      
                            "to"  : {
                              "type" : "object",
                              "properties": {
                                "url" : {
                                  "$ref": "#/$defs/url",
                                  "description": "The URL of the agent to be invited."
                                }
                              },
                              "additionalProperties": false
                            }        
                          },
                          "additionalProperties": false
                        }
                      }
                    }
                  },


                  {
                    "if": {
                      "properties": {
                        "eventType": { "const": "bye" }
                      },
                      "required": ["eventType"]
                    },
                    "then" : {
                      "properties": {
                        "parameters" : {
                          "description": "Parameters for the bye event.",
                          "type": "object",
                          "properties": { },
                          "additionalProperties": false
                        }
                      }
                    }
                  }


                ]
              }
            }


          }
        }
      },


      "$defs": {
        "url": {
          "type": "string",
          "description": "Any valid URL"
        },
        "uri": {
          "type": "string",
          "description": "Any valid URI"
        }
      }
    }


## Chapter 4. References

[1] [https://openvoicenetwork.org/docs/interoperability-of-conversational-assistants/] **Interoperability of Conversational Assistants**\
[2] [https://openvoicenetwork.org/docs/interoperable-dialog-event-object-specification-version-1-0/] **Interoperable Dialog Event Object Specification Version 1.0**\
[3] [https://datatracker.ietf.org/doc/html/rfc9110/] **IETF RFC 9110 HTTP Semantics.**

## Chapter 5. Glossary of Terms

|Term|Definition|
|-|-|
|channeling|A conversational assistant acts as an intermediary between a user and another conversational assistant, passing through requests and returning responses. The intermediary assistant will often make no modifications to the requests or the responses but may do so, for example increasing the speed or volume or even translating between languages.   
|confidence |A number representing a measure of the confidence that the information contained in the associated value is ‘correct’.
|conversation floor manager|A component that manages which conversants are active in a given conversation and which agent is the current focal agent. 
|delegation|A conversational assistant passes control and management of the dialog to another conversational assistant, along with a negotiated amount of context and dialog history. 
|derived specification|A derivative standard, built upon this specification, which defines a specific way to use a dialog event in a particular context.
|dialog event |A linguistic event in a spoken or written monologue or dialog between two or more speakers.  
|ialog event feature |A layer of information of a certain type associated with the dialog event.
|dialog event object|A JSON object encoding a dialog event as part of an interface to a natural language component in a text or speech processing solution or dialog system.
|dialog event object id |The unique identifier of the dialog event object
|dialog event span|Identifies the span of time for the dialog event
|dialog event speaker id|A unique id of the human or machine associated with content of the dialog event
|envelope|Structured data (hierarchical structure of data names and values) used to describe events to be communicated by a speaker or agent in a conversation.  Envelopes can contain multiple ordered events to support simple protocols such as HTTPS where servers can only respond with a single response to a single request.
|feature mime type|The type of token values in a specific dialog event feature.
|feature token|A representation of part of the information that makes up a feature value.  
|focal agent|The agent that is currently tasked with responding to agent input.
|JSON path |An unambiguous reference to part of a JSON object.
|language code|A code representing the language (e.g., American English, British English, New Norsk, etc.)
|mandatory elements|The mandatory elements that are required in the various elements of the envelope in order to be OVON compliant.  These elements are enough to allow basic no-frills interoperation between agents.
|mediation|A conversational assistant acting as a user, has a conversation with another conversational assistant behind the scenes using dialog – semantic or linguistic – interfaces to achieve a goal and return to the user.
|stand-off-annotation |A method of feature layering and cross-referencing that permits the different features of an utterance or linguistic event to be kept separate but also linked logically and temporally with each other.  
|token encoding|The specific encoding used to represent text in a token.
|token link|A link from one token in a feature to part or all of another token in a feature used to implement stand-off annotation.
|token object|A JSON object representing a feature token which defines the value of the feature and other associated information such as its span and how it links to other feature tokens.
|token span|Identifies the span of time for an individual token object
|user proxy agent|A component that implements converts between a human user interacting via certain media into OVON-compliant dialog envelopes and renders utterances from other conversants back to the human user in the appropriate media.

## Chapter 6. Decision Log

This section documents some of the key design decisions that were made by the team during the development of this specification.  It is informative, not normative.

|Issue Topic|Issue/Decision|
|-|-|
|Using JSON rather than other representations.|_Question:_ Why JSON and not other formats? (i.e. XML). </br>_Answer:_ JSON is Open and Human Readable Standard format for Data Exchange well suited for API designed for communications. JSON is a data format that extends from JavaScript. It does not use tags, which makes it more compact and easier to read for humans. JSON can represent the same data in a smaller file size for faster data transfer.  Furthermore JSON parsing is safer than XML, which leads JSON as the preferred format for secure communications among Conversational agents.
|Addressing Multiple Targets|_Question_: How might envelopes address more than one target? We are trying to use the envelopes to allow one agent to send events to more than one destination.</br>_Answer:_ This needs to be addressed in subsequent versions of the specification.
|Go-Back|_Question:_ How can control be passed back to a previous agent?</br>_Answer:_ There is no explicit 'go-back' event or concept.   Control can be explicitly passed back to a previous agent through an explicit invite from the current focal agent.  Alternatively, the conversation floor manager can generate an invite to bring a 'favored' agent into the conversation, for example where the current focal agent is not meeting expectations for conversational interaction.  In a future version of this document, we may consider explicitly supporting requests from one agent to ‘go-back’ to the previous focal agent.
|Control flow and exceptions|_Question:_ How do we handle timeouts and what is responsible for them?</br>_Answer:_ There is no mandated behavior regarding timeouts and exceptions.   Each conversation floor manager can choose the behavior in this regard.  Later incarnations of this specification are likely to address common exception conditions where standardization of behavior becomes important for interoperability.
|Host Browsers and User Proxies|_Question:_ Is the host browser a user proxy or does it have unique responsibilities for control?</br>_Answer:_ The host browser is a close coupling of a user proxy and a conversation floor manager.  It is anticipated that user proxies and floor managers can be implemented separately and communicate using dialog envelopes.  This has not been fully proven yet and there are likely to be additional control structures needed to fully support the separation.  We anticipate that early adoption of this standard will require a unified host browser.
|responseCodes|_Question:_ Are responseCodes optional or mandatory</br>_Answer:_ It was agreed that this section is optional in the envelope.|
|Dialog Status Codes|_Question_: Do we need more specific dialog status codes that are tied to the discourse. Examples include adding a discourse status to a 'bye' event. e.g. "discourseStatus" : "closed|failed|open|paused|uncategorized"</br>_Answer:_ Parked for future versions
|location of schemas|_Question:_ Where should we publish the schema?</br>_Answer:_ Let's use https://github.com/open-voice-interoperability/lib-interop/tree/main/schemas, but add a new subfolder for each version (e.g. "0.9.0" for the current schemas).
|replyTo Optional|_Question_: Should replyTo be optional or mandatory?</br>_Answer:_ We agreed that it would be optional and if not present then replies would be addressed to the sender.  In the HTTPS model, even the sender is redundant and may only be useful for logging.   
|Returning Control|_Question_:Have we fully addressed what happens on handing back control? Need to discuss control on 'bye' and 'invite'.</br>_Answer:_ Minimal behaviors for 'bye' and 'invite' are defined in this specification with the introduction of a 'focal agent'.  Subsequent incarnations of this specification are likely to formalize these further in order to support multi-participant conversations.
|How are conversations started?|_Question:_ There is nothing in the envelope spec to allow a conversant to initiate a conversation.  How do conversations start?</br>_Answer:_ It is envisaged that a 'start' event (or some similar name) will be added in later versions of the specification.  Such an event would for example come from the proxy agent to the conversation floor manager and result in the creation of a conversationId.   It is also possible that an Invite could be used for this purpose and a new event is not needed.  For now, it is assumed that implementation of the current version of the specification will have a combined proxy agent and conversation floor manager and the initiation of a dialog will be a proprietary feature of that combined component.
|Interruptions and Univiting agents.|_Question:_ How does one conversation stop the operation of another conversant?  For example, how might a user tell an agent to be quiet?  </br>_Answer:_ We anticipate that later versions of this specification will require more explicit floor management and control features, including the ability to 'uninvite' conversants.    In the meantime, interruption of real-time streaming of audio to the user can only happen in the user-proxy-agent, for example under the control of a barge-in mechanism.  The conversation floor manager can also choose to 'uninvite' an agent by simply stopping communication with it.  That agent will need to infer that it is no longer part of the conversation by the use of heuristic time-outs.
|Multi-conversant support|_Question:_ How might this specification be extended to support multiple conversant support?   The most pressing need for this is to enable a 'favored' agent to be able to respond to interruption messages from the user.   After that, use cases could include having multiple users on a single conversation and multiple agents competing for the floor - i.e. a conference call involving multiple users and/or agents.</br>_Answer:_  This specification fully anticipates that the conversation floor manager could support multiple conversants.   The group has identified the following features that will probably be needed to support this.</br> - Uninviting conversants (See above)</br> - Adding targetSpeakerID to dialog events (to allow one conversant to specifically address another specific conversant).</br> - Adding a speakers section to the conversation object to keep track of the speakers in the conversation including their speakerIDs, URLs, displayNames, and spokenNames.

### Chapter 7. Document Change Log

|Version|Release Date|Description|
|-|-|-|
|0.9.0|2024.01.16|Initial Published Draft|
|0.9.1|2024.04.16|- Replaced code example images with text</br>- Added PersistentState which was accidentally omitted from 0.9.0| 

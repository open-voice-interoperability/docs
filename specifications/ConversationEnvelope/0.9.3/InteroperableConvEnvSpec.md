<img src="https://github.com/open-voice-interoperability/artwork/blob/main/horizontal/color/Interoperability_Logo_color.png" width="200">

# Interoperable Conversation Envelope Specification Version 0.9.3

The Open Voice Network\
Open Voice Interoperability Initiative - LF AI & Data Foundation\
Architecture Work Group

26 November 2024 \
Draft Version 0.9.3
Status: Published

*_Editor-in-Chief: David Attwater_*\
*_Contributors: Emmett Coin,  Deborah Dahl,  Jim Larson, Allan Wylie,  Rainer Türner and Diego Gosmar_*

## TABLE OF CONTENTS
### CHAPTER 0. SCOPE AND INTRODUCTION
#### &nbsp; 0.1 Document Scope
#### &nbsp; 0.2 Conversation envelopes
#### &nbsp; 0.3 Delegation, Channeling and Mediation
#### &nbsp; 0.4 Discovery
### CHAPTER 1. SPECIFICATION
#### &nbsp; 1.1 Syntax and Protocol
#### &nbsp; 1.2 AAA & Security
#### &nbsp; 1.3 Nomenclature
#### &nbsp; 1.4 Conversation Envelope Object Structure
#### &nbsp; 1.5 Schema Object
#### &nbsp; 1.6 Conversation Object
#### &nbsp; 1.7 Sender Object
#### &nbsp; 1.8 Events Object
#### &nbsp; 1.9 Event-Types
#### &nbsp; 1.10 Utterance Events
##### &nbsp; &nbsp; &nbsp;  1.10.1 Utterance Text Feature
#### 1.11 Whisper Events
##### &nbsp; &nbsp; &nbsp;  1.11.1 Whisper Text Feature
#### 1.12 Extensible Dialog Event Features (Informative)
#### 1.13 Invite Event
#### 1.14 Bye Event
#### 1.15 requestManifest Event
#### 1.16 publishManifest Event
#### 1.17 findAssistant Event
#### 1.18 proposeAssistant Event

### CHAPTER 2. MINIMAL BEHAVIORS
#### &nbsp; 2.1 Minimal Assistant Behavior
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
Each arrow in the diagram denotes a message passing from one agent to another.  Each of these messages will comprise a single conversation envelope.   Each conversant can 'hear' things said to it or 'say' things.   They can also 'whisper' to each other behind the scenes.   Conversants may also 'invite' other agents to join the conversation or they might ask other agents if they are capabale of a certain activity or would like recommend another agent for a certain task.

#### 0.3 Delegation, Channeling and Mediation

The conversational floor manager is in control of which conversants are engaged in a conversation at any given moment.\
\
To start a conversation, the proxy agent invites a specific dialog agent (e.g. Dialog Assistant A) to take part in a conversation with the user.   Once engaged, a dialog agent receives a dialogue envelope containing each user utterance and sends an utterance back to the user in response.  Once the assistant is done with the interaction they can simply end the interaction or invite another agent to enter the conversation (e.g. Dialogue Assistant B in the diagram).   The change of control of a dialog between two agents is called 'delegation'.\
\
Dialog Assistants may also engage the services of another dialog agent behind the scenes to assist with the conversation (e.g. Assistant A engages the services of target agent C to support it in its interaction with the user.)    The controlling agent might choose to pass user utterances unaltered to the target agent and may in return pass the target agent's responses unaltered to the user.  This pattern is termed 'channeling'. It is in many ways functionally equivalent to delegating the conversation but the channeling agent is also able to take, intervene or override the contribution of the agent to which the conversation is being channeled.  While channeling does not change the conversational intent of the utterance, it may change how the content is rendered.  For example, increase the volume for the hard of hearing, decrease the speed of presentation for the cognitively challenged or for non-native speakers, change the voice characteristics of the presentation, or change the language, or change visual characteristics if these are present.\
\
Alternatively, the intermediate agent may reformulate the user's utterances and/or the target agent's utterances, holding whole conversations behind the scenes in order to achieve a goal.  This pattern is termed 'mediation'.  The mediation pattern also may be particularly relevant in the case where the target agent is in fact another human user (e.g. where an autonomous agent acts on behalf of a user to book an appointment with a doctor or restaurant).\
\
There is no limit to the depth of a channeling or mediation chain and delegation can happen at any level in such a chain.  Any agent that is hosting a mediated or channeled conversation will need to perform the functions of a conversation floor manager to those agents it is hosting.\
\
The patterns described above allow for conversation between one user and multiple agents where it is clear which agent has the conversational floor at any given moment.  As noted the channeling and mediation pattern do allow for more than one conversational agent to be involved in the conversation at the same time.\
\
It is envisaged that later extensions to this specification will also support simultaneous multi-party conversation where multiple users and agents may be listening to the conversation simultaneously and take turns to speak or even speak over one another.

### 0.4 Discovery

Agents can ask other agents if they are able to satisfy a certain enquiry or whether they can recommend another agent for the task.  This pattern is called 'discovery'.  The initiating agent asks another agent to 'find' an assistant and 'whispers' details of the task to this agent.  The recipient can then respond by:

- Proposing themself for the task (i.e. 'accept' the request to do a task)
- Proposing one or more agents for the task with a rating (i.e. act as a discovery agent)
- Proposing one or more agents to help 'find' an agent for the task (i.e. recommend another discovery agent)
- Proposing no agents (i.e. the agent cannot do the task or recommend any other agent)

The requesting agent can then choose to invite the proposed agent to the conversation, or simply speak directly to the proposed agent if they already party to the conversation,

By combining this discovery mechanism with the delegation and channelling patterns mentioned above rich patterns of agent interaction can emerge. Some agents can specialize as 'discovery agents' whose only role is to provide recommendations of other agents. This provides the conversational equivalent of a web search.  Agents can also recommend themselves for some enquiries and recommend other agents for others. This allows, for example, for a primary assistant to perform day to day tasks and recommend other agents for less common tasks. Agents can ask one or more agents to assist with this search who in turn can ask other agents. 

### CHAPTER 1. SPECIFICATION

#### 1.1 Syntax and Protocol

A conversation envelope will be represented as a JSON [1] object in a string format.  The JSON conversation envelope is expected to be a stand-alone document or object but there is no reason that it cannot be part of a larger JSON document.

JSON was chosen for the OVON conversation envelope as it is an Open and Human Readable Standard format for Data Exchange that is independent of any particular protocol.  Supported protocols and the mechanisms by which two agents agree on a protocol to be used are currently outside the scope of this document.\
\
For the sake of simplicity, it is anticipated that OVON implementations will initially use HTTPS as underlying communication protocol, but could include several other ones currently available (i.e. SIP, Websockets, WebRTC, etc) or any future available ones (i.e, HTTP/3, etc).

#### 1.2 AAA & Security

Authorization, Authentication, Accounting, and Security specifications are outside the scope of this document and will be defined in separate documents. 

#### 1.3 Nomenclature

This specification uses ‘camelCase’ (i.e. no spaces with new words being capitalized) for all nominal property names, for example, _eventType_ and _persistentState_.  

#### 1.4 Conversation Envelope Object Structure

    {
      "ovon": {

          "schema": {
              "version": "0.9.2"      
              "url": "https://github.com/open-voice-interoperability/docs/tree/main/schemas/conversation-envelope/0.9.2/conversation-envelope-schema.json"
          },

          "conversation": {
              "id": "31050879662407560061859425913208"
          },
    
          "sender": {
              "from": "https://example.com/message-from"
          },
    
          "events": [
              {
                  "to" : "intended recipient A",
                  "eventType": "event type A",
                  "parameters": {
                    "parameter 1" : { parameter 1 values },  
                    … 
                    "parameter n" : { parameter n values }  
                  }
              },
              {
                  "to" : "intended recipient B",
                  "eventType": "event type B",
                  "parameters": {
                    "parameter 1" : { parameter 1 values },
                    … 
                    "parameter n" : { parameter n values }   
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
* events - a list of OVON 'events'

The responseCode section is optional.  All other sections are mandatory.

#### 1.5 Schema Object

    {
      "ovon": {
        ..
        "schema": { "version": "0.9.2" }
        ..
      }
    }

##### Figure 3. Mandatory elements of the _schema_ object.

The _schema_ object specifies the format of the message in this OVON envelope.  It is mandatory. It must contain a valid _version_ number for an OVON envelope.  Figure 3 shows the minimal information that must be present in an OVON-compliant envelope.

    {
      "ovon": {
        ..
        "schema": {
          "version": "0.9.2",   
          "url": "https://github.com/open-voice-interoperability/docs/tree/main/schemas/conversation-envelope/0.9.2/conversation-envelope-schema.json"
        }
        ..
      }
    }

##### Figure 4. Other optional elements in the _schema_ object.

The schema for the version of the envelope specification can be found in **Chapter 3. Schema**.  Figure 4 shows an optional _url_ that may also be included which should point to the correct version of that JSON schema.

#### 1.6 Conversation Object

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
          "id": "jk31050879662407560061859425913208",

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

#### 1.7 Sender Object

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

#### 1.8 Events Object

    {
      ovon {
        ..
        "events": [
          {
            "to" : "https://intended recipient A",
            "eventType": "event type A",
            "parameters": {
              "parameter 1" : { parameter 1 values },  
              … 
              "parameter n" : { parameter n values } 
            },
            "private" : false
          },
          {
            "to" : "intended recipient B",
            "eventType": "event type B",
            "parameters": {
              "parameter 1" : { parameter 1 values },
              … 
              "parameter n" : { parameter n values }   
            }
          },
        ]
      }
    }

##### Figure 8. The _events_ object

Figure 8 shows the structure of the _events_ object.  This should be an array of one or more objects which we will call an event object. 

Each event object must have an _eventType_, which is a string.  Other parameters may be present depending on the eventType. The _parameters_ object is a dictionary of parameter objects with standard key names specific to the event-type.  Some eventTypes support a 'bare' mode without any parameters. 

The _to_ parameter is a valid URL of the assistant that the message is intended for.  If the parameter is not present then is can be assumed that the event is intended for all recipients of the envelope.

The _private_ boolean parameter indicates that the event is only intended for the _to_ agent and should not be copied to any other agents in a multi-participant conversation. If it is not defined it is assumed to be _false_ i.e. any message intented for another recipient can be copied to other participants in the conversation for context. 

#### 1.9 Event-Types

The following are valid values for _eventTypes_.

* utterance events - spoken or written natural language
  * _utterance_  - An utterance spoken in the conversation itself by a user or agent
  * _whisper_ - An out-of-band linguistic instruction from one agent to another

* agent control events  - structure control messages
  * _invite_ - A user-agent or assistant is invited to join the conversation.
  * _bye_ -  A user-agent or assistant is leaving the conversation
  * _requestManifest_ - Ask another agent for information about their identity and capabilties.
  * _publishManifest_ - Publish information about an agents' identity and capabilties.
  * _findAssistant_ - Ask an agent to recommend themself or another agent for a task.
  * _proposeAssistant_ - Return a list of recommended agents for a task.

The following sections define these event objects in more detail.

#### 1.10 Utterance Events

    {
      "ovon": {
        ..
        "events": [
          {
            "to" : "https://someBotOrPerson.com",
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

Figure 9. Example of an OVON _utterance_ event.

Figure 9 shows the structure of an event with the _eventType_ of _utterance_.  This object contains just one mandatory parameter with the key-name _dialogEvent_.  The _to_ parameter is optional and can be used to designate that the utterance is directed to a certain participant in the conversation.  This can be thought of as the equivalent of catching someone's eye during a round table conversation.

OVON events of this type are sent whenever a user or an assistant takes a dialog act.  The value of the _dialogEvent_ dictionary key must contain a valid dialog event object as specified in [Interoperable Dialog Event Object Specification Version 1.0](https://docs.google.com/document/d/1ld0tbGhQEOcZ4toCi0R4AEIWlIET8PgF1b-xKhtwsm0/edit?userstoinvite=jim42%40larson-tech.com&sharingaction=manageaccess&role=writer#bookmark=id.mnvmxlp2vaay ).
Figure 9 shows the structure of an event with the eventType of utterance.  This object contains just one parameter with the key-name dialogEvent.

OVON events of this type are sent whenever a user or an assistant takes a dialog act.  The value of the dialogEvent dictionary key must contain a valid dialog event object as specified in Interoperable Dialog Event Object Specification Version 1.0.   

The following sections describe the features that are currently supported in the utterance dialog event.   

Additional keys and mime-types are permitted.  Compliant OVON dialog agents do not need to respond to any unsupported keys in the dialog event..   

The following sections describe the _features_ that are currently supported in the _utterance_ dialog event.   

Additional keys and mime-types are permitted.  Compliant OVON dialog agents do not need to respond to any unsupported keys in the dialog event.

##### 1.10.1 Utterance Text Feature

The _text_ feature is **mandatory** in all _utterance_ dialog events.

|parameter|Description|
|-|-|
|_mimeType_|text/plain
|_speakerId_|The _speakerId_ should be a unique identifier to the speaker that was or will be perceived as the social actor in the conversation.  Assistants that are channeling utterances from other agents or speakers should keep the _speakerId_ of the original speaker.   This is one of the main distinctions between channeling and mediation.  It is the responsibility of the agent that generates the event to decide which _speakerId_ to attach to the event.
|_value_|Any number of values are allowed as strings in the _tokens_ section.  When concatenated together the _tokens_ should represent the orthographic representation of the utterance.
|_valueUrl_|Any number of value URLs are allowed in the tokens section.  These URLs should locate content of type 'text/plain' and when downloaded and concatenated together the _tokens_ should represent the orthographic representation of the utterance.

### 1.11 Whisper Events

    {
      "ovon": {
        ..
        "events": [
          {
            "to" : "https://someBotOrPerson.com",
            "eventType": "whisper",
            "parameters": {
              "dialogEvent": {
                "speakerId": "agent08kkmy6gt",
                "span": { "start-time": "2023-06-19 03:09:07+00:00" },
                "context" : "The user has a history of serious depression and is seeking information about the side effects of different drug types.",
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

##### Figure 10. Example OVON _whisper_ event.

Figure 10 shows the structure of an OVON event with the _eventType_ of _whisper_. 

_whisper_ events are sent whenever an assistant wants to send a natural language instruction or request to another agent.   _whisper_ events are similar to _utterance_ events but they are not to be directly voiced in the dialog.\

This event contains just one mandatory parameter with the key-name _dialogEvent_.   The _dialogEvent_ can contain any linguistic information but it expected to contain a brief utterance or instruction to the recipient as to what is expected of them at this point in the dialog. For example, in the simplest use-case the _dialogEvent_ may repeat an utterance given by the user in order to delegate the servicing of this request to a newly invited agent. This parameter can contain any valid dialog event objects as specified in [Interoperable Dialog Event Object Specification Version 1.0](https://docs.google.com/document/d/1ld0tbGhQEOcZ4toCi0R4AEIWlIET8PgF1b-xKhtwsm0/edit?userstoinvite=jim42%40larson-tech.com&sharingaction=manageaccess&role=writer#bookmark=id.mnvmxlp2vaay).   

The _context_ parameter is an optional part of the _dialogEvent_ and is a plain text.  This field should contain a natural language description of the context of the request at this point in the dialog. It should not be used to communicate instructions from one agent to another agent.\

##### 1.11.1 Whisper Text Feature

The _text_ feature is **mandatory** in all _whisper_ dialog events.

|parameter|Description|
|-|-|
|_mimeType_|text/plain
|_speakerId_|The _speakerId_ should be a unique identifier of the agent or speaker that generated the request.  In the case where the event is simply a forwarded version of a user utterance, this event should be given the _speakerId_ of the forwarding agent not the original speaker.
|_value_|Any number of values are allowed as strings in the _tokens_ section.  When concatenated together the _tokens_ should represent the orthographic representation of the utterance.
|_valueUrl_|Any number of value URLs are allowed in the tokens section.  These URLs should locate content of type 'text/plan' and when downloaded and concatenated together the _tokens_ should represent the orthographic representation of the utterance.


### 1.12 Extensible Dialog Event Features (informative)

The features in Dialog Events are intentionally intended to be extensible.  This specification does not limit the features that can be put into an utterance event or a whisper event.

The current version of this specification mandates only the text feature in each dialog event.  Future versions are likely to support and standardize additional event features such as:

* _ssml_ to describe how text should be rendered as speech
* _speech_ to send raw speech for output or input.

There are no limitations on the features that are added to a dialog event.  This enables agents to exchange any media that they wish in addition to the text message.  For example, a video feature intended to represent Video Conversational agent communications (i.e. Avatar communications) could be added as shown in Figure 11.  This example is informative only

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

#### Figure 11. Example video feature, which at present would be considered a custom feature.

### 1.13 Invite Event

    "ovon": {
        "schema": {
          "version": "0.9.2"      
        },
        "conversation": {
          "id": "someUniqueIdCreatedByTheFirstParticipant"
        },
        "sender": {
            "from": "https://someBotThatOfferedTheInvite.com"
        },
        "events": [
            {
                "to": "https://someBotThatIsBeingInvited.com",
                "eventType": "invite"
            }
    }


##### Figure 12. Mandatory elements of the _invite_ object shown as a 'bare invite'

Invite events act as an invitation for the target agent to enter the conversation.  They also invite the target agent to take the conversational floor and respond to all utterances from this point onwards.  The optional _to_ object is used to specify the URL of the agent that is being invited.  If it is absent, then all recipients of the envelope should consider themselves invited to the conversation.\
\
It is possible to invite an agent to a conversation without giving it any other events.  This is termed a bare invite as shown in Figure 12.  The recipient of such a bare invitation is being invited to engage with the user without being given any context.  A suitable response would be to speak a greeting and ask how the agent can help.

    {
      "ovon": {
        "schema": {
          "version": "0.9.2"      
        },
        "conversation": {
          "id": "31050879662407560061859425913208"
        },
        "sender": {
          "from": "https://someBotThatOfferedTheInvite.com"
        },
        "events": [
          {
            "eventType": "utterance",
            "parameters": {
              "dialogEvent": {
                "speakerId": "humanOrAssistantID",
                "span": { "startTime": "2023-06-14 02:06:07+00:00" },
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
            "to": "https://my-weather-bot.com",
            "eventType": "invite"
          },
          {
            "to": "https://my-weather-bot.com",
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
          }
        ]
      }
    }

##### Figure 13. A typical dialog envelope for an invite, including a voiced transfer prompt.

Invite events will typically be accompanied by additional events.  Figure 13 shows a conversation envelope where the inviting agent tells the user that they are inviting another agent to speak with them.  Then the invite event issues the invitation, accompanied by a whisper which tells the new bot what it is that is being asked of it.

### 1.14 Bye Event

    {
      "ovon": {
      "schema": {
        "version": "0.9.2"      
      },
      "conversation": {
          "id": "31050879662407560061859425913208"
        },
        "sender": {
          "from": "https://someBotThatOfferedTheBye.com"
        },
        "events": [
          {
            "eventType" : "bye"
          }
        ]
      }
    }

Figure 14. A minimal _bye_ envelope detaching an agent from a conversation.

When an agent wants to leave the conversation it sends a _bye_ event.  This message indicates that the agent is leaving the dialog, and if it currently has control it also relinquishes the floor.   An example of the _bye_ event is shown in Figure 14. It has no _parameters_.  The optional _to_ object can be included but it is not neccessary.

    "ovon": {
      "schema": {
        "version": "0.9.2"      
      },
      "conversation": {
        "id": "31050879662407560061859425913208"
      },
      "sender": {
        "from": "https://someBot.com"
      },
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
            "eventType" : "bye"
          }
        }
      ]
    }

Figure 15. A _bye_ event with a voiced farewell.

As with the _invite_ event, the _bye_ event can be accompanied by other events as shown in Figure 15.  In this example the agent indicates its intention to leave the conversation and voices a farewell as it does so.


### 1.15 requestManifest Event

    {
      "ovon": {
        "schema": {
          "version": "0.9.2"      
        },
        "conversation": {
          "id": "31050879662407560061859425913208"
        },
        "sender": {
          "from": "https://someBot.com"
        },
        "events": [
          {
            "to": "https://dev.buerokratt.ee/ovon/conversation",
            "eventType": "requestManifest"
          }
        ]
      }
    }

##### Figure 16. A typical dialog envelope for a requestManifest event

All participants in a conversation are expected to maintain a manifest of their capabilities and core attributes.  They should publish this on request. The _requestManifest_ event is used for this.  This is a bare event with no _parameters_. 

Figure 16 shows an example of a _requestManifest_ event. On receipt of this event, the target assistant should return a _publishManifest_ event.

The _to_ object is optional. If it is absent then the recipient of the envelope should consider themself the target of the request.

### 1.16 publishManifest Event

    {
      "ovon": {
        "schema": {
          "version": "0.9.2"      
        },
        "conversation": {
          "id": "31050879662407560061859425913208"
        },
        "sender": {
          "from": "https://dev.buerokratt.ee/ovonr/conversation",
          "to": "https://someBot.com"
        },
        "events": [
            { 
                "to" : "https://whoeverAskedForTheManifest.com",
                "eventType": "publishManifest",
                "parameters": {
                    "manifest" : {
                        "identification":
                        {
                            "serviceEndpoint": "https://dev.buerokratt.ee/ovonr/conversation",
                            "organization": "Government of Estonia",
                            "conversationalName": "Buerokratt",
                            "serviceName": "Passport Office",
                            "role": "Immigration Specialist",
                            "synopsis" : "Immigration specialist as part of the Buerokratt system."
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
                }
            }
        ]
      }
    }

##### Figure 17. A typical dialog envelope for a publishManifest event

The _publishManifest_ event can be used to publish information about the capabilities and identity of a participant in a conversation.   
 
This event can be sent at any time but should always be sent in response to a _requestManifest_ event.  Figure 17 shows an example of a _publishManifest_ event.  The event has one mandatory parameter _manfest_.  This parameter must be in the format specified in [3]

The _to_ object is optional.  It can be used to indicate whether the manifest is intended for a specific conversant in the conversation.  

### 1.17 findAssistant Event

    {
      "ovon": {
        "schema": {
          "version": "0.9.2"      
        },
        "conversation": {
          "id": "31050879662407560061859425913208"
        },
        "sender": {
          "from": "https://someBot.com"
        },
        "events": [
          {
            "to": "https://myFavoriteDiscoveryBot.com",
            "eventType": "findAssistant"
          },
          {
            "to": "https://myFavoriteDiscoveryBot.com"
            "eventType": "whisper",
            "parameters": {
              "dialogEvent": {
                "speakerId": "speaker0819",
                "span": { "startTime": "2023-06-14 02:06:07+00:00" },
                "features": {
                  "text": {
                    "mimeType": "text/plain",
                    "tokens": [ { "value": "Who is the author of war and peace?" } ] 
                  }
                }
              }
            }
          }
        ]
      }
    }

##### Figure 18. A typical dialog envelope for a findAssistant event

The _findAssistant_ event can be used to ask any other assistant to recommend one or more assistants.    There are a two primary use-cases for this event.

1. Asking an assistant to recommend one or more assistants that can help with a certain task.
2. Asking an assistant (or human agent) if they are willing and able to support a certain task.

A _findAssistant_ event will normally be accompanied by a _whisper_ event containing a natural language description of the task to be performed.  A _proposeAssistant_ event will be returned in response to this event.

_findAssistant_ events can be sent without an associated _whisper_ event.  This is not very meaningful for use case 1, but if a discovery agent does receive an _findAssistant_ event with no _whisper_, then the recipient assistant could simply return the address of their favorite general purpose assistant. 

For use case 2, a missing _whisper_ event could be interpreted as a request to see whether that assistant is willing and able to recieve requests in general under the assumption that the client already knows the capabilities of the recipient.

The optional _to_ object can be used to indicate which agent is the intended recipient of the event.  If absent then all recipients should consider the request directed at them.

See section 1.19 for more information on _proposeAssistant_ event behaviors.

### 1.18 proposeAssistant Event

    {
      "ovon": {
        "schema": {
          "version": "0.9.2"      
        },
        "conversation": {
          "id": "31050879662407560061859425913208"
        },
        "sender": {
          "from": "https://myFavoriteDiscoveryBot.com "
        },
        "events": [
          { 
            "to": "https://someBotThatAskedForIt.com",
            "eventType": "proposeAssistant",
            "parameters": {
              "servicingManifests" : [
                  {
                    "identification": {
                      "serviceEndpoint": "https://findMyAIAssistant.com",
                      "synopsis" : "A bot for those who love reading."
                    },
                    "score": 100
                  },
                  {
                    "identification": {
                      "url": "https://nationalLibraryArchive.org",
                      "synopsis" : "A government catalog of every book published in the USA."
                    },
                    "score": 25
                  },
                  {
                    "identification": {
                      "url": "https://booksRUs.com",
                      "synopsis" : "Browse, sample and buy any book you desire."
                    },
                    "score": 14
                  }
                ],
                "discoveryManifests": [
                  {
                    "identification": {
                      "serviceEndpoint": "https://findMyAIAssistant.com",
                      "synopsis" : "Finds assistants anywhere in the world"
                    },
                    "score": 100
                  }
                ]
            }
          }     
        ]
      }
    }

##### Figure 19. A typical proposeAssistant event 

The _proposeAssistant_ event is sent when one agent would like to recommend one or more agents (or itself) for a certain task.   This will usually be in response to a _findAssistant_ event but can also be used to make a delegation suggestion in response to an _utterance_.

Once an agent receives a _findAssistant_ event it can do a combination of the following:

- Recommend one or more agents (including itself) to service this request.
- Recommend one or more agents to help find who can service this request.

In order to support this the _proposeAssistant_ event has two mandatory parameters:

- _servicingManifests_ - A list of agents that can service this request.
- _discoveryManifests_ - A list of agents that can recommend other agents to service this request.

It is the responsibility of the receiver of this event to choose one (or none) of the proposed agents and to issue an _invite_ to that agent.  This will typically be accompanied by the same _whisper_ event.

If an agent receives an _utterance_ event that it does not feel capable of servicing, it can also return a _proposeAssistant_ event.  

The optional _to_ can be used to indicate a specific agent to which the proposal is addressed.  Otherwise any recipient should consider the proposal addressed to themselves.

Each list item in the recommendation is the manifest format as specified in [4] with two notable exceptions:

1. It is permissible to return only a subset of the manifest. The only mandatory manifest items is:
  - _identification_/_serviceEndpoint_ - The service endpoint of the assistant.  
  - _identification_/_synopsis_ - A brief synopsis of the capabilities of this endpoint.  

2. The manifest object can contain one additional optional key that is not present in the manifest specification:
  - _score_ - A recommendation score between 0 and 100.  

Any assistant that is returned in the _servicingManifests_ can be considered suitable to be sent an _invite_ to join the conversation and service the request.   If there are no recommendations to be made then an empty array should be returned in _servicingManifests_.  An agent can also recommend itself. This means that the _findAssistant_ event can also be used to check if a servicing agent is willing and able to service an enquiry prior to inviting it to do so. 

Any assistant that is returned in the _discoveryManifests_ can be considered by the client as suitable to be sent a _findAssistant_ event with the same _whisper_.  This allows an agent to recommend that the client uses another discovery agent to find a solution.  The _discoveryManifests_ parameter is mandatory and should contain an empty array if no discovery agents are to be recommended.  An agent should not recommend itself in the _discoveryManifests_.  This could lead to infinite regress.

Note that there is no requirement in the OVON framework for an assistant to be exclusively either a discovery agent or a servicing agent. They can be both and the requesting assistant should be prepared to support both use case 1 or 2 - i.e. prepared for an agent to recommend itself for a task or recommend another agent for a task.  There is also nothing to stop an agent recommending servicing agents and discovery agents in its response.

The recommending agent is free to use any mechanism it wants to generate the _score_.   

## Chapter 2. Minimal Behaviors

#### 2.1 Minimal Assistant Behaviors

OVON-compliant dialog assistants must support all event types in order to be considered fully compliant.  This section documents the minimal behavior expected from an OVON-compliant dialog assistant.  

* utterance events - spoken or written natural language
  * _utterance_  -  Answer the speaker with an utterance in return.
  * _whisper_ - Ignore it if the contents cannot be understood.

* agent control events  - structure control messages
  * _invite_ - Say 'hello' and respond to any whisper utterances. 
  * _bye_ - Ignore this event from another assistant.
  * _requestManifest_ - Return a minimal manifest that meets the manifest schema.
  * _publishManifest_ - Ignore this event if you did not ask for a manifest.
  * _findAssistant_ - Return your own URL as a candidate servicingManifest in a _proposeAssistant_ event.
  * _proposeAssistant_ - Ignore this event if you did not ask for a recommendation.

#### 2.2 Minimal Conversation Floor Manager Behaviors
OVON-compliant conversation floor managers (including host browsers) agents must support all types in order to be considered fully compliant.\
\
The minimal behavior expected from an OVON-compliant conversation floor manager in response to these event types is as follows:

* _utterance events_ - spoken or written natural language
  * _utterance_  -  Send this _utterance_ to all current conversants apart from the sender. 
  * _whisper_ - Send this _whisper_ to the current focal agent.

* _agent control events_  - structured control messages
  * _invite_ -  Forward the _invite_ to any agent designated by the _to_, otherwise ignore it.
  * _bye_ - Remove this agent from the current register of active conversants.
  * _requestManifest_ - Send this event to any agent designated by the _to_, otherwise send to all active conversants.
  * _publishManifest_ - Send this event to any agent designated by the _to_, otherwise send to all active conversants.
  * _findAssistant_ - Send this event to any agent designated by the _to_, otherwise send to all active conversants.
  * _proposeAssistant_ - Send this event to any agent designated by the _to_, otherwise send to all active conversants.

The conversation floor manager retains ultimate responsibility for deciding which conversants are currently considered to be active in the conversation and which agent is the current focal agent.  This can for example include removing agents from the conversation if they do not respond within an allotted time, inviting trusted agents to the conversation to deal with exceptions, and deciding when to terminate a conversation with the user.  

## Chapter 3. JSON Envelope Schema

The structure of a JSON conversation envelope is defined as a JSON Schema located at [https://github.com/open-voice-interoperability/docs/tree/main/schemas/conversation-envelope/0.9.2/conversation-envelope-schema.json]

## Chapter 4. References

[1] **Interoperability of Conversational Assistants** [https://openvoicenetwork.org/docs/interoperability-of-conversational-assistants/] \
[2] **Interoperable Dialog Event Object Specification Version 1.0.1** [https://github.com/open-voice-interoperability/docs/blob/main/specifications/DialogEvents/1.0.1/InteropDialogEventSpecs.md] \
[3] **IETF RFC 9110 HTTP Semantics.** [https://datatracker.ietf.org/doc/html/rfc9110/] \
[4] **Assistant Manifest Specification Version 0.9.0** [https://github.com/open-voice-interoperability/docs/blob/main/specifications/AssistantManifest/0.9.0/AssistantManifestSpec.md] 

## Chapter 5. Glossary of Terms

|Term|Definition|
|-|-|
|channeling|A conversational assistant acts as an intermediary between a user and another conversational assistant, passing through requests and returning responses. The intermediary assistant will often make no modifications to the requests or the responses but may do so, for example increasing the speed or volume or even translating between languages.   
|confidence |A number representing a measure of the confidence that the information contained in the associated value is ‘correct’.
|conversation floor manager|A component that manages which conversants are active in a given conversation and which agent is the current focal agent. 
|delegation|A conversational assistant passes control and management of the dialog to another conversational assistant, along with a negotiated amount of context and dialog history. 
|derived specification|A derivative standard, built upon this specification, which defines a specific way to use a dialog event in a particular context.
|dialog event |A linguistic event in a spoken or written monologue or dialog between two or more speakers.  
|dialog event feature |A layer of information of a certain type associated with the dialog event.
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
|location of schemas|_Question:_ Where should we publish the schema?</br>_Answer:_ Let's use https://github.com/open-voice-interoperability/lib-interop/tree/main/schemas, but add a new subfolder for each version (e.g. "0.9.2" for the current schemas).
|reply_to |_Question_: Should reply_to be optional, mandatory (or removed)?</br>_Answer:_ The reply_to element appears to have become redundant and was retired out of the specification in version 9.9.2.  
|Returning Control|_Question_:Have we fully addressed what happens on handing back control? Need to discuss control on 'bye' and 'invite'.</br>_Answer:_ Minimal behaviors for 'bye' and 'invite' are defined in this specification with the introduction of a 'focal agent'.  Subsequent incarnations of this specification are likely to formalize these further in order to support multi-participant conversations.
|How are conversations started?|_Question:_ There is nothing in the envelope spec to allow a conversant to initiate a conversation.  How do conversations start?</br>_Answer:_ It is envisaged that a 'start' event (or some similar name) will be added in later versions of the specification.  Such an event would for example come from the proxy agent to the conversation floor manager and result in the creation of a conversationId.   It is also possible that an Invite could be used for this purpose and a new event is not needed.  For now, it is assumed that implementation of the current version of the specification will have a combined proxy agent and conversation floor manager and the initiation of a dialog will be a proprietary feature of that combined component.
|Interruptions and Univiting agents.|_Question:_ How does one conversation stop the operation of another conversant?  For example, how might a user tell an agent to be quiet?  </br>_Answer:_ We anticipate that later versions of this specification will require more explicit floor management and control features, including the ability to 'uninvite' conversants.    In the meantime, interruption of real-time streaming of audio to the user can only happen in the user-proxy-agent, for example under the control of a barge-in mechanism.  The conversation floor manager can also choose to 'uninvite' an agent by simply stopping communication with it.  That agent will need to infer that it is no longer part of the conversation by the use of heuristic time-outs.
|Multi-conversant support|_Question:_ How might this specification be extended to support multiple conversant support?   The most pressing need for this is to enable a 'favored' agent to be able to respond to interruption messages from the user.   After that, use cases could include having multiple users on a single conversation and multiple agents competing for the floor - i.e. a conference call involving multiple users and/or agents.</br>_Answer:_  This specification fully anticipates that the conversation floor manager could support multiple conversants.   The group has identified the following features that will probably be needed to support this.</br> - Uninviting conversants (See above)</br> - Adding targetSpeakerID to dialog events (to allow one conversant to specifically address another specific conversant).</br> - Adding a speakers section to the conversation object to keep track of the speakers in the conversation including their speakerIDs, URLs, displayNames, and spokenNames.
|candidateAssistants|_Question_: should we rename this to be _proposeAssistant_ to follow the pattern that events are generally a verb phrase not a noun phrase?<br>_Answer_: It was agreed to rename this _proposeAssistant_.|
|'to' destinations|Question: There is an urgent need to agree how to express 'to' and discuss exactly how the addressing of events is managed.  We have removed 'to' from the Invite but not put it anywhere else. <br>_Answer_: We have added an optional 'to' parameter to all events.  This helps the recipient decide whether they want to ignore it or not.|
|responseCode|Question: This is anachronistic and may not be useful. We need to know how current users are using this parameter and considering retiring it.<br>_Answer_: We decided to deprecate the responseCode and introduce an 'acknowledge' event instead. |
|discovery or servicing agent in manifest|_Question_: Should manifests have explicit coding for whether an agent can provide discovery services or not? </br> _Answer_: We have deliberately left this out for now, but if we need it, we will consider additional flags on the manifest in some form.|


### Chapter 7. Document Change Log

|Version|Release Date|Description|
|-|-|-|
|0.9.0|2024.01.16|Initial Published Draft|
|0.9.1|2024.04.16|- Added a new section introducing discovery</br>- Merged the 'Representation' section into the 'Syntax and Protocol' section. </br>- Replaced code example images with text</br>- Added PersistentState which was accidentally omitted from 0.9.0| 
|0.9.2|2024.07.03|- Added findAssistant event</br> - Added proposeAssistant event</br> - Added requestManifest event</br> - Added publishManifest event </br>- Deprecated responseCode</br>- Made "to" optional on all events</br>- Removed inline schema and kept a link instead.</br>- Removed reply_to</br>|  
|0.9.3|2024.11.26|- Added private to event objects</br>- Added context parameter to whisper</br>|
<img src="https://github.com/open-voice-interoperability/artwork/blob/main/horizontal/color/Interoperability_Logo_color.png" width="200">

# open-voice-interoperability/docs

## Introduction

This repository contains the specification and schemas for the ***Open-Floor*** family of specifications and supporting schemas developed developed by the Open Voice Interoperability Initiative operating within the Linux Foundation AI & Data Foundation. 

The [**specifications**](specifications) folder contains the current normative specifications.  
The [**schemas**](schemas) folder contains JSON schemas in support of the specifications.

### Inter-Agent Messages (Envelopes)

Latest Specification: 
[**Open Floor Inter-Agent Message Specification Version 1.0.0**](https://github.com/open-voice-interoperability/docs/blob/main/specifications/ConversationEnvelope/1.0.0/InteroperableConvEnvSpec.md) <br>
Latest Schema: [Open Floor Inter-Agent Message Schema Version 1.0.0](https://github.com/open-voice-interoperability/docs/blob/main/schemas/conversation-envelope/1.0.0/conversation-envelope-schema.json)

The OVON Conversation Envelope is a universal JSON structure whose purpose is to allow human or automatic agents (assistants) to interoperably participate in a conversation. 
When coupled with a specific protocol, such as HTTPS, a dialog agent that can generate and send Conversation Envelopes is capable of inter-operating with any other OVON-compliant agent, 
regardless of the technology or architecture on which that other agent is based.

### Dialog Events

Latest Specification: [**Open Floor Dialog Event Object Specification Version 1.0.2**](https://github.com/open-voice-interoperability/docs/blob/main/specifications/DialogEvents/1.0.2/InteropDialogEventSpecs.md) <br>
Latest Schema: [Open Floor Dialog Event Object Schema Version 1.0.2](https://github.com/open-voice-interoperability/docs/blob/main/schemas/dialog-event/1.0.2/dialog-event-schema.json)

The purpose of a dialog event is to define a generic standardized data structure that can be used in any component of a dialog system to express a ‘language event’, that is to say, any features associated with a phrase, utterance or part of an utterance. Dialog events span a certain time period and are associated with a single speaker.

The Dialog Event is used as a component of other specifications such as the Envelope specification. It is not intended to stand alone.

### Assistant Manifest

Latest Specification: [**Open Floor Assistant Manifest Specification Version 1.0.0**](https://github.com/open-voice-interoperability/docs/blob/main/specifications/AssistantManifest/1.0.0/AssistantManifestSpec.md) <br>
Latest Schema: [Open Floor Assistant Manifest Schema Version 1.0.0*](https://github.com/open-voice-interoperability/docs/blob/main/schemas/assistant-manifest/1.0.0/assistant-manifest-schema.json)

The Assistant Manifest is a structured description of the key characteristics and capabilities of a conversational assistant that is associated with a unique serviceEndpoint. The manifest can be thought of as the curriculum vitae of the conversational agent and a public record of the services that it offers. It can be used, for example, by other agents or users to decide whether to invite a particular agent to join a conversation. In this regard, it is particularly relevant to discovery agents who provide services to other agents to help them find assistants to achieve certain tasks for them.

The Assistant Manifest is used as a component of other specifications such as the Envelope specification. It is not intended to stand alone.

# TLDR

If you don't have time to read the specifications. Read this instead.

## Purpose and Usefulness

The Open Floor Interoperable Conversation Envelope Specification provides a standardized message format for facilitating communication between conversants in a conversation including human users and various dialog agents (assistants), whether human-operated or autonomous. 

This specification aims to create a seamless interaction experience by standardizing the format of conversation envelopes, which are structured in JSON. The standardized approach allows different systems to communicate effectively, paving the way for interoperable, cross-platform conversational agents.

### Example Use Cases

1. **Basic Communication:** A user interacts with multiple autonomous assistants sequentially, managed by a floor manager converting and forwarding messages.
2. **Delegated Tasks:** A dialog agent delegates part of the conversation or task to another agent, retaining overall control and context.
3. **Discovery and Recommendations:** Agents can query other agents to determine their capabilities and seek recommendations. Agents can propose themselves or others for specific tasks.
4. **Multi-agent Interaction:**  Multiple agents and/or users gather round a floor under a convener agent and collaborate to achieve a task. 

### Delegation, Channeling, Mediation, Orchestration

- **Delegation:** Transferring control from one dialog agent to another.
- **Channeling:** Acting as an intermediary, passing messages between agents without altering the message content.
- **Mediation:** Interacting with multiple agents behind the scenes to achieve a goal before responding to the user.
- **Orchestration** A convener agent manages communication around an open floor for agents and human conversants to collaborate to achieve a goal.

These patterns allow for sophisticated interactions and coordination between dialog agents.

## Specification Details

#### Syntax and Protocol

The conversation envelope is a standalone JSON object designed for flexibility across various communication protocols, with HTTPS being the initial recommended protocol.

#### Schema and Structures

The conversation **Envelope** contains the following:
* **speaking or sending multi-media events** (publicly or privately)
  * _utterance_  - An 'utterance' spoken or whispered from one conversant to some or all participants
  * _context_ - Optional additional text or media to accompany an utterance.

* **joining or leaving conversations**
  * _invite_ - A conversant is invited to join the conversation.
  * _uninvite_ - A conversant is removed from the conversation.
  * _declineInvite -  A conversant is declining an invitiation to join a conversation.
  * _bye_ -  A conversant is leaving the conversation

* **discovering other agents and establishing their capabilities**
  * _getManifests_ - Ask an agent to recommend themself or another agent for a task.
  * _publishManifests_ - Return a list of manifests for the current assistant.

* **managing who has the conversational floor** (support for multi-party conversations and floor passing between agents)
  * _requestFloor_ - Used by a conversant to request the floor.
  * _grantFloor_ - Used by a convener agent to offer the floor to another conversant. 
  * _revokeFloor_ - Used by a convener agent to revoke the floor from another conversant. 
  * _yieldFloor_ - Used by a conversant to yield the floor to another conversant.

### Example Envelope

    {
        "openFloor": {
            "schema": {
                "version": "1.0.0"
            },
            "conversation": {
                "id": "conv:ffe67361-b072-40e7-ab70-9c83ab90509f",
                "conversants": [
                    {
                        "identification": {
                            "speakerUri": "tag:userproxy.com,2025:abc123",
                            "serviceUrl": "https://userproxy.com",
                            "conversationalName": "John Doe",
                            "role": "User",
                            "synopsis": "",
                            "organization": ""
                        }
                    },
                    {
                        "identification": {
                            "speakerUri": "tag:dev.travelbot,2025:0001",
                            "serviceUrl": "https://dev.travelbot.ee/openfloor/conversation",
                            "organization": "Travelbot Inc.",
                            "conversationalName": "travelbot",
                            "department": "Reservations and Customer Service",
                            "role": "Reservation Specialist",
                            "synopsis": "Reservation specialist as part of the Travelbot system."
                        }
                    }
                ]
            },
            "sender": {
                "speakerUri": "tag:userproxy.com,2025:abc123"
            },
            "events": [
                {
                    "eventType": "utterance",
                    "to": {
                        "speakerUri": "tag:dev.travelbot,2025:0001",
                        "private": true
                    },
                    "parameters": {
                        "dialogEvent": {
                            "id": "de:0d5bc7da-2d72-48a0-9d50-73ebdb433278",
                            "speakerUri": "tag:userproxy.com,2025:abc123",
                            "span": {
                                "startTime": "2025-05-09T17:33:47.884788"
                            },
                            "features": {
                                "text": {
                                    "mimeType": "text/plain",
                                    "tokens": [
                                        {
                                            "value": "Give me the times to Vancouver!"
                                        }
                                    ]
                                }
                            }
                        }
                    }
                }
            ]
        }
    }

An example envelope showing an agent speaking an utterance to the conversation floor, specifically addressing another agent.

## LICENSE

The specifications are published under the terms of the license as defined in: [SPEC_LICENSE](specifications/SPEC_LICENSE.md)

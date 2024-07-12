# Open voice interoperability specifications

This folder contains the following official specifications of the Open Voice Interoperability Project of the LF AI & Data Foundation. 

## Conversation Envelope
The OVON Conversation Envelope is a universal JSON structure whose purpose is to allow human or automatic agents (assistants) to interoperably participate in a conversation. 
When coupled with a specific protocol, such as HTTPS, a dialog agent that can generate and send Conversation Envelopes is capable of inter-operating with any other OVON-compliant agent, 
regardless of the technology or architecture on which that other agent is based.

## Dialog Events
The purpose of a dialog event is to define a generic standardized data structure that can be used in any component of a dialog system to express a ‘language event’, that is to say, any features associated with a phrase, utterance or part of an utterance. Dialog events span a certain time period and are associated with a single speaker.

## Assistant Manifest
The Assistant Manifest is a structured description of the key characteristics and capabilities of a conversational assistant that is associated with a unique serviceEndpoint. The manifest can be thought of as the curriculum vitae of the conversational agent and a public record of the services that it offers. It can be used, for example, by other agents or users to decide whether to invite a particular agent to join a conversation. In this regard, it is particularly relevant to discovery agents who provide services to other agents to help them find assistants to achieve certain tasks for them.

The Assistant Manifest is intended to be used as a component of other specifications; it is not a stand-alone document.

# TLDR

If you don't have time to read the specifications. Read this instead.

## Interoperable Conversation Envelope Specification Overview

### Purpose and Usefulness

The Interoperable Conversation Envelope Specification, by the Open Voice Network (OVON), outlines a standardized method for facilitating communication between various dialog agents (assistants), whether human-operated or autonomous. This specification aims to create a seamless interaction experience by standardizing the format of conversation envelopes, which are structured in JSON. The standardized approach allows different systems to communicate effectively, paving the way for interoperable, cross-platform conversational agents.

### Example Use Cases

1. **Basic Communication:** A user interacts with multiple autonomous assistants sequentially, managed by a conversation manager converting and forwarding messages.
2. **Delegated Tasks:** A dialog agent delegates part of the conversation or task to another agent, retaining overall control and context.
3. **Discovery and Recommendations:** Agents can query other agents to determine their capabilities and seek recommendations. Agents can propose themselves or others for specific tasks.

### Delegation, Channeling, and Mediation

- **Delegation:** Transferring control from one dialog agent to another.
- **Channeling:** Acting as an intermediary, passing messages between agents without altering the message content.
- **Mediation:** Interacting with multiple agents behind the scenes to achieve a goal before responding to the user.

These patterns allow for sophisticated interactions and coordination between dialog agents.

### Specification Details

#### Syntax and Protocol

The conversation envelope is a standalone JSON object designed for flexibility across various communication protocols, with HTTPS being the initial recommended protocol.

#### Schema and Structures

The conversation envelope and its components:
- **Schema Object:** Specifies the format and version.
- **Conversation Object:** Persistent information about the conversation.
- **Sender Object:** Details of the sender.
- **Events Object:** One or more event from the sender to one or more intended recipients.
  - **Utterance Events:** For spoken or written input.
  - **Whisper Events:** For non-voiced instructions between agents.
  - **Agent Control Events:** Include invite, bye, requestManifest, publishManifest, findAssistant, and proposeAssistant.

### Example Envelope

    {
      "ovon" {

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

An example envelope showing an agent speaking an utterance to the conversation floor, specifically addressing another agent.

# Links

- [OVON Interoperability of Conversational Assistants (White Paper) ](https://openvoicenetwork.org/docs/interoperability-of-conversational-assistants/)
- [Interoperable Conversational Envelope Specification Version 0.9.2](https://github.com/open-voice-interoperability/docs/blob/main/specifications/ConversationEnvelope/0.9.2/InteroperableConvEnvSpec.md)

- [Interoperable Dialog Event Object Specification Version 1.0.1](https://github.com/open-voice-interoperability/docs/blob/main/specifications/DialogEvents/1.0.1/InteropDialogEventSpecs.md)
- [Assistant Manifest Specification Version 0.9.0](https://github.com/open-voice-interoperability/docs/blob/main/specifications/AssistantManifest/0.9.0/AssistantManifestSpec.md)
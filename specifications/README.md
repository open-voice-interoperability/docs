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

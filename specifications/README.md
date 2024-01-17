# Open voice interoperability specifications

This folder contains the following official specifications of the Open Voice Interoperability Project of the LF AI & Data Foundation. 

## Conversation Envelope
The OVON Conversation Envelope is a universal JSON structure whose purpose is to allow human or automatic agents (assistants) to interoperably participate in a conversation. 
When coupled with a specific protocol, such as HTTPS, a dialog agent that can generate and send Conversation Envelopes is capable of inter-operating with any other OVON-compliant agent, 
regardless of the technology or architecture on which that other agent is based.

## Dialog Events
The purpose of a dialog event is to define a generic standardized data structure that can be used in any component of a dialog system to express a ‘language event’, that is to say, any features associated with a phrase, utterance or part of an utterance. Dialog events span a certain time period and are associated with a single speaker.
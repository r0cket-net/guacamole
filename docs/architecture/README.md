# Motivation

TLS/SSL is very common in almost every part of IT today. OpenStack lacks a way to manage and operate PKI components.
OpenStack Anchor was an attempt to solve this issue but ultimately got abandoned.
This is my 2 cents to create a Multi-Tenant PKI-as-a-Service Solution for the OpenStack Ecosystem leveraging Keystone for Authentication and Barbican for Private Key Storage.

Everything is subject to change. This is Work In Progress.

# Idea

In similar fashion to other OpenStack components, Guacamole aims to be scalable and follow the common Architecture of OpenStack.

Guacamole implements 3 moving parts:
* Guacamole API
* Guacamole Worker
* Guacamole Driver

An example architecture can be found in `Architecture.svg`. Sadly at the time of writing I only had LibreOffice Draw available, thus the chart is rather sparse and not very beautiful. It will be updated once I have access to Visio again.

## Guacamole API

This is the client facing API Service that will serve all REST requests and interacts with:
* Keystone for token validation
* Oslo Policy Framework for policy enforcement
* AMQP for Job Dispatch
* DB for persistent storage and retrieval of less senstive data

Note that the API Service will not talk to Barbican and shouldn't be required to either. This is a security consideration to avoid Private Key leaks in case the API is breached.

## Guacamole Worker

This is the actual Guacamole Engine.
It listens for Jobs via AMQP and acts upon them by loading the required Driver.

The Wroker interacts with:
* AMQP for new Jobs
* Barbican for private key storage and retrieval
* Guacamole Driver for acting on the Job

## Guacamole Driver

This is just a python class depending on what backend to use to interact with the Certificates.
The example implementation will use the CFSSL binary out of convinience but by implementing a standard interface for the Worker to interact with Drivers it's easily expandable to other methods.

The Driver interacts with:
* Guacamole Worker to return results obviously
* Barbican for private key retrieval (if the method requires a CA-Key for example)
* The underlying binary/implementation of the driver (cfssl binary for example)

# Flows

A flow draft can be found in `flow-draft.txt` to visualize how the components interact with eachother.
This is subject to change as it's a draft and work-in-progress.

# Help

Yes please.
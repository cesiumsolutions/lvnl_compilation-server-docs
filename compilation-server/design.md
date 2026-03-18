
# iCAS R&A Development Compilation Server

## Introduction

### Description

This document describes the technical architecture of the Compile Server which is being proposed to replace and/or augment the current R&A iCAS compilation system.

### Scope

### Contents


### Target Audiences

#### Software Engineers/Developers
#### R&A Developers
#### Project Managers
#### System Administrators





### References

### Abbreviations and Initialisms

## Background

### Motivation

### Current System

### Current Performance



## Users Guides

The following are descriptions of the types of users that are expected to be using the system:

**End Users:** R&A developers who may not have in-depth knowledge of operating system commands and tools and are using the compilation system via specialized scripts and commands. These users will typically use the compilation server system via these scripts and will not make any customizations or internal changes.

Prerequisite knowledge includes: basic Linux commands, iCAS specific scripts.

**Software Engineers**:


**Application Administrators:** R&A developers who are comfortable with operating system commands and tools and have extensive familiarity with how the current build system works as well as the iCAS specific software. They will be responsible for setting up iCAS specific parameters in configuration scripts such as compile commands, flags, and parameters.

Prerequisite knowledge includes: Extensive Linux command syntax, Linux scripting and makefiles, iCAS organisation.

**System Administrators:** System administrators who may not necessarily have (iCAS) domain knowledge but are responsible for setting up the installation process and administrating the hardware, operating system, network and other resources.

### End Users

#### Build commands

#### Selecting Build System

### Application Administrators

#### Common Configuration

#### Compile Service Configuration

#### Gateway Service Configuration

#### Compile Client Configuration

#### Resource Monitoring

### System Administrators

#### Common Setup

#### Compile Service

#### Gateway Service

## Requirements

Although no formal requirements have been made for this activity, the following proposed list of (informal) requirements ensures  compatibility



### System

- Compatible with RHEL 7.9 and 8.6 and currently installed standard software
- Should be compatible with future OS's and OS version releases
- Compatible with current network environment
- Compatible with current filesystem environment
- Capable of operating with a minimum of 32Gb of RAM
- Compatible/integratable within the current build system
- Compatible with current and future iCAS software versions
- Easily selectable between current and new system

### Behavioral

- Generate identical artifacts from the same inputs as the current system
- Optimize generation of artifacts that were previously generated from the same inputs, i.e. caching
- Make optimal use of existing resources, memory, CPUs, machines
- Operationally equivalent to existing system for End Users



### Administrative

- Configurable/adaptable to network and filesystem environment
- Configurable as to how many resources (memory, CPU, machine) will be used
- Configurable to iCAS versions
- Configurable as to compilation flags and parameters
  - Release vs Debug
  - Verbosity
- Logging and monitoring
  -

### Performance and Resources



## Architectural Design

### Definitions and General Concepts:
- Task
- Generalisation of something that (may) require processing and typically consists of the following elements:
	- Input(s)
	- Command
	- Options
	- Artifacts
	- Messages

- Compilation Tas
	- A task specific for compiling source code using a compiler command and associated parameters and flags to generate an artifact.

- Artifact
	- Anything that is the result of function or process that has been requested by a client. It can be the result of a current or previous executed process.

- Service
- Client



Client-Server
File Transfer
- Protocol Message
	- Header
	- MetaData
	- Payload


### System Overview

![](S:\sandbox\lvnl\docs\compilation-server\assets\System Overview.png)

#### Compilation Client

- Batch submits compilation tasks
- Integrated into current Makefiles

Has adaptation of Makefile_common which calls compilation-client script
When building:
- Reads configuration from current_build_slot
- Makes connection to Gateway Service
- Gathers source data (e.g. into zip file)
- Submits build request with current_build_slot configuration and source data
- Receives results package (e.g. in zip file)
- Unzips and places results in appropriate location


#### Gateway Service

- Single point of entry to handle requestions from task clients
- Handles multiple clients simultaneously
- Coordinates tasks with backend

Summarised as follows:
FrontEnd
- Task Client interface

Backend(s)
- Artifact cache
- Task Services

Middleware
- Coordinate queueing of tasks

- Subcomponents
  - Server
    - accepts connections from clients
    - hands off requests to appropriate client handler based on task type (e.g. ICAS)
  - Client Handler factory
    - Creates a client handler based on task type
  - Client Handler
    - Is assigned communication to compilation client
    - Communicates with Task Dispatcher
    - ICAS Client Handler
      - Receives requests from client which contain:
        - ICAS software version
        - zip file of source code
        - additional parameters
    - Generates a hash id for each
  - Task Dispatcher
    - Return results and generated artifacts to Client Handlers
    - Queries Cache (service) for existing artifacts
    - Queries Task Queue for task currently in progress
      - if matching task exists, attaches callback
    - If doesn't exist, submits new tasks to Task Queue

  - Task Queue
    - Accepts task requests from Dispatcher
    - Queries Task Service Manager

  - Task Service Manager
    - Manages discovery and connection to Task Service Providers


- Queries Cache Service for existing artifacts


Design Tradeoffs:

- Where should task command configuration be?
  - Compilation Client
    - Allows for more flexibility
      - have a standard configuration setup by system administrators
      - user's can customize, e.g. Debug vs Release mode, verbosity, etc.
  - Compilation Service
    - Configured by system expert
    - automatically discovered
    - hides details from end user
    - could still provide some flexibility as specific options, e.g.:
      - CONFIG=Release or Debug
      - VERBOSITY=0,1, (or higher)

OLD:

- Reads service configuration which contains:
  - Server Host address and port for Compilation Request Clients
  - Server Host address and port for Compilation Service Clients
- (optional broadcasts to request and service clients)
- Receives and manages connections from Compilation Request Clients
- Receives and manages connections from Compilation Service Clients
- Manages database of cached artifacts
- Receives compilation requests from Compilation Request Clients


#### Cache Service
- Maintains a cache of previously built artifacts
- Either in a database or filemapping




#### Compilation Service

- Accepts connections from clients for single tasks
- Reports status
  - supported tasks (e.g. ICAS compilation)
    for ICAS compilation, also which versions supported
  - maximum processes
  - current running/available processes
  - status of specific current process?

- submit tasks in multitprocess Queue
- Reports results

ICAS specific features
- Reads ICAS software config files (based on lsicas)
- Reports currently supported versions
- Configured to run certain command based on (ICAS) version

OLD:

- Connects to Gateway Service
- Reads configuration from icas CONFIG files for each build slot
- Reads associated compile configuration for each build slot
  - compiler
  - compiler flags
- Reports supported platforms, versions to Gateway Service
- Reports current and available resources to Gateway Service
- Receives compilation requests from Gateway Service
- Queues/performs compilation
- Returns results to Gateway Service
- Reports status of current builds to Gateway Service



### Technical Design

#### Compilation Client


#### Gateway Service

##### Components

Client Handler
  - ICAS ClientHandler

Task Dispatcher

Task Allocator
Analyses the tasks currently in the backlog and selects the next task to be submitted based on priority criteria:
Priority queue criteria
- size - static
- age - dynamic
- Num requests - dynamic
- availability of service provider(s)

Dynamic means the priority is time dependent, can change over time,

Once selected, a service provider is searched for.
If matching service provider found:
 - if not available, place back 
If none found, error message sent back

Because some tasks are dynamic, the Task Allocator must be queried every time a new task slot becomes available.

Gateway service
- client handlers
- dispatcher
- cache database
- task (priority) queue
 - compile service manager

##### Interactions



OLD:

##### Compile Client API
##### Compile Service API
##### Cache Database
##### Compile Service Dictionary
- maintains mapping between a compile client and current build artifacts

Hash Key
Based on:
 - OS Name and Version
 - Compiler name and version?
 - iCAS version
 - 


#### Compile Service

Task Client Handler
  - ICAS Client Handler


OLD:

##### iCAS Configuration
##### Compile Configuration
- for each iCAS configuration
  - compiler executable/version
  - compile flags
- working directory
##### Compilation Queue



- cwp can be rebooted - handle shutdown




## Development Roadmap

Cache Mechanism

Build Server - single

Distributed Services

Optimization

Final Configuration

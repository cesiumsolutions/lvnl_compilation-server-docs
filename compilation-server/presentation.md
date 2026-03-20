# Compilation Server Presentation

## Title : Contents

1. Intro
2. Architectural Design
  a High level
  b Deep dive
  c Advanced topics
3. System Administration
4. End User

(2-4 could be done in whatever order or selection is appropriate to the audience, although the high level Architectural design would probably be beneficial for all audiences)

## Intro : Background/History/Motivation

- Problems during Red Hat 8.6 Migration in late 2025
- Analysis revealed:
  - Some generated C++ files were using large amounts of RAM to compile (e.g. > 40 Gb)
  - Compilation computers being used had only a total of 64Gb
  - In some generated files, some individual _lines_ of code exceeded 100.000 characters!
    -> this is a topic for another conversation

## Intro : Solution

- Install more RAM! (currently 96Gb)
- Use a better caching mechanism
  - Currently using ccache
- Make better of use of (hardware) resources
  - e.g. distribute build over multiple computers
  - similar idea to SETI@home or Incredibuild

## Intro : General Requirements

Minimum:
- Enable compilation of iCAS-generated C++ code on Red Hat 8.6
- Be at least as fast as previous compilations
- Generated artifacts be the same as with current system

Performance:
- Better use of (hardware) resources
  - Caching
  - Using multiple computers

System:
- Support multiple (ICAS) software versions
- Support multiple OS (versions)
  - e.g. For future RHEL version updates

User:

- Minimal impact on end user (R&A Developer)
  - Largely transparent to end user
  - Can switch between using the compilation server and previous mechanism
- Configurable by expert users

## Intro : Audience

- R&A Developers (sections : 1, 2a, 4)
  End users with domain(ICAS) knowledge, but not necessarily IT knowledge

- R&A Administrators (sections : all, 2b optional)
  Users with domain knowledge _and_ also comfortable with software development/IT.

- System Administrators (sections : 3 )
  Deep knowledge of the hardware/software resources, not necessarily of the domain

- Managers

## Architectural Design : Intro

- Top Down explanation
- Will not (always) be discussing tradeoffs
- More detailed Architectural Design Document available

## Architectural Design : General Terms

- Task: Generalization of any unit of work to be executed, consisting of:
  - Inputs: e.g. source files
  - Artifacts: The desired output to be produced, e.g. object files
    ** Lists also expected generated artifacts
  - Command: What is needed to produce Artifacts from Inputs
    - Could also include options, flags, etc
    - Assumption: command line(Linux at the moment) interface
  - Byproducts:
    - Output messages (success or failure) as a result of executing command
    - Statistics: e.g.
      - wall clock/user/system time
      - Resources used: memory

  - For compilation server, specifically compilation of C++ source files to produce object files.

- Caching
  - Storing results of previous tasks
  - Reuse stored result when identical task is requested
  - Hash Id: Generation a small unique id based on the contents of a task

- Client/Server
  - Client: Requests some task to be done
  - Server: fulfills request
  - (typically) network-based communication

- Service-based Architecture (SBA)
  - Functionality split up
  - (typically) network-based communication
  - Allows for scalability, reliability
  - However increases complexity
  - Commonly heard: Microservices
    -> Depends on level of granularity

- ICAS Versions
  - Read from CONFIG files
  - based on 'lsicas' command


Advanced Terms:

- Service Discovery
- Calback/Notification
- Generalization via factory pattern


## Architectural Design : High Level Components

(Diagram: PRES: AD : 1 : High Level Components)

- Compilation Client
  - Prepares Inputs
  - Submit Tasks
  - Receives Artifacts
  - (Optional) displays Byproducts

- Backends
  - Actual fulfilment of Tasks
  - Can be implemented as services
  - Cache
  - Compilation

- Gateway
  - Single point of communication for all Compilation Clients
  - Orchestrates and distributes Compilation Client requests to backends
    (technically, Gateway should be a thin layer, but the orchestration will also be a singleton, so gateway and orchestration are considered combined, at the moment)
  - [This is the most complicated component]


## Architectural Design : Compilation Client

- Current Process
  - Makefile (e.g. see Makefile_common)
  - Compilation command(s) issued directly for each source file
  - ccache is used via symbolic link/replacement of gcc

- New Process
  - a new target in the Makefile
  - calls (python) script
  - uses same/similar command line arguments

- Compilation Client Script:
  - Archive (tar/zip) input (source) files
  - Connects to Gateway
  - Submit request, which includes:
    - ICAS software version (e.g. current_build_slot)
    - Input archive
  - Wait for reply
  - Unzip artifacts into appropriate location/directory
  - Report byproducts/messages

## Architectural Design : Backends

- Cache

- Compilation Service

## Architectural Design : Backend : Cache


(Diagram: PRES: AD : 2 : Backend : Cache )

- Stores Artifacts and (optionally) Byproducts
- Identified by Hash
  - MD5SUM based on:
    - Contents of input
    - Command line
    - ICAS version
- Large artifacts (e.g. object files) stored as files
  - all artifacts/byproducts archived/zipped
  - named as the hash (e.g. <hash>.tgz)
  - archive contents:
    - <filename>.cpp
    - stdout.txt - Capture of stdout messages during compilation
    - stderr.txt - Capture of stderr messages during compilation
    - stats.txt  - Associated statistics during original compilation

- Database
  - Maintains association between hash and filename
  - SQLite(?)

- Single instance (singleton)

- Currently not implemented as service (component of Gateway)
  - could be split out in future


## Architectural Design : Backend : Compilation


## System Administration


## End User



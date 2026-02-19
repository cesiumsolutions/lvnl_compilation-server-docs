# iCAS R&A Development Compilation Server

## Introduction

### Description

This document describes the technical architecture of the Compile Server which is being proposed to replace and/or augment the current R&A iCAS compilation system.

### Scope



### Contents



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

### System Overview

![](S:\sandbox\lvnl\docs\compilation-server\assets\System Overview.png)

#### Compilation Client

#### Gateway Service

#### Compilation Service

### Technical Design

## Development Roadmap

Cache Mechanism

Build Server - single

Distributed Services

Optimization

Final Configuration

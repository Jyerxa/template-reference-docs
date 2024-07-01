# Software / System Architectural Document

## Overview
**Description:**
A brief overview of the software / system from the businesses perspective, include purpose, scope, and stakeholders.
Highlight business objectives and goals that the software / system should achieve.

**Prompts:**
- What is the primary purpose of this project?
- What is the business justification for adding the system or replacing an old system?
- What is the expected business impact?

## Requirements
### Functional Requirements
**Description:**
List the specific functions and features that the system must support. This includes user interactions, system behaviors, and business processes.
see functional requirements [wikipedia](https://en.wikipedia.org/wiki/Functional_requirement)

**Prompts:**
- What are the primary functions the system must perform?
- What specific user interactions need to be supported?
- What should the system do?

### Non-Functional Requirements
**Description:**
List the performance, security, usability, and other operational criteria that the system must meet. This includes system reliability, availability, and scalability.
see non-functional requirements [wikipedia](https://en.wikipedia.org/wiki/Non-functional_requirement)

**Prompts:**
- What are the required latency and throughput standards?
- What are the SLA expectations?

## Overall Architecture
### Components
**Description:**
Describe the different components that make up the system, including their roles, interactions, and dependencies.
Use a high level system diagram of the logical (not hardware) flow.

**Prompt:**
- What services are included in the system?
- What are the roles and responsibilities of each service?
- How do these services interact and depend on each other?

### Scaling
**Description:**
Explain how the system can be scaled to handle increased loads, including horizontal and vertical scaling.

**Prompt:**
- What scaling strategies are in place for the system?
- How does the system handle increased loads?
- Are there any limitations to the system's scalability?

### Messaging
**Description:**
Describe the messaging mechanisms used within the system for communication between components, including protocols, message formats, and communication patterns.

**Prompt:**
- What messaging protocols are used in the system?
- How do components communicate with each other?
- What are the message formats and communication patterns?
- What are the expected and max sizes of messages between components.

## Components (Detailed)
Use this section to give a highly detailed description of each component. Include diagrams, API design, layers. Be as
detailed as possible.

### Component A
#### Description / Role
Define the specific function and role of Component A within the overall system.

#### Technology Stack
List the technologies used to build and operate Component A.

#### Architecture
Describe the internal architectural design of Component A.

#### Implementation Guidance
Provide best practices and guidelines for implementing Component A.

#### Integration Points
Detail how Component A integrates with other services and external systems.

**Prompts:**
- What is the primary function of Component A?
- What technologies are used in Component A?
- How is Component A architected?
- What are the best practices for implementing Component A?
- How does Component A integrate with other services?

### Component B
#### Description / Role
Repeat component sections as needed.

## Additional Sections
### Deployment Strategy
**Description:**
Detail the deployment plan, including environments, tools, and processes.

**Prompt:**
- What is the deployment plan for the system?
- What environments will be used?
- What tools and processes will be employed?

### Risk Management
**Description:**
Identify potential risks to the system and describe mitigation strategies.

**Prompt:**
- What are the major risks associated with this architecture?
- What mitigation strategies are in place?

### Maintenance and Support
**Description:**
Describe the plan for maintaining and supporting the system post-deployment.

**Prompt:**
- What is the maintenance plan for the system?
- How will support be provided to users?

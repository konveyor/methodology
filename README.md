# Konveyor Application Modernization and Migration Guide

This guide provides an open methodology for migrating and modernizing applications to leverage Kubernetes. It provides suggestions for how to use the projects within the [Konveyor](www.konveyor.io) community to accelerate the process where applicable.


## Table of contents


- [Contribution Guide](#contribution-guide)
- [Introduction](#introduction)
- [Assess](#assess)
  - [AS-IS scenario: Understanding the Application Landscape](#as-is-scenario-understanding-the-application-landscape)
    - [Application Portfolio Management](#application-portfolio-management)
    - [Gathering Intelligence](#gathering-intelligence)
  - [TO-BE scenario: Designing the vision for the Target Platform](#to-be-scenario-designing-the-vision-for-the-target-platform)
  - [Identifying Risks Early](#identifying-risks-early)
- [Rationalize](#rationalize)
  - [Migration Strategies](#migration-strategies)
  - [Migration Strategies in the Context of Kubernetes Adoption](#migration-strategies-in-the-context-of-kubernetes-adoption)
  - [Reviewing and Deciding Modernization Paths](#reviewing-and-deciding-modernization-and-migration-paths)
- [Prepare](#prepare)
  - [Executing the mitigation strategy](#executing-the-mitigation-strategy)
  - [Implementing an application deployment model](#implementing-an-application-deployment-model)
    - [Creating deployment Manifests](#creating-deployment-manifests)
    - [Runtime images](#runtime-images)
    - [Configuration model](#configuration-model)
    - [Application Lifecycle Management](#application-lifecycle-management)
- [Pilot](#pilot)
  - [Execute the first adoption sprints](#execute-the-first-adoption-sprints)
    - [Migrate a representative set of applications](#migrate-a-representative-set-of-applications)
    - [Documentation](#documentation)
    - [Testing the migration process](#testing-the-migration-process)
    - [Testing the organization](#testing-the-organization)
- [Scale](#scale)
  - [Migrating the application portfolio](#migrating-the-application-portfolio)
    - [Scaling the adoption team](#scaling-the-adoption-team)
    - [Migrating in sprints](#migrating-in-sprints)
- [Knowledge Management](#knowledge-management)
  - [Establish quality and documentation governance](#establish-quality-and-documentation-governance)
- [Glossary](#glossary)

## Contribution Guide

To contribute to this methodology you can raise a pull request following the [Contribution Guide](/CONTRIBUTING.md).

## Introduction

The Konveyor Application Modernization Methodology has 5 phases.

![Process](/images/overview.png)
[Source](https://docs.google.com/drawings/d/1zHTILKacmiP6fHKyqQPHLVsDyHnyiB7sMGQ4clsKzks/edit)

1. [Assess](#assess) - Engagement in which an exhaustive analysis of the current situation is made, including infrastructure and applications, technologies used and dependencies. Not only is it technical, it also affects processes and application lifecycle management. Next, a definition of the TO-BE scenario is proposed to implement the new platform, clearly identifying gaps and defining the next steps to follow. In addition, an initial estimate of the complete migration process is offered. This first estimate does not have great precision, but it does serve to give an idea of ​​orders of magnitude.

2. [Rationalize](#rationalize) - Based on the outputs obtained during the Assess phase, the most suitable migration strategy for each application type is decided during the Rationalize phase. These strategies are based on the now standard [6 Rs approach](https://aws.amazon.com/blogs/enterprise-strategy/6-strategies-for-migrating-applications-to-the-cloud/) that Amazon popularized a few years ago.

3. [Prepare](#prepare) - This phase focuses on ensuring everything is ready to start deploying applications on the target platform and on conducting technical feasibility tests for migration. Its main mission is to solve the technical challenges encountered during the assessment and mitigate the risks associated with them, while initializing the common knowledge base with the findings that are being made.

4. [Pilot](#pilot) - After solving the main technical problems in the Prove phase, a complete migration of a representative set of applications to the new environment is executed, thoroughly documenting the process, focusing on making it repeatable.

5. [Scale](#scale) - Using the information collected in the previous points it is possible to offer a much more accurate estimate of the following steps. In addition, the definitive implementation strategy is defined with its associated project plan, including the design of the different teams to be involved in the migration and the management and communication model between them. Once everything is set, execution of the migration plan can be carried out, starting the “factory mode” in which applications are migrated in parallel by various teams in typically two-week sprints.


## Assess

The Assessment comprises of the following stages:


- Understanding the **AS-IS scenario** by providing a holistic view of the application landscape used in the organization, including aspects related to infrastructure, technologies, application architecture, processes and knowledge. It is especially interesting to identify non-standard aspects, manual tasks, gaps between systems and common pain points, as they might become the main problems for a successful adoption initiative.

- Designing the **TO-BE scenario** addressing how the new platform will affect the same aspects that were thoroughly analyzed on the AS-IS scenario: infrastructure, technologies, application architecture, processes and knowledge. This could include the architecture for the deployment of the Kubernetes clusters if they are not already in place within the organization.

- Determining the gap between the two scenarios described in the previous stages by **identifying a list of Risks and Migration Items**. Early identification of risks is key for large scale adoption projects, as it allows to define mitigation strategies that ensure that the adoption process is predictable and measurable. Migration Items are meant to describe the required actions to adapt the application portfolio and its associated process landscape to the target platform.

### AS-IS scenario: Understanding the Application Landscape

There are several activities that are essential to achieve a holistic view of the application portfolio managed by an organization.

#### Application Portfolio Management

The first step for the holistic view is to have a full list or inventory of the applications that could be potentially affected by the adoption initiative. Since leaving some applications in their current state could be an option (Retain migration strategy), it is better to include as many applications as possible, rather than leaving some outside the inventory and having to reconsider the migration approach latter on because they are added at a latter stage of the adoption initiative.

The key for being able to manage a large portfolio of applications is grouping them in application types based on several kinds of criteria, depending on organizational or technical aspects. A very typical way to classify applications into application types would be based on the corporate frameworks or architectures used to develop them, to latter on come up with a suitable migration approach for each one of these frameworks. The main idea behind this approach is trying to **solve each problem once**, document the solution and then leverage that knowledge to be used in all the occurrences of the same problem. This could be also applied when no corporate frameworks or architectures are used, trying to classify applications by their technology stack.

Traditionally this has been done using spreadsheets shared between the adoption team and the rest of the organization. This becomes cumbersome and difficult to manage when dealing with a large portfolio, specially on the early stages of the adoption initiative when information is constantly updated, and leading to data synchronization problems when the source of truth is not centralized.

To simplify the application portfolio management, Konveyor offers the Application Inventory tool from the [Tackle](https://www.konveyor.io/tackle) project. The Application Inventory has been designed to be the entry point and the natural integration interface for all tools within the Tackle toolkit. The idea behind it is to allow organizations to manage their portfolio in a centralized fashion, and from there being able to classify applications and launch any kind of actions that might be related to them, such as analysis and assessments. By itself, the Application Inventory provides a way to segregate an organization’s application portfolio through an extensible tagging model that allows the classification of applications in multiple dimensions. The tool is shipped with a series of curated tags out of the box related to typical aspects of application development such as programming languages, OS in which an application is deployed or the databases it may be consuming, but users are able to extend it in any way they may see fit to accommodate concepts like the custom corporate framework used to develop an application as classification criteria. Aside from the tagging model, the Application Inventory also allows to group applications by the Business Service they belong to, and provides a way to manage dependencies between applications.

#### Gathering Intelligence

The most straightforward way for the adoption team to gather intelligence about the application landscape is to conduct a series of organized interviews addressing topics like application architectures, technologies, operations, lifecycle management, configuration model or infrastructure among others. Some of the typical roles involved in these interviews would be the following:

- **Project manager**: Manages the modernization & migration project
- **Business owner**: Key decision makers from business and IT operations.
- **Enterprise architect**: Governs software portfolio, projects and processes.
- **Software architect(s)**: Oversees and leads software standards, guidelines and architecture.
- **Infrastructure architect(s)**: Oversees and leads the infrastructure, platform, or operations teams.
- **Monitoring specialist**: Accountable for monitoring, alerting, log management.
- **Test specialist**: Responsible for functional and nonfunctional tests.
- **Security specialist**: Tests and enforce compliance on security governance.
- **3rd party component specialist**: Specialist accountable for a relevant 3rd party system.
- **Lead developer**: Developer accountable for a specific application.
- **IT operations lead**: Operations engineer accountable for specific relevant systems.

The number of Software Architects to interview will depend on the different corporate architectures or technology stacks available in the organization, as each one of them is will be likely to have its own considerations when defining a migration strategy. The same can be applied to the Infrastructure Architects, as applications can be deployed in different runtimes depending on their nature.

A way to make sure that nothing gets overlooked during the interviews is to prepare a questionnaire addressing all the topics concerning a certain role. This has typically been done manually, ideally storing documents on some shared storage space if possible to allow all adoption team members to access the information. In order to have all of this fully centralized and accessible, along with ensuring that all concerns related to Kubernetes adoption are addressed, Konveyor has developed the Pathfinder tool from the [Tackle](https://www.konveyor.io/tackle) project. The aim of the tool is to assist organizations on the assessment of their application portfolio to determine containerization suitability and enable adoption leads to make informed decisions on the migration strategy they want to pursue for each application or application type. This is achieved in a questionnaire driven approach, covering all essential topics related to technology and application lifecycle management.


### TO-BE scenario: Designing the vision for the Target Platform


There is no defined way to come up with a design that can satisfy all organizations, as the application landscape is key in determining the TO-BE scenario. Requirements gathered on the different interviews conducted during the previous stage could be a starting point, or maybe the initiative itself had a series of requirements that were designed prior to its approval and execution. There are, though, a series of concerns that should be addressed properly on Kubernetes adoption initiatives:

**Application Adaptation**

- Code changes to accommodate to the new environment.
- Remove any kind of coupling with the previous runtime platform and come up with alternatives for concerns such as clustering, job scheduling and configuration consumption.
- Define alternatives for discovery and load balancing in case of usage of solutions like Netflix OSS.
- Find Cloud antipatterns (access to local storage, hardcoded IPs...).

**Manifests Generation or Translation**

- If an application comes from other container platform, translate deployment descriptors / manifests from the source platform.
- If an application was previously deployed on a traditional platform, define the suitable deployment manifest.
- There should be at least one manifest template per application type to ensure uniformity across the whole portfolio.  
- Should be automated if possible

**Runtime Images**

- Define base runtime images per application type or adapt the previous ones to the new platform.
- Come up with a build strategy for runtime images.

**Configuration Model**

- Define ConfigMaps and Secrets and adapt the configuration files each application type uses to be distributed between them.
- Establish how configuration is stored:
  - Git repository per application and environment.
  - Secrets store (Hashicorp Vault, Cyberark Conjur...)
- Decide consumption model for configuration:
  - Mounted as a volume for files to be accessed directly.
  - If the framework allows for it (like Spring Boot or Quarkus) consume directly from the API (requires adding additional dependencies).

**Application Lifecycle**

- Automate Application Lifecycle Management to leverage Kubernetes.
- Define pipelines for building runtime images and promoting across the different environments.
- Decide the strategy for instantiating manifests when deploying an application:
  - Managed by a dedicated pipeline or as part of the promotion pipelines.
  - Managed by a Continuous Delivery tool (ArgoCD, Spinnaker, UrbanCode Deploy...).


Most of these concerns are addressed with more detail on the [Prepare](#prepare) phase section. Traditionally, the design for each one of the aspects of deploying the application portfolio in Kubernetes would be done during the Assessment phase, and all the required artifacts would be implemented during the Prepare phase. Since tools like [Move2kube](https://move2kube.konveyor.io/) allow to generate some of these artifacts automatically, the frontier between the two phases has become a little bit blurry, although having a clear design for the deployment approach for each application type as early as possible is essential.

Of course, if no Kubernetes cluster is already available in the corporate infrastructure, designing the deployment architecture for the platform should be addressed on this stage as well.


### Identifying Risks Early

Early identification of risks allows the adoption team to design and execute mitigation strategies that ensure that the adoption initiative remains measurable and predictable, and is one of the main reasons for doing such a deep dive into the application landscape. If risks are not correctly managed, breaking issues could appear on latter stages of the adoption initiative, making it impossible to at least estimate the length and cost of the project with a certain degree of confidence.

Having an uniform way to describe risks helps with their management, so the following template is proposed for documenting risks and migration items:

> **[Qualifier] Title/short description**
>
> *Rationale*
>
> Brief explanation of the problem or action required, reasoning why it should be considered a risk.
>
> *Recommendation*
>
> Provide an action plan or approach to mitigate the risk.
>
> *References*
>
> List of links to further document either the problem or the potential solution. This could reference both the internal knowledge base for the project or external sources like official documentation of the tools, projects or products to be used.

The following could be considered an example of how an actual migration item would look like following this template:

> **[Caching] Distributed cache membership coupled with the Apache Mesos API**
>
> *Rationale*
>
> Some applications use a Hazelcast cache in embedded mode for distributed caching. Cluster membership in applications running in Apache Mesos is handled using a custom library that integrates with its API developed by the Corporate Architecture team. The API for Kubernetes is completely different, so this custom library is not suitable for the new scenario.
>
> *Recommendation*
>
> Use the Hazelcast Discovery Plugin for Kubernetes to handle cluster membership in OCP. This plugin has been developed by the Hazelcast team and enables embedded Hazelcast members to discover each other automatically when deployed in a Kubernetes environment.  
>
> *References*
>
> - [Hazelcast official blog - How to Use Embedded Hazelcast on Kubernetes](https://hazelcast.com/blog/how-to-use-embedded-hazelcast-on-kubernetes/)
> - [Hazelcast GitHub repository - Hazelcast Discovery Plugin for Kubernetes](https://github.com/hazelcast/hazelcast-kubernetes)


Risks and migration items should be the product of the interviews conducted on previous stages. Being able to identify risks requires a very knowledgeable adoption lead to draw correct conclusions out of the intelligence gathered from the interviews, although tooling can help here as well. For example, based on the answers provided on the assessment questionnaire, the the Pathfinder tool from the [Tackle](https://www.konveyor.io/tackle) project is able to detect any potential risks that might prevent an application from running in containers, providing insight for the adoption lead to decide what could be the best migration strategy in each case.


## Rationalize

### Migration Strategies

### Migration Strategies in the Context of Kubernetes Adoption

When dealing with Kubernetes adoption initiatives, in the Konveyor community we understand each of these Rs in our own way:

- **Rehost** is for moving applications towards Kubernetes without making any changes. These applications can be:

  - Virtual Machines that we move without any changes for them to be executed with Kubevirt.

  - Already containerized applications that are moved from any other Kubernetes cluster retaining the original images, manifests and state.


- **Replatform** stands for moving containerized applications from other container platforms like Cloud Foundry or Docker Swarm to Kubernetes.


- **Refactor** applies to containerizing applications that come from more traditional platforms like application servers, requiring some degree of change into its source code to make it at least Cloud friendly. This includes both application modernization and migration to leverage Kubernetes.

The Konveyor community is actively working on building a series of tools based on Open Source projects to aid in each one of the previously discussed migration strategies. These projects can be seen on the following diagram:


The benefits that these projects provide on each stage of the adoption process will be discussed in their corresponding points from this guide.


### Reviewing and Deciding Modernization and Migration Paths

## Prepare

In the prepare phase ...

### Executing the mitigation strategy

### Implementing an application deployment model





#### Creating deployment Manifests

Kubernetes provides a series of objects to enable applications to run in containers in a simple way, adding an abstraction layer that solves problems such as service discovery, load balancing, fault tolerance and externalized configuration. The following list contains the some of the key objects to deploy and run applications in Kubernetes:

- **Deployment**: Kubernetes deployments provide a way to manage how and when an application is deployed into a given namespace. The [Deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) object defines a template of the Pods to be created, the [ReplicaSet](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/) to handle availability, and the deployment strategy to follow.

- **Service**: The [Service](https://kubernetes.io/docs/concepts/services-networking/service/) object serves as an internal load balancer and identifies a set of replicated pods in order to proxy the connections it receives to them. Additional backing pods can be added to, or removed from a service, while the service itself remains consistently available, enabling anything that depends on the service to refer to it through a consistent address.

- **Ingress**: The Service object only provides visibility inside a Kubernetes cluster, so in order to publish it to be consumed from the outside, the [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/) object is one of the most common approaches. This object provides an abstraction layer that automates the creation of routing rules pointing to a certain Service in a Kubernetes [Ingress controller](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/).

- **ConfigMap**: In order to keep containerized applications portable, configuration artifacts should be decoupled from image content. Kubernetes provides the [ConfigMap](https://kubernetes.io/docs/concepts/configuration/configmap/) object as a mechanism to inject configuration data in containers while keeping them agnostic to the platform. This configuration can then be consumed as environment variables or configuration files in a volume attached to the container.

- **Secret**: Since applications usually require some sort of sensitive information, like passwords or usernames, Kubernetes provides the [Secret](https://kubernetes.io/docs/concepts/configuration/secret/) object as a means to decouple such sensitive contents from the pods. As with ConfigMaps, the contents of a secret can be consumed by the application running inside a container via environment variables or as files inside an attached volume.

The level of knowledge about these objects required to bring a given application into a Kubernetes cluster will depend on the migration strategy that has been determined for that applications.

##### Rehost




##### Replatform

Replaforming applications deployed in other container platforms would traditionally require extensive knowledge of the specifics of the deployment manifests from the source platform, as they would have to be translated into Kubernetes objects. Doing this work manually is hard and doesn't scale well when dealing with a large portfolio of applications, so in most cases some degree of adhoc automation was used for this task.

Nowadays, tools like [Move2kube](https://move2kube.konveyor.io/) provide an automated approach for manifest translation, making this migration path very straightforward, automatable and ultimately scalable.


##### Refactor

One of the key concerns when onboarding an application coming from a traditional platform into Kubernetes is finding the right configuration for these objects to enable the application to run properly when it is containerized and deployed in a cluster. Traditionally this has required a deep knowledge about the application, its runtime and the configuration model it embraces, aside from a clear understanding of how to configure these objects. As with

#### Runtime images

A scalable way to make sure that applications can be onboarded safely into Kubernetes is to define a set of standard runtime images for the organization, managed and supported internally by a working group, area or department. It is important to make sure that these images are updated regularly, including fixes for all known CVEs and OS and runtime environment version updates, and always take care of backwards compatibility. For an adoption initiative, the proposed approach would be to define a standard image per runtime, or in very specific cases per application type.

#### Configuration model

#### Application Lifecycle Management

Without any doubt, the main reference in this new way of conceiving CI/CD is Tekton, a Kubernetes native CI/CD engine that fully implements a headless approach in which the cluster becomes the actual CI/CD tool through the use of Kubernetes CRDs. Tekton is an integral part of the Continuous Delivery Foundation that has been fully embraced by the community.

A common companion for Tekton is Argo CD, a Continuous Delivery tool that easily enables the adoption of GitOps principles in ALM. Argo CD is able to synchronize the desired state of deployment configuration stored in a Git repository with the live state of Kubernetes manifests deployed in a cluster, thus making the latter the source of truth for defining the desired application state. A typical way to combine Tekton and Argo CD is to let the first handle artifact and image builds while the second handles the actual application deployment and configuration.

## Pilot

The idea of the Pilot phase is to execute a migration sprint over a representative sample of all applications to cover as much of the available technical spectrum as possible.

### Execute the first adoption sprints

#### Migrate a representative set of applications

#### Documentation

#### Testing the migration process

#### Testing the organization

## Scale

In the scale phase ...

### Migrating the application portfolio

#### Scaling the adoption team

#### Migrating in sprints

## Knowledge Management

During the migration initiative, it is recommended to create and maintain a wiki to include all adoption/migration activities across the teams. The space should cover findings, solutions / tips & tricks, and lessons-learned in each of the teams.

A dedicated sharing space within the wiki should be consolidated to host the following content:

- **Scope and goals of the adoption/migration** outlining the main activities for each involved team (like application development, operations, infrastructure, etc.).

- **Step-by-step migration guide** containing a comprehensive and pragmatic approach to migrate an application from scratch. This guide should be prepared and written for the mentored developers conducting the migration of their own applications.

- **Migration cookbooks** being a thematic collection of “How-to” and “Known-solution” recipes. A recipe is a knowledge article describing an issue, its resolution and some additional information to learn more about it. In addition, it might link to the ticketing system to find tickets addressing the same subject. A wiki template should be created for migration "recipe" articles. The cookbook captures and documents solutions to all encountered issues so far in a central place accessible to all developers.

- **Platform FAQ** providing links and information to learn fast about Kubernetes (including CI/CD, development, provisioning, automation, promotion, monitoring).

- **Documentation of migration pilots** detailing meticulously all steps and changes done to the migrated pilot applications.

### Establish quality and documentation governance

The documentation will be crucial to scaling and increasing the efficiency of the migration execution. For many developers, it will be the first contact and the main interface to the project. Having a high-quality process encourages developers to contribute and improve the documentation.

This is why it is very important to aim for high standards. A review process should be defined for all new articles coming in, making sure they are meeting the expectations of the project (to the point, tagged, clear, complete). Dedicated roles (like architects or technical project lead(s)) have to endorse the responsibility of maintaining high documentation standards.


## Glossary

A glossary of common terms used within the guide

 - Rehost
 - Replatform
 - Refactor

# Infrastructure

**Authorship**<br/>
Written by Andres<br/>
Component developed by Amer


Needless to say, infrastructure plays a critical role in any cloud-based system. In this section, we will discuss the requirements which were specific to the choice of cloud platform and orchestration as well as all activities surrounding deployment to the cloud.

## Requirements

Based on the particular factors discussed, we identified the following points as being important requirements as part of our infrastructure:

1. **Platform agnostic components**: Our infrastructure must be deployable to both public and private clouds. Therefore, making use of provider-specific features was not possible.
1. **Budget**: The choice of cloud provider should allow us to prototype and test in a shoestring budget (US$100 for the whole semester).

In addition, the general requirements for the system with regards to scalability, performance, etc. also apply to this component, as mentioned.


## Evaluation Criteria & Decision-making Process

###  Cloud Platform

The cloud-agnostic nature of our system would have allowed us to deploy prototypes to any cloud provider to which we had access. Due to the unavailability of cloud resources to our project, however, we had a single choice when it came to deciding on the platform on which to deploy our infrastructure, namely AWS since it was the only platform for which we had credits. Conducting a thorough comparison of cloud platforms which we subsequently wouldn't have been able to use, did not seem like a good use of our time.

### Orchestration

Originally an internal Google project, Kubernetes was opensourced via a donation to the Cloud Native Computing Foundation (CNCF). Among others, its core functionality includes support for deployment, maintenance, and scalability of applications.


## Implementation Details

### Orchestration

Given its prominence in the cloud arena and the fact that it is open source software, we used Kubernetes for orchestration of cloud components. Having containerized data importers was a requirement from early on in order to accomplish scalability, and Kubernetes supports the creation and monitoring of containers natively via its *Pod* abstraction. In addition, the building blocks of our architecture such as the queue and the database, which must be guaranteed to be up and running, were declared as Kubernetes *Services*, which provide additional self-healing, should these critical components crash.

![](https://upload.wikimedia.org/wikipedia/commons/thumb/b/be/Kubernetes.png/600px-Kubernetes.png)

Additionally, internal DNS provides ease-of-use by being able to refer to components by a friendly name as opposed to IP addresses. As mentioned in the Architecture section, scheduling was realized via simple cron job.

Finally, in addition to the command-line interface provided by kops, a state-of-the-art web interface is provided out-of-the-box, making for a superior user experience.

### Configuration

One of the main advantages of Kubernetes is the ability to specify behavior of common abstractions via configuration alterable at run-time rather than by having to program components responsible for carrying out hard-coded logic. Configuration is provided by YAML files for registering crons, data importer job runtime configuration, and service-specific configuration necessary for inter-node communication.

### Deployment Automation

Due to the very specific constrains that our project found itself in, a disproportionately large portion of the effort went into managing infrastructure. Having no budget meant that whatever infrastructure was deployed had to be immediately torn down after it was no longer needed.

Difficulties notwithstanding, this meant that the automation process for deployemnt got well refined, with robust scripts, and the addition of Kubernetes Operations (kops) and its native support for AWS translated into an even smoother deployment experience.

Spot instances also played an important role in keeping to the budget, and support for these were was integrated into the scripts, which allowed us to monitor and specify the price for spot instances.

## Discussion

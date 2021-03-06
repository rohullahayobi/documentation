# Project Management & Organization

**Authorship**

|Version|Date|Modified by|Summary of changes|
|-------|----|-----------|------------------|
| 0.0.1 | 2017-07-19 | Andres, Oliver | Sections and initial outline |
| 0.1.0 | 2017-07-27 | Oliver | Outline & first written draft |
| 0.2.0 | 2017-07-28 | Oliver | Rephrased & extended team section |

The main challenge of project management is to achieve specific results within a given scope, time, quality and budget. At the end of every successful project, stands a unique product meeting the specified requirements. This chapter elaborates on the available resources and the development process and chosen approach. The resources that this chapter will focus on are the heterogeneous team and a limited budget.

## The Team

The project team consisted of seven masters students majoring in computer science, computer engineering and information systems management. Moreover, the team members are native of five different countries with different cultural backgrounds and native languages. Due to varying English skills and professional experience some discussion resulted to be time consuming. Furthermore, most members work besides being students, hence schedules had to be aligned accordingly.

The team was organized into expert groups for the various building blocks with one person in a coordinative role as the project manager. Thus, a hierarchy was formed within the group that was hard to enforce since in the end, the team was formed of peers. In the beginning, two teams had been formed; one that was responsible for the importing framework and data sources and one for the database and infrastructure. Throughout the project period the roles of several members shifted. In the end, one member focused on the modelling and administration of data sources, another member focused on the infrastructure provisioning and deployment, the team responsible for the importing framework consisted of three persons and lastly, one team two person team worked on the backend.

## Constraints

Like every other project, this one was subject to several constraints. The very nature of a project makes it a temporary endeavor. Thus, the product had to be finished within eleven weeks starting April 27 and ending July 13. Additionally, towards the end of the first half June 1, an interim meeting was held. During these eleven weeks three feedback sessions were held,one of them before the interim meeting and two after. After July 13 the two more weeks were available to write the documentation.

Moreover, monetary restrictions added an extra level of difficulty. While developing software consisting of several disparate building blocks, it is crucial to deploy and test the components individually and collectively. Due to lack of an own infrastructure, the only feasible way to conduct testing - in functionality and performance - was to use a cloud provider. Owing to no available budget the decision for the 100$ student grant offered by Amazon Webservices (AWS) was made. Nevertheless, this added even more effort to receive access to the student grant and, furthermore, to also be able to use all of the services provided. Some members did not receive any free credit from AWS.

Moreover, the budget constraints forced the team to start and terminate the whole infrastructure every time a test was conducted. In other words, a significant amount of time was necessary to create the test environment each time, which put additional straints to compensation of the already strict time limit. Hence, a significant effort was put into automating processes in order to provision the infrastructure and deployment of components.

Having dedicated infrastructure from the beginning would have allowed to focus more on the actual challenges of the project itself.

## Development Process & Approach

To best commence the given challenges and requirements, we decided for an agile development process. To be more specific, we used a stripped-down Scrum approach. In this approach, the distinction between different roles was not strict so that every team member held simultaneously the role of a product owner and developer for his specific building block of the global architecture. The same is applicable for the role of the Scrum Master, which was shared among the whole team, but still was mainly the responsibility of the Project Manager. Specifically, this means that every team member was encouraged to ensure an agile process and administrate issues. Furthermore, the workflow was adapted to our specific flow of work so that we convened a sprint meeting weekly with an additional weeekly interim meeting and omitted the daily scrum.

To keep track of every issue and the global progress, we used a Trello. Trello follows the kanban model and arranges issues into lists. We created four lists to mirror the lifecycle of issues. The first list - product backlog - is where requirements,issues and generally tasks are created that the product must satisfy. This list was populated coutinously as the project progressed. The next list included tasks due until the next sprint meeting. Following the next list contained tasks that were currently worked on and finally, all finished tasks were archived.

Slack was the chosen medium of communication, because it allows to distinguish between independant topics by using channels. Additionally, a shared calendar was used to remember deadlines and a shared file system to store the protocols of our meetings. More importantly we used GitHub to store the code written. We formed an organization and create a separate repository for every component.

In order to intergrate the tools we used as well as to automate workflows a service called Zapier was used. We used it to receive notifications about changes on Github and Google Drive and the progress made on Trello. Though, after the trial ended the functionality was limited so that only notifications about Github updates were send.
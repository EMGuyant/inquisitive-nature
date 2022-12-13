---
layout: post
author: Ethan Guyant
title:  "What is the Microsoft Power Platform"
category: Quick Start
tags: [Microsoft 365, Power Automate, Power Apps, Power Platform]
description: The Power Platform is a low-code platform that consists of various applications, tools, and services. The main applications of the Power Platform are Power BI, Power Apps, Power Automate, and Power Virtual Agents. The platform also contains other components to connect to data services, store data in a common data model, and build in artificial intelligence. This post offers an exploratory overview of the Power Platform and will be followed by a series of more deep-dive posts and walkthroughs. 
image: /assets/img/post_img/power-platform.jpg
image_by: Cristi Ursea
image_by_link: https://unsplash.com/ja/@cristiursea?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText
---

## Introducing Microsoft Power Platform
Microsoft Power Platform is a set of tools and services used to build custom applications and automate processes. It provides a low-code development platform for building applications, business intelligence tools, and process automation. The main components of the Power Platform are Power BI, Power Apps, Power Automate, and Power Virtual Agents. You can use the main components together or individually. 

Power BI is a business intelligence tool that allows for analyzing data and communicating insights. Power BI includes a desktop application for report development and a cloud service to host and share reports and dashboards.

Power Apps is a low-code platform for custom application development. Power App's simple and approachable interface allows business users and developers to create applications.

Power Automate is a workflow automation tool that helps automate repetitive processes (e.g. data collection, and document approvals). 

Power Virtual Agents is a tool to develop and deploy chatbots in a low-code environment. 

Utilizing the components of the Power Platform allows organizations to **Analyze** data and deliver insights, **Act** by building low-code solutions, **Automate** business processes, and **Assist** with inquiries with chatbots.

<br>

---

<br>

## Power BI
Power BI is a tool for analyzing data and making informed decisions. The basic parts of Power BI include Workspaces, Datasets, Reports & Dashboards, and Apps.

A workspace is a container to store related datasets, reports, dashboards, and dataflows. Workspaces come in two types either My workspace or workspaces. My workspace is a personal workspace. Workspaces is a container for collaborating and sharing content. All workspace members require a Power BI Pro license.

Datasets are the data imported, connected to, or created within Power BI. The datasets are the data that underlie Power BI reports. When creating a  dataset you associate it with a workspace. You can include a dataset in multiple workspaces and use it in multiple reports. 

A report is a collection of visualizations (e.g. line chart, bar chart, KPIs, etc.). Reports can consist of multiple pages each with its own set of visualizations. A Dashboard is a collection of tiles. A tile can display a single visualization pinned from a report or an entire report page. An app in Power BI is a collection of reports, dashboards, and datasets that you package together and share.

<br>

### Power BI Desktop
Power BI Desktop is a free application that you can use to extract and transform data. The data can be from various sources and you build reports on this data. After extracting the data Power BI provides the option to transform the data. The data transformations can range from data cleaning operations to improving readability by clarifying column names and setting data types to more complex operations.

There are three main pages in Power BI Desktop which you can navigate between on the left menu. The first is the Report page. This is the report canvas where you add and configure visualizations. 

The second is the Data page. Once you load data to the Power BI data model you can view it on this page. Also, from here you can perform additional data manipulation. This can include operations such as adding calculated columns using the Power Query Editor.

The last is the Model page. On this page, you can construct and view the data model. You can also view, configure, and add any relationships between different data tables. 

<br>

### Power BI Report Visuals
When viewing the report canvas you can add a variety of elements to your report from the Insert menu on the top ribbon. On the right-hand side of the Power BI Desktop application, there are panes for Filters, Visualizations, and Fields. You can add visuals to the report from the Visualizations pane. On the Field pane, you are able to add or change the data displayed on the visual. There are many built-in visualizations to add to Power BI reports. A few examples include bar and column charts, single and multi-row cards, KPIs, pie charts, and tables. In addition to the built-in visualizations, you can add custom visuals from the Power BI AppSource.

<br>

### Publishing Power BI Reports
Once you complete a report you must publish it to share with others. You publish a report from the Power BI Desktop Home menu using the Publish option in the top ribbon. When publishing a report you must select the workspace to associate it with. Selecting any workplace other than My workspace requires a Power BI Pro license. Any reports published to My workspace are for personal content.

For more details on Power BI Fundamentals check out this four-part series.
* [Power BI Fundamentals: Part 1 - Row Context](https://ethanguyant.com/blog/2022-09-28-power-bi-row-context/){: .post__link}
* [Power BI Fundamentals: Part 2 - Iterator Functions](https://ethanguyant.com/blog/2022-10-11-power-bi-iterators/){: .post__link}
* [Power BI Fundamentals: Part 3 - Filer Context](https://ethanguyant.com/blog/2022-09-28-power-bi-row-context/){: .post__link}
* [Power BI Fundamentals: Part 4 - Context Transition](https://ethanguyant.com/blog/2022-11-08-power-bi-context-transition/){: .post__link}

<br>

---

<br>

## Power Apps
Power Apps is a rapid low-code platform for custom application development. It consists of apps, connectors, and data that are all integrated providing the tools and environment required for application development. 

With Power Apps, there are 3 types of apps that you can create. The first type is a Canvas app. These apps start blank and connect to various data sources. You construct the app using the low code interface. Model-driven apps are applications built on top of an existing data model. You build these apps using forms, views, and dashboards. Dataverse is the data source for model-driven apps. The last type of app is Portal. Portal creates public-facing websites. Like model-driven apps, Dataverse is the data source for Power Apps Portal.

<br>

### The Build Blocks of Power Apps
The basic building blocks of Power Apps include screens, controls, and functions. Screens are the canvas of the app's user interface. You add different components and controls to the screens. Each app can have multiple screens. Each with its own set of controls, with each screen typically serving a different purpose.

Components are reusable groupings of controls and formulas within the app. Components become helpful when creating parts of an app (e.g. navigation section) that repeat on multiple screens. Without the use of components the repeated part of the app would have to be rebuilt on each screen.

Controls are the different elements that make up the app. Controls include things such as buttons, text labels and inputs, galleries, and icons. The complete list is viewable in the Power Apps Studio on the Insert tab. Each control has its own array of properties and events which are viewable after adding it to the app.

The basic building blocks above develop the visual aspects of the app. However, typically there is a data aspect to the app. Power Apps connectors connect to and access data from various sources. There are standard connectors and there are premium connectors that require a Power Apps premium license. 

<br>

---

<br>

## Power Automate
Power Automate is a workflow engine used to improve business processes through automation. It excels at automating repetitive manual processes which consist of predefined steps. The processes automated can range in complexity. They can be as simple as sending notifications and document approvals. Or complex multi-flow processes where certain tasks are conditionally triggered.

Power Automate flows consist of the trigger, actions, and controls. The triggers available depend on the type of flow. While the actions available depend on the specific connector. Controls can create conditional evaluations and branches within the workflow.

<br>

### Types of Flows
In Power Automate there are three types of flows.

Cloud flows are flows built that consist of a trigger and at least one other action. There are different types of cloud flows.  Automated flows get triggered when a specific action occurs. Examples of these trigger actions include a new document in a SharePoint document library or when a new Outlook email arrives. Instant flows are triggered by a user. The trigger of instant flows can be a button click, running the flow from a Power App, or on a selected SharePoint list item.

Business Process flows provide a guided experience for the collection and entry of data. They augment the experience of a model-driven app.

Desktop flows provide robotic process automation to Power Automate. Desktop flows allow users to record their actions while completing a process. These actions and interactions with applications are then played back and automated by the flow.

For Power Automate examples please see the following related posts.
* [Microsoft Power Automate: Outlook Inbox Cleanup](https://ethanguyant.com/blog/2022-08-19-powerautomate-inbox-cleanup/){: .post__link}
* [Microsoft Power Automate: Dynamic Approval Cycle](https://ethanguyant.com/blog/2022-09-07-powerautomate-dyanmic-approval-cycle/){: .post__link}

<br>

---

<br>

## Power Virtual Agents
Power Virtual Agents is an app powered by AI and used to create chatbots. Power Virtual Agents is a tool to develop solutions for a specific topic where the bot will ask a series of questions. Using the responses to the questions the Power Virtual Agent will perform associated actions.

<br>

### Topics
When developing a Power Virtual Agent a topic is what the person who is interacting with the bot talks to the bot about. The topic is a discrete conversation path that defines how the conversation will be processed. Each topic has phrases, keywords, or questions that act as trigger phrases. These phrases define how the bot responds and what it should do.

<br>

### Entities
Entities group information. Power Virtual Agents provide prebuilt entities and the ability to create custom ones. Pre-built entities represent commonly used information. With the use of these entities, the bot recognizes relevant information from user interactions. The information is then saved and used to inform later actions. Use custom entities when developing a chatbot for a specific purpose. Creating a custom entity involves teaching the chatbot language understanding model the domain-specific information.

<br>

### Canvas
The canvas is where the conversation pattern gets constructed. The conversation pattern generally consists of questions, conditions, and messages. Questions can be multiple choice, text input, or an entity. The response to questions then gets stored in variables. Conditions create flow control and branches within the conversation pattern based on the responses to questions. Messages are the blocks of text displayed on the screen and viewed by the user. 

<br>

### Actions
The Power Virtual Agents can perform actions by calling a Power Automate flow. The flows get passed the required information from Power Virtual Agent. Power Virtual agents can leverage flows that are already created in the Power Apps environment or can use a flow created within the Power Virtual Agent canvas.

<br>

### Publishing
Once complete the chatbot can be published to multiple platforms or channels including websites, mobile apps, and Microsoft Teams. Following each update of the chatbot, it must be published again to update the bot on all channels.

<br>

---

<br>

## Power Platform Related Components
Across each of the four apps mentioned there are cross-cutting features that enable utilizing the Power Platform to its full potential. The Power Platform products use a set of three shared services or components. The core components include AI Builder, Dataverse, and Connectors. These components allow the Power Platform apps to be closely integrated.

AI Builder is a solution that lets users add intelligence to created workflows and apps. These AI capabilities can predict outcomes and aid in improving business performance.

Dataverse is a data storage service that allows users to securely store and manage data. A Dataverse database provides the data structure supporting interconnected apps and processes.

Connectors enable users to connect apps, data, and devices. They act as an abstraction layer for APIs for other services. 

<br>

---

<br>

If you enjoy what you read and find it helpful please check back, and check back often. Follow me on <a class="post__link" href="https://medium.com/@emguyant"><i class="fab fa-medium"></i> Medium</a> while giving a clap to the article! Also don't forget to subscribe to the <a class="post__link" href="https://medium.com/inquisitive-nature"><b>INQUISITIVE NATURE</b></a> publication.
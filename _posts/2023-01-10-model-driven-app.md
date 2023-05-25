---
layout: post
author: Ethan Guyant
title:  "Lets make a model-driven app"
category: Deep Dive
tags: [Microsoft 365, Power Apps, Power Platform]
description: Take a dive into Power Apps Model-driven Apps. This post covers general background of Power Apps and the Power Platform Dataverse. Providing foundational knowledge before diving into Model-driven app specific information. Finally, the post wraps up with a step-by-step walk-through on creating your first Model-driven app.
image: /assets/img/post_img/model-driven-app.jpg
image_by: Resource Database
image_by_link: https://unsplash.com/@resourcedatabase?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText
---

<br>

# Model-Driven Apps

<br>

This post starts with background information on Power Apps in general, Dataverse, and Model-Driven apps. If you are looking to jump right into building a model-driven app click here [Build a Model-driven App](#build-a-model-driven-app){: .post__link}

<br>

## Background Information

<br>

Power Apps is a rapid low-code application development environment consisting of apps, services, connectors, and a data platform. It provides tools to build apps that can connect to your data stored in various data sources. The data sources can be Microsoft Dataverse (i.e. the underlying data platform) or other online and on-premises data sources including SharePoint, Excel, SQL Server, etc.

Within the Power Apps development environment you build applications with visual tools and apply app logic using formulas. This approach is similar to other commonly used business tools. Meaning you can get started using skills and knowledge you already have. The Power Platform also provides the opportunity to build upon the platform with custom developed components providing a way to create enriched experiences using web development practices.

The Power Apps platform provides two different types of apps that you can create. There are Canvas Apps and Model-driven Apps. Both app types are similar and built with similar components however, the major difference lie in the amount of user control and uses cases.

Canvas apps provide the user with the most control when developing the app. A canvas app starts with a blank canvas and provides full control over every aspect of the app. In addition to providing full control over the app a canvas app supports a wide variety of data sources.

Model-driven apps begin with the data model and are built using a data-first approach. The data-first approach requires more technical knowledge and is best suited for more complex applications. Model-driven apps are controlled by and depend on the underlying data model. The layout and functionality of the app is determined by the data rather than the user who is developing the app.

The use cases of canvas apps and model-driven apps are different and each are leveraged in different situations. Canvas apps provide flexibility in the appearance and data sources and excel at creating simplified apps. Model-driven apps build a user interface on top of a data model utilized for a well defined business process.

This article will focus on creating a model-driven app including the underlying data model. Check back for a follow up article on creating your first Canvas App.

<br>

---

<br>

## Dataverse Introduction

<br>

Dataverse is a relational database and is the data layer of the Power Platform.

>Dataverse was formerly known as the Common Data Services

Like other relational databases it contains tables or entities as the representation of real world objects. Relationships define how table rows relate to rows from other tables. What sets it apart from traditional relational databases are the business-oriented features. Some of these features include the set of standard tables and automatically adding columns to custom tables which support underlying processes. It also provides features such as creating views, forms, dashboards, charts, and business rules directly from the table configuration.

The components of the Dataverse include Storage, Metadata, Compute, Security, and Lifecycle Management.

The storage layer has different types of data storage available. Each type suited for different needs and types of data. These storage types include relational data, file storage, and key:value data.

The metadata component stores information about the primary data in Dataverse. A simple example of metadata for a table is the `Display Name` which you can customized. After applying customizations the changes in the table definition get stored in the metadata layer. The metadata layer is then available to the various components of the Power Platform. The metadata layer consists of the schema and the data catalog.

The compute layer is a set of functionalities that include Business Logic, Data Integration, and the API layer. Business rules or logic that apply across all applications for an organization get applied in a single location rather than each individual application. The single location these rules get applied is the Business Logic sub-layer. The business logic layer contains business rules, workflows, and plugins. The Data Integration layer consists of methods which bring data into the platform and integrating existing data in other data sources. The API layer provides the interface for other application to connected to Dataverse.

The security layer of Dataverse can support enterprise-grade security for applications. Some features relevant to building applications include authorization, privilege, roles/groups, and auditing. Data in Dataverse is only available to authorized users with a security model based on the various components (e.g. table, columns, rows). A user's privilege define what level of access they have or what they are able to do within the system (e.g. read, write, delete). Roles/groups are privileges that get bundled together. Authentication is the process of validating the identity of someone trying to access the system.

Application lifecycle management (ALM) is the process of managing the different phases of creating and maintaining an application. Such as development, maintenance, and decommissioning. The Lifecycle Management layer helps this process through implementing different environments, diagnostics, and solutions.

>When working with the Power Platform, Dataverse, and multiple times in this post already you will come across the term Data Model so it is important to define. The data model is the set of tables and table metadata (e.g. relationships between tables). In the context of model-driven apps when we refer to the data model we mean Dataverse.

<br>

---

<br>

## Model-Driven Apps Introduction

<br>

Model-driven apps build a user interface on top Dataverse. The foundation of the app is the underlying data model. Although a main requirement in any app development is setting up data sources, for model-driven apps it is the primary requirement. The first and essential step of model-driven app development is properly structuring the data and processes. The user interface and functionality of the app is dependent on the data model.

The development approach for model-driven apps has 3 focus areas:

* Data Model: determining the required data and how it relates to other data
* Defining Processes: defining and enforcing consistent processes is a key aspect of a model-driven app
* Composing the app: using the app designer to add and configure the pages of the application

### Components of Model-driven Apps

The components of a model-driven app get added through the app designer and build the appearance and functionality of the app. The components included in the app and their properties make up the app's metadata.

There are four main types of components in the application and each has a designer used to create and edit the component.

#### Data Components

The data components specify the data the app builds upon.

The app design approach focuses on adding dashboards, forms, views, and charts to the application. The primary goal of a model-driven app is to provide a quick view of the data and support decision making.

| Component | Description | Designer |
| :-------: | :---------: | :------: |
| Table | A container of related records | Power Apps table designer |
| Column | A property of a record within a table. | Power Apps table designer |
| Relationship | Define how data in different tables relate to one another. | Power Apps table designer |
| Choice | Specialized column that provides the user a set of predefined options. | Power Apps option set designer |

#### User Interface Components

The user interface (UI) components define how the user will interact with the app.

| Component | Description | Designer |
| :-------: | :---------: | :------: |
| App | The fundamental properties of the application which specify the components, properties, client types, and URL | App designer |
| Site Map | Determines the navigation of the app | Site map designer |
| Form | A set of data-entry columns for a specified table | Form designer |
| View | Define how a list of records for a table appear in the app | View designer |

#### App Logic

The logic defines the processes, rules, and automation of the app.

| Component | Description | Designer |
| :-------: | :---------: | :------: |
| Business Process Flow | A step-by-step aid guiding user through a process | Business process flow designer |
| Workflow | Automated process without a user interface | Workflow designer |
| Actions | A type of process invoked from a workflow | Process designer |
| Business Rule | Apply rules or recommendation logic to a form | Business Rule Designer |
| Power Automate | Cloud-based service to create automated workflows between apps and services | Power Automate |

#### Visual Components

The visual components visualize the app data.

| Component | Description | Designer |
| :-------: | :---------: | :------: |
| Chart | A single graphic visualization within a view, on a form, or added to a dashboard | Chart designer |
| Dashboard | A collection of one or more visualizations | Dashboard Designer |
| Embedded Power BI | Power BI tiles and dashboards embedded in an app | Chart designer, dashboard designer, Power BI |

<br>

---

<br>

## Build a Model-driven App

<br>

We will be creating an app for Dusty Bottle Brewing. Dusty Bottle Brewing is a brewery that operates multiple tap rooms and distributes products to other local businesses.

The goal of the app is provide greater insight on tap room locations and partners. The app should provide details on tap room capacities, outdoor seating, food availability, pet policies, landlords, etc.

### Setting Up a Development Environment

First we will set up an environment to develop the app within. An environment is a container to store, manage, and share apps, workflows, and data. Environments can separate apps by security requirements or audiences (e.g. dev, test, prod).

Environments are created and configured on the Environments page of the [Power Platform Admin Center](https://admin.powerplatform.microsoft.com/){: .post__link} (`admin.powerplatform.microsoft.com`). Each tenant when created has a Default environment, although it is generally recommended not to use this environment for app development that is not intended for personal use.

![Power Platform Admin Center](/assets/img/2023-04-01-model-driven-app/1_powerplat_admin_center.png){: .post__img}

We will start by creating a environment for use during this post. Types of environments include Sandbox, Trial (subscription-based), Developer, and Production. Choose the environment appropriate for your needs, you can review more details [here](https://learn.microsoft.com/en-us/power-platform/admin/create-environment){: .post__link}.

During creation we toggle on `Create a database for the environment` which will set up Dataverse within the environment. On the next pane for this demo we also toggle on `Deploy sample apps and data`. This will provide data that we can easily work with for this demo app. Then clicking `Save` will provision the environment.

![Power Platform Admin Center](/assets/img/2023-04-01-model-driven-app/2_create_environment.gif){: .post__img}

Then we can navigate to [make.powerapps.com](https://make.powerapps.com/){: .post__link}.

When your tenant has multiple environments it is important to note which environment you are working in. You can view the current environment on the top right of the screen. You switch between environments by clicking on the `Environment` area of the top menu. This will open the Select environment pane where you can switch between the available environments.

![Power Platform Admin Center](/assets/img/2023-04-01-model-driven-app/3_environments.png){: .post__img}

Once in the correct environment you could start creating apps with the options provided. However, before we do we will first look at solutions.

### Solutions Overview

A solution is a container within an environment that holds system changes and customizations (e.g. apps). You can export a solution from an environment, as a `.zip` file and deploy it to other environments.

In this demo we will first create a solution to hold the customization needed for the app we will create.

When working with solutions you will also need to be familiar with publishers. All solutions require a publisher and the publisher will provide a prefix to all the customizations (e.g. a prefix to table names).

![Power Platform Admin Center](/assets/img/2023-04-01-model-driven-app/4_create_a_solution.gif){: .post__img}

Now that we created the `DustyBottleBrewery` solution we can start developing our model-driven app within this solution.

### Design the Data-model

We will start creating the data model for the app first focusing on tables, columns, rows, and relationships in Dataverse. When working with Dataverse the common terminology includes table, column, row, choice, and Yes/No. You may come across the associated legacy terms entity, field/attribute, record, option set/multi-select option set, pick list and two options.

The tables that we will need for the application include Brew Pub, Landlord, Accounts, and Contact tables. Here it is important to note that when we provisioned Dataverse for an environment it comes with a set of commonly used standard tables. So before creating custom tables it is helpful to evaluate the standard tables.

>Although helpful to use standard tables when possible it is important to not use a standard table for something completely different than it's intended use

For example, in Dataverse there already is a Contact table that includes columns such as address and phone number. We can use this standard table for the Brew Pub's contact information.

>Note: the contact table could also be used for the landlord information, but for demonstration purposes we will create a custom table

A summary of the tables we will work with is:

| BrewPub (Custom) | Landlord (Custom) | Contact (Standard Table) | Account (Standard Table) |
| :--: | :------: | :-----: | :-----: |
| Street Address | Street Address | Built-in Columns | Built-in Columns |
| City | City | | |
| State | State | | |
| Phone Number | Phone Number | | |
| Capacity | | | |
| Pets Allowed | | | |
| Patio Seating | | | |
| Food Available | | | |
| Landlord | | | |
| Contact | | |

You may have noticed that the `BrewPub` table contains a Landlord and Contact column. These will be created when creating the relationships between these tables and serve as the basis for the relationships we will create within the data model.

#### Creating Tables

You create a custom table by navigating to the environment where the app is being developed (e.g. `DustyBottleBrewery`). Then on the Solutions page select the  solution. On the Solution Objects page you can create a new table by selecting New on the top menu and then table. For this demo we will provide the table name and leave the other options as their default values. However, there are many advanced options that you can configure if needed.

![Power Platform Admin Center](/assets/img/2023-04-01-model-driven-app//5_create_custom_table.gif){: .post__img}

After creating the table you can view the columns and see that it comes with a number of automatically created columns. These columns support underlying system processes. We will have to add some column such as Phone Number, Street Address, City, State, and all other columns listed above.

You add columns by expanding Tables on the Object pane of the solution and then expanding the specific table to show its components. Then select columns to view the columns page.

From the columns page there are two options, add a New column or Add existing column. We will add the columns below with the New column option.

| Column Name | Data Type |
| :---------: | :-------: |
| Street Address | Text > Plain text |
| City | Text > Plain text |
| State | Text > Plain text |
| Phone Number | Text > Phone number |
| Capacity | Number > Whole number |
| Pet Allowed | Choice > Yes/no|
| Patio Seating | Choice > Yes/no |
| Food Available | Choice > Yes/no |

![Power Platform Admin Center](/assets/img/2023-04-01-model-driven-app/6_create_columns.gif){: .post__img}

After adding the columns to the newly created `BrewPub` table repeat the process for the `Landlord` table.

After creating our two custom tables we must add the existing Contacts and Accounts table to our solution. We can do this by using the Add existing option in the menu. After selecting the table there are two options to be aware of. The first is include all components. The components of the table include the columns, relationships, views, etc. If this option is not selected specific components can be explicitly selected and added to the solution. The second is include table metadata. If you include all components then this option is selected and disabled. If components are added individually this option will have to be selected to include the metadata.

![Power Platform Admin Center](/assets/img/2023-04-01-model-driven-app/7_add_existing_table.gif){: .post__img}

#### Creating Table Relationships

Relationships define how tables in the database relate to other tables. When working with the Power Platform there are two main relationship types to work with.

A one-to-many relationship is when a row in the *Primary Table* is associated or reference many rows in the *Related Table*. In Power Apps there are actually three relationship types listed when creating a relationship. However, every one-to-many relationship is also a many-to-one relationship viewed from the perspective of the related table. For this type of relationship, different relationship behaviors can be defined (e.g. cascading delete). For more detail on the various behaviors and actions check out [Table Relationships](https://learn.microsoft.com/en-us/power-apps/maker/data-platform/create-edit-entity-relationships){: .post__link} for more information. Power Apps does provide pre-defined behavior/action grouping that can be used. These include Referential Remove Link, Referential Restrict Delete, and Parental.

A many-to-many relationship is when many rows in one table are associated or reference many rows in another table.

Creating a one-to-many (or many-to-one) relationship can be done in two ways. The first is to create a lookup field which creates the relationship for you. The second is to manually create the relationship which creates the lookup field for you. Manually creating the relationship is the only option available for the many-to-many relationship.

We will first create the one-to-many relationship between `Landlord` and `BrewPub` by creating  a lookup field. In this relationship a `BrewPub` can have *one* landlord and a `Landlord` can have *many* BrewPubs. So the first question is on which table to add the lookup field.

>When using a lookup field you are looking up a **single** thing. You are looking up to the one side of the relationship. This means the lookup field should be created in the table on the *many* side of the relationship.

![Power Platform Admin Center](/assets/img/2023-04-01-model-driven-app/9_create_one_to_many.gif){: .post__img}

Now will will create the many-to-many relationship between `BrewPub` and `Contacts`. In this relationship a `BrewPub` can have multiple contacts and a `Contact` can be associated with multiple BrewPubs. Since this relationship is many-to-many in Power Apps it does not matter which table you select to create the relationship.

![Power Platform Admin Center](/assets/img/2023-04-01-model-driven-app/10_create_many_to_many.gif){: .post__img}

<br>

---

<br>

## Creating the App and the App Components

<br>

Now that the underlying data model is complete we can move to creating the user interface (UI). The UI resides on top of the data-model that users will interact with. Creating the app involves working with various UI components including the site map, forms, and views. In addition to the visual components we will incorporate business rules.

We create the model-driven app from within the solution under New > App > Model-driven app.

![Power Platform Admin Center](/assets/img/2023-04-01-model-driven-app/11_create_app.gif){: .post__img}

### App Navigation

With the app now created we start by building out the site map or the navigation element of the app. On the left hand menu select Navigation to view the Navigation pane. Here you will see listed a few options created automatically. The Navigation bar is where you can set the options to show Home, Recent, and Pinned which are enabled by default. You can also enable collapsible groups and enable areas, both of these options are disabled by default.

The `Group` (and `Area` if enabled) are different levels of containers that hold the navigation links. The `Subarea` which are contained within a group are linked to a content type. The content types include tables, dashboards, custom pages, web resources, or a URL.

We will first create an `Accounts and Contacts` group with two subareas. The subareas will be `Accounts` and `Contacts` linked to the associated table. Then we will repeat this process creating a second `Dusty Bottle Brewery` group with subareas for `BrewPubs` and `Landlords.`

![Power Platform Admin Center](/assets/img/2023-04-01-model-driven-app/12_site_map.gif){: .post__img}

>Before getting to far building the app its important to mention the top right options in the menu bar. Mainly `Play` and the difference between `Save` and `Publish`. Within the app designer you can view a preview of the app's UI, the app can also be viewed by pressing `Play`. `Save` saves the version of the app you are working on but does not make those changes live and viewable to users of the application. To make any changes available to users the changes must be published first.

### App Forms

After building the navigation element we will add Forms to the app. Forms display a single row of data from a table. There are various elements of the form used to view associated data and carry out tasks.

1. Command Bar: used to take action such as saving a record or creating a new one

2. Tabs: easy access to grouped information

3. Columns: displays the column data for the specific record

4. Lookup Fields: specialized column to lookup a single related record

5. Timeline: a list of recent actions or activities associated with the record

6. Subgrid: displays the many side of a relationship (e.g. all the contacts associated with the account below)

7. Form Selector: navigate between different forms for the specific table

![Power Platform Admin Center](/assets/img/2023-04-01-model-driven-app/13_form_description.png){: .post__img}

The form selector (#7 above) navigates to different forms. There are various types of forms that look different and offer different functionalities.

| Type | Description |
| :--: | :---------: |
| Main | main user interface for working with table data |
| Quick Create | abbreviated form optimized for creating new rows |
| Quick View | read-only form contained within another form to show information about related data |
| Card | presents data in the unified interface dashboards |

We will modify the main form of the `BrewPub` table. We locate the table in the objects viewer and navigate to Forms. All forms created automatically have a Name of `Information` so the main form can be identified by the Form type. Select the correct form to open the Form designer.

![Power Platform Admin Center](/assets/img/2023-04-01-model-driven-app/14_form_designer.gif){: .post__img}

From here we can modify and add elements to the form. By default any required fields are included in the form (e.g. Name and Owner).

Within the form designer the default layout is 1 column and can be modified to 2 or 3 columns in the Formatting properties of the section. We will use 2 columns. Following this we will add additional table columns to the form. All available columns can be seen by selecting the Table columns in the left hand menu and then dragged and dropped on the form.

![Power Platform Admin Center](/assets/img/2023-04-01-model-driven-app/15_edit_form.gif){: .post__img}

Additional sections can also be added from the Components menu. Sections here are containers for the displayed fields. We will add a new Contacts section to display the related contacts for the BrewPub record. Previously, we created a many-to-many relationship between the `BrewPub` and the `Contact` tables. Since for each BrewPub we need to display multiple contacts we will need to add a subgrid to this new section.

![Power Platform Admin Center](/assets/img/2023-04-01-model-driven-app/16_add_section.gif){: .post__img}

Following the change we Save and Publish to make the updates available. Then we can go to the Power App and add an example BrewPub and Landlord. Navigate to each in the left-hand Navigation of the app and select New.

After adding the data we can view a BrewPub record and associated contacts with that BrewPub using the subgrid. Navigate to the BrewPub Form and in the Contacts section select Add Existing Contact in the subgrid. This will open a lookup record menu, and since the dataverse was loaded with sample data, a list of contacts is presented. Select appropriate records and click Add.

>To add or remove sample data view more information here [Add or remove sample data](https://learn.microsoft.com/en-us/power-platform/admin/add-remove-sample-data){: .post__link}

![Power Platform Admin Center](/assets/img/2023-04-01-model-driven-app/17_add_contacts.gif){: .post__img}

### App Views

Views within a model-driven app display a list of rows that are typically the same type (e.g. Active Contacts). The view definition is primarily made up of the columns that are displayed and any sorting or filter that should be applied.

There are three main types of views. Personal views are owned by an individual and only visible to them and anyone they share it with. Public views are general purpose and viewable by everyone. System views are special views used by the application (e.g. Quick Find, Lookup).

You toggle between different views from within the App. The default view can also be changed from within the app. The current default view for Contacts in My Active Contacts. We will first change the view to Inactive Contacts and then set the default to Active Contacts.

![Power Platform Admin Center](/assets/img/2023-04-01-model-driven-app/18_change_views.gif){: .post__img}

### Business Rules

Business rules are utilized to dynamically update the app UI. Typical use cases for business rules include displaying error messages, setting or clearing field values, setting required fields, showing or hiding fields, and enabling or disabling fields.

>It is important to note that business rules are table specific.

We will create a business rule for the `Account` table to set the value of a company's `Payment Terms` based on the company's `Credit Limit`. First we look at the details of an Account and in the Billing section we can see both of these values are blank.

![Power Platform Admin Center](/assets/img/2023-04-01-model-driven-app/19_billing_section.png){: .post__img}

The business rule we will create looks at the companies credit limit and if it is greater or equal to $125,000 then the payment terms should be set to Net 60. Otherwise, the payment terms is set to Net 30.

To create the new business rule we must go to view the objects in the solution. Expand the `Account` table then Business rule, and finally New business rule. After selecting New business rule the visual designer will open in a new tab.

First we need to create the condition of the business rule. We click on the condition in the designer window and enter the properties and then Apply. Following the condition we specify what should occur if the condition is `True` and if it is `False`. For this we will add two `Set a Field Value` components.

Once the business rule is complete we must Save it and then Activate the rule.

![Power Platform Admin Center](/assets/img/2023-04-01-model-driven-app/20_create_business_rule.gif){: .post__img}

After activating the business rule we can move back to the app UI to see it in action.

<br>

---

<br>

## Model-Drive App Security

<br>

Before adding a user to the environment the new app resides in the user must first be a user in the Microsoft tenant. If the user does not yet exist the account must be created in the tenant before adding then to the Power Apps environment.

After creating the user in the tenant we can go to the [Power Apps Admin Center](https://admin.powerplatform.microsoft.com/), and select Environments. We then navigate to the environment we are working in. Users are added to an environment in the environment settings under `Users + permissions`. You can also access this on the Environment overview screen in the `Access` section, under Users select `See all`. Once on the Users page select `Add user` and then search for the user to add. After adding the user you are prompted to set an appropriate security role for the user.

![Power Platform Admin Center](/assets/img/2023-04-01-model-driven-app/21_add_user.gif){: .post__img}

In general a security role defines what actions a user is allowed to do and where they are allowed to do those actions. For example a user's security role could specify they have read permissions on the `Account` table. The permissions provided by the security role are on a per table basis. The same role describe above could provide the user read permissions on the `Account` table and write permissions on the `BrewPub` table.  

In addition, to specifying the entity within the security role definition you can also specify which rows within the table the user can read or modify.  More information on the built-in security roles and configuring a custom security role can be found here: [Configure user security to resources in an environment](https://learn.microsoft.com/en-us/power-platform/admin/database-security){: .post__link}.  When we added the new user to the environment we assigned them the `Basic User` security role. Looking at the documentation, linked above, we can get more information on the type of privileges the role has.  

| Security Role | Privileges | Description |
| :-----------: | :--------: | :---------: |
| Basic User | Read (self), Create (self), Write (self), Delete (self) | Can run an app within the environment and perform common tasks for the records that they own. Note that this only applies to non-custom entities. |  

An important thing to notice in the description is the last note. The `Basic User` role's default privileges only apply to non-custom entities. For any custom table the privileges must be explicitly assigned.

>Users, by default, do not have access to Custom tables. Privileges must be explicitly granted.  

When building the Data Model for the app we created the `BrewPub` and `Landlord` custom tables. In this environment We want the `Basic User` role to have access to these tables. To provide this access we must customize the role.

Security roles are viewed, modified, and created by navigating to the environment settings > Users + permissions > security roles. In the list of roles we will locate the `Basic User` role and select the check mark next to it. Then on the top menu select `Edit` to open the security role designer.  Then on the Custom Entities tab we locate the `BrewPub` and `Landlord` table and give the role basic access to these tables.

![Power Platform Admin Center](/assets/img/2023-04-01-model-driven-app/22_configure_security_role.gif){: .post__img}

>Creating, configuring, and managing security roles can be very detailed and granular which highlights the importance of having clearly defined requirements related to app security.

<br>

---

<br>

## Sharing a Model-driven App

<br>

Sharing a model-driven app consists of three steps including setting up the security roles, sharing the app, and finally providing the app URL to the users.

To share our Dusty Bottle Brewery app, we select the check mark next to the app in our solution. Then on the top menu select `Share`. This opens a pane to share the app and consists of a couple different parts.

First on the top left is the security role of the app. This specifies the security roles that can be used by the app.

Second, under people we search for the users that we want to share the app with and set their security role.

![Power Platform Admin Center](/assets/img/2023-04-01-model-driven-app/23_share_app.gif){: .post__img}

Lastly, we must share the app URL with the users. The web URL is located on the app details page and provided to the users of the app. In addition, there are other ways to provide access and includes methods such as embedding an app within Microsoft Teams.

<br>

---

<br>

## Next Steps

<br>

Now that the data-model and the app UI have been created, the security roles configured and assigned, and the app has been shared with the users the focus shifts to management of the app.

This management is simplified through the use of solutions. Remember the solution acts as a container of all the different components which can be deployed to different environments (e.g. development to testing to production).

There are many options on how the management can be carried out and involves working with unmanaged and managed solutions. The deployment of the app can be a manual process or utilize tools such as Azure DevOps pipelines to automate and incorporate source control into the deployment process.

Check back for a follow up post focused specifically on the lifecycle management of apps created in Power Apps.

<br>

---

<br>

## Summary

<br>

This post covered the different aspects of building and deploying a Power Apps Model-driven app. We started with general background information about Power Apps. Highlighted the two types of apps that you can build in Power Apps. These app type primarily differ on the amount of user control over the app's data sources and interface elements. Canvas apps can be used with a variety of data sources and allow full control over the app's user interface. And Model-driven apps must be used with Dataverse and the user interface is driven by the underlying data-model.

After covering the basics of Power Apps the post provided an introduction to Dataverse. Understanding of Dataverse and its components is critical for model-driven apps.

Then the post provides more detailed background on Model-driven apps specifically. Covering the different components that make up the model-driven app.

Finally, the post provided a step-by-step to get a model-driven app up and running. This started with the development of the data-model, the creation of the app UI, defining security roles, and sharing the app with end users.

<br>

---
<br>

If you enjoy what you read and find it helpful please check back, and check back often. Follow me on <a class="post__link" href="https://medium.com/@emguyant"><i class="fab fa-medium"></i> Medium</a> while giving a clap to the article! Also don't forget to subscribe to the <a class="post__link" href="https://medium.com/inquisitive-nature"><b>INQUISITIVE NATURE</b></a> publication.

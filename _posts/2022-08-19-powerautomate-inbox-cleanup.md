---
layout: post
author: Ethan Guyant
title:  "From Chaos to Clarity: Revolutionize Your Inbox with Power Automate"
category: Quick Start
tags: [Microsoft 365, Power Automate, Outlook, Power Apps, Power Platform]
description: Escape the email madness! Discover how to use Power Automate to declutter your inbox with effortless automation. Take back control of your inbox!
image: /assets/img/post_img/outlook-inbox-cleanup.jpg
image_by: Alvaro Reyes
image_by_link: https://unsplash.com/@alvarordesign?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText
---

## Contents
* [Office 365 Outlook Actions](#office-365-outlook-actions)
  * [Get emails](#get-emails-v3)
  * [Move email](#move-email-v2)
* [The Basics](#the-basics)
  * [Flow Diagram](#flow-diagram)
  * [Power Automate Flow](#power-automate-flow)
  * [Limitations](#limitations)
* [Outlook Inbox Cleanup Workflow](#outlook-inbox-cleanup-workflow)
  * [Workflow Diagram](#workflow-diagram)
  * [Outlook Folder Setup](#outlook-folder-setup)
  * [Get Folder IDs](#get-folder-ids)
  * [Power Automate Flow - Variable](#power-automate-flow---variable)
  * [Power Automate Flow - Microsoft Graph](#power-automate-flow---microsoft-graph)
* [Summary](#summary)

<br>

---

<br>

Power Automate offers a wide range of connectors for various services allowing for the automation of certain tasks. Managing and organizing emails is a common repetitive task that can take a fair amount of time and manual effort, and let's face it's not exciting work. Thankfully, Power Automate and the Outlook in Office 365 connector can provide some relief.

>When searching the Power Automate connectors and actions for `Outlook` there is an `Office 365 Outlook` and an `Outlook.com` option. Microsoft recommends when using a work or school email account use `Office 365 Outlook` and when using a personal account use the `Outlook.com` connector

This article will provide an overview of the Outlook in Office 365 connector and some examples of how it can be used to conduct an Inbox Cleanup task.

<br>

---

<br>

## Office 365 Outlook Actions
The Office 365 Outlook connector offers several actions or events that the Power Automate workflow could do - [List of Office 365 Outlook Actions](https://docs.microsoft.com/en-us/connectors/office365/#actions){: .post__link} -  below shows just a partial list. 

![Partial list of Outlook Actions](/assets/img/2022-08-19-pwrauto-move-email/outlook_action_overview.png){: .post__img}

Automating an Outlook inbox cleanup task will focus on the following actions.

### Get emails (V3)
This action will retrieve emails from a folder (e.g. Inbox). This action can be configured to fetch both read and unread emails.

![Get Emails (V3) Action](/assets/img/2022-08-19-pwrauto-move-email/outlook_get_email_v3.png){: .post__img}

### Move email (V2)
This action will move an email to the specified folder, within the same mailbox. 
>Using the low-code interface the specified folder must be selected, to dynamically set the destination folder based on the characteristics of the email the folder ID must be specified, see below for details.

![Move Email (V2)](/assets/img/2022-08-19-pwrauto-move-email/outlook_move_email_v2.png){: .post__img}

<br>

---

<br>

## The Basics
To get started the workflow will fetch up to 10 emails, and process them based on the Read status. Unread emails will be moved to the Unread folder and Read emails will be moved to the Read folder.

### Flow Diagram

![Basic workflow diagram](/assets/img/2022-08-19-pwrauto-move-email/basic_workflow_diagram.png){: .post__img}

### Power Automate Flow
To start the Power Automate flow the trigger is defined, here it is a manual trigger, but could be scheduled. Following this, a `noEmails` variable is defined to be used to exit the `Do until` loop which will continue to fetch 10 email batches from the inbox until there are no more emails (`noEmails = True`). After initializing the variable the `Do until` flow control is added.

![Basic workflow starting steps](/assets/img/2022-08-19-pwrauto-move-email/flow_start.png){: .post__img}

Within the `Do until` loop, there are 3 main events:

1) The `Get emails` action retrieves the top 10 emails from the Inbox. To configure this action, select the folder to retrieve the emails from, and then for this flow the `Fetch Only Unread Messages` was set to `No` and the `Top` option was set to 10 (pictured above in the [Get emails (V3)](#get-emails-v3) section).

2) Determine if there are emails to process and set the `noEmails` variable to the appropriate `True` or `False` value. The set variable value is determined by the expression:
```
if(
    equals(
        length(
            outputs('Get_emails_(V3)_from_Inbox')?['body/value']
        )
        , 0
    )
    , true
    , false
)
```

3) The `For Each Email` loop iterates over each of the retrieved emails and moves the emails to the appropriate folder based on the email's read status.

![Until noEmail True Loop](/assets/img/2022-08-19-pwrauto-move-email/flow_do_until.png){: .post__img}

The `For Each Email` control structure applies the actions contained within it to each of the emails retrieved by the `Get emails` action. 

The first step is a `Condition` flow control, this control evaluates the `Is Read` status of the current email. If `Is Read` is `True` the email is moved to a Read folder and if `False` the email is moved to the Unread folder.

![For Each Email Loop](/assets/img/2022-08-19-pwrauto-move-email/flow_for_each_email.png){: .post__img}

### Limitations
A limitation of the above flow is that the destination folder for the `Move email` actions had to be explicitly selected. This will likely be fine if there are only a few potential mailbox folders, however can easily become unmanageable when the number of potential Inbox folders grows. 

The limitation can be addressed by using `Id:: <folder_id>` for the `Folder` option within the `Move email` action. This solution comes with two main points to address.

1) Where to find the Outlook folder IDs

2) How to store/search for the folder IDs based on specific email characteristics (e.g. from specific people or domains).

<br>

---

<br>

## Outlook Inbox Cleanup Workflow

The first example shown in this article explicitly selected the destination folder, in this example, the folder display names and IDs will be stored in an object variable. The workflow will then process the email, identifying which property from the object variable should be retrieved. Then the returned value (the folder ID) will be used in the `Move email` action. In this example, an object variable was used to store the display name and folder IDs, a [Microsoft List](https://www.microsoft.com/en-us/microsoft-365/microsoft-lists){: .post__link} could also be used. Retrieving the folder ID using this option would require a `Get items` action with the appropriate filter query.

### Workflow Diagram
![Outlook Inbox Cleanup Workflow Diagram](/assets/img/2022-08-19-pwrauto-move-email/workflow_diagram_2.png){: .post__img}

### Outlook Folder Setup
The outlook mailbox used by this flow has the following folders to which the emails will be moved from the inbox. Internal emails will be moved into the Departments subfolder based on the sender's department. External emails will be moved into the External subfolder based on the sender's domain.

![Outlook Folder Setup](/assets/img/2022-08-19-pwrauto-move-email/outlook_folder_setup.png){: .post__small__img}

### Get Folder IDs
Getting the folder IDs of an Outlook mailbox folder can involve using the Microsoft Graph API or using the `peak code` setting within Power  Automate. Using Microsoft Graph can be a bit more advanced but still approachable when manually retrieving the folder IDs using `peak code` is unmanageable. 

#### Peak Code
This method requires manually identifying each destination folder's ID and adding it to the object variable. This can be an appropriate method when the number of destination folders is low and does not change frequently. The organizing of emails in this example primarily focuses on sorting into department folders which can be a good use case for this method because the number of departments in an organization is likely to be relatively stable.

The `Move email` action, shown below, shows that the folder is selected (HR).
![Move Email Folder Selection](/assets/img/2022-08-19-pwrauto-move-email/peak_code_1.png){: .post__img}

After selecting the destination folder, in the upper right of the action select the ellipses and then `Peak code`.

![Move Email Peak Code](/assets/img/2022-08-19-pwrauto-move-email/peak_code_2.png){: .post__img}

This will reveal the inputs to this action. Under the `parameters` property, the `folderPath` can be seen. What is required to be stored for this flow is the entire folder path following `Id::`, this ID and the folders display name are what is stored and accessed by the workflow. See the [Power Automate Flow - Variable](#power-automate-flow---variable){: .post__link} section for an example.

#### Microsoft Graph
When using the Microsoft Graph approach [Graph Explorer](https://developer.microsoft.com/en-us/graph/graph-explorer){: .post__link} is a helpful tool. This method can also be used to get the display names and IDs of child folders. See the [Power Automate Flow - Microsoft Graph](#power-automate-flow---microsoft-graph){: .post__link} section for an example.

### Power Automate Flow - Variable
<br>

![Flow Example 1 Starting Actions](/assets/img/2022-08-19-pwrauto-move-email/flow_example_2.png){: .post__img}

Again the workflow is started with a manual trigger but could be set to a scheduled trigger. The first few actions of the flow initialize four variables utilized by the flow: `noEmails`, `folderIDs`, `propertyName`, and `sorted`.
* noEmails: boolean variable used to exit the `Do until` loop which continues to retrieve emails from the Inbox until `noEmails=True`
* folderIDs: an object variable that stores a folder's display name (key) and folder id (value)

![folderIDs variable](/assets/img/2022-08-19-pwrauto-move-email/variable_folderIDs.png){: .post__img}

* propertyName: string variable used to get the corresponding folderIDs from the `folderIDs` object
* sorted: boolean variable used to determine if the current email has been sorted by a prior step

The `Do until noEmail=True` is a `Do until` flow control action which will continue to process and sort emails in the Inbox until there are no more emails to be processed. This contains the same three actions that were described above `Get emails`, determine if there are emails to process, then process each email. The `For Each Email` loop is where a majority of the Inbox management actions occur.

![Flow Example 2 For Each Email Loop](/assets/img/2022-08-19-pwrauto-move-email/flow_for_each_email_2_0.png){: .post__img}

The `For each` loop contains 4 main steps:

1) Get the current email object, this will make attributes of the email accessible through dynamic content

2) Set the `sorted` variable to `False` and split the `From` email address into the domain and the user Id (everything after `@` and everything before `@`, respectively)

3) Move Unread emails into an Unread folder. These will be moved back to the Inbox after all emails have been processed

4) Check if the email was sorted into the Unread folder and if not evaluate email characteristics and move the email to the appropriate folder

#### Action Step #1
The first step gets the current email and makes the properties of the email accessible through dynamic content.

![Flow Example 2 Get email](/assets/img/2022-08-19-pwrauto-move-email/flow_for_each_email_2_1.png){: .post__img}

#### Action Step #2
This step consists of setting the `sorted` value and two `Compose` data operations which splits the `From` email address into two parts, before the `@` and after. The output of these two actions will be used to determine if the email came from an internal source which will be sorted by the sender's department or an external source which will be sorted by domain. 

![Flow Example 2 Set Characteristics](/assets/img/2022-08-19-pwrauto-move-email/flow_for_each_email_2_2.png){: .post__img}

The inputs expressions used are:
* Compose - Domain: `last(split(outputs('Get_email_(V2)_-_Current_Email_Object')?['body/from'], '@'))`
* Compose - User Id: `first(split(outputs('Get_email_(V2)_-_Current_Email_Object')?['body/from'], '@'))`

#### Action Step #3
This set of actions uses a `Condition` flow control to check the `Is Read` status of the current email. If `False` the email is moved to an Unread folder to be moved back to the Inbox folder later in the flow and `sorted` is set to `True`. If `True` no action is taken and the flow moves to the next set of actions.

![Flow Example 2 Read Status](/assets/img/2022-08-19-pwrauto-move-email/flow_for_each_email_2_3.png){: .post__img}

#### Action Step #4
This step carries out the processing and sorting of each of the read emails. First, the flow checks the value of `sorted`, if `sorted` equals `True` no additional actions are taken, if `False` the email is sorted based on the attributes of the email.

![Flow Example 2 Check Sorted Status](/assets/img/2022-08-19-pwrauto-move-email/flow_for_each_email_2_3_5.png){: .post__img}

When sorting actions are needed (`sorted = False`) the first action is another `Condition` flow control which checks if the `Domain` is equal to an internal domain.

![Flow Example 2 Process Emails](/assets/img/2022-08-19-pwrauto-move-email/flow_for_each_email_2_4.png){: .post__img}

If the above condition is met the email is sorted into a department folder by searching for the user profile of who sent the email using the Office 365 connector and setting the `propertyName` variable equal to the `department` attribute. Then the folder argument of the `Move email` action is set to: 

`variables('folderIds')?['departments'][variables('propertyName')]`.

![Flow Example 2 Internal Sorting](/assets/img/2022-08-19-pwrauto-move-email/flow_for_each_email_2_5.png){: .post__img}

>It is important to note that the folder ID in the `Move email` actions is preceded by `Id::`

If the above condition is not met, the email is sorted by domain. This sorting involves three steps. First, a compose action is used to get the folderID of the corresponding domain folder with the inputs argument set to:

`variables('folderIds')?['domain'][variables('propertyName')]`

This is then followed by two `Move email` actions that have different configure after run settings. The `Sorting Needed` is configured to run only when the compose `Get propertName` action fails (i.e. a property matching the domain does not exist in the `folderIDs` variable) and the email is moved into a `Sorting Needed` folder to be manually sorted.

The following `External Sorting` action is configured to run only when the `Sorting Needed` action is skipped (i.e. the `Get propertName` action was successful). The folder argument of the `Move email` action is set to the same expression as the above `Get propertyName` action.

![Flow Example 2 External Sorting](/assets/img/2022-08-19-pwrauto-move-email/flow_for_each_email_2_6.png){: .post__img}

After all emails in the Inbox have been processed the flow retrieves all emails that were moved to the Unread folder and moves them back to the Inbox.

![Flow Example 3 Move Unread to Inbox](/assets/img/2022-08-19-pwrauto-move-email/flow_move_back_inbox.png){: .post__img}

#### Limitation
A main advantage of the approach is that the destination folder can be dynamically set without nesting `Condition` flow controls. However, this approach may become unmanageable if the destination folders change or if new folders are frequently added. To continue sorting into new folders would require a manual update of the folder name and ID storage source (variable or Microsoft List).

### Power Automate Flow - Microsoft Graph

This approach has the same general layout as the previous flow, the only difference occurs within the `Check if Internal Email` flow control.

![Flow Example 3 Check If Internal Email](/assets/img/2022-08-19-pwrauto-move-email/flow_for_each_email_3_0.png){: .post__img}

Here there are additional actions: `Send an HTTP request`, `Parse JSON`, and `For Each Child Folder`.

![Flow Example 3 Internal Email](/assets//img/2022-08-19-pwrauto-move-email/flow_for_each_email_3_1.png){: .post__img}

The `Send an HTTP request` Outlook action requires a `URI` this can be explored and created using the [Graph Explorer](https://developer.microsoft.com/en-us/graph/graph-explorer){: .post__link} tool. The generalized query used here is: 

`https://graph.microsoft.com/v1.0/me/mailFolders/<ParentFolderId>/childFolders`. 

The returned body of this action is then parsed with the `Parse JSON` action which allows properties of child folders (folders within Department and External) to be utilized as dynamic content.

>The Schema of the `Parse JSON` action can be generated from a sample by testing the workflow before adding this action. Following the test, the output of the `Send an HTTP request` can be copied, then add the `Parse JSON` action and paste the output into the Insert a sample JSON Payload dialog box

After these actions, there is a `For Each` flow control that iterates through each child folder and compares the display name of the folder with the department of the sender. If the two are equal the `folderID` variable is set to the id of the corresponding folder.

![Flow Example 3 For Each Folder](/assets/img/2022-08-19-pwrauto-move-email/flow_for_each_email_3_3.png){: .post__img}

The `folderID` variable is then used in the following `Move email` action in the same way as the prior flow example.

The `False` branch of the `Check if Internal Email` operates in the same way, with the only changes being there is no need to search for users (external email) and the `<ParentFolderID>` is the folder ID corresponding to the External folder, rather than Department.

<br>

---

<br>

## Summary
This article covered various approaches using Power Automate to automate Outlook inbox cleanup and management. The three approaches shown in the article covered a basic application of sorting emails into a Read and Unread folder. The main limitations of this flow is that the destination folder was explicitly selected and set. The next two approaches in the article covered two methods of addressing this limitation by using a variable object and querying the folders present using Microsoft Graph.

<br>

---

<br>

If you enjoy what you read and find it helpful please check back, and check back often. Follow me on <a class="post__link" href="https://medium.com/@emguyant"><i class="fab fa-medium"></i> Medium</a> while giving a clap to the article! Also don't forget to subscribe to the <a class="post__link" href="https://medium.com/inquisitive-nature"><b>INQUISITIVE NATURE</b></a> publication.

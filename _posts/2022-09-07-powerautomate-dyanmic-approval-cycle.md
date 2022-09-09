---
layout: post
author: Ethan Guyant
title:  "Microsoft Power Automate: Dynamic Approval Cycle"
category: How To
tags: [Microsoft 365, Power Automate, Outlook, Power Apps]
description: The focus of this article is on creating a dynamic document approval cycle. This process will allow for a feedback cycle when a higher-level approver may have a question or require clarification on the document requested for approval. 
image: /assets/img/post_img/dynamic-approval-cycle.jpg
image_by: Jon Tyson
image_by_link: https://unsplash.com/@jontyson?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText
---

## Contents
* [Approval Action](#approvals-action)
* [Flow Control Actions](#flow-control-actions)
* [Dynamic Approval Cycle Workflow](#dynamic-approval-cycle-workflow)
  * [Power Automate Workflow Overview](#power-automate-workflow-overview)
  * [Workflow Setup](#workflow-setup)
  * [Approval Loop](#approval-loop)
    * [Level 1 Approval](#level-1-approval)
    * [Level 2 Approval](#level-2-approval)
    * [Level 3 Approval](#level-3-approval)
    * [Exit Approval Loop](#exit-approval-loop)
* [Summary](#summary)

---

<br>

Power Automate offers a wide range of connectors for various services allowing for the automation of certain tasks. A typical task of most organizations is a process to request authorization or some approval task (e.g. vacation requests, or document approvals). Power Automate offers an `Approvals` action to incorporate human decisions into the automated process.

For a process that only requires a single or sequential approvals, please see the following documentation.
* [Get started with approval (single)](https://docs.microsoft.com/en-us/power-automate/get-started-approvals){: .post__link}
* [Manage sequential approval with Power Automate](https://docs.microsoft.com/en-us/power-automate/sequential-modern-approvals){: .post__link}

The focus of this article is on creating a dynamic document approval cycle. This process will allow for a feedback cycle when a higher-level approver may have a question or require clarification on the document requested for approval. The need for the dynamic feedback cycle arises when during the approval of a document a minor error is identified, with a sequential approval workflow the document would have to be rejected and an entirely new workflow would have to be triggered. The flexibility to request feedback or correction of minor errors can streamline and make the overall approval process more efficient and effective.

This process will consist of 2-3 approval levels based on the approver's position and leverage a `Switch` flow control to implement a state machine solution.  

<br>

---

<br>

## Approvals Action
The Power Automate `Approvals` action enables the ability to incorporate approvals or human decisions into a workflow. The specific action that is used in the workflow detailed in this article is `Start and wait for an approval`. This action starts an automated approval process and then pauses the workflow until a response has been received.

![Start and Wait Approval - Approve/Reject-First to Respond](/assets/img/2022-09-09-dynamic-approval-cycle/approval_action.png){: .post__img}

<br>

---

<br>

## Flow Control Actions
The workflow will leverage the functionality of two flow control actions. The first, `Do until`, will continue to execute a series of contained actions until a specified condition is met. The second, `Switch`, evaluates an expression and determines whether the result matches any of the specified values.

In the context of this workflow the `Do until` will continue to execute until a workflow variable (e.g. `swithExit`) is equal to `True`, indicating that the workflow should exit the approval loop. The `Switch` control will evaluate the value of an `approvalState` variable and execute the specific actions of the corresponding branch. 

<br>

---

<br>

## Dynamic Approval Cycle Workflow
<br>

### Power Automate Workflow Overview

![Workflow Overview](/assets/img/2022-09-09-dynamic-approval-cycle/workflow_overview.png){: .post__img}

### Workflow Setup

The trigger of the workflow is for a selected SharePoint file however, the trigger could be defined as several other options. The trigger has two inputs: the Level 1 approver, and comments for the requested document approval.

![Workflow trigger configuration](/assets/img/2022-09-09-dynamic-approval-cycle/workflow_trigger.png){: .post__img}

Following this, the workflow initializes a series of workflow variables that will be utilized throughout the workflow including:
* Storing Approval information (level1/2/3Approval)

![Approval Variable Initialization](/assets/img/2022-09-09-dynamic-approval-cycle/approval_variables.png){: .post__img}

* The `approvalState` of the workflow, is initially set to `Level 1` and will be updated as needed dependent on approval outcomes
* A `switchExit` boolean variable which is used to exit or end the `Do until` approval Loop
* An `approvalOutcome` string variable which will store the overall outcome of the sequence of approvals

![Approval Variable Initialization](/assets/img/2022-09-09-dynamic-approval-cycle/approval_variables_2.png){: .post__img}

* Two `managerEmail` string variables to store the email addresses of the level 2 and level 3 approvers. These approvers are the manager of the previous level approver.

![Manager Email Variable Initialization](/assets/img/2022-09-09-dynamic-approval-cycle/manager_emails.png){: .post__img}

Before the approval loop, the Office 365 user profile is retrieved for the initial level 1 approver, this user is specified during the triggering of workflow. The user email and Id are then exposed through dynamic content with the outputs of two `Compose` data operation actions.

![Input User Office 365 Profile](/assets/img/2022-09-09-dynamic-approval-cycle/office_input_user_profile.png){: .post__img}

### Approval Loop
The approval loop is initiated with a `Do until` flow control which will continue to execute the contained actions (i.e. the `Switch` flow control) until the `switchExit` variable is equal to `True`.

The only action within the `Do until` loop is the `Switch` flow control. The `Switch` is defined with four conditions, or in the context of this workflow 3 levels of approvals and 1 branch to exit the approval loop.

![Do until  loop overview](/assets/img/2022-09-09-dynamic-approval-cycle/do_until.png){: .post__img}

For each iteration of the `Do until` loop the `Switch` flow control evaluates the value of the `approvalState` variable and then executes the actions contained within the branch whose condition is equal to this value. There are four main branches contained within the `Switch` with the conditions of: `Level 1`, `Level 2`, `Level 3`, and `Exit`. It is the functionality of the `Switch` flow control that allows for feedback or the cyclic nature of the approval workflow.

>In general the Level 1 approval is a preliminary approval conducted by the initial user. The Level 2 approval is conducted by the manager of the Level 1 approval however, the Level 2 approval action has an option to request feedback from Level 1 in addition to approving/rejecting the approval. Following the Level 2 approval the Level 3 approval is assigned to the manager of the Level 2 approval (if they have one, i.e. the Level 2 approver is not the president of the organization). Similar to the Level 2 approval, Level 3 can request feedback from any of the prior approvals.

![Switch flow control](/assets//img/2022-09-09-dynamic-approval-cycle/switch.png){: .post__img}

#### **Level 1 Approval**   
The Level 1 branch is the first approval in the sequence of approvals needed. The branch starts with the `Start and wait` Approval action. 

Following the approval, the `approvalOutcome` is updated to the value of the Approval outcome property and the `level1Approval` is set to the value of the Approval response summary value. The `level1Approval` variable is used in the final notification email and documents the final approval history.

![Level 1 Initial Actions](/assets/img/2022-09-09-dynamic-approval-cycle/level_1_1.png){: .post__img}

After the approval and setting of the variables, the Office 365 `Get manager` action is used to get the user profile information for the manager of the level 1 approver. For this workflow there are always at least two levels of approval, meaning the level 1 approver will always have a manager, see the `Level 2` description for the handling of when this may not be the case.

![Level 1 Get Manager](/assets/img/2022-09-09-dynamic-approval-cycle/level_1_2.png){: .post__img}

Following this action, the workflow updates the value of `approvalState` to `Level 2` and sets the variable `managerEmail2` to the email address from the body of the above `Get manager` action. Setting this variable allows the workflow to set the approver of the `Level 2` approval dynamically.

This completes the first iteration of the `Do until` loop, and since the `switchExit` variable was not updated, and is still equal to `False` the loop starts again by moving to the `Switch` flow control and evaluating `approvalState`, which has now been set to `Level 2`.

#### **Level 2 Approval**
The level 2 branch is executed any time during the workflow when the `approvalState` variable is equal to `Level 2`, this can occur following a level 1 approval or by the level 3 approver requesting feedback or more information from the level 2 approver.

The branch has three main sections: 1) the approval, 2) updating and setting workflow variables, and 3) evaluating the `Outcome` property of the approval to move the workflow to the next branch of actions.

![Level 2 Initial Actions](/assets/img/2022-09-09-dynamic-approval-cycle/level_2_1.png){: .post__img}

The level 2 approval action is a Custom Response - Wait for one response type, this differs from the level 1 approval. The custom responses type is needed to provide the `Feedback - Level 1` response option, this option indicates that the workflow should be sent back and execute the `Level 1` branch actions. Following the approval, the `approvalOutcome` and `level2Approval` variables are set, the same as they were in the `Level 1` branch.

After setting the variables the level 2 branch has a nested `Switch` flow control: `Evaluate Approval Outcome Level 2`

![Level 2 Evaluate Approval Outcome](/assets/img/2022-09-09-dynamic-approval-cycle/level_2_2.png){: .post__img}

The `Switch` flow control has 3 branches utilized by the workflow. The `Reject` and `Feedback - Level 1` branches are executed when the corresponding approval response is selected and each sets the `approvalState` to the appropriate value. A `Reject` outcome exits the approval loop, and a `Feedback - Level 1` outcome moves the workflow back to the `Level 1` branch. The `Approve` branch searches for the user profile of the email stored in the variable `managerEmail2` (i.e. the level 2 approver - the manager of the level 1 approver) and then has a `Condition` flow control based on the Job Title of the level 2 approver. 

![Level 2 Approve Branch](/assets/img/2022-09-09-dynamic-approval-cycle/level_2_3.png){: .post__img}

If the `JobTitle` property is equal to `President` (i.e. the level 2 approver is the President of the organization) there are no additional approvals required and `approvalState` is set to `Exit`. If the `JobTitle` property is not equal to `President` the workflow uses the Office 365 `Get manager` action to get the manager of the level 2 approver and then sets the `approvalState` and `managerEmail3` variables, similar to the last actions of the `Level 1` branch.

This completes this iteration of the `Do until` loop, and since the `switchExit` variable was not updated, and is still equal to `False` the loop starts again by moving to the `Switch` flow control and evaluating `approvalState`, which could now have a value of `Exit`, `Level 3`, or `Level 1`.

#### **Level 3 Approval**
The `Level 3` branch is similar to the `Level 2` branch. Major differences include the addition of the `Feedback - Level 2` response in the Approval action as well as in the `Evaluate Approval Outcome Level 3` switch flow control.

![Level 3 Initial Actions](/assets/img/2022-09-09-dynamic-approval-cycle/level_3_1.png){: .post__img}

The `Switch` flow control on this branch only sets the `approvalState` variable based on the `Outcome` of the approval, there is no need in the `Approve` branch for the `Get manager` and `Condition` control because this is the final level of approvals. If the `Outcome` of the approval is equal to either `Approve` or `Reject` the `approvalState` is set to `Exit` and if equal to one of the Feedback responses the `approvalState` is set to the corresponding level.

![Level 3 Evaluate Approval Outcome](/assets/img/2022-09-09-dynamic-approval-cycle/level_3_2.png){: .post__img}

#### **Exit Approval Loop**
If any approver rejects the document or if all approvers approve the document the workflow sets `approvalState` to `Exit` which executes the `Exit` branch. This branch sets the `switchExit` variable to `True` which will exit the `Do until` approval loop and sends a summary email to who triggered the workflow (i.e. requested the document approval).

![Exit Branch](/assets/img/2022-09-09-dynamic-approval-cycle/exit.png){: .post__img}

Once the `Do until` loop is exited the workflow is complete however, this could also be followed by additional actions if required by the business process.

<br>

---

<br>

## Summary
A couple of scenarios that can be encountered during an approval process are the identification of minor errors which may not warrant rejecting the document fully followed by a new approval workflow, or an approver may just require clarification or additional information from a prior approver. This article highlighted and demonstrated how the Power Automate flow controls `Do until` and `Switch` can be used together to achieve an approval process that allows for a feedback cycle. Utilizing this method no longer limits the approval workflow to just a set of sequential approvals and facilitates back-and-forth collaboration between the different levels of approvals.

<br>

---

<br>

If you enjoy what you read and find it helpful please check back, and check back often. Follow me on <a class="post__link" href="https://medium.com/@emguyant"><i class="fab fa-medium"></i> Medium</a> while giving a clap to the article! Also don't forget to subscribe to the <a class="post__link" href="https://medium.com/inquisitive-nature"><b>INQUISITIVE NATURE</b></a> publication.

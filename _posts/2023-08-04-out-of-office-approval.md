---
layout: post
author: Ethan Guyant
title:  "Outsmarting the Out-of-Office Quandary: A Power Automate Approval Guide"
subtitle: Your document approvals are about to get a whole lot smarter!
category: How To
tags: [Microsoft 365, Power Automate, Outlook, Power Apps, Power Platform]
description: Say goodbye to the world of endless email threads and approval documents that somehow get lost in the abyss of your inbox. With Power Automate, ensure that documents get the thumbs up even when the chosen approver is out of the office.  Let's roll up our sleeves and get automating!
image: /assets/img/post_img/out-of-office-approval.jpg
image_by: Ethan Robertson
image_by_link: https://unsplash.com/@ethanrobertson?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText
---

## From Hurdle to Handshake: Streamline Document Approvals with Power Automate

<br>

In an era of remote work, efficient collaboration and communication have become as crucial to a project's success as coffee is to a Monday morning. One hurdle that often stumbles onto the path of efficient collaboration is a document approval. You might wonder, what happens when the assigned approver is nowhere to be found? Here's where Microsoft Power Automate takes center stage, ensuring that no document is left unapproved.

This post is a step-by-step guide to establish a workflow that checks if the approver is available before passing the document to them for approval. If the approver is unavailable the workflow pokes you and lets you assign a new approver. No magic to be found here, just good ol' automation at work!

<br>

---

<br>

## A Quick Glance: What the Power Automate Workflow Looks Like

<br>

Our Power Automate workflow gets triggered for a selected document within a SharePoint document library. You provide the approver's email address and any comments you have about the document, then set the Power Automate workflow into motion.

Now comes the genius part. The workflow examines the approver's Outlook inbox, searching for an automatic or out-of-office reply (everyone deserves a vacation, right?). If the workflow finds an automatic reply, it sends you a notification via a Teams adaptive card. You then have the freedom to pick a new approver or stick to your initial choice.

If no automatic reply is detected, the document goes straight to the approver you chose when triggering the workflow. 

Once the approval is complete, you'll receive a final notification informing you of the approval's outcome and any comments from the approver.

For a detailed guide on Power Automate, visit Microsoft's [official documentation](https://docs.microsoft.com/en-us/power-automate/){: .post__link}.

<br>

---

<br>

## Unpacking the Details: Inside the Power Automate Workflow

<br>

Let's delve deeper into the workflow.

### Triggering the Process

Our trigger is a SharePoint for a selected document trigger. When triggering the workflow you will be prompted to enter two essential pieces of information:

1. The email of the document approver.
2. Your comments about the document.

This sets the dominoes in motion.

![Triggering the Workflow](/assets/img/2023-08-03-out-of-office-approval/trigger-workflow.gif){: .post__img}

<br>

### Assembling the Essentials: Get the Approver's Profile, File Properties, and Initialize Variables

This phase of the workflow encompasses three actions to collect the information needed for the workflow.

1. Get user profile: This action retrieves the approver's user profile, making information such as display name available to the workflow as dynamic content.
2. Get file properties: This action fetches the details of the selected document, providing useful information like the document's link to the workflow.
3. Initialize variables: Three string variables are used within the workflow to gather and store information about the approval. These include `approvalOutcome`, `approvalComments`, and `approvalApprover`.

![Assembling the Essentials](/assets/img/2023-08-03-out-of-office-approval/assembling-essentials.png){: .post__img}

For detailed instructions on each action check out Microsoft's guides on the Office 365 Users action [Get user profile (V2)](https://learn.microsoft.com/en-us/connectors/office365users/#get-user-profile-(v2)){: .post__link}, the SharePoint action [Get file properties](https://learn.microsoft.com/en-us/connectors/sharepointonline/#get-file-properties){: .post__link}, and Variable action [Initialize a variable](https://learn.microsoft.com/en-us/power-automate/create-variable-store-values#initialize-a-variable){: .post__link}.

<br>

### Is the Approver Available? How to Check for Automatic Replies

This stage uses the the Outlook action `Get mail tips for a mailbox` to see if the approver has an automatic reply turn on for their inbox. The automatic reply status is key because this could often signify the approver is either out of the office or generally unavailable.

![Get Mailbox Tips](/assets/img/2023-08-03-out-of-office-approval/get-mail-box-tips.png){: .post__img}

For more detailed instructions, refer to Microsoft's guide on the Outlook action [Get mail tips for a mailbox (V2)](https://learn.microsoft.com/en-us/connectors/office365/#get-mail-tips-for-a-mailbox-(v2)){: .post__link}.

<br>

### Evaluating the Approver's Availability: An Inside Look at the Workflow Decision-Making Process

A critical part of the workflow is determining whether the assigned approver is available. This is done by checking if there is an automatic reply set for the approver's inbox. To achieve this, the output of the `Get mail tips for a mailbox` action is examined. Here is an example of the information contained within the output body:

```json
[
  {
    "mailboxFull": false,
    "externalMemberCount": 0,
    "totalMemberCount": 1,
    "deliveryRestricted": false,
    "isModerated": false,
    "maxMessageSize": 37748736,
    "emailAddress": {
      "name": "",
      "address": "AlexW@02pby.onmicrosoft.com"
    },
    "automaticReplies": {
      "message": "<div>\r\n<div style=\"font-family:Calibri,Arial,Helvetica,sans-serif; font-size:12pt; color:rgb(0,0,0)\">\r\nI am currently out of the office.</div>\r\n</div>",
      "messageLanguage": {
        "locale": "en-US",
        "displayName": "English (United States)"
      }
    }
  }
]
```

From this data, the following expression is used to determine if an automatic reply is set for the inbox:
`empty(outputs('Get_mail_tips_for_a_mailbox_(V2)')?['body/value'][0]?['automaticReplies/message'])`

This expression checks the `message` attributes within the `automaticReplies` property. If the `message` is empty (meaning no automatic reply message), the expression will evaluate to `true`.

If `true`, the workflow proceeds as planned, assigning the approval to the assignee. If `false`, however, the process takes a more intriguing turn, revealing deeper layers of automation. Continue reading to uncover the magic that unfolds.

![Check Automatic Reply](/assets/img/2023-08-03-out-of-office-approval/check-automatic-reply.png){: .post__img}

For more detailed instructions on adding conditions to your workflows, check out Microsoft's guide [Add a condition to a cloud flow](https://learn.microsoft.com/en-us/power-automate/add-condition){: .post__link}.

<br>

### From Alert to Decision: Using Adaptive Cards for Approval Reassignment

When an approver is unavailable and an automatic reply is detected, the workflow seamlessly switches to an alternative process to ensure that the approval request doesn't get stuck in limbo. Through utilizing Teams adaptive cards, this branch of the workflow handles the process of alerting you and providing options for reassigning the approval or continuing with the initial approver. Allowing for flexibility and control even when the primary approver is out of reach.

![Reassign the Approval](/assets/img/2023-08-03-out-of-office-approval/reassign-approval-branch.png){: .post__img}

### Interactive Alerts: How Adaptive Cards Enhance the Approval Workflow

First, the workflow sends you a Teams adaptive card. This card alerts you that your action is needed and prompts you to either reassign the approval or send it to the initial approver.

![Adaptive Card](/assets/img/2023-08-03-out-of-office-approval/adaptive-card.png){: .post__img}

For detailed instructions on creating and using Adaptive Cards, refer to Microsoft's guide [Create your first adaptive card](https://learn.microsoft.com/en-us/power-automate/create-adaptive-cards){: .post__link}. Also, when designing your adaptive cards, the [Adaptive Card Designer](https://adaptivecards.io/designer/){: .post__link} can be a helpful tool.

### Decision-Making with Adaptive Cards: Reassigning or Confirming

The adaptive card presents you with two option: the first is to reassign the approval, and the second is to send it to the initial approver. Once you select an option and submit your response on the adaptive card, the workflow receives your response. Your response is then evaluated in another condition action within the workflow.

If the `reassignApproval` attribute of you response is `false`, the workflow will continue to send the approval to the initial approver. However, if the `reassignApproval` is `true`, it will assign an approval to the email you provided as input into the adaptive card.

<br>

### Closing the Loop: Notification of the Approval Outcome

Once the approver has completed their task, you are notified of the final outcome of their approval and informed of any comments that they may have left during the approval process.

![Final Notification](/assets/img/2023-08-03-out-of-office-approval/final-notification.png){: .post__img}

<br>

---

<br>

## Reaping the Benefits of Power Automate Approvals

<br>

By leveraging Power Automate efficiencies and features, we have turned a potential bottleneck into a smooth and efficient process. No more waiting for approvals or wondering about the status of a document. We have created a dynamic system that adapts to real-life situations, keeping your approvals moving and your teams productive.

Power Automate might seem like magic, but it is simply a powerful tool that can make your work life a whole lot easier. Embrace Power Automate's efficiency and the rich array of Power Automate features to make document approval automation a breeze. 

For further reading, visit:

* [Microsoft Power Automate Documentation](https://learn.microsoft.com/en-us/power-automate/){: .post__link}
* [Creating Workflows in Power Automate](https://learn.microsoft.com/en-us/power-automate/get-started-logic-flow){: .post__link}
* [Microsoft Power Automate Training](https://learn.microsoft.com/en-us/training/powerplatform/power-automate){: .post__link}

Check out these blog posts for other helpful Power Automate Guides:

* [Dynamic Approvals Made Easy: A Deep Dive into Power Automate's Approval Functionality](https://medium.com/inquisitive-nature/microsoft-power-automate-dynamic-approval-cycle-e9ea785081a1){: .post__link}
* [From Chaos to Clarity: Revolutionize Your Inbox with Power Automate](https://medium.com/inquisitive-nature/microsoft-power-automate-outlook-inbox-cleanup-cd5aa67a096d){: .post__link}

Now that you have the blueprint for creating a document approval workflow with Power Automate, it is time to put it into action.  

And remember, as Albert Einstein once said, "Anyone who has never made a mistake has never tried anything new." So don't be afraid to experiment, learn, and create workflows. A little bit of automation today can save you a lot of manual work tomorrow.

Happy Automating!

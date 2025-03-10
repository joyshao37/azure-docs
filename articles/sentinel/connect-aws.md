---
title: Connect AWS CloudTrail to Microsoft Sentinel | Microsoft Docs
description: Use the AWS connector to delegate Microsoft Sentinel access to AWS resource logs, creating a trust relationship between AWS CloudTrail and Microsoft Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
---

# Connect AWS CloudTrail to Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Use the AWS connector to stream your AWS CloudTrail management events into Microsoft Sentinel. This connection process delegates access for Microsoft Sentinel to your AWS resource logs, creating a trust relationship between AWS CloudTrail and Microsoft Sentinel. This is accomplished on AWS by creating a role that gives permission to Microsoft Sentinel to access your AWS logs.

> [!NOTE]
> AWS CloudTrail has [built-in limitations](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/WhatIsCloudTrail-Limits.html) in its LookupEvents API. It allows no more than two transactions per second (TPS) per account, and each query can return a maximum of 50 records. Consequently, if a single tenant constantly generates more than 100 records per second in one region, backlogs and delays in data ingestion will result.
> Currently, you can only connect your AWS Commercial CloudTrail to Microsoft Sentinel and not AWS GovCloud CloudTrail.

## Prerequisites

You must have write permission on the Microsoft Sentinel workspace.

> [!NOTE]
> Microsoft Sentinel collects CloudTrail management events from all regions. It is recommended that you do not stream events from one region to another.

## Connect AWS 


1. In Microsoft Sentinel, select **Data connectors** and then select the **Amazon Web Services** line in the table and in the AWS pane to the right,  select **Open connector page**.

1. Follow the instructions under **Configuration** using the following steps.
 
1.  In your Amazon Web Services console, under **Security, Identity & Compliance**, select **IAM**.

    ![AWS1](./media/connect-aws/aws-1.png)

1.  Choose **Roles** and select **Create role**.

    ![AWS2](./media/connect-aws/aws-2.png)

1.  Choose **Another AWS account.** In the **Account ID** field, enter the **Microsoft Account ID** (**123412341234**) that can be found in the AWS connector page in the Microsoft Sentinel portal.

    ![AWS3](./media/connect-aws/aws-3.png)

1.  Make sure **Require External ID** is selected and then and enter the External ID (Workspace ID) that can be found in the AWS connector page in the Microsoft Sentinel portal.

    ![AWS4](./media/connect-aws/aws-4.png)

1.  Under **Attach permissions policy** select **AWSCloudTrailReadOnlyAccess**.

    ![AWS5](./media/connect-aws/aws-5.png)

1.  Enter a Tag (Optional).

    ![AWS6](./media/connect-aws/aws-6.png)

1.  Then, enter a **Role name** and select the **Create role** button.

    ![AWS7](./media/connect-aws/aws-7.png)

1.  In the Roles list, choose the role you created.

    ![AWS8](./media/connect-aws/aws-8.png)

1.  Copy the **Role ARN**. In the Microsoft Sentinel portal, in the Amazon Web Services connector screen, paste it into the **Role to add** field and select **Add**.

    ![AWS9](./media/connect-aws/aws-9.png)

1. To use the relevant schema in Log Analytics for AWS events, search for **AWSCloudTrail**.

    > [!IMPORTANT]
    > As of December 1, 2020, the **AwsRequestId** field has been replaced by the **AwsRequestId_** field (note the added underscore). The data in the old **AwsRequestId** field will be preserved through the end of the customer's specified data retention period.

## Next steps
In this document, you learned how to connect AWS CloudTrail to Microsoft Sentinel. To learn more about Microsoft Sentinel, see the following articles:
- Learn how to [get visibility into your data, and potential threats](get-visibility.md).
- Get started [detecting threats with Microsoft Sentinel](detect-threats-built-in.md).
- [Use workbooks](monitor-your-data.md) to monitor your data.

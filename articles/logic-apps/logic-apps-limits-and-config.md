---
title: Limits and configuration reference guide
description: Reference guide to limits and configuration information for Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: rohithah, rarayudu, azla
ms.topic: reference
ms.date: 11/02/2021
---

# Limits and configuration reference for Azure Logic Apps

> For Power Automate, review [Limits and configuration in Power Automate](/power-automate/limits-and-config).

This article describes the limits and configuration information for Azure Logic Apps and related resources. To create logic app workflows, you choose the **Logic App** resource type based on your scenario, solution requirements, the capabilities that you want, and the environment where you want to run your workflows.

> [!NOTE]
> Many limits are the same across the available environments where Azure Logic Apps runs, but differences are noted where they exist. 

The following table briefly summarizes differences between the original **Logic App (Consumption)** resource type and the **Logic App (Standard)** resource type. You'll also learn how the *single-tenant* environment compares to the *multi-tenant* and *integration service environment (ISE)* for deploying, hosting, and running your logic app workflows.

[!INCLUDE [Logic app resource type and environment differences](../../includes/logic-apps-resource-environment-differences-table.md)]

<a name="definition-limits"></a>

## Workflow definition limits

The following tables list the values for a single workflow definition:

| Name | Limit | Notes |
| ---- | ----- | ----- |
| Workflows per region per subscription | 1,000 workflows ||
| Triggers per workflow | 10 triggers | This limit applies only when you work on the JSON workflow definition, whether in code view or an Azure Resource Manager (ARM) template, not the designer. |
| Actions per workflow | 500 actions | To extend this limit, you can use nested workflows as necessary. |
| Actions nesting depth | 8 actions | To extend this limit, you can use nested workflows as necessary. |
| Trigger or action - Maximum name length | 80 characters ||
| Trigger or action - Maximum input or output size | 104,857,600 bytes <br>(105 MB) | To change the default value in the single-tenant service, review [Edit host and app settings for logic apps in single-tenant Azure Logic Apps](edit-app-settings-host-settings.md). |
| Action - Maximum combined inputs and outputs size | 209,715,200 bytes <br>(210 MB) | To change the default value in the single-tenant service, review [Edit host and app settings for logic apps in single-tenant Azure Logic Apps](edit-app-settings-host-settings.md). |
| Expression character limit | 8,192 characters ||
| `description` - Maximum length | 256 characters ||
| `parameters` - Maximum number of items | 50 parameters ||
| `outputs` - Maximum number items | 10 outputs ||
| `trackedProperties` - Maximum size | 8,000 characters ||
||||

<a name="run-duration-retention-limits"></a>

## Run duration and retention history limits

The following table lists the values for a single workflow run:

| Name | Multi-tenant | Single-tenant | Integration service environment | Notes |
|------|--------------|---------------|---------------------------------|-------|
| Run history retention in storage | 90 days | 90 days <br>(Default) | 366 days | The amount of time to keep a workflow's run history in storage after a run starts. <p><p>**Note**: If the workflow's run duration exceeds the retention limit, that run is removed from the run history in storage. If a run isn't immediately removed after reaching the retention limit, the run is removed within 7 days. <p><p>Whether a run completes or times out, run history retention is always calculated by using the run's start time and the current limit specified in the workflow setting, [**Run history retention in days**](#change-retention). No matter the previous limit, the current limit is always used for calculating retention. <p><p>For more information, review [Change duration and run history retention in storage](#change-retention). |
| Run duration | 90 days | - Stateful workflow: 90 days <br>(Default) <p><p>- Stateless workflow: 5 min <br>(Default) | 366 days | The amount of time that a workflow can continue running before forcing a timeout. <p><p>The run duration is calculated by using a run's start time and the limit that's specified in the workflow setting, [**Run history retention in days**](#change-duration) at that start time. <p>**Important**: Make sure the run duration value is always less than or equal to the run history retention in storage value. Otherwise, run histories might be deleted before the associated jobs are complete. <p><p>For more information, review [Change run duration and history retention in storage](#change-duration). |
| Recurrence interval | - Min: 1 sec <p><p>- Max: 500 days | - Min: 1 sec <p><p>- Max: 500 days | - Min: 1 sec <p><p>- Max: 500 days ||
||||||

<a name="change-duration"></a>
<a name="change-retention"></a>

### Change run duration and history retention in storage

In the designer, the same setting controls the maximum number of days that a workflow can run and for keeping run history in storage.

* For the multi-tenant service, the 90-day default limit is the same as the maximum limit. You can only decrease this value.

* For the single-tenant service, you can decrease or increase the 90-day default limit. For more information, review [Edit host and app settings for logic apps in single-tenant Azure Logic Apps](edit-app-settings-host-settings.md).

* For an integration service environment, you can decrease or increase the 90-day default limit.

For example, suppose that you reduce the retention limit from 90 days to 30 days. A 60-day-old run is removed from the runs history. If you increase the retention period from 30 days to 60 days, a 20-day-old run stays in the runs history for another 40 days.

> [!IMPORTANT]
> If the run's duration exceeds the current run history retention limit, the run is removed from the runs history in storage. 
> To avoid losing run history, make sure that the retention limit is *always* more than the run's longest possible duration.

To change the default value or current limit for these properties, follow these steps:

#### [Portal (multi-tenant service)](#tab/azure-portal)

1. In the [Azure portal](https://portal.azure.com) search box, find and select **Logic apps**.

1. Find and open your logic app in the Logic App Designer.

1. On the logic app's menu, select **Workflow settings**.

1. Under **Runtime options**, from the **Run history retention in days** list, select **Custom**.

1. Drag the slider to change the number of days that you want.

1. When you're done, on the **Workflow settings** toolbar, select **Save**.

#### [Resource Manager template](#tab/azure-resource-manager)

If you use an Azure Resource Manager template, this setting appears as a property in your workflow's resource definition, which is described in the [Microsoft.Logic workflows template reference](/azure/templates/microsoft.logic/workflows):

```json
{
   "name": "{logic-app-name}",
   "type": "Microsoft.Logic/workflows",
   "location": "{Azure-region}",
   "apiVersion": "2019-05-01",
   "properties": {
      "definition": {},
      "parameters": {},
      "runtimeConfiguration": {
         "lifetime": {
            "unit": "day",
            "count": {number-of-days}
         }
      }
   }
}
```
---

<a name="concurrency-looping-and-debatching-limits"></a>
<a name="looping-debatching-limits"></a>

## Looping, concurrency, and debatching limits

The following table lists the values for a single workflow run:

### Loop actions

<a name="for-each-loop"></a>

#### For each loop

The following table lists the values for a **For each** loop:

| Name | Multi-tenant | Single-tenant | Integration service environment | Notes |
|------|--------------|---------------|---------------------------------|-------|
| Array items | 100,000 items | - Stateful workflow: 100,000 items <br>(Default) <p><p>- Stateless workflow: 100 items <br>(Default) | 100,000 items | The number of array items that a **For each** loop can process. <p><p>To filter larger arrays, you can use the [query action](logic-apps-perform-data-operations.md#filter-array-action). <p><p>To change the default limit in the single-tenant service, review [Edit host and app settings for logic apps in single-tenant Azure Logic Apps](edit-app-settings-host-settings.md). |
| Concurrent iterations | Concurrency off: 20 <p><p>Concurrency on: <p>- Default: 20 <br>- Min: 1 <br>- Max: 50 | Concurrency off: 20 <br>(Default) <p><p>Concurrency on: <p><p>- Default: 20 <br>- Min: 1 <br>- Max: 50 | Concurrency off: 20 <p><p>Concurrency on: <p>- Default: 20 <br>- Min: 1 <br>- Max: 50 | The number of **For each** loop iterations that can run at the same time, or in parallel. <p><p>To change this value in the multi-tenant service, see [Change **For each** concurrency limit](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency) or [Run **For each** loops sequentially](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each). <p><p>To change the default limit in the single-tenant service, review [Edit host and app settings for logic apps in single-tenant Azure Logic Apps](edit-app-settings-host-settings.md). |
||||||

<a name="until-loop"></a>

#### Until loop

The following table lists the values for an **Until** loop:

| Name | Multi-tenant | Single-tenant | Integration service environment | Notes |
|------|--------------|---------------|---------------------------------|-------|
| Iterations | - Default: 60 <br>- Min: 1 <br>- Max: 5,000 | Stateful workflow: <p><p>- Default: 60 <br>- Min: 1 <br>- Max: 5,000 <p><p>Stateless workflow: <p><p>- Default: 60 <br>- Min: 1 <br>- Max: 100 | - Default: 60 <br>- Min: 1 <br>- Max: 5,000 | The number of cycles that an **Until** loop can have during a workflow run. <p><p>To change this value in the multi-tenant service, in the **Until** loop shape, select **Change limits**, and specify the value for the **Count** property. <p><p>To change the default value in the single-tenant service, review [Edit host and app settings for logic apps in single-tenant Azure Logic Apps](edit-app-settings-host-settings.md). |
| Timeout | Default: PT1H (1 hour) | Stateful workflow: PT1H (1 hour) <p><p>Stateless workflow: PT5M (5 min) | Default: PT1H (1 hour) | The amount of time that the **Until** loop can run before exiting and is specified in [ISO 8601 format](https://en.wikipedia.org/wiki/ISO_8601). The timeout value is evaluated for each loop cycle. If any action in the loop takes longer than the timeout limit, the current cycle doesn't stop. However, the next cycle doesn't start because the limit condition isn't met. <p><p>To change this value in the multi-tenant service, in the **Until** loop shape, select **Change limits**, and specify the value for the **Timeout** property. <p><p>To change the default value in the single-tenant service, review [Edit host and app settings for logic apps in single-tenant Azure Logic Apps](edit-app-settings-host-settings.md). |
||||||

<a name="concurrency-debatching"></a>

### Concurrency and debatching

| Name | Multi-tenant | Single-tenant | Integration service environment | Notes |
|------|--------------|---------------|---------------------------------|-------|
| Trigger - concurrent runs | Concurrency off: Unlimited <p><p>Concurrency on (irreversible): <p><p>- Default: 25 <br>- Min: 1 <br>- Max: 100 | Concurrency off: Unlimited <p><p>Concurrency on (irreversible): <p><p>- Default: 100 <br>- Min: 1 <br>- Max: 100 | Concurrency off: Unlimited <p><p>Concurrency on (irreversible): <p><p>- Default: 25 <br>- Min: 1 <br>- Max: 100 | The number of concurrent runs that a trigger can start at the same time, or in parallel. <p><p>**Note**: When concurrency is turned on, the **SplitOn** limit is reduced to 100 items for [debatching arrays](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch). <p><p>To change this value in the multi-tenant service, see [Change trigger concurrency limit](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency) or [Trigger instances sequentially](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-trigger). <p><p>To change the default value in the single-tenant service, review [Edit host and app settings for logic apps in single-tenant Azure Logic Apps](edit-app-settings-host-settings.md). |
| Maximum waiting runs | Concurrency off: <p><p>- Min: 1 run <p>- Max: 50 runs <p><p>Concurrency on: <p><p>- Min: 10 runs plus the number of concurrent runs <p>- Max: 100 runs | Concurrency off: <p><p>- Min: 1 run <br>(Default) <p>- Max: 50 runs <br>(Default) <p><p>Concurrency on: <p><p>- Min: 10 runs plus the number of concurrent runs <p>- Max: 200 runs <br>(Default) | Concurrency off: <p><p>- Min: 1 run <p>- Max: 50 runs <p><p>Concurrency on: <p><p>- Min: 10 runs plus the number of concurrent runs <p>- Max: 100 runs | The number of workflow instances that can wait to run when your current workflow instance is already running the maximum concurrent instances. <p><p>To change this value in the multi-tenant service, see [Change waiting runs limit](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs). <p><p>To change the default value in the single-tenant service, review [Edit host and app settings for logic apps in single-tenant Azure Logic Apps](edit-app-settings-host-settings.md). |
| **SplitOn** items | Concurrency off: 100,000 items <p><p>Concurrency on: 100 items | Concurrency off: 100,000 items <p><p>Concurrency on: 100 items | Concurrency off: 100,000 items <br>(Default) <p><p>Concurrency on: 100 items <br>(Default) | For triggers that return an array, you can specify an expression that uses a **SplitOn** property that [splits or debatches array items into multiple workflow instances](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) for processing, rather than use a **For each** loop. This expression references the array to use for creating and running a workflow instance for each array item. <p><p>**Note**: When concurrency is turned on, the **SplitOn** limit is reduced to 100 items. |
||||||

<a name="throughput-limits"></a>

## Throughput limits

The following table lists the values for a single workflow definition:

| Name | Multi-tenant | Single-tenant | Notes |
|------|--------------|---------------|-------|
| Action - Executions per 5-minute rolling interval | Default: 100,000 executions <br>- High throughput mode: 300,000 executions | None | In the multi-tenant service, you can raise the default value to the maximum value for your workflow. For more information, see [Run in high throughput mode](#run-high-throughput-mode), which is in preview. Or, you can [distribute the workload across more than one workflow](handle-throttling-problems-429-errors.md#logic-app-throttling) as necessary. |
| Action - Concurrent outbound calls | ~2,500 calls | None | You can reduce the number of concurrent requests or reduce the duration as necessary. |
| Managed connector throttling | Throttling limit varies based on connector | Throttling limit varies based on connector | For multi-tenant, review [each managed connector's technical reference page](/connectors/connector-reference/connector-reference-logicapps-connectors). <p><p>For more information about handling connector throttling, review [Handle throttling problems ("429 - Too many requests" errors)](handle-throttling-problems-429-errors.md#connector-throttling). |
| Runtime endpoint - Concurrent inbound calls | ~1,000 calls | None | You can reduce the number of concurrent requests or reduce the duration as necessary. |
| Runtime endpoint - Read calls per 5 min  | 60,000 read calls | None | This limit applies to calls that get the raw inputs and outputs from a workflow's run history. You can distribute the workload across more than one workflow as necessary. |
| Runtime endpoint - Invoke calls per 5 min | 45,000 invoke calls | None | You can distribute workload across more than one workflow as necessary. |
| Content throughput per 5 min | 600 MB | None | You can distribute workload across more than one workflow as necessary. |
|||||

<a name="run-high-throughput-mode"></a>

### Run in high throughput mode

For a single workflow definition, the number of actions that run every 5 minutes has a [default limit](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). To raise the default value to the [maximum value](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) for your workflow, which is three times the default value, you can enable high throughput mode, which is in preview. Or, you can [distribute the workload across more than one workflow](../logic-apps/handle-throttling-problems-429-errors.md#logic-app-throttling) as necessary.

#### [Portal (multi-tenant service)](#tab/azure-portal)

1. In the Azure portal, on your logic app's menu, under **Settings**, select **Workflow settings**.

1. Under **Runtime options** > **High throughput**, change the setting to **On**.

   ![Screenshot that shows logic app menu in Azure portal with "Workflow settings" and "High throughput" set to "On".](./media/logic-apps-limits-and-config/run-high-throughput-mode.png)

#### [Resource Manager Template](#tab/azure-resource-manager)

To enable this setting in an ARM template for deploying your logic app, in the `properties` object for your logic app's resource definition, add the `runtimeConfiguration` object with the `operationOptions` property set to `OptimizedForHighThroughput`:

```json
{
   <template-properties>
   "resources": [
      // Start logic app resource definition
      {
         "properties": {
            <logic-app-resource-definition-properties>,
            <logic-app-workflow-definition>,
            <more-logic-app-resource-definition-properties>,
            "runtimeConfiguration": {
               "operationOptions": "OptimizedForHighThroughput"
            }
         },
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {},
         "apiVersion": "2016-06-01",
         "dependsOn": [
         ]
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

For more information about your logic app resource definition, review [Overview: Automate deployment for Azure Logic Apps by using Azure Resource Manager templates](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#logic-app-resource-definition).

---

### Integration service environment (ISE)

* [Developer ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level): Provides up to 500 executions per minute, but note these considerations:

  * Make sure that you use this SKU only for exploration, experiments, development, or testing - not for production or performance testing. This SKU has no service-level agreement (SLA), scale up capability, or redundancy during recycling, which means that you might experience delays or downtime.

  * Backend updates might intermittently interrupt service.

* [Premium ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level): The following table describes this SKU's throughput limits, but to exceed these limits in normal processing, or run load testing that might go above these limits, [contact the Logic Apps team](mailto://logicappsemail@microsoft.com) for help with your requirements.

  | Name | Limit | Notes |
  |------|-------|-------|
  | Base unit execution limit | System-throttled when infrastructure capacity reaches 80% | Provides ~4,000 action executions per minute, which is ~160 million action executions per month |
  | Scale unit execution limit | System-throttled when infrastructure capacity reaches 80% | Each scale unit can provide ~2,000 more action executions per minute, which is ~80 million more action executions per month |
  | Maximum scale units that you can add | 10 scale units | |
  ||||

<a name="gateway-limits"></a>

## Data gateway limits

Azure Logic Apps supports write operations, including inserts and updates, through the on-premises data gateway. However, these operations have [limits on their payload size](/data-integration/gateway/service-gateway-onprem#considerations).

<a name="variables-action-limits"></a>

## Variables action limits

The following table lists the values for a single workflow definition:

| Name | Multi-tenant | Single-tenant | Integration service environment | Notes |
|------|--------------|---------------|---------------------------------|-------|
| Variables per workflow | 250 variables | 250 variables <br>(Default) | 250 variables ||
| Variable - Maximum content size | 104,857,600 characters | Stateful workflow: 104,857,600 characters <br>(Default) <p><p>Stateless workflow: 1,024 characters <br>(Default) | 104,857,600 characters | To change the default value in the single-tenant service, review [Edit host and app settings for logic apps in single-tenant Azure Logic Apps](edit-app-settings-host-settings.md). |
| Variable (Array type) - Maximum number of array items | 100,000 items | 100,000 items <br>(Default) | Premium SKU: 100,000 items <p><p>Developer SKU: 5,000 items | To change the default value in the single-tenant service, review [Edit host and app settings for logic apps in single-tenant Azure Logic Apps](edit-app-settings-host-settings.md). |
||||||

<a name="http-limits"></a>

## HTTP request limits

The following tables list the values for a single inbound or outbound call:

<a name="http-timeout-limits"></a>

### Timeout duration

By default, the HTTP action and APIConnection actions follow the [standard asynchronous operation pattern](/azure/architecture/patterns/async-request-reply), while the Response action follows the *synchronous operation pattern*. Some managed connector operations make asynchronous calls or listen for webhook requests, so the timeout for these operations might be longer than the following limits. For more information, review [each connector's technical reference page](/connectors/connector-reference/connector-reference-logicapps-connectors) and also the [Workflow triggers and actions](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) documentation.

> [!NOTE]
> For the **Logic App (Standard)** resource type in the single-tenant service, stateless workflows can only run *synchronously*.

| Name | Multi-tenant | Single-tenant | Integration service environment | Notes |
|------|--------------|---------------|---------------------------------|-------|
| Outbound request | 120 sec <br>(2 min) | 235 sec <br>(3.9 min) <br>(Default) | 240 sec <br>(4 min) | Examples of outbound requests include calls made by the HTTP trigger or action. <p><p>**Tip**: For longer running operations, use an [asynchronous polling pattern](../logic-apps/logic-apps-create-api-app.md#async-pattern) or an ["Until" loop](../logic-apps/logic-apps-workflow-actions-triggers.md#until-action). To work around timeout limits when you call another workflow that has a [callable endpoint](logic-apps-http-endpoint.md), you can use the built-in Azure Logic Apps action instead, which you can find in the designer's operation picker under **Built-in**. <p><p>To change the default limit in the single-tenant service, review [Edit host and app settings for logic apps in single-tenant Azure Logic Apps](edit-app-settings-host-settings.md). |
| Inbound request | 120 sec <br>(2 min) | 235 sec <br>(3.9 min) <br>(Default) | 240 sec <br>(4 min) | Examples of inbound requests include calls received by the Request trigger, HTTP Webhook trigger, and HTTP Webhook action. <p><p>**Note**: For the original caller to get the response, all steps in the response must finish within the limit unless you call another nested workflow. For more information, see [Call, trigger, or nest logic apps](../logic-apps/logic-apps-http-endpoint.md). <p><p>To change the default limit in the single-tenant service, review [Edit host and app settings for logic apps in single-tenant Azure Logic Apps](edit-app-settings-host-settings.md). |
||||||

<a name="message-size-limits"></a>

### Messages

| Name | Chunking enabled | Multi-tenant | Single-tenant | Integration service environment | Notes |
|------|------------------|--------------|-------------------------|---------------------------------|-------|
| Content download - Maximum number of requests | Yes | 1,000 requests | 1,000 requests <br>(Default) | 1,000 requests ||
| Message size | No | 100 MB | 100 MB | 200 MB | To work around this limit, see [Handle large messages with chunking](../logic-apps/logic-apps-handle-large-messages.md). However, some connectors and APIs don't support chunking or even the default limit. <p><p>- Connectors such as AS2, X12, and EDIFACT have their own [B2B message limits](#b2b-protocol-limits). <p>- ISE connectors use the ISE limit, not the non-ISE connector limits. <p><p>To change the default value in the single-tenant service, review [Edit host and app settings for logic apps in single-tenant Azure Logic Apps](edit-app-settings-host-settings.md). |
| Message size | Yes | 1 GB | 1,073,741,824 bytes <br>(1 GB) <br>(Default) | 5 GB | This limit applies to actions that either natively support chunking or let you enable chunking in their runtime configuration. <p><p>If you're using an ISE, the Azure Logic Apps engine supports this limit, but connectors have their own chunking limits up to the engine limit, for example, see the [Azure Blob Storage connector's API reference](/connectors/azureblob/). For more information about chunking, see [Handle large messages with chunking](../logic-apps/logic-apps-handle-large-messages.md). <p><p>To change the default value in the single-tenant service, review [Edit host and app settings for logic apps in single-tenant Azure Logic Apps](edit-app-settings-host-settings.md). |
| Content chunk size | Yes | Varies per connector | 52,428,800 bytes (52 MB) <br>(Default) | Varies per connector | This limit applies to actions that either natively support chunking or let you enable chunking in their runtime configuration. <p><p>To change the default value in the single-tenant service, review [Edit host and app settings for logic apps in single-tenant Azure Logic Apps](edit-app-settings-host-settings.md). |
|||||||

### Character limits

| Name | Limit | Notes |
|------|-------|-------|
| Expression evaluation limit | 131,072 characters | The `@concat()`, `@base64()`, `@string()` expressions can't be longer than this limit. |
| Request URL character limit | 16,384 characters | |
||||

<a name="retry-policy-limits"></a>

### Retry policy

| Name | Multi-tenant limit | Single-tenant limit | Notes |
|------|--------------------|---------------------|-------|
| Retry attempts | - Default: 4 attempts <br> - Max: 90 attempts | - Default: 4 attempts | To change the default limit in the multi-tenant service, use the [retry policy parameter](logic-apps-exception-handling.md#retry-policies). <p><p>To change the default limit in the single-tenant service, review [Edit host and app settings for logic apps in single-tenant Azure Logic Apps](edit-app-settings-host-settings.md). |
| Retry interval | None | Default: 7 sec | To change the default limit in the multi-tenant service, use the [retry policy parameter](logic-apps-exception-handling.md#retry-policies). <p><p>To change the default limit in the single-tenant service, review [Edit host and app settings for logic apps in single-tenant Azure Logic Apps](edit-app-settings-host-settings.md). |
| Retry max delay | Default: 1 day | Default: 1 hour | To change the default limit in the multi-tenant service, use the [retry policy parameter](logic-apps-exception-handling.md#retry-policies). <p><p>To change the default limit in the single-tenant service, review [Edit host and app settings for logic apps in single-tenant Azure Logic Apps](edit-app-settings-host-settings.md). |
| Retry min delay | Default: 5 sec | Default: 5 sec | To change the default limit in the multi-tenant service, use the [retry policy parameter](logic-apps-exception-handling.md#retry-policies). <p><p>To change the default limit in the single-tenant service, review [Edit host and app settings for logic apps in single-tenant Azure Logic Apps](edit-app-settings-host-settings.md). |
|||||

<a name="authentication-limits"></a>

### Authentication limits

The following table lists the values for a workflow that starts with a Request trigger and enables [Azure Active Directory Open Authentication](../active-directory/develop/index.yml) (Azure AD OAuth) for authorizing inbound calls to the Request trigger:

| Name | Limit | Notes |
| ---- | ----- | ----- |
| Azure AD authorization policies | 5 policies | |
| Claims per authorization policy | 10 claims | |
| Claim value - Maximum number of characters | 150 characters |
||||

<a name="switch-action-limits"></a>

## Switch action limits

The following table lists the values for a single workflow definition:

| Name | Limit | Notes |
| ---- | ----- | ----- |
| Maximum number of cases per action | 25 ||
||||

<a name="inline-code-action-limits"></a>

## Inline Code action limits

The following table lists the values for a single workflow definition:

| Name | Multi-tenant | Single-tenant | Integration service environment | Notes |
|------|--------------|---------------|---------------------------------|-------|
| Maximum number of code characters | 1,024 characters | 100,000 characters | 1,024 characters | To use the higher limit, create a **Logic App (Standard)** resource, which runs in single-tenant Azure Logic Apps, either [by using the Azure portal](create-single-tenant-workflows-azure-portal.md) or [by using Visual Studio Code and the **Azure Logic Apps (Standard)** extension](create-single-tenant-workflows-visual-studio-code.md). |
| Maximum duration for running code | 5 sec | 15 sec | 1,024 characters | To use the higher limit, create a **Logic App (Standard)** resource, which runs in single-tenant Azure Logic Apps, either [by using the Azure portal](create-single-tenant-workflows-azure-portal.md) or [by using Visual Studio Code and the **Azure Logic Apps (Standard)** extension](create-single-tenant-workflows-visual-studio-code.md). |
||||||

<a name="custom-connector-limits"></a>

## Custom connector limits

In multi-tenant Azure Logic Apps and the integration service environment only, you can create and use [custom managed connectors](/connectors/custom-connectors), which are wrappers around an existing REST API or SOAP API. In single-tenant Azure Logic Apps, you can create and use only [custom built-in connectors](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272).

The following table lists the values for custom connectors:

| Name | Multi-tenant | Single-tenant | Integration service environment | Notes |
|------|--------------|---------------|---------------------------------|-------|
| Custom connectors | 1,000 per Azure subscription | Unlimited | 1,000 per Azure subscription ||
| Custom connectors - Number of APIs | SOAP-based: 50 | Not applicable | SOAP-based: 50 ||
| Requests per minute for a custom connector | 500 requests per minute per connection | Based on your implementation | 2,000 requests per minute per *custom connector* ||
| Connection timeout | 2 min | Idle connection: <br>4 min <p><p>Active connection: <br>10 min | 2 min ||
||||||

For more information, review the following documentation:

* [Custom managed connectors overview](/connectors/custom-connectors)
* [Enable built-in connector authoring - Visual Studio Code with Azure Logic Apps (Standard) extension](create-single-tenant-workflows-visual-studio-code.md#enable-built-in-connector-authoring)

<a name="managed-identity"></a>

## Managed identity limits

| Name | Limit |
|------|-------|
| Managed identities per logic app | Either the system-assigned identity or 1 user-assigned identity |
| Number of logic apps that have a managed identity in an Azure subscription per region | 1,000 |
|||

> [!NOTE] 
> By default, a Logic App (Standard) resource has its system-assigned managed identity automatically enabled to 
> authenticate connections at runtime. This identity differs from the authentication credentials or connection 
> string that you use when you create a connection. If you disable this identity, connections won't work at 
> runtime. To view this setting, on your logic app's menu, under **Settings**, select **Identity**.

<a name="integration-account-limits"></a>

## Integration account limits

Each Azure subscription has these integration account limits:

* One [Free tier](../logic-apps/logic-apps-pricing.md#integration-accounts) integration account per Azure region. This tier is available only for public regions in Azure, for example, West US or Southeast Asia, but not for [Azure China 21Vianet](/azure/china/overview-operations) or [Azure Government](../azure-government/documentation-government-welcome.md).

* 1,000 total integration accounts, including integration accounts in any [integration service environments (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) across both [Developer and Premium SKUs](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level).

* Each ISE, whether [Developer or Premium](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level), can use a single integration account at no extra cost, although the included account type varies by ISE SKU. You can create more integration accounts for your ISE up to the total limit for an [extra cost](logic-apps-pricing.md#ise-pricing).

  | ISE SKU | Integration account limits |
  |---------|----------------------------|
  | **Premium** | 20 total accounts, including one Standard account at no extra cost. With this SKU, you can have only [Standard](../logic-apps/logic-apps-pricing.md#integration-accounts) accounts. No Free or Basic accounts are permitted. |
  | **Developer** | 20 total accounts, including one [Free](../logic-apps/logic-apps-pricing.md#integration-accounts) account (limited to 1). With this SKU, you can have either combination: <p>- A Free account and up to 19 [Standard](../logic-apps/logic-apps-pricing.md#integration-accounts) accounts. <br>- No Free account and up to 20 Standard accounts. <p>No Basic or more Free accounts are permitted. <p><p>**Important**: Use the [Developer SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) for experimenting, development, and testing, but not for production or performance testing. |
  |||

To learn how pricing and billing work for ISEs, see the [Logic Apps pricing model](../logic-apps/logic-apps-pricing.md#ise-pricing). For pricing rates, see [Logic Apps pricing](https://azure.microsoft.com/pricing/details/logic-apps/).

<a name="artifact-number-limits"></a>

### Artifact limits per integration account

The following tables list the values for the number of artifacts limited to each integration account tier. For pricing rates, see [Logic Apps pricing](https://azure.microsoft.com/pricing/details/logic-apps/). To learn how pricing and billing work for integration accounts, see the [Logic Apps pricing model](../logic-apps/logic-apps-pricing.md#integration-accounts).

> [!NOTE]
> Use the Free tier only for exploratory scenarios, not production scenarios. 
> This tier restricts throughput and usage, and has no service-level agreement (SLA).

| Artifact | Free | Basic | Standard |
|----------|------|-------|----------|
| EDI trading agreements | 10 | 1 | 1,000 |
| EDI trading partners | 25 | 2 | 1,000 |
| Maps | 25 | 500 | 1,000 |
| Schemas | 25 | 500 | 1,000 |
| Assemblies | 10 | 25 | 1,000 |
| Certificates | 25 | 2 | 1,000 |
| Batch configurations | 5 | 1 | 50 |
||||

<a name="artifact-capacity-limits"></a>

### Artifact capacity limits

| Artifact | Limit | Notes |
| -------- | ----- | ----- |
| Assembly | 8 MB | To upload files larger than 2 MB, use an [Azure storage account and blob container](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
| Map (XSLT file) | 8 MB | To upload files larger than 2 MB, use the [Azure Logic Apps REST API - Maps](/rest/api/logic/maps/createorupdate). <p><p>**Note**: The amount of data or records that a map can successfully process is based on the message size and action timeout limits in Azure Logic Apps. For example, if you use an HTTP action, based on [HTTP message size and timeout limits](#http-limits), a map can process data up to the HTTP message size limit if the operation completes within the HTTP timeout limit. |
| Schema | 8 MB | To upload files larger than 2 MB, use an [Azure storage account and blob container](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
||||

<a name="integration-account-throughput-limits"></a>

### Throughput limits

| Runtime endpoint | Free | Basic | Standard | Notes |
|------------------|------|-------|----------|-------|
| Read calls per 5 min | 3,000 | 30,000 | 60,000 | This limit applies to calls that get the raw inputs and outputs from a logic app's run history. You can distribute the workload across more than one account as necessary. |
| Invoke calls per 5 min | 3,000 | 30,000 | 45,000 | You can distribute the workload across more than one account as necessary. |
| Tracking calls per 5 min | 3,000 | 30,000 | 45,000 | You can distribute the workload across more than one account as necessary. |
| Blocking concurrent calls | ~1,000 | ~1,000 | ~1,000 | Same for all SKUs. You can reduce the number of concurrent requests or reduce the duration as necessary. |
||||

<a name="b2b-protocol-limits"></a>

### B2B protocol (AS2, X12, EDIFACT) message size

The following table lists the message size limits that apply to B2B protocols:

| Name | Multi-tenant | Single-tenant | Integration service environment | Notes |
|------|--------------|---------------|---------------------------------|-------|
| AS2 | v2 - 100 MB<br>v1 - 25 MB | Unavailable | v2 - 200 MB <br>v1 - 25 MB | Applies to decode and encode |
| X12 | 50 MB | Unavailable | 50 MB | Applies to decode and encode |
| EDIFACT | 50 MB | Unavailable | 50 MB | Applies to decode and encode |
||||

<a name="configuration"></a>
<a name="firewall-ip-configuration"></a>

## Firewall configuration: IP addresses and service tags

If your environment has strict network requirements or firewalls that limit traffic to specific IP addresses, your environment or firewall needs to allow access for *both* the [inbound](#inbound) and [outbound](#outbound) IP addresses used by the Azure Logic Apps service or runtime in the Azure region where your logic app resource exists. To set up this access, you can create [Azure Firewall rules](../firewall/rule-processing.md). *All* logic apps in the same region use the same IP address ranges.

> [!NOTE]
> If you're using [Power Automate](/power-automate/getting-started), some actions, such as **HTTP** and **HTTP + OpenAPI**, 
> go directly through the Azure Logic Apps service and come from the IP addresses that are listed here. For more information 
> about the IP addresses used by Power Automate, see [Limits and configuration for Power Automate](/power-automate/limits-and-config#ip-address-configuration).

For example, suppose your logic apps are deployed in the West US region. To support calls that your logic apps send or receive through built-in triggers and actions, such as the [HTTP trigger or action](../connectors/connectors-native-http.md), your firewall needs to allow access for *all* the Azure Logic Apps service inbound IP addresses *and* outbound IP addresses that exist in the West US region.

If your workflow uses [managed connectors](../connectors/managed.md), such as the Office 365 Outlook connector or SQL connector, or uses [custom connectors](/connectors/custom-connectors/), the firewall also needs to allow access for *all* the [managed connector outbound IP addresses](/connectors/common/outbound-ip-addresses) in your logic app's Azure region. If your workflow uses custom connectors that access on-premises resources through the [on-premises data gateway resource in Azure](logic-apps-gateway-connection.md), you need to set up the gateway installation to allow access for the corresponding [*managed connector* outbound IP addresses](/connectors/common/outbound-ip-addresses). For more information about setting up communication settings on the gateway, review these topics:

* [Adjust communication settings for the on-premises data gateway](/data-integration/gateway/service-gateway-communication)
* [Configure proxy settings for the on-premises data gateway](/data-integration/gateway/service-gateway-proxy)

> [!IMPORTANT]
> If you're using [Microsoft Azure operated by 21Vianet](/azure/china/), managed connectors and custom connectors don't have reserved or fixed IP addresses. 
> So, you can't set up firewall rules for logic apps that use these connectors in this cloud. For the Azure Logic Apps service IPs, review the 
> [documentation version for Azure operated by 21Vianet](https://docs.azure.cn/en-us/logic-apps/logic-apps-limits-and-config#firewall-ip-configuration).

<a name="ip-setup-considerations"></a>

### Firewall IP configuration considerations

Before you set up your firewall with IP addresses, review these considerations:

* If your logic app workflows run in single-tenant Azure Logic Apps, you need to find the fully qualified domain names (FQDNs) for your connections. For more information, review the corresponding sections in these topics:

  * [Firewall permissions for single tenant logic apps - Azure portal](create-single-tenant-workflows-azure-portal.md#firewall-setup)
  * [Firewall permissions for single tenant logic apps - Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md#firewall-setup)

* If your logic app workflows run in an [integration service environment (ISE)](connect-virtual-network-vnet-isolated-environment-overview.md), make sure that you [open these ports too](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#network-ports-for-ise).

* To help you simplify any security rules that you want to create, you can optionally use [service tags](../virtual-network/service-tags-overview.md) instead, rather than specify IP address prefixes for each region. These tags work across the regions where the Logic Apps service is available:

  * **LogicAppsManagement**: Represents the inbound IP address prefixes for the Logic Apps service.

  * **LogicApps**: Represents the outbound IP address prefixes for the Logic Apps service.

  * **AzureConnectors**: Represents the IP address prefixes for managed connectors that make inbound webhook callbacks to the Logic Apps service and outbound calls to their respective services, such as Azure Storage or Azure Event Hubs.

* If your logic apps have problems accessing Azure storage accounts that use [firewalls and firewall rules](../storage/common/storage-network-security.md), you have [various other options to enable access](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls).

  For example, logic apps can't directly access storage accounts that use firewall rules and exist in the same region. However, if you permit the [outbound IP addresses for managed connectors in your region](/connectors/common/outbound-ip-addresses), your logic apps can access storage accounts that are in a different region except when you use the Azure Table Storage or Azure Queue Storage connectors. To access your Table Storage or Queue Storage, you can use the HTTP trigger and actions instead. For other options, see [Access storage accounts behind firewalls](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls).

<a name="inbound"></a>

### Inbound IP addresses

This section lists the inbound IP addresses for the Azure Logic Apps service only. If you're using Azure Government, see [Azure Government - Inbound IP addresses](#azure-government-inbound).

> [!TIP]
> To help reduce complexity when you create security rules, you can optionally use the [service tag](../virtual-network/service-tags-overview.md), 
> **LogicAppsManagement**, rather than specify inbound Logic Apps IP address prefixes for each region.
>
> Some managed connectors make inbound webhook callbacks to the Azure Logic Apps service. For these managed connectors, you can optionally use the 
> **AzureConnectors** service tag for these managed connectors, rather than specify inbound managed connector IP address prefixes for each region. 
> These tags work across the regions where the Logic Apps service is available.
>
> The following connectors make inbound webhook callbacks to the Logic Apps service:
>
> Adobe Creative Cloud, Adobe Sign, Adobe Sign Demo, Adobe Sign Preview, Adobe Sign Stage, Microsoft Sentinel, Business Central, Calendly, 
> Common Data Service, DocuSign, DocuSign Demo, Dynamics 365 for Fin & Ops, LiveChat, Office 365 Outlook, Outlook.com, Parserr, SAP*, 
> Shifts for Microsoft Teams, Teamwork Projects, Typeform
>
> \* **SAP**: The return caller depends on whether the deployment environment is either multi-tenant Azure or ISE. In the 
> multi-tenant environment, the on-premises data gateway makes the call back to the Logic Apps service. In an ISE, the SAP 
> connector makes the call back to the Logic Apps service.

<a name="multi-tenant-inbound"></a>

#### Multi-tenant & single-tenant - Inbound IP addresses

| Region | IP |
|--------|----|
| Australia East | 13.75.153.66, 104.210.89.222, 104.210.89.244, 52.187.231.161, 13.70.78.192 - 13.70.78.223 |
| Australia Southeast | 13.73.115.153, 40.115.78.70, 40.115.78.237, 52.189.216.28 |
| Brazil South | 191.235.86.199, 191.235.95.229, 191.235.94.220, 191.234.166.198, 191.233.207.32 - 191.233.207.63 |
| Brazil Southeast | 20.40.32.59, 20.40.32.162, 20.40.32.80, 20.40.32.49 |
| Canada Central | 13.88.249.209, 52.233.30.218, 52.233.29.79, 40.85.241.105, 20.38.149.160 - 20.38.149.191 |
| Canada East | 52.232.129.143, 52.229.125.57, 52.232.133.109, 40.86.202.42 |
| Central India | 52.172.157.194, 52.172.184.192, 52.172.191.194, 104.211.73.195 |
| Central US | 13.67.236.76, 40.77.111.254, 40.77.31.87, 104.43.243.39, 52.182.141.160 - 52.182.141.191 |
| East Asia | 168.63.200.173, 13.75.89.159, 23.97.68.172, 40.83.98.194 |
| East US | 137.135.106.54, 40.117.99.79, 40.117.100.228, 137.116.126.165, 20.42.72.160 - 20.42.72.191 |
| East US 2 | 40.84.25.234, 40.79.44.7, 40.84.59.136, 40.70.27.253, 20.44.17.224 - 20.44.17.255 |
| France Central | 52.143.162.83, 20.188.33.169, 52.143.156.55, 52.143.158.203, 40.79.139.160 - 40.79.139.191 |
| France South | 52.136.131.145, 52.136.129.121, 52.136.130.89, 52.136.131.4 |
| Germany North | 51.116.211.29, 51.116.208.132, 51.116.208.37, 51.116.208.64 |
| Germany West Central | 51.116.168.222, 51.116.171.209, 51.116.233.40, 51.116.175.0, 51.116.243.224 - 51.116.243.255 |
| Japan East | 13.71.146.140, 13.78.84.187, 13.78.62.130, 13.78.43.164, 13.78.111.160 - 13.78.111.191 |
| Japan West | 40.74.140.173, 40.74.81.13, 40.74.85.215, 40.74.68.85 |
| Jio India West | 20.193.206.48,20.193.206.49,20.193.206.50,20.193.206.51 |
| Korea Central | 52.231.14.182, 52.231.103.142, 52.231.39.29, 52.231.14.42 |
| Korea South | 52.231.166.168, 52.231.163.55, 52.231.163.150, 52.231.192.64 |
| North Central US | 168.62.249.81, 157.56.12.202, 65.52.211.164, 65.52.9.64 |
| North Europe | 13.79.173.49, 52.169.218.253, 52.169.220.174, 40.112.90.39, 13.69.231.160 - 13.69.231.191 |
| Norway East | 51.120.88.93, 51.13.66.86, 51.120.89.182, 51.120.88.77 |
| South Africa North | 102.133.228.4, 102.133.224.125, 102.133.226.199, 102.133.228.9 |
| South Africa West | 102.133.72.190, 102.133.72.145, 102.133.72.184, 102.133.72.173 |
| South Central US | 13.65.98.39, 13.84.41.46, 13.84.43.45, 40.84.138.132, 13.73.244.160 - 13.73.244.191 |
| South India | 52.172.9.47, 52.172.49.43, 52.172.51.140, 104.211.225.152 |
| Southeast Asia | 52.163.93.214, 52.187.65.81, 52.187.65.155, 104.215.181.6, 13.67.13.224 - 13.67.13.255 |
| Switzerland North | 51.103.128.52, 51.103.132.236, 51.103.134.138, 51.103.136.209 |
| Switzerland West | 51.107.225.180, 51.107.225.167, 51.107.225.163, 51.107.239.66 |
| UAE Central | 20.45.75.193, 20.45.64.29, 20.45.64.87, 20.45.71.213 |
| UAE North | 20.46.42.220, 40.123.224.227, 40.123.224.143, 20.46.46.173 |
| UK South | 51.140.79.109, 51.140.78.71, 51.140.84.39, 51.140.155.81, 51.105.69.96 - 51.105.69.127 |
| UK West | 51.141.48.98, 51.141.51.145, 51.141.53.164, 51.141.119.150 |
| West Central US | 52.161.26.172, 52.161.8.128, 52.161.19.82, 13.78.137.247 |
| West Europe | 13.95.155.53, 52.174.54.218, 52.174.49.6, 13.69.71.160 - 13.69.71.191 |
| West India | 104.211.164.112, 104.211.165.81, 104.211.164.25, 104.211.157.237 |
| West US | 52.160.90.237, 138.91.188.137, 13.91.252.184, 157.56.160.212 |
| West US 2 | 13.66.224.169, 52.183.30.10, 52.183.39.67, 13.66.128.68, 40.78.245.160 - 40.78.245.191 |
| West US 3| 20.150.172.240, 20.150.172.242, 20.150.172.243, 20.150.172.241 |
|||

<a name="azure-government-inbound"></a>

#### Azure Government - Inbound IP addresses

| Azure Government region | IP |
|-------------------------|----|
| US Gov Arizona | 52.244.67.164, 52.244.67.64, 52.244.66.82 |
| US Gov Texas | 52.238.119.104, 52.238.112.96, 52.238.119.145 |
| US Gov Virginia | 52.227.159.157, 52.227.152.90, 23.97.4.36 |
| US DoD Central | 52.182.49.204, 52.182.52.106 |
|||

<a name="outbound"></a>

### Outbound IP addresses

This section lists the outbound IP addresses for the Azure Logic Apps service. If you're using Azure Government, see [Azure Government - Outbound IP addresses](#azure-government-outbound). If your workflow uses [managed connectors](../connectors/managed.md), such as the Office 365 Outlook connector or SQL connector, or uses [custom connectors](/connectors/custom-connectors/), the firewall also needs to allow access for *all* the [managed connector outbound IP addresses](/connectors/common/outbound-ip-addresses) in your logic app's Azure region. If your workflow uses custom connectors that access on-premises resources through the [on-premises data gateway resource in Azure](logic-apps-gateway-connection.md), you need to set up the gateway installation to allow access for the corresponding [*managed connector* outbound IP addresses](/connectors/common/outbound-ip-addresses). For more information about setting up communication settings on the gateway, review these topics:

* [Adjust communication settings for the on-premises data gateway](/data-integration/gateway/service-gateway-communication)
* [Configure proxy settings for the on-premises data gateway](/data-integration/gateway/service-gateway-proxy)

> [!TIP]
> To help reduce complexity when you create security rules, you can optionally use the [service tag](../virtual-network/service-tags-overview.md), 
> **LogicApps**, rather than specify outbound Logic Apps IP address prefixes for each region. Optionally, you can also use the **AzureConnectors** 
> service tag for managed connectors that make outbound calls to their respective services, such as Azure Storage or Azure Event Hubs, rather than 
> specify outbound managed connector IP address prefixes for each region. These tags work across the regions where the Logic Apps service is available.

<a name="multi-tenant-outbound"></a>

#### Multi-tenant & single-tenant - Outbound IP addresses

| Region | Logic Apps IP |
|--------|---------------|
| Australia East | 13.75.149.4, 104.210.91.55, 104.210.90.241, 52.187.227.245, 52.187.226.96, 52.187.231.184, 52.187.229.130, 52.187.226.139, 13.70.78.192 - 13.70.78.223 |
| Australia Southeast | 13.73.114.207, 13.77.3.139, 13.70.159.205, 52.189.222.77, 13.77.56.167, 13.77.58.136, 52.189.214.42, 52.189.220.75 |
| Brazil South | 191.235.82.221, 191.235.91.7, 191.234.182.26, 191.237.255.116, 191.234.161.168, 191.234.162.178, 191.234.161.28, 191.234.162.131, 191.233.207.32 - 191.233.207.63 |
| Brazil Southeast | 20.40.32.81, 20.40.32.19, 20.40.32.85, 20.40.32.60, 20.40.32.116, 20.40.32.87, 20.40.32.61, 20.40.32.113 |
| Canada Central | 52.233.29.92, 52.228.39.244, 40.85.250.135, 40.85.250.212, 13.71.186.1, 40.85.252.47, 13.71.184.150, 20.38.149.160 - 20.38.149.191 |
| Canada East | 52.232.128.155, 52.229.120.45, 52.229.126.25, 40.86.203.228, 40.86.228.93, 40.86.216.241, 40.86.226.149, 40.86.217.241 |
| Central India | 52.172.154.168, 52.172.186.159, 52.172.185.79, 104.211.101.108, 104.211.102.62, 104.211.90.169, 104.211.90.162, 104.211.74.145 |
| Central US | 13.67.236.125, 104.208.25.27, 40.122.170.198, 40.113.218.230, 23.100.86.139, 23.100.87.24, 23.100.87.56, 23.100.82.16, 52.182.141.160 - 52.182.141.191 |
| East Asia | 13.75.94.173, 40.83.127.19, 52.175.33.254, 40.83.73.39, 65.52.175.34, 40.83.77.208, 40.83.100.69, 40.83.75.165 |
| East US | 13.92.98.111, 40.121.91.41, 40.114.82.191, 23.101.139.153, 23.100.29.190, 23.101.136.201, 104.45.153.81, 23.101.132.208, 20.42.72.160 - 20.42.72.191 |
| East US 2 | 40.84.30.147, 104.208.155.200, 104.208.158.174, 104.208.140.40, 40.70.131.151, 40.70.29.214, 40.70.26.154, 40.70.27.236, 20.44.17.224 - 20.44.17.255 |
| France Central | 52.143.164.80, 52.143.164.15, 40.89.186.30, 20.188.39.105, 40.89.191.161, 40.89.188.169, 40.89.186.28, 40.89.190.104, 40.79.139.160 - 40.79.139.191 |
| France South | 52.136.132.40, 52.136.129.89, 52.136.131.155, 52.136.133.62, 52.136.139.225, 52.136.130.144, 52.136.140.226, 52.136.129.51 |
| Germany North | 51.116.211.168, 51.116.208.165, 51.116.208.175, 51.116.208.192, 51.116.208.200, 51.116.208.222, 51.116.208.217, 51.116.208.51 |
| Germany West Central | 51.116.233.35, 51.116.171.49, 51.116.233.33, 51.116.233.22, 51.116.168.104, 51.116.175.17, 51.116.233.87, 51.116.175.51, 51.116.243.224 - 51.116.243.255 |
| Japan East | 13.71.158.3, 13.73.4.207, 13.71.158.120, 13.78.18.168, 13.78.35.229, 13.78.42.223, 13.78.21.155, 13.78.20.232, 13.78.111.160 - 13.78.111.191 |
| Japan West | 40.74.140.4, 104.214.137.243, 138.91.26.45, 40.74.64.207, 40.74.76.213, 40.74.77.205, 40.74.74.21, 40.74.68.85 |
| Jio India West | 20.193.206.128, 20.193.206.129, 20.193.206.130, 20.193.206.131, 20.193.206.132, 20.193.206.133, 20.193.206.134, 20.193.206.135 |
| Korea Central | 52.231.14.11, 52.231.14.219, 52.231.15.6, 52.231.10.111, 52.231.14.223, 52.231.77.107, 52.231.8.175, 52.231.9.39 |
| Korea South | 52.231.204.74, 52.231.188.115, 52.231.189.221, 52.231.203.118, 52.231.166.28, 52.231.153.89, 52.231.155.206, 52.231.164.23 |
| North Central US | 168.62.248.37, 157.55.210.61, 157.55.212.238, 52.162.208.216, 52.162.213.231, 65.52.10.183, 65.52.9.96, 65.52.8.225 |
| North Europe | 40.113.12.95, 52.178.165.215, 52.178.166.21, 40.112.92.104, 40.112.95.216, 40.113.4.18, 40.113.3.202, 40.113.1.181, 13.69.231.160 - 13.69.231.191 |
| Norway East | 51.120.88.52, 51.120.88.51, 51.13.65.206, 51.13.66.248, 51.13.65.90, 51.13.65.63, 51.13.68.140, 51.120.91.248 |
| South Africa North | 102.133.231.188, 102.133.231.117, 102.133.230.4, 102.133.227.103, 102.133.228.6, 102.133.230.82, 102.133.231.9, 102.133.231.51 |
| South Africa West | 102.133.72.98, 102.133.72.113, 102.133.75.169, 102.133.72.179, 102.133.72.37, 102.133.72.183, 102.133.72.132, 102.133.75.191 |
| South Central US | 104.210.144.48, 13.65.82.17, 13.66.52.232, 23.100.124.84, 70.37.54.122, 70.37.50.6, 23.100.127.172, 23.101.183.225, 13.73.244.160 - 13.73.244.191 |
| South India | 52.172.50.24, 52.172.55.231, 52.172.52.0, 104.211.229.115, 104.211.230.129, 104.211.230.126, 104.211.231.39, 104.211.227.229 |
| Southeast Asia | 13.76.133.155, 52.163.228.93, 52.163.230.166, 13.76.4.194, 13.67.110.109, 13.67.91.135, 13.76.5.96, 13.67.107.128, 13.67.13.224 - 13.67.13.255 |
| Switzerland North | 51.103.137.79, 51.103.135.51, 51.103.139.122, 51.103.134.69, 51.103.138.96, 51.103.138.28, 51.103.136.37, 51.103.136.210 |
| Switzerland West | 51.107.239.66, 51.107.231.86, 51.107.239.112, 51.107.239.123, 51.107.225.190, 51.107.225.179, 51.107.225.186, 51.107.225.151, 51.107.239.83 |
| UAE Central | 20.45.75.200, 20.45.72.72, 20.45.75.236, 20.45.79.239, 20.45.67.170, 20.45.72.54, 20.45.67.134, 20.45.67.135 |
| UAE North | 40.123.230.45, 40.123.231.179, 40.123.231.186, 40.119.166.152, 40.123.228.182, 40.123.217.165, 40.123.216.73, 40.123.212.104 |
| UK South | 51.140.74.14, 51.140.73.85, 51.140.78.44, 51.140.137.190, 51.140.153.135, 51.140.28.225, 51.140.142.28, 51.140.158.24, 51.105.69.96 - 51.105.69.127 |
| UK West | 51.141.54.185, 51.141.45.238, 51.141.47.136, 51.141.114.77, 51.141.112.112, 51.141.113.36, 51.141.118.119, 51.141.119.63 |
| West Central US | 52.161.27.190, 52.161.18.218, 52.161.9.108, 13.78.151.161, 13.78.137.179, 13.78.148.140, 13.78.129.20, 13.78.141.75, 13.71.199.128 - 13.71.199.159 |
| West Europe | 40.68.222.65, 40.68.209.23, 13.95.147.65, 23.97.218.130, 51.144.182.201, 23.97.211.179, 104.45.9.52, 23.97.210.126, 13.69.71.160, 13.69.71.161, 13.69.71.162, 13.69.71.163, 13.69.71.164, 13.69.71.165, 13.69.71.166, 13.69.71.167, 13.69.71.160 - 13.69.71.191 |
| West India | 104.211.164.80, 104.211.162.205, 104.211.164.136, 104.211.158.127, 104.211.156.153, 104.211.158.123, 104.211.154.59, 104.211.154.7 |
| West US | 52.160.92.112, 40.118.244.241, 40.118.241.243, 157.56.162.53, 157.56.167.147, 104.42.49.145, 40.83.164.80, 104.42.38.32, 13.86.223.0, 13.86.223.1, 13.86.223.2, 13.86.223.3, 13.86.223.4, 13.86.223.5 |
| West US 2 | 13.66.210.167, 52.183.30.169, 52.183.29.132, 13.66.210.167, 13.66.201.169, 13.77.149.159, 52.175.198.132, 13.66.246.219, 40.78.245.160 - 40.78.245.191 |
| West US 3 | 20.150.181.32, 20.150.181.33, 20.150.181.34, 20.150.181.35, 20.150.181.36, 20.150.181.37, 20.150.181.38, 20.150.173.192 |
|||

<a name="azure-government-outbound"></a>

#### Azure Government - Outbound IP addresses

| Region | Logic Apps IP |
|--------|---------------|
| US DoD Central | 52.182.48.215, 52.182.92.143 |
| US Gov Arizona | 52.244.67.143, 52.244.65.66, 52.244.65.190 |
| US Gov Texas | 52.238.114.217, 52.238.115.245, 52.238.117.119 |
| US Gov Virginia | 13.72.54.205, 52.227.138.30, 52.227.152.44 |
|||

## Next steps

* Learn how to [create your first logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* Learn about [common examples and scenarios](../logic-apps/logic-apps-examples-and-scenarios.md)

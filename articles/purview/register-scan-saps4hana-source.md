---
title: Connect to and manage an SAP S/4HANA source
description: This guide describes how to connect to SAP S/4HANA in Azure Purview, and use Purview's features to scan and manage your SAP S/4HANA source.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
---

# Connect to and manage SAP S/4HANA in Azure Purview

This article outlines how to register SAP S/4HANA, and how to authenticate and interact with SAP S/4HANA in Azure Purview. For more information about Azure Purview, read the [introductory article](overview.md).

## Supported capabilities

|**Metadata Extraction**|  **Full Scan**  |**Incremental Scan**|**Scoped Scan**|**Classification**|**Access Policy**|**Lineage**|
|---|---|---|---|---|---|---|
| [Yes](#register)| [Yes](#scan)| No | No | No | No| [Yes**](how-to-lineage-sapecc.md)|

\** Lineage is supported if dataset is used as a source/sink in [Data Factory Copy activity](how-to-link-azure-data-factory.md) 

## Prerequisites

* An Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* An active [Purview resource](create-catalog-portal.md).

* You will need to be a Data Source Administrator and Data Reader to register a source and manage it in the Purview Studio. See our [Azure Purview Permissions page](catalog-permissions.md) for details.

* Set up the latest [self-hosted integration runtime](https://www.microsoft.com/download/details.aspx?id=39717). For more information, see [the create and configure a self-hosted integration runtime guide](../data-factory/create-self-hosted-integration-runtime.md).

    >[!NOTE]
    >Scanning SAP S/4HANA is a memory intensive operation, you are recommended to install Self-hosted Integration Runtime on a machine with large memory e.g. 128 GB.

* Ensure [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) is installed on the virtual machine where the self-hosted integration runtime is installed.

* Ensure Visual C++ Redistributable for Visual Studio 2012 Update 4 is installed on the self-hosted integration runtime machine. If you don't have this update installed, [you can download it here](https://www.microsoft.com/download/details.aspx?id=30679).

* Download the 64-bit [SAP Connector for Microsoft .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html) from SAP\'s website and install it on the self-hosted integration runtime machine. During installation, make sure you select the **Install Assemblies to GAC** option in the **Optional setup steps** window.

    :::image type="content" source="media/register-scan-saps4hana-source/requirement.png" alt-text="pre-requisite" border="true":::

* The connector reads metadata from SAP using the [SAP Java Connector (JCo)](https://support.sap.com/en/product/connectors/jco.html) 3.0 API. Hence make sure the Java Connector is available on your virtual machine where self-hosted integration runtime is installed. Make sure that you are using the correct JCo distribution for your environment. For example, on a Microsoft Windows machine, make sure the sapjco3.jar and sapjco3.dll files are available.

    > [!Note]
    >The driver should be accessible to all accounts in the VM. Do not install it in a user account.

* Deploy the metadata extraction ABAP function module on the SAP server by following the steps mentioned in [ABAP functions deployment guide](abap-functions-deployment-guide.md). You will need an ABAP developer account to create the RFC function module on the SAP server. The user account requires sufficient permissions to connect to the SAP server and execute the following RFC function modules:
  * STFC_CONNECTION (check connectivity)
  * RFC_SYSTEM_INFO (check system information)

## Register

This section describes how to register SAP S/4HANA in Azure Purview using the [Purview Studio](https://web.purview.azure.com/).

### Authentication for registration

The only supported authentication for SAP S/4HANA source is **Basic authentication**.

### Steps to register

1. Navigate to your Purview account.
1. Select **Data Map** on the left navigation.
1. Select **Register**
1. On Register sources, select **SAP S/4HANA.** Select **Continue**

    :::image type="content" source="media/register-scan-saps4hana-source/register-saps-4-hana.png" alt-text="register SAPS/4Hana options" border="true":::

On the **Register sources (SAP S/4HANA)** screen, do the following:

1. Enter a **Name** that the data source will be listed within the Catalog.

1. Enter the **Application server** name to connect to SAP S/4HANA source. It can also be an IP address of the SAP application server host.

1. Enter the SAP **System number**. This is a two-digit integer between 00 and 99.

1. Select a collection or create a new one (Optional)

1. Finish to register the data source.

    :::image type="content" source="media/register-scan-saps4hana-source/register-saps-4-hana-2.png" alt-text="register SAP S/4HANA" border="true":::

## Scan

Follow the steps below to scan SAP S/4HANA to automatically identify assets and classify your data. For more information about scanning in general, see our [introduction to scans and ingestion](concept-scans-and-ingestion.md).

### Create and run scan

1. In the Management Center, select Integration runtimes. Make sure a self-hosted integration runtime is set up. If it is not set up, use the steps mentioned [here](./manage-integration-runtimes.md) to create a self-hosted integration runtime

1. Navigate to **Sources.**

1. Select the registered SAP S/4HANA source.

1. Select **+ New scan**

1. Provide the below details:

    1. **Name**: The name of the scan

    1. **Connect via integration runtime**: Select the configured self-hosted integration runtime.

    1. **Credential**: Select the credential to connect to your data source. Make sure to:

        * Select Basic Authentication while creating a credential.
        * Provide a user ID to connect to SAP server in the User name input field.
        * Store the user password used to connect to SAP server in the secret key.

    1. **Client ID:** Enter here the SAP system client ID. The client
        is identified with three-digit numeric number from 000 to 999.

    1. **JCo library path**: Specify the path to the folder where the JCo libraries are located.

    1. **Maximum memory available:** Maximum memory (in GB) available on customer's VM to be used by scanning processes. This is dependent on the size of SAP S/4HANA source to be scanned. It's recommended to provide large available memory e.g. 100.

    :::image type="content" source="media/register-scan-saps4hana-source/scan-saps-4-hana.png" alt-text="scan SAP S/4HANA" border="true":::

1. Select **Continue**.

1. Choose your **scan trigger**. You can set up a schedule or ran the scan once.

1. Review your scan and select **Save and Run**.

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## Next steps

Now that you have registered your source, follow the below guides to learn more about Purview and your data.

- [Data insights in Azure Purview](concept-insights.md)
- [Lineage in Azure Purview](catalog-lineage-user-guide.md)
- [Search Data Catalog](how-to-search-catalog.md)

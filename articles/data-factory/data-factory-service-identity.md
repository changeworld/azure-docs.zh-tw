---
title: 適用於 Data Factory 的受控身分識別
description: 瞭解 Azure Data Factory 的受控識別。
services: data-factory
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jingwang
ms.openlocfilehash: 45699680ad2003c034bce588857f8b102a0b6d26
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79261121"
---
# <a name="managed-identity-for-data-factory"></a>適用於 Data Factory 的受控身分識別

本文可協助您瞭解 Data Factory 的受控識別（先前稱為受控服務識別/MSI）及其運作方式。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>概觀

建立資料處理站時，可以建立受控身分識別和 factory 建立。 受控識別是向 Azure Active Directory 註冊的受控應用程式，代表此特定的 data factory。

Data Factory 的受控識別可享有下列功能：

- [將認證儲存在 Azure Key Vault](store-credentials-in-key-vault.md)中，在此情況下，會使用 data factory 受控識別進行 Azure Key Vault 驗證。
- 連接器，包括 [Azure Blob 儲存體](connector-azure-blob-storage.md)、[Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、[Azure Data Lake Storage Gen2 ](connector-azure-data-lake-storage.md)、[Azure SQL Database](connector-azure-sql-database.md)，以及 [Azure SQL 資料倉儲](connector-azure-sql-data-warehouse.md)。
- [網路活動](control-flow-web-activity.md)。

## <a name="generate-managed-identity"></a>產生受控識別

Data Factory 的受控識別產生方式如下所示：

- 透過**Azure 入口網站或 PowerShell**建立 data factory 時，一律會自動建立受控識別。
- 透過**SDK**建立 data factory 時，只有當您在建立的 factory 物件中指定 "identity = new FactoryIdentity （）" 時，才會建立受控識別。 請參閱 [.NET 快速入門 - 建立資料處理站](quickstart-create-data-factory-dot-net.md#create-a-data-factory)中的範例。
- 透過**REST API**建立 data factory 時，只有當您在要求主體中指定 "identity" 區段時，才會建立受控識別。 請參閱 [REST 快速入門 - 建立資料處理站](quickstart-create-data-factory-rest-api.md#create-a-data-factory)中的範例。

如果您發現您的資料處理站沒有與 [抓取[受控識別](#retrieve-managed-identity)] 指令相關聯的受控識別，您可以透過以程式設計方式更新 data factory 與身分識別啟動器，以明確地產生一個。

- [使用 PowerShell 產生受控識別](#generate-managed-identity-using-powershell)
- [使用 REST API 產生受控識別](#generate-managed-identity-using-rest-api)
- [使用 Azure Resource Manager 範本產生受控識別](#generate-managed-identity-using-an-azure-resource-manager-template)
- [使用 SDK 產生受控識別](#generate-managed-identity-using-sdk)

>[!NOTE]
>- 無法修改受控識別。 更新已有受控識別的資料處理站不會有任何影響，受控識別會保持不變。
>- 如果您更新已有受控識別的資料處理站，但未在 factory 物件中指定 "identity" 參數，或未在 REST 要求本文中指定 "identity" 區段，您將會收到錯誤。
>- 當您刪除資料處理站時，將會一併刪除相關聯的受控識別。

### <a name="generate-managed-identity-using-powershell"></a>使用 PowerShell 產生受控識別

再次呼叫**set-azdatafactoryv2**命令，然後您會看到新產生的「身分識別」欄位：

```powershell
PS C:\WINDOWS\system32> Set-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```

### <a name="generate-managed-identity-using-rest-api"></a>使用 REST API 產生受控識別

在要求本文中以 "identity" 區段呼叫下面的 API：

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2018-06-01
```

**要求本文**：新增 "identity": { "type": "SystemAssigned" }。

```json
{
    "name": "<dataFactoryName>",
    "location": "<region>",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
```

**回應**：系統會自動建立受控識別，並據此填入「身分識別」區段。

```json
{
    "name": "<dataFactoryName>",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2017-09-26T04:10:01.1135678Z",
        "version": "2018-06-01"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "<region>"
}
```

### <a name="generate-managed-identity-using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本產生受控識別

**Template**: add "identity": { "type": "SystemAssigned" }.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "resources": [{
        "name": "<dataFactoryName>",
        "apiVersion": "2018-06-01",
        "type": "Microsoft.DataFactory/factories",
        "location": "<region>",
        "identity": {
            "type": "SystemAssigned"
        }
    }]
}
```

### <a name="generate-managed-identity-using-sdk"></a>使用 SDK 產生受控識別

以 Identity=new FactoryIdentity() 呼叫資料處理站 create_or_update 函式。 使用 .NET 的範例程式碼：

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-managed-identity"></a>取出受控識別

您可以從 Azure 入口網站或以程式設計方式抓取受控識別。 下列各節會顯示一些範例。

>[!TIP]
> 如果您沒有看到受控識別，請更新您的處理站來[產生受控識別](#generate-managed-identity)。

### <a name="retrieve-managed-identity-using-azure-portal"></a>使用 Azure 入口網站取出受控識別

您可以從 Azure 入口網站 > 您的資料處理站 > 屬性來尋找受控識別資訊。

- 受控識別物件識別碼
- 受控識別租使用者
- 受控識別應用程式識別碼

當您建立支援受控識別驗證（例如 Azure Blob、Azure Data Lake Storage、Azure Key Vault 等）的連結服務時，也會顯示受控識別資訊。

授與許可權時，請使用物件識別碼或 data factory 名稱（做為受控識別名稱）來尋找此身分識別。

### <a name="retrieve-managed-identity-using-powershell"></a>使用 PowerShell 取出受控識別

當您取得特定的資料處理站時，將會傳回受控識別主體識別碼和租使用者識別碼，如下所示。 使用**PrincipalId**授與存取權：

```powershell
PS C:\WINDOWS\system32> (Get-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

您可以藉由複製上述主體識別碼來取得應用程式識別碼，然後以主體識別碼作為參數執行 Azure Active Directory 命令下方執行。

```powershell
PS C:\WINDOWS\system32> Get-AzADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="next-steps"></a>後續步驟
請參閱下列主題，其仲介紹使用 data factory 受控識別的時機和方法：

- [在 Azure Key Vault 中儲存認證](store-credentials-in-key-vault.md)
- [使用 Azure 資源的受控識別驗證，從 Azure Data Lake Store 來回複製資料](connector-azure-data-lake-store.md)

請參閱適用于[Azure 資源的受控識別總覽](/azure/active-directory/managed-identities-azure-resources/overview)，以取得 azure 資源受控識別的更多背景資訊，而這是以 data factory 受控識別為基礎。 

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
ms.date: 07/06/2020
ms.author: jingwang
ms.openlocfilehash: 117b0db4f04c3fd631f6692d288945019507f5c6
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92632799"
---
# <a name="managed-identity-for-data-factory"></a>適用於 Data Factory 的受控身分識別

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文可協助您瞭解 Data Factory (的受控識別（先前稱為「受控服務識別」/MSI) ）及其運作方式。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>概觀

建立資料處理站時，可以建立受控識別，以及建立 factory。 受控識別是已向 Azure Active Directory 註冊的受控應用程式，並代表這個特定的資料處理站。

Data Factory 的受控識別可享有下列功能：

- [將認證儲存在 Azure Key Vault](store-credentials-in-key-vault.md)中，在此情況下會使用 data factory 受控識別來進行 Azure Key Vault 驗證。
- 連接器包括 [Azure Blob 儲存體](connector-azure-blob-storage.md)、 [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、 [Azure SQL Database](connector-azure-sql-database.md)和 [Azure Synapse Analytics (先前的 SQL 資料倉儲) ](connector-azure-sql-data-warehouse.md)。
- [Web 活動](control-flow-web-activity.md)。

## <a name="generate-managed-identity"></a>產生受控識別

Data Factory 的受控識別產生方式如下：

- 透過 **Azure 入口網站或 PowerShell** 建立資料處理站時，一律會自動建立受控識別。
- 透過 **SDK** 建立資料處理站時，只有在您于 factory 物件中指定 "identity = new FactoryIdentity ( # A1" 以供建立時，才會建立受控識別。 請參閱 [.NET 快速入門 - 建立資料處理站](quickstart-create-data-factory-dot-net.md#create-a-data-factory)中的範例。
- 透過 **REST API** 建立資料處理站時，只有當您在要求主體中指定 "identity" 區段時，才會建立受控識別。 請參閱 [REST 快速入門 - 建立資料處理站](quickstart-create-data-factory-rest-api.md#create-a-data-factory)中的範例。

如果您發現您的資料處理站沒有相關聯的受控 [識別，](#retrieve-managed-identity) 請以程式設計方式使用身分識別啟動器來更新 data factory，以明確地產生一個：

- [使用 PowerShell 產生受控識別](#generate-managed-identity-using-powershell)
- [使用 REST API 產生受控識別](#generate-managed-identity-using-rest-api)
- [使用 Azure Resource Manager 範本產生受控識別](#generate-managed-identity-using-an-azure-resource-manager-template)
- [使用 SDK 產生受控識別](#generate-managed-identity-using-sdk)

>[!NOTE]
>- 無法修改受控識別。 更新已有受控識別的資料處理站不會有任何影響，受控識別會保持不變。
>- 如果您更新已有受控識別的資料處理站，但未在處理站物件中指定 "identity" 參數，或未在 REST 要求本文中指定 "identity" 區段，您將會收到錯誤。
>- 當您刪除資料處理站時，將會一併刪除相關聯的受控識別。

### <a name="generate-managed-identity-using-powershell"></a>使用 PowerShell 產生受控識別

呼叫 **>set-azdatafactoryv2** 命令之後，您會看到新產生的「身分識別」欄位：

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

**要求本文** ：新增 "identity": { "type": "SystemAssigned" }。

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

**回應** ：系統會自動建立受控識別，並據此填入「身分識別」區段。

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

**Template** : add "identity": { "type": "SystemAssigned" }.

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

您可以從 Azure 入口網站或以程式設計方式取得受控識別。 下列各節會顯示一些範例。

>[!TIP]
> 如果您沒有看到受控識別，請更新您的 factory 以 [產生受控識別](#generate-managed-identity) 。

### <a name="retrieve-managed-identity-using-azure-portal"></a>使用 Azure 入口網站取出受控識別

您可以從 Azure 入口網站 > 您的 data factory > 屬性中找到受控識別資訊。

- 受控識別物件識別碼
- 受控識別租使用者
- 受控識別應用程式識別碼

當您建立連結服務時，也會顯示受控識別資訊，它支援受控識別驗證，例如 Azure Blob、Azure Data Lake Storage、Azure Key Vault 等等。

授與許可權時，請使用物件識別碼或 data factory 名稱 (作為受控識別名稱) 來尋找此身分識別。

### <a name="retrieve-managed-identity-using-powershell"></a>使用 PowerShell 取出受控識別

當您取得特定的資料處理站時，就會傳回受控識別主體識別碼和租使用者識別碼，如下所示。 使用 **PrincipalId** 授與存取權：

```powershell
PS C:\WINDOWS\system32> (Get-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

您可以藉由複製上述主體識別碼來取得應用程式識別碼，然後以主體識別碼作為參數來執行下列 Azure Active Directory 命令。

```powershell
PS C:\WINDOWS\system32> Get-AzADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

### <a name="retrieve-managed-identity-using-rest-api"></a>使用 REST API 取出受控識別

當您取得特定的資料處理站時，就會傳回受控識別主體識別碼和租使用者識別碼，如下所示。

在要求中呼叫下列 API：

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}?api-version=2018-06-01
```

**回應** ：您將會收到如下列範例所示的回應。 [識別] 區段會據以填入。

```json
{
    "name":"<dataFactoryName>",
    "identity":{
        "type":"SystemAssigned",
        "principalId":"554cff9e-XXXX-XXXX-XXXX-90c7d9ff2ead",
        "tenantId":"72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>",
    "type":"Microsoft.DataFactory/factories",
    "properties":{
        "provisioningState":"Succeeded",
        "createTime":"2020-02-12T02:22:50.2384387Z",
        "version":"2018-06-01",
        "factoryStatistics":{
            "totalResourceCount":0,
            "maxAllowedResourceCount":0,
            "factorySizeInGbUnits":0,
            "maxAllowedFactorySizeInGbUnits":0
        }
    },
    "eTag":"\"03006b40-XXXX-XXXX-XXXX-5e43617a0000\"",
    "location":"<region>",
    "tags":{

    }
}
```

> [!TIP] 
> 若要從 ARM 範本取出受控識別，請在 ARM JSON 中新增 **輸出** 區段：

```json
{
    "outputs":{
        "managedIdentityObjectId":{
            "type":"string",
            "value":"[reference(resourceId('Microsoft.DataFactory/factories', parameters('<dataFactoryName>')), '2018-06-01', 'Full').identity.principalId]"
        }
    }
}
```

## <a name="next-steps"></a>後續步驟
請參閱下列主題，這些主題會介紹使用 data factory 受控識別的時機和方式：

- [在 Azure Key Vault 中儲存認證](store-credentials-in-key-vault.md)
- [使用 Azure 資源的受控識別驗證，從 Azure Data Lake Store 來回複製資料](connector-azure-data-lake-store.md)

如需 Azure 資源受控識別的詳細背景資訊，請參閱適用 [于 Azure 資源的受控](../active-directory/managed-identities-azure-resources/overview.md) 識別（data factory 受控識別的基礎）。
---
title: 適用於 Data Factory 的受控身分識別
description: 瞭解 Azure 資料工廠的託管標識。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261121"
---
# <a name="managed-identity-for-data-factory"></a>適用於 Data Factory 的受控身分識別

本文可説明您瞭解資料工廠的託管標識（以前稱為託管服務標識/MSI）及其工作原理。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>總覽

創建資料工廠時，可以同時創建託管標識和工廠創建。 託管標識是註冊到 Azure 活動目錄的託管應用程式，表示此特定資料工廠。

資料工廠的託管標識具有以下功能：

- [在 Azure 金鑰保存庫中存儲憑據](store-credentials-in-key-vault.md)，在這種情況下，資料工廠託管標識用於 Azure 金鑰保存庫身份驗證。
- 連接器，包括 [Azure Blob 儲存體](connector-azure-blob-storage.md)、[Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、[Azure Data Lake Storage Gen2 ](connector-azure-data-lake-storage.md)、[Azure SQL Database](connector-azure-sql-database.md)，以及 [Azure SQL 資料倉儲](connector-azure-sql-data-warehouse.md)。
- [網路活動](control-flow-web-activity.md).

## <a name="generate-managed-identity"></a>生成託管標識

資料工廠的託管標識生成如下：

- 通過**Azure 門戶或 PowerShell**創建資料工廠時，託管標識將始終自動創建。
- 通過**SDK**創建資料工廠時，僅當在工廠物件中指定"標識 = 新工廠標識（））"以進行創建時，才會創建託管標識。 請參閱 [.NET 快速入門 - 建立資料處理站](quickstart-create-data-factory-dot-net.md#create-a-data-factory)中的範例。
- 通過**REST API**創建資料工廠時，僅當在請求正文中指定"標識"部分時，才會創建託管標識。 請參閱 [REST 快速入門 - 建立資料處理站](quickstart-create-data-factory-rest-api.md#create-a-data-factory)中的範例。

如果您發現資料工廠在[檢索託管標識](#retrieve-managed-identity)指令後沒有關聯的託管標識，則可以通過以程式設計方式更新資料工廠來顯式生成一個標識：

- [使用 PowerShell 生成託管標識](#generate-managed-identity-using-powershell)
- [使用 REST API 生成託管標識](#generate-managed-identity-using-rest-api)
- [使用 Azure 資源管理器範本生成託管標識](#generate-managed-identity-using-an-azure-resource-manager-template)
- [使用 SDK 生成託管標識](#generate-managed-identity-using-sdk)

>[!NOTE]
>- 無法修改託管標識。 更新已具有託管標識的資料工廠不會產生任何影響，託管標識保持不變。
>- 如果更新已具有託管標識的資料工廠而不在工廠物件中指定"標識"參數，或者未在 REST 請求正文中指定"標識"部分，則將出現錯誤。
>- 刪除資料工廠時，關聯的託管標識將隨之刪除。

### <a name="generate-managed-identity-using-powershell"></a>使用 PowerShell 生成託管標識

再次調用**Set-AzDataFactoryV2**命令，然後看到新生成的"標識"欄位：

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

### <a name="generate-managed-identity-using-rest-api"></a>使用 REST API 生成託管標識

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

**回應**：託管標識將自動創建，並且相應地填充"標識"部分。

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

### <a name="generate-managed-identity-using-an-azure-resource-manager-template"></a>使用 Azure 資源管理器範本生成託管標識

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

### <a name="generate-managed-identity-using-sdk"></a>使用 SDK 生成託管標識

以 Identity=new FactoryIdentity() 呼叫資料處理站 create_or_update 函式。 使用 .NET 的範例程式碼：

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-managed-identity"></a>檢索託管標識

可以從 Azure 門戶或以程式設計方式檢索託管標識。 下列各節會顯示一些範例。

>[!TIP]
> 如果看不到託管標識，請通過更新工廠[生成託管標識](#generate-managed-identity)。

### <a name="retrieve-managed-identity-using-azure-portal"></a>使用 Azure 門戶檢索託管標識

可以從 Azure 門戶找到託管標識資訊 ->資料工廠 -> 屬性。

- 託管標識物件識別碼
- 託管標識租戶
- 託管標識應用程式 ID

當您創建支援託管標識身份驗證的連結服務（如 Azure Blob、Azure 資料湖存儲、Azure 金鑰保存庫等）時，也會顯示託管標識資訊。

授予許可權時，請使用物件識別碼 或資料工廠名稱（作為託管標識名稱）來查找此標識。

### <a name="retrieve-managed-identity-using-powershell"></a>使用 PowerShell 檢索託管標識

當您獲取特定資料工廠時，將返回託管標識主體 ID 和租戶 ID，如下所示。 使用**主體 Id**授予存取權限：

```powershell
PS C:\WINDOWS\system32> (Get-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

可以通過複製上面的主體 ID，然後在 Azure 活動目錄命令下方以主體 ID 作為參數來獲取應用程式 ID。

```powershell
PS C:\WINDOWS\system32> Get-AzADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="next-steps"></a>後續步驟
請參閱以下主題，這些主題介紹何時以及如何使用資料工廠託管標識：

- [在 Azure Key Vault 中儲存認證](store-credentials-in-key-vault.md)
- [使用 Azure 資源的受控識別驗證，從 Azure Data Lake Store 來回複製資料](connector-azure-data-lake-store.md)

有關 Azure 資源的託管標識的更多背景，請參閱[Azure 資源管理標識，](/azure/active-directory/managed-identities-azure-resources/overview)資料工廠託管標識基於這些背景。 

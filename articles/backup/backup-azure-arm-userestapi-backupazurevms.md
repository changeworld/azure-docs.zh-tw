---
title: 使用 REST API 備份 Azure VM
description: 在本文中，瞭解如何使用 REST API 配置、啟動和管理 Azure VM 備份的備份操作。
ms.topic: conceptual
ms.date: 08/03/2018
ms.assetid: b80b3a41-87bf-49ca-8ef2-68e43c04c1a3
ms.openlocfilehash: 4789ef1e0e09df521f8cab539d972e9e669e0a58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248160"
---
# <a name="back-up-an-azure-vm-using-azure-backup-via-rest-api"></a>透過 REST API 使用 Azure 備份來備份 Azure VM

本文將說明如何透過 REST API，使用 Azure 備份來管理 Azure VM 的備份。 首次為以前未受保護的 Azure VM 配置保護，為受保護的 Azure VM 觸發按需備份，並通過 REST API 修改備份 VM 的備份屬性，如此處所述。

請參閱[建立保存庫](backup-azure-arm-userestapi-createorupdatevault.md)和[建立原則](backup-azure-arm-userestapi-createorupdatepolicy.md) REST API 教學課程來建立新的保存庫和原則。

假設您想要使用預設原則 (名為 "DefaultPolicy")，將 "testRG" 資源群組下方的 VM "testVM" 保護到復原服務保存庫 "testVault" (存在於資源群組 "testVaultRG" 內)。

## <a name="configure-backup-for-an-unprotected-azure-vm-using-rest-api"></a>使用 REST API 為未受保護的 Azure VM 設定備份

### <a name="discover-unprotected-azure-vms"></a>探索未受保護的 Azure VM

首先，保存庫應該能夠識別 Azure VM。 這會使用[重新整理作業](https://docs.microsoft.com/rest/api/backup/protectioncontainers/refresh) \(英文\) 來觸發。 此為非同步的 *POST* 作業，以確定保存庫會取得目前訂用帳戶中所有受保護 VM 的最新清單，並「快取」它們。 一旦「快取」VM 之後，復原服務將能存取該 VM 並保護它。

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01
```

POST URI 具有 `{subscriptionId}`、`{vaultName}`、`{vaultresourceGroupName}`、`{fabricName}` 參數。 `{fabricName}` 為 "Azure"。 根據我們的範例，`{vaultName}` 為 "testVault" 且 `{vaultresourceGroupName}` 為 "testVaultRG"。 因為已在 URI 中指定所有必要參數，所以不需要個別的要求本文。

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/refreshContainers?api-version=2016-12-01
```

#### <a name="responses"></a>回應

「重新整理」作業為[非同步作業](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations)。 這表示此作業會建立另一項需要個別追蹤的作業。

它會傳回兩個回應：在建立另一項作業時傳回 202 (已接受)，然後在該作業完成時傳回 200 (確定)。

|名稱  |類型  |描述  |
|---------|---------|---------|
|204 沒有內容     |         |  確定，但不會傳回任何內容      |
|202 已接受     |         |     已接受    |

##### <a name="example-responses"></a>範例回應

一旦提交 *POST* 要求之後，就會傳回 202 (已接受) 回應。

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
X-Content-Type-Options: nosniff
x-ms-request-id: 43cf550d-e463-421c-8922-37e4766db27d
x-ms-client-request-id: 4910609f-bb9b-4c23-8527-eb6fa2d3253f; 4910609f-bb9b-4c23-8527-eb6fa2d3253f
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 43cf550d-e463-421c-8922-37e4766db27d
x-ms-routing-request-id: SOUTHINDIA:20180521T105701Z:43cf550d-e463-421c-8922-37e4766db27d
Cache-Control: no-cache
Date: Mon, 21 May 2018 10:57:00 GMT
Location: https://management.azure.com/subscriptions//00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/operationResults/aad204aa-a5cf-4be2-a7db-a224819e5890?api-version=2019-05-13
X-Powered-By: ASP.NET
```

使用 "Location" 標頭搭配簡單的 *GET* 命令，來追蹤所產生的作業。

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/operationResults/aad204aa-a5cf-4be2-a7db-a224819e5890?api-version=2019-05-13
```

探索過所有 Azure VM 之後，GET 命令就會傳回 204 (沒有內容) 回應。 保存庫現在能夠探索訂用帳戶內的任何 VM。

```http
HTTP/1.1 204 NoContent
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: cf6cd73b-9189-4942-a61d-878fcf76b1c1
x-ms-client-request-id: 25bb6345-f9fc-4406-be1a-dc6db0eefafe; 25bb6345-f9fc-4406-be1a-dc6db0eefafe
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14997
x-ms-correlation-request-id: cf6cd73b-9189-4942-a61d-878fcf76b1c1
x-ms-routing-request-id: SOUTHINDIA:20180521T105825Z:cf6cd73b-9189-4942-a61d-878fcf76b1c1
Cache-Control: no-cache
Date: Mon, 21 May 2018 10:58:25 GMT
X-Powered-By: ASP.NET
```

### <a name="selecting-the-relevant-azure-vm"></a>選取相關的 Azure VM

 您可以藉由在訂用帳戶下方[列出所有可保護的項目](https://docs.microsoft.com/rest/api/backup/backupprotectableitems/list) \(英文\) 來確認「快取」已完成，並在回應中找出所需的 VM。 [此操作的回應](#example-responses-1)還提供有關恢復服務如何標識 VM 的資訊。  一旦您熟悉此模式之後，就可略過此步驟，並直接前往[啟用保護](#enabling-protection-for-the-azure-vm)。

這項作業為 *GET* 作業。

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter=backupManagementType eq 'AzureIaasVM'
```

*GET* URI 具備所有必要參數。 不需任何額外的要求本文。

#### <a name="responses"></a><a name="responses-1"></a>回應

|名稱  |類型  |描述  |
|---------|---------|---------|
|200 確定     | [WorkloadProtectableItemResourceList](https://docs.microsoft.com/rest/api/backup/backupprotectableitems/list#workloadprotectableitemresourcelist)        |       [確定] |

#### <a name="example-responses"></a><a name="example-responses-1"></a>範例回應

一旦提交 *GET* 要求之後，就會傳回 200 (確定) 回應。

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: 7c2cf56a-e6be-4345-96df-c27ed849fe36
x-ms-client-request-id: 40c8601a-c217-4c68-87da-01db8dac93dd; 40c8601a-c217-4c68-87da-01db8dac93dd
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14979
x-ms-correlation-request-id: 7c2cf56a-e6be-4345-96df-c27ed849fe36
x-ms-routing-request-id: SOUTHINDIA:20180521T071408Z:7c2cf56a-e6be-4345-96df-c27ed849fe36
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:14:08 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainerv2;testRG;testVM/protectableItems/vm;iaasvmcontainerv2;testRG;testVM",
      "name": "iaasvmcontainerv2;testRG;testVM",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
      "properties": {
        "virtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
        "virtualMachineVersion": "Compute",
        "resourceGroup": "testRG",
        "backupManagementType": "AzureIaasVM",
        "protectableItemType": "Microsoft.Compute/virtualMachines",
        "friendlyName": "testVM",
        "protectionState": "NotProtected"
      }
    },……………..

```

> [!TIP]
> *GET* 回應中針對「頁面」的值數字僅限於 200。 使用 'nextLink' 欄位來取得下一組回應的 URL。

回應包含所有未受保護的 Azure VM 清單，每個 `{value}` 均包含 Azure 復原服務設定備份所需的所有資訊。 若要設定備份，請注意 `{properties}` 區段中的 `{name}` 欄位與 `{virtualMachineId}` 欄位。 從這些欄位值建構兩個變數，如下所述。

- containerName = "iaasvmcontainer;"+`{name}`
- protectedItemName = "vm;"+ `{name}`
- `{virtualMachineId}` 稍後會在[要求本文](#example-request-body)中使用

在此範例中，上述值會轉譯為：

- containerName = "iaasvmcontainer;iaasvmcontainerv2;testRG;testVM"
- protectedItemName = "vm;iaasvmcontainerv2;testRG;testVM"

### <a name="enabling-protection-for-the-azure-vm"></a>啟用 Azure VM 的保護

在「快取」並「識別出」相關的 VM 之後，選取要保護的原則。 若要深入了解保存庫中的現有原則，請參閱[清單原則 API](https://docs.microsoft.com/rest/api/backup/backuppolicies/list) \(英文\)。 然後藉由參考原則名稱來選取[相關的原則](/rest/api/backup/protectionpolicies/get) \(英文\)。 若要建立原則，請參閱[建立原則教學課程](backup-azure-arm-userestapi-createorupdatepolicy.md)。 已在下列範例中選取 "DefaultPolicy"。

啟用保護是建立「受保護的項目」的非同步 *PUT* 作業。

```http
https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

`{containerName}` 和 `{protectedItemName}` 如上述所建構的。 `{fabricName}` 為 "Azure"。 針對我們的範例，這會轉譯為：

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2019-05-13
```

#### <a name="create-the-request-body"></a>建立要求本文

若要建立受保護的項目，以下是要求本文的元件。

|名稱  |類型  |描述  |
|---------|---------|---------|
|properties     | AzureIaaSVMProtectedItem        |ProtectedItem 資源屬性         |

如需要求本文的完整定義清單及其他詳細資訊，請參閱[建立受保護的項目 REST API 文件](https://docs.microsoft.com/rest/api/backup/protecteditems/createorupdate#request-body) \(英文\)。

##### <a name="example-request-body"></a>要求本文範例

下列要求本文會定義建立受保護的項目所需的屬性。

```json
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy"
  }
}
```

`{sourceResourceId}` 是上述來自[清單可保護項目之回應](#example-responses-1)的 `{virtualMachineId}`。

#### <a name="responses"></a>回應

受保護項目的建立是[非同步作業](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations)。 這表示此作業會建立另一項需要個別追蹤的作業。

它會傳回兩個回應：在建立另一項作業時傳回 202 (已接受)，然後在該作業完成時傳回 200 (確定)。

|名稱  |類型  |描述  |
|---------|---------|---------|
|200 確定     |    [ProtectedItemResource](https://docs.microsoft.com/rest/api/backup/protecteditemoperationresults/get#protecteditemresource)     |  [確定]       |
|202 已接受     |         |     已接受    |

##### <a name="example-responses"></a>範例回應

一旦提交 *PUT* 要求以建立或更新受保護的項目之後，初始回應會是 202 (已接受) 並具備位置標頭或 Azure-async-header。

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
X-Content-Type-Options: nosniff
x-ms-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-client-request-id: e1f94eef-9b2d-45c4-85b8-151e12b07d03; e1f94eef-9b2d-45c4-85b8-151e12b07d03
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-routing-request-id: SOUTHINDIA:20180521T073907Z:db785be0-bb20-4598-bc9f-70c9428b170b
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:39:06 GMT
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
X-Powered-By: ASP.NET
```

然後，使用位置標頭或 Azure-AsyncOperation 標頭搭配簡單的 *GET* 命令，來追蹤所產生的作業。

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
```

當作業完成時，它會在回應本文中傳回 200 (確定) 以及受保護的項目內容。

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM",
  "name": "VM;testRG;testVM",
  "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems",
  "properties": {
    "friendlyName": "testVM",
    "virtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "protectionStatus": "Healthy",
    "protectionState": "IRPending",
    "healthStatus": "Passed",
    "lastBackupStatus": "",
    "lastBackupTime": "2001-01-01T00:00:00Z",
    "protectedItemDataId": "17592691116891",
    "extendedInfo": {
      "recoveryPointCount": 0,
      "policyInconsistent": false
    },
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "backupManagementType": "AzureIaasVM",
    "workloadType": "VM",
    "containerName": "iaasvmcontainerv2;testRG;testVM",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy",
    "policyName": "DefaultPolicy"
  }
}
```

這樣即可確認會針對 VM 啟用該保護，並根據原則排程觸發第一個備份。

## <a name="trigger-an-on-demand-backup-for-a-protected-azure-vm"></a>針對受保護的 Azure VM 觸發隨選備份

設定 Azure VM 以進行備份之後，即會根據原則排程進行備份。 您可以等候第一個排定的備份，或隨時觸發隨選備份。 隨選備份的保留期與備份原則的保留期不同，且可指定為特定的日期時間。 如果未指定，即會假設為從觸發隨選備份當日起算的 30 天。

觸發隨選備份為 *POST* 作業。

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

`{containerName}` 和 `{protectedItemName}` 如[上述](#responses-1)所建構的。 `{fabricName}` 為 "Azure"。 針對我們的範例，這會轉譯為：

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM/backup?api-version=2016-12-01
```

### <a name="create-the-request-body"></a>建立要求本文

若要觸發隨選備份，以下是要求本文的元件。

|名稱  |類型  |描述  |
|---------|---------|---------|
|properties     | [IaaSVMBackupRequest](https://docs.microsoft.com/rest/api/backup/backups/trigger#iaasvmbackuprequest)        |BackupRequestResource 屬性         |

如需要求本文的完整定義清單及其他詳細資訊，請參閱[觸發受保護項目的備份 REST API 文件](https://docs.microsoft.com/rest/api/backup/backups/trigger#request-body) \(英文\)。

#### <a name="example-request-body"></a>要求本文範例

下列要求本文會定義觸發受保護項目之備份所需的屬性。 如果未指定保留期，它將會保留 30 天 (從觸發備份作業當日起算)。

```json
{
   "properties": {
    "objectType": "IaasVMBackupRequest",
    "recoveryPointExpiryTimeInUTC": "2018-12-01T02:16:20.3156909Z"
  }
}
```

### <a name="responses"></a>回應

觸發隨選備份為[非同步作業](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations)。 這表示此作業會建立另一項需要個別追蹤的作業。

它會傳回兩個回應：在建立另一項作業時傳回 202 (已接受)，然後在該作業完成時傳回 200 (確定)。

|名稱  |類型  |描述  |
|---------|---------|---------|
|202 已接受     |         |     已接受    |

#### <a name="example-responses"></a><a name="example-responses-3"></a>範例回應

一旦提交 *POST* 要求以進行隨選備份之後，初始回應會是 202 (已接受) 並具備位置標頭或 Azure-async-header。

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testVaultRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/b8daecaa-f8f5-44ed-9f18-491a9e9ba01f?api-version=2019-05-13
X-Content-Type-Options: nosniff
x-ms-request-id: 7885ca75-c7c6-43fb-a38c-c0cc437d8810
x-ms-client-request-id: 7df8e874-1d66-4f81-8e91-da2fe054811d; 7df8e874-1d66-4f81-8e91-da2fe054811d
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 7885ca75-c7c6-43fb-a38c-c0cc437d8810
x-ms-routing-request-id: SOUTHINDIA:20180521T083541Z:7885ca75-c7c6-43fb-a38c-c0cc437d8810
Cache-Control: no-cache
Date: Mon, 21 May 2018 08:35:41 GMT
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testVaultRG;testVM/protectedItems/vm;testRG;testVM/operationResults/b8daecaa-f8f5-44ed-9f18-491a9e9ba01f?api-version=2019-05-13
X-Powered-By: ASP.NET
```

然後，使用位置標頭或 Azure-AsyncOperation 標頭搭配簡單的 *GET* 命令，來追蹤所產生的作業。

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
```

當作業完成之後，它會在回應本文中傳回 200 (確定) 以及結果備份作業的識別碼。

```json
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: a8b13524-2c95-445f-b107-920806f385c1
x-ms-client-request-id: 5a63209d-3708-4e69-a75f-9499f4c8977c; 5a63209d-3708-4e69-a75f-9499f4c8977c
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14995
x-ms-correlation-request-id: a8b13524-2c95-445f-b107-920806f385c1
x-ms-routing-request-id: SOUTHINDIA:20180521T083723Z:a8b13524-2c95-445f-b107-920806f385c1
Cache-Control: no-cache
Date: Mon, 21 May 2018 08:37:22 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "00000000-0000-0000-0000-000000000000",
  "status": "Succeeded",
  "startTime": "2018-05-21T08:35:40.9488967Z",
  "endTime": "2018-05-21T08:35:40.9488967Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "7ddead57-bcb9-4269-ac31-6a1b57588700"
  }
}
```

由於備份作業是長時間執行的作業，因此需要加以追蹤，如[使用 REST API 監視作業文件](backup-azure-arm-userestapi-managejobs.md#tracking-the-job)所述。

## <a name="modify-the-backup-configuration-for-a-protected-azure-vm"></a>修改受保護 Azure VM 的備份設定

### <a name="changing-the-policy-of-protection"></a>變更保護原則

若要變更用來保護 VM 的原則，您可以使用與[啟用保護](#enabling-protection-for-the-azure-vm)相同的格式。 只在[要求本文](#example-request-body)中提供新的原則識別碼並提交要求。 例如：要將 testVM 的策略從"預設策略"更改為"ProdPolicy"，請在請求正文中提供"ProdPolicy"ID。

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/ProdPolicy"
  }
}
```

回應將遵循與[針對啟用保護](#responses-2)所述相同的格式。

### <a name="stop-protection-but-retain-existing-data"></a>停止保護，但保留現有資料

若要在受保護的 VM 上移除保護，但保留已經備份的資料，請在要求本文中移除原則並提交要求。 移除與原則的關聯之後，就不會再觸發備份，而且不會建立任何新的復原點。

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": ""
  }
}
```

回應將遵循與[針對觸發隨選備份](#example-responses-3)所述相同的格式。 您應該追蹤結果作業，如[使用 REST API 監視作業文件](backup-azure-arm-userestapi-managejobs.md#tracking-the-job)所述。

### <a name="stop-protection-and-delete-data"></a>停止保護並刪除資料

若要在受保護的 VM 上移除保護，同時也要刪除備份資料，請執行刪除作業，如[此處](https://docs.microsoft.com/rest/api/backup/protecteditems/delete)所詳述。

停止保護並刪除資料為 *DELETE* 作業。

```http
DELETE https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

`{containerName}` 和 `{protectedItemName}` 如[上述](#responses-1)所建構的。 `{fabricName}` 是 "Azure"。 針對我們的範例，這會轉譯為：

```http
DELETE https://management.azure.com//Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2019-05-13
```

#### <a name="responses"></a><a name="responses-2"></a>回應

*DELETE* 作業為[非同步作業](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations)。 這表示此作業會建立另一項需要個別追蹤的作業。

它會傳回兩個回應：在建立另一項作業時傳回 202 (已接受)，然後在該作業完成時傳回 204 (NoContent)。

|名稱  |類型  |描述  |
|---------|---------|---------|
|204 NoContent     |         |  NoContent       |
|202 已接受     |         |     已接受    |

> [!IMPORTANT]
> 為了防止意外刪除方案，有一個[虛刪除功能可用於](use-restapi-update-vault-properties.md#soft-delete-state)恢復服務保存庫。 如果保存庫的虛刪除狀態設置為啟用，則刪除操作不會立即刪除資料。 它將保存14天，然後永久清除。 在這 14 天的期間，客戶不收取存儲費用。 要撤銷刪除操作，請參閱[撤銷刪除部分](#undo-the-stop-protection-and-delete-data)。

### <a name="undo-the-stop-protection-and-delete-data"></a>撤銷停止保護並刪除資料

撤銷意外刪除類似于創建備份項。 撤銷刪除後，將保留該專案，但不會觸發將來的備份。

撤銷刪除是*PUT*操作，與[更改策略](#changing-the-policy-of-protection)和/或[啟用保護](#enabling-protection-for-the-azure-vm)非常相似。 只需提供在[請求正文](#example-request-body)中用變數*rehydrate*來撤銷刪除的意圖並提交請求。 例如：要撤銷 testVM 的刪除，應使用以下請求正文。

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "protectionState": "ProtectionStopped",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "isRehydrate": true
  }
}
```

回應將遵循與[針對觸發隨選備份](#example-responses-3)所述相同的格式。 您應該追蹤結果作業，如[使用 REST API 監視作業文件](backup-azure-arm-userestapi-managejobs.md#tracking-the-job)所述。

## <a name="next-steps"></a>後續步驟

[從 Azure 虛擬機器備份還原資料](backup-azure-arm-userestapi-restoreazurevms.md)。

如需 Azure 備份 REST API 的詳細資訊，請參閱下列文件：

- [Azure 復原服務提供者 REST API](/rest/api/recoveryservices/)
- [Get started with Azure REST API](/rest/api/azure/) (開始使用 Azure REST API)

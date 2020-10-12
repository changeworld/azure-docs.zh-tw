---
title: 使用 REST API 來還原 Azure VM
description: 在本文中，您將瞭解如何使用 REST API 來管理 Azure 虛擬機器備份的還原作業。
ms.topic: conceptual
ms.date: 09/12/2018
ms.assetid: b8487516-7ac5-4435-9680-674d9ecf5642
ms.openlocfilehash: ad60436d82ccc8049a4509ba5bf1e244bee150ea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89506672"
---
# <a name="restore-azure-virtual-machines-using-rest-api"></a>使用 REST API 還原 Azure 虛擬機器

一旦使用 Azure 備份完成 Azure 虛擬機器的備份之後，您就可以從相同的備份複本還原整個 Azure 虛擬機器或磁片或檔案。 本文說明如何使用 REST API 還原 Azure VM 或磁碟。

對於任何還原作業，您必須先識別相關的復原點。

## <a name="select-recovery-point"></a>選取復原點

使用[列出復原點 REST API](/rest/api/backup/recoverypoints/list)，可以列出備份項目可用的復原點。 它是具有所有相關值的簡單 *GET* 作業。

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints?api-version=2019-05-13
```

`{containerName}` 和 `{protectedItemName}` 的建構方式在[這裡](backup-azure-arm-userestapi-backupazurevms.md#example-responses-to-get-operation)。 `{fabricName}` 是 "Azure"。

*GET* URI 具備所有必要參數。 不需要額外的要求主體。

### <a name="responses"></a>回應

|名稱  |類型  |描述  |
|---------|---------|---------|
|200 確定     |   [RecoveryPointResourceList](/rest/api/backup/recoverypoints/list#recoverypointresourcelist)      |       確定  |

#### <a name="example-response"></a>範例回應

一旦提交 GET** URI，就會傳回 200 (確定) 回應。

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: 03453538-2f8d-46de-8374-143ccdf60f40
x-ms-client-request-id: c48f4436-ce3f-42da-b537-12710d4d1a24; c48f4436-ce3f-42da-b537-12710d4d1a24
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14998
x-ms-correlation-request-id: 03453538-2f8d-46de-8374-143ccdf60f40
x-ms-routing-request-id: SOUTHINDIA:20180604T071851Z:03453538-2f8d-46de-8374-143ccdf60f40
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 07:18:51 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/VM;testRG;testVM/recoveryPoints/20982486783671",
      "name": "20982486783671",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "IaasVMRecoveryPoint",
        "recoveryPointType": "AppConsistent",
        "recoveryPointTime": "2018-06-04T06:06:00.5121087Z",
        "recoveryPointAdditionalInfo": "",
        "sourceVMStorageType": "NormalStorage",
        "isSourceVMEncrypted": false,
        "isInstantIlrSessionActive": false,
        "recoveryPointTierDetails": [
          {
            "type": 1,
            "status": 1
          },
          {
            "type": 2,
            "status": 1
          }
        ],
        "isManagedVirtualMachine": true,
        "virtualMachineSize": "Standard_A1_v2",
        "originalStorageAccountOption": false
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/VM;testRG;testVM/recoveryPoints/23358112038108",
      "name": "23358112038108",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "IaasVMRecoveryPoint",
        "recoveryPointType": "CrashConsistent",
        "recoveryPointTime": "2018-06-03T06:20:56.3043878Z",
        "recoveryPointAdditionalInfo": "",
        "sourceVMStorageType": "NormalStorage",
        "isSourceVMEncrypted": false,
        "isInstantIlrSessionActive": false,
        "recoveryPointTierDetails": [
          {
            "type": 1,
            "status": 1
          },
          {
            "type": 2,
            "status": 1
          }
        ],
        "isManagedVirtualMachine": true,
        "virtualMachineSize": "Standard_A1_v2",
        "originalStorageAccountOption": false
      }
    },
......
```

使用上面回應中的 `{name}` 欄位可識別復原點。

## <a name="restore-operations"></a>還原作業

選取相關的 [還原點](#select-recovery-point)之後，請繼續觸發還原作業。

***備份專案上的所有還原作業都是使用相同的 *POST* API 來執行。只有要求主體會隨著還原案例而變更。***

> [!IMPORTANT]
> [這裡](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-options)提及各種還原選項及其相依性的所有詳細資料。 請先參閱，再繼續觸發這些作業。

觸發還原作業是 *POST* 要求。 若要深入瞭解 API，請參閱「 [觸發程式還原」 REST API](/rest/api/backup/restores/trigger)。

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/restore?api-version=2019-05-13
```

`{containerName}` 和 `{protectedItemName}` 的建構方式在[這裡](backup-azure-arm-userestapi-backupazurevms.md#example-responses-to-get-operation)。 `{fabricName}` 是 "Azure"，而 `{recoveryPointId}` 是[上述](#example-response)復原點的 `{name}` 欄位。

取得復原點之後，我們必須針對相關的還原案例來建立要求主體。 下列各節概述每個案例的要求主體。

- [復原磁碟](#restore-disks)
- [更換磁片](#replace-disks-in-a-backed-up-virtual-machine)
- [還原為新的虛擬機器](#restore-as-another-virtual-machine)

### <a name="restore-response"></a>還原回應

觸發任何還原作業是 [非同步作業](../azure-resource-manager/management/async-operations.md)。 這表示此作業會建立另一項需要個別追蹤的作業。

它會傳回兩個回應：在建立另一項作業時傳回 202 (已接受)，然後在該作業完成時傳回 200 (確定)。

|名稱  |類型  |描述  |
|---------|---------|---------|
|202 已接受     |         |     已接受    |

#### <a name="example-responses"></a>範例回應

一旦提交 POST** URI 以供觸發還原磁碟，初始回應為 202 (已接受) 以及位置標頭或 Azure-async-header。

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2019-05-13
X-Content-Type-Options: nosniff
x-ms-request-id: 893fe372-8d6c-4c56-b589-45a95eeef95f
x-ms-client-request-id: a15ce064-25bd-4ac6-87e5-e3bc6ec65c0b; a15ce064-25bd-4ac6-87e5-e3bc6ec65c0b
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1198
x-ms-correlation-request-id: 893fe372-8d6c-4c56-b589-45a95eeef95f
x-ms-routing-request-id: SOUTHINDIA:20180604T130003Z:893fe372-8d6c-4c56-b589-45a95eeef95f
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 13:00:03 GMT
Location: https://management.azure.com/subscriptions//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2019-05-13
X-Powered-By: ASP.NET
```

然後，使用位置標頭或 Azure-AsyncOperation 標頭搭配簡單的 *GET* 命令，來追蹤所產生的作業。

```http
GET https://management.azure.com/subscriptions//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2019-05-13
```

當作業完成時，它會傳回 200 (確定) 以及回應本文中所產生還原作業的識別碼。

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: ea2a8011-eb83-4a4b-9ed2-e694070a966a
x-ms-client-request-id: a7f3a144-ed80-41ee-bffe-ae6a90c35a2f; a7f3a144-ed80-41ee-bffe-ae6a90c35a2f
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14973
x-ms-correlation-request-id: ea2a8011-eb83-4a4b-9ed2-e694070a966a
x-ms-routing-request-id: SOUTHINDIA:20180604T130917Z:ea2a8011-eb83-4a4b-9ed2-e694070a966a
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 13:09:17 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "781a0f18-e250-4d73-b059-5e9ffed4069e",
  "name": "781a0f18-e250-4d73-b059-5e9ffed4069e",
  "status": "Succeeded",
  "startTime": "2018-06-04T13:00:03.8068176Z",
  "endTime": "2018-06-04T13:00:03.8068176Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "3021262a-fb3a-4538-9b37-e3e97a386093"
  }
}
```

因為還原作業是長時間執行的作業，所以應該依照 [使用 REST API 檔監視作業](backup-azure-arm-userestapi-managejobs.md#tracking-the-job)中的說明來追蹤。

### <a name="restore-disks"></a>還原磁碟

如果需要自訂從備份資料建立 VM，您可以直接將磁片還原至所選的儲存體帳戶，並根據其需求從這些磁片建立 VM。 儲存體帳戶應位於與復原服務保存庫相同的區域中，且不應該是區域冗余。 磁片以及備份 VM ( 「vmconfig.json」 ) 的設定將會儲存在指定的儲存體帳戶中。 [如上所述，以下](#restore-operations)提供復原磁碟的相關要求主體。

#### <a name="create-request-body"></a>建立要求本文

若要觸發從 Azure VM 備份還原磁碟，以下是要求本文的元件。

|名稱  |類型  |描述  |
|---------|---------|---------|
|properties     | [IaaSVMRestoreRequest](/rest/api/backup/restores/trigger#iaasvmrestorerequest)        |    RestoreRequestResourceProperties     |

如需要求本文的完整定義清單及其他詳細資訊，請參閱[觸發還原 REST API 文件](/rest/api/backup/restores/trigger#request-body)。

##### <a name="example-request"></a>範例要求

下列要求本文會定義觸發磁碟還原所需的屬性。

```json
{
  "properties": {
    "objectType": "IaasVMRestoreRequest",
    "recoveryPointId": "20982486783671",
    "recoveryType": "RestoreDisks",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testAccount",
    "region": "westus",
    "createNewCloudService": false,
    "originalStorageAccountOption": false,
    "encryptionDetails": {
      "encryptionEnabled": false
    }
  }
}
```

### <a name="restore-disks-selectively"></a>選擇性地復原磁碟

如果您 [選擇備份磁片](backup-azure-arm-userestapi-backupazurevms.md#excluding-disks-in-azure-vm-backup)，則會在 [復原點摘要](#select-recovery-point) 和 [詳細回應](https://docs.microsoft.com/rest/api/backup/recoverypoints/get)中提供目前的備份磁片清單。 您也可以選擇性地復原磁碟，也可以在 [這裡](selective-disk-backup-restore.md#selective-disk-restore)提供更多詳細資料。 若要在備份的磁片清單中選擇性地復原磁碟，請從復原點回應中找出磁片的 LUN，然後將 **restoreDiskLunList** 屬性新增至 [上面的要求主體](#example-request) ，如下所示。

```json
{
    "properties": {
        "objectType": "IaasVMRestoreRequest",
        "recoveryPointId": "20982486783671",
        "recoveryType": "RestoreDisks",
        "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
        "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testAccount",
        "region": "westus",
        "createNewCloudService": false,
        "originalStorageAccountOption": false,
        "encryptionDetails": {
          "encryptionEnabled": false
        },
        "restoreDiskLunList" : [0]
    }
}

```

當您追蹤 [上述](#responses)的回應，而且長時間執行的作業完成時，備份虛擬機器的磁片和設定 ( 「VMConfig.js開啟」 ) 將會出現在指定的儲存體帳戶中。

### <a name="replace-disks-in-a-backed-up-virtual-machine"></a>更換已備份之虛擬機器中的磁片

雖然復原磁碟會從復原點建立磁片，但更換磁片會將備份 VM 的目前磁片取代為復原點的磁片。 [如上所述，以下](#restore-operations)提供用於取代磁片的相關要求主體。

#### <a name="create-request-body"></a>建立要求本文

若要從 Azure VM 備份觸發磁片更換，以下是要求本文的元件。

|名稱  |類型  |描述  |
|---------|---------|---------|
|properties     | [IaaSVMRestoreRequest](/rest/api/backup/restores/trigger#iaasvmrestorerequest)        |    RestoreRequestResourceProperties     |

如需要求本文的完整定義清單及其他詳細資訊，請參閱[觸發還原 REST API 文件](/rest/api/backup/restores/trigger#request-body)。

#### <a name="example-request"></a>範例要求

下列要求本文會定義觸發磁碟還原所需的屬性。

```json
{
    "properties": {
        "objectType": "IaasVMRestoreRequest",
        "recoveryPointId": "20982486783671",
        "recoveryType": "OriginalLocation",
        "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
        "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testAccount",  
        "region": "westus",
        "createNewCloudService": false,
        "originalStorageAccountOption": false,
        "affinityGroup": "",
        "diskEncryptionSetId": null,
        "subnetId": null,
        "targetDomainNameId": null,
        "targetResourceGroupId": null,
        "targetVirtualMachineId": null,
        "virtualNetworkId": null
     }
}

```

### <a name="restore-as-another-virtual-machine"></a>還原為另一部虛擬機器

[如上所述，下列](#restore-operations)要求本文會定義觸發虛擬機器還原所需的屬性。

```json
{
  "parameters": {
        "subscriptionId": "00000000-0000-0000-0000-000000000000",
        "resourceGroupName": "testVaultRG",
        "vaultName": "testVault",
        "fabricName": "Azure",
        "containerName": "IaasVMContainer;iaasvmcontainerv2;testRG;testVM",
        "protectedItemName": "VM;iaasvmcontainerv2;testRG;testVM",
        "recoveryPointId": "348916168024334",
        "api-version": "2019-05-13",
      "parameters": {
        "properties": {
          "objectType":  "IaasVMRestoreRequest",
          "recoveryPointId": "348916168024334",
          "recoveryType": "AlternateLocation",
          "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
          "targetVirtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Compute/virtualmachines/targetVM",
          "targetResourceGroupId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG",
          "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testingAccount",
          "virtualNetworkId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Network/virtualNetworks/testNet",
          "subnetId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Network/virtualNetworks/testNet/subnets/default",
          "region": "westus",
          "createNewCloudService": false,
          "originalStorageAccountOption": false,
          "encryptionDetails": {
            "encryptionEnabled": false
          }
        }
      }
    }
}
```

處理回應的方式應該如同[上述的還原磁碟](#responses)。

## <a name="next-steps"></a>後續步驟

如需 Azure 備份 REST API 的詳細資訊，請參閱下列文件：

- [Azure 復原服務提供者 REST API](/rest/api/recoveryservices/)
- [Get started with Azure REST API](/rest/api/azure/) (開始使用 Azure REST API)

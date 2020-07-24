---
title: 使用 REST API 備份 Azure 檔案共用
description: 瞭解如何使用 REST API 來備份復原服務保存庫中的 Azure 檔案共用
ms.topic: conceptual
ms.date: 02/16/2020
ms.openlocfilehash: 7059dbae9d448b710880f1f9d72b843a6d77d98b
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87055023"
---
# <a name="backup-azure-file-share-using-azure-backup-via-rest-api"></a>使用 Azure 備份透過 Rest API 備份 Azure 檔案共用

本文說明如何使用 Azure 備份透過 REST API 來備份 Azure 檔案共用。

本文假設您已建立復原服務保存庫和原則，以設定檔案共用的備份。 如果您尚未這麼做，請參閱[建立保存庫](./backup-azure-arm-userestapi-createorupdatevault.md)和[建立原則](./backup-azure-arm-userestapi-createorupdatepolicy.md)REST API 教學課程，以建立新的保存庫和原則。

在本文中，我們將使用下列資源：

- **RecoveryServicesVault**： *azurefilesvault*

- **原則：** *schedule1*

- **資源群組**： *azurefiles*

- **儲存體帳戶**： *testvault2*

- 檔案**共用**： *testshare*

## <a name="configure-backup-for-an-unprotected-azure-file-share-using-rest-api"></a>使用 REST API 設定未受保護的 Azure 檔案共用的備份

### <a name="discover-storage-accounts-with-unprotected-azure-file-shares"></a>探索具有未受保護之 Azure 檔案共用的儲存體帳戶

保存庫必須探索訂用帳戶中具有可備份至復原服務保存庫之檔案共用的所有 Azure 儲存體帳戶。 這會使用[重新整理作業](/rest/api/backup/protectioncontainers/refresh) \(英文\) 來觸發。 這是一項非同步*POST*作業，可確保保存庫取得目前訂用帳戶中所有未受保護 Azure 檔案共用的最新清單，並加以「快取」。 檔案共用「快取」之後，復原服務就可以存取檔案共用並加以保護。

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01&$filter={$filter}
```

POST URI 有 `{subscriptionId}` 、、 `{vaultName}` `{vaultresourceGroupName}` 和 `{fabricName}` 參數。 在我們的範例中，不同參數的值如下所示：

- `{fabricName}`為*Azure*

- `{vaultName}`為*azurefilesvault*

- `{vaultresourceGroupName}`為*azurefiles*

- $filter = backupManagementType eq ' AzureStorage '

由於所有必要的參數都是在 URI 中提供，因此不需要個別的要求主體。

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/refreshContainers?api-version=2016-12-01&$filter=backupManagementType eq 'AzureStorage'
```

#### <a name="responses"></a>回應

「重新整理」作業為[非同步作業](../azure-resource-manager/management/async-operations.md)。 這表示此作業會建立另一項需要個別追蹤的作業。

它會傳回兩個回應：當另一個作業已建立時，202（已接受），而當該作業完成時，則傳回200（確定）。

##### <a name="example-responses"></a>範例回應

一旦提交 *POST* 要求之後，就會傳回 202 (已接受) 回應。

```http
HTTP/1.1 202 Accepted
'Pragma': 'no-cache'
'Expires': '-1'
'Location': ‘https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/ResourceGroups
/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/operationResults/
cca47745-12d2-42f9-b3a4-75335f18fdf6?api-version=2016-12-01’
'Retry-After': '60'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'x-ms-client-request-id': ‘3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61’
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-reads': '11996'
'x-ms-correlation-request-id': '6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'x-ms-routing-request-id': CENTRALUSEUAP:20200203T091326Z:6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'Date': 'Mon, 03 Feb 2020 09:13:25 GMT'
```

使用 "Location" 標頭搭配簡單的 *GET* 命令，來追蹤所產生的作業。

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/operationResults/cca47745-12d2-42f9-b3a4-75335f18fdf6?api-version=2016-12-01
```

一旦探索到所有 Azure 儲存體帳戶，GET 命令就會傳回200（沒有內容）回應。 保存庫現在能夠探索具有可在訂用帳戶內備份之檔案共用的任何儲存體帳戶。

```http
HTTP/1.1 200 NoContent
Cache-Control  : no-cache
Pragma   : no-cache
X-Content-Type-Options  : nosniff
x-ms-request-id    : d9bdb266-8349-4dbd-9688-de52f07648b2
x-ms-client-request-id  : 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security  : max-age=31536000; includeSubDomains
X-Powered-By    : ASP.NET
x-ms-ratelimit-remaining-subscription-reads: 11933
x-ms-correlation-request-id   : d9bdb266-8349-4dbd-9688-de52f07648b2
x-ms-routing-request-id  : CENTRALUSEUAP:20200127T105304Z:d9bdb266-8349-4dbd-9688-de52f07648b2
Date   : Mon, 27 Jan 2020 10:53:04 GMT
```

### <a name="get-list-of-storage-accounts-that-can-be-protected-with-recovery-services-vault"></a>取得可使用復原服務保存庫保護的儲存體帳戶清單

若要確認「快取」已完成，請列出訂用帳戶下所有可保護的儲存體帳戶。 然後，在回應中找出所需的儲存體帳戶。 這會使用[GET ProtectableContainers](/rest/api/backup/protectablecontainers/list)作業來完成。

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectableContainers?api-version=2016-12-01&$filter=backupManagementType eq 'AzureStorage'
```

*GET* URI 具備所有必要參數。 不需任何額外的要求本文。

回應主體的範例：

```json
{

  "value": [

    {

      "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers
 /Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/

protectableContainers/StorageContainer;Storage;AzureFiles;testvault2",

      "name": "StorageContainer;Storage;AzureFiles;testvault2",

      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectableContainers",

      "properties": {

        "friendlyName": "testvault2",

        "backupManagementType": "AzureStorage",

        "protectableContainerType": "StorageContainer",

        "healthStatus": "Healthy",

        "containerId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/
 AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2"

      }

    }

  ]

}
```

由於我們可以在具有易記名稱的回應本文中找到*testvault2*儲存體帳戶，因此上述執行的重新整理作業已成功。 復原服務保存庫現在可以成功探索相同訂用帳戶中具有未受保護檔案共用的儲存體帳戶。

### <a name="register-storage-account-with-recovery-services-vault"></a>向復原服務保存庫註冊儲存體帳戶

只有在您稍早未向保存庫註冊儲存體帳戶時，才需要執行此步驟。 您可以透過[ProtectionContainers-register](/rest/api/backup/protectioncontainers/register)作業註冊保存庫。

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}?api-version=2016-12-01
```

設定 URI 的變數，如下所示：

- {resourceGroupName}- *azurefiles*
- {fabricName}- *Azure*
- {vaultName}- *azurefilesvault*
- {容器名稱}-這是 GET ProtectableContainers 作業回應主體中的 name 屬性。
   在我們的範例中，它是*StorageContainer;容量AzureFiles; testvault2*

>[!NOTE]
> 請一律採用回應的 name 屬性，並在此要求中填入。 不要以硬式編碼或建立容器名稱格式。 如果您建立或硬式編碼，則如果容器名稱格式未來變更，API 呼叫將會失敗。

<br>

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2?api-version=2016-12-01
```

建立要求主體如下所示：

```json
{

 "properties": {


  "containerType": "StorageContainer",


  "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",


  "resourceGroup": "AzureFiles",


  "friendlyName": "testvault2",


  "backupManagementType": "AzureStorage"


 }
```

如需要求本文的完整定義清單及其他詳細資料，請參閱[ProtectionContainers-Register](/rest/api/backup/protectioncontainers/register#azurestoragecontainer)。

這是非同步作業，並會傳回兩個回應：當作業已被接受時，會傳回「202已接受」，而當作業完成時，則會傳回「200 OK」。  若要追蹤作業狀態，請使用 location 標頭來取得作業的最新狀態。

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/operationresults/1a3c8ee7-e0e5-43ed-b8b3-73cc992b6db9?api-version=2016-12-01
```

作業完成時的回應主體範例：

```json
{
    "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/
protectionContainers/StorageContainer;Storage;AzureFiles;testvault2",
    "name": "StorageContainer;Storage;AzureFiles;testvault2",
    "properties": {
        "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
        "protectedItemCount": 0,
        "friendlyName": "testvault2",
        "backupManagementType": "AzureStorage",
        "registrationStatus": "Registered",
        "healthStatus": "Healthy",
        "containerType": "StorageContainer",
        "protectableObjectType": "StorageContainer"
    }
}
```

您可以從回應主體中的*registrationstatus*參數值確認註冊是否成功。 在我們的案例中，它會將狀態顯示為 [已註冊*testvault2*]，因此註冊作業已成功。

### <a name="inquire-all-unprotected-files-shares-under-a-storage-account"></a>在儲存體帳戶下查詢所有未受保護的檔案共用

您可以使用[保護容器-inquire](/rest/api/backup/protectioncontainers/inquire)作業來查詢儲存體帳戶中的可保護專案。 這是非同步作業，而且應該使用 location 標頭來追蹤結果。

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/inquire?api-version=2016-12-01
```

設定上述 URI 的變數，如下所示：

- {vaultName}- *azurefilesvault*
- {fabricName}- *Azure*
- {容器名稱}-在 GET ProtectableContainers 作業的回應主體中，參考 name 屬性。 在我們的範例中，它是*StorageContainer;容量AzureFiles; testvault2*

```http
https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/inquire?api-version=2016-12-01
```

一旦要求成功，它會傳回狀態碼「確定」

```http
Cache-Control : no-cache
Pragma   : no-cache
X-Content-Type-Options: nosniff
x-ms-request-id  : 68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
x-ms-client-request-id  : 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security: max-age=31536000; includeSubDomains
Server  : Microsoft-IIS/10.0
X-Powered-B : ASP.NET
x-ms-ratelimit-remaining-subscription-reads: 11932
x-ms-correlation-request-id  : 68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
x-ms-routing-request-id   : CENTRALUSEUAP:20200127T105305Z:68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
Date  : Mon, 27 Jan 2020 10:53:05 GMT
```

### <a name="select-the-file-share-you-want-to-back-up"></a>選取您想要備份的檔案共用

您可以列出訂用帳戶下所有可保護的專案，並使用[GET backupprotectableItems](/rest/api/backup/backupprotectableitems/list)作業找出所需的檔案共用，以進行備份。

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter={$filter}
```

依照下列方式來建立 URI：

- {vaultName}- *azurefilesvault*
- {$filter}- *backupManagementType eq ' AzureStorage '*

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupProtectableItems?$filter=backupManagementType eq 'AzureStorage'&api-version=2016-12-01
```

範例回應：

```json
Status Code:200

{
    "value": [
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afaccount1/protectableItems/azurefileshare;azurefiles1",
            "name": "azurefileshare;azurefiles1",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afaccount1",
                "parentContainerFriendlyName": "afaccount1",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "azurefiles1",
                "protectionState": "NotProtected"
            }
        },
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afsaccount/protectableItems/azurefileshare;afsresource",
            "name": "azurefileshare;afsresource",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
                "parentContainerFriendlyName": "afsaccount",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "afsresource",
                "protectionState": "NotProtected"
            }
        },
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;testvault2/protectableItems/azurefileshare;testshare",
            "name": "azurefileshare;testshare",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
                "parentContainerFriendlyName": "testvault2",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "testshare",
                "protectionState": "NotProtected"
            }
        }
    ]
}
```

回應會包含所有未受保護的檔案共用清單，並包含 Azure 復原服務用來設定備份所需的所有資訊。

### <a name="enable-backup-for-the-file-share"></a>啟用檔案共用的備份

使用易記名稱「識別」相關的檔案共用之後，請選取要保護的原則。 若要深入瞭解保存庫中的現有原則，請參閱[清單原則 API](/rest/api/backup/backuppolicies/list)。 然後藉由參考原則名稱來選取[相關的原則](/rest/api/backup/protectionpolicies/get) \(英文\)。 若要建立原則，請參閱[建立原則教學課程](./backup-azure-arm-userestapi-createorupdatepolicy.md)。

啟用保護是一個可建立「受保護專案」的非同步*PUT*作業。

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

在 GET backupprotectableitems 作業的回應主體中，使用 ID 屬性來設定**容器**和**protecteditemname**變數。

在我們的範例中，我們想要保護的檔案共用的識別碼是：

```output
"/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;testvault2/protectableItems/azurefileshare;testshare
```

- {容器型}- *storagecontainer; storage; azurefiles; testvault2*
- {protectedItemName}- *azurefileshare; testshare*

或者，您可以參考保護容器的**name**屬性和可保護的專案回應。

>[!NOTE]
>請一律採用回應的 name 屬性，並在此要求中填入。 請勿硬式編碼或建立容器名稱格式或受保護的專案名稱格式。 如果您建立或硬式編碼，則如果容器名稱格式或受保護的專案名稱格式未來變更，API 呼叫將會失敗。

<br>

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare?api-version=2016-12-01
```

建立要求主體：

下列要求本文會定義建立受保護的項目所需的屬性。

```json
{
  "properties": {
    "protectedItemType": "AzureFileShareProtectedItem",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
    "policyId": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupPolicies/schedule1"
  }
}
```

**SourceResourceId**是 GET backupprotectableItems 回應中的**parentcontainerFabricID** 。

範例回應

建立受保護的專案是非同步作業，它會建立另一個需要追蹤的作業。 它會傳回兩個回應：在建立另一個作業時，202（已接受），而當該作業完成時，則會傳回200（確定）。

提交*PUT*要求以進行受保護的專案建立或更新之後，初始回應會是202（已接受）與位置標頭。

```http
HTTP/1.1 202 Accepted
Cache-Control  : no-cache
Pragma  : no-cache
Location : https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare/operationResults/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
Retry-Afte  : 60
Azure-AsyncOperation  : https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare/operationResults/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
X-Content-Type-Options : nosniff
x-ms-request-id : b55527fa-f473-4f09-b169-9cc3a7a39065
x-ms-client-request-id: 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security : max-age=31536000; includeSubDomains
X-Powered-By  : ASP.NET
x-ms-ratelimit-remaining-subscription-writes: 1198
x-ms-correlation-request-id : b55527fa-f473-4f09-b169-9cc3a7a39065
x-ms-routing-request-id  : CENTRALUSEUAP:20200127T105412Z:b55527fa-f473-4f09-b169-9cc3a7a39065
Date : Mon, 27 Jan 2020 10:54:12 GMT
```

然後使用 location 標頭或 AsyncOperation 標頭搭配*GET*命令來追蹤所產生的作業。

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
```

當作業完成時，它會在回應本文中傳回 200 (確定) 以及受保護的項目內容。

範例回應主體：

```json
{
    "id": "c3a52d1d-0853-4211-8141-477c65740264",
    "name": "c3a52d1d-0853-4211-8141-477c65740264",
    "status": "Succeeded",
    "startTime": "2020-02-03T18:10:48.296012Z",
    "endTime": "2020-02-03T18:10:48.296012Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b"
    }
}
```

這會確認已啟用檔案共用的保護，並根據原則排程觸發第一個備份。

## <a name="trigger-an-on-demand-backup-for-file-share"></a>觸發檔案共用的隨選備份

一旦將 Azure 檔案共用設定為備份，備份就會根據原則排程執行。 您可以等候第一個排定的備份，或隨時觸發隨選備份。

觸發隨選備份為 POST 作業。

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

{容器化} 和 {protectedItemName} 在啟用備份時的結構如上。 針對我們的範例，這會轉譯為：

```http
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/backup?api-version=2017-07-01
```

### <a name="create-request-body"></a>建立要求本文

若要觸發隨選備份，以下是要求本文的元件。

| 名稱       | 類型                       | 說明                       |
| ---------- | -------------------------- | --------------------------------- |
| [內容] | AzurefilesharebackupReques | BackupRequestResource 屬性 |

如需要求本文的完整定義清單及其他詳細資訊，請參閱[觸發受保護項目的備份 REST API 文件](/rest/api/backup/backups/trigger#request-body) \(英文\)。

要求本文範例

```json
{

  "properties":{

   "objectType":"AzureFileShareBackupRequest",
    "recoveryPointExpiryTimeInUTC":"2020-03-07T18:29:59.000Z"
}

}
```

### <a name="responses"></a>回應

觸發隨選備份為[非同步作業](../azure-resource-manager/management/async-operations.md)。 這表示此作業會建立另一項需要個別追蹤的作業。

它會傳回兩個回應：在建立另一個作業時，202（已接受），而當該作業完成時，則會傳回200（確定）。

### <a name="example-responses"></a>範例回應

一旦提交 *POST* 要求以進行隨選備份之後，初始回應會是 202 (已接受) 並具備位置標頭或 Azure-async-header。

```http
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Expires': '-1'
'Location': https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationResults/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2017-07-01
'Retry-After': '60'
'Azure-AsyncOperation': https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationsStatus/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2017-07-01
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'x-ms-client-request-id': 'a644712a-4895-11ea-ba57-0a580af42708, a644712a-4895-11ea-ba57-0a580af42708'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-writes': '1199'
'x-ms-correlation-request-id': '2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'x-ms-routing-request-id': 'WESTEUROPE:20200206T040339Z:2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'Date': 'Thu, 06 Feb 2020 04:03:38 GMT'
'Content-Length': '0'
```

然後使用 location 標頭或 AsyncOperation 標頭搭配*GET*命令來追蹤所產生的作業。

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2016-12-01
```

當作業完成之後，它會在回應本文中傳回 200 (確定) 以及結果備份作業的識別碼。

#### <a name="sample-response-body"></a>範例回應主體

```json
{
    "id": "dc62d524-427a-4093-968d-e951c0a0726e",
    "name": "dc62d524-427a-4093-968d-e951c0a0726e",
    "status": "Succeeded",
    "startTime": "2020-02-06T11:06:02.1327954Z",
    "endTime": "2020-02-06T11:06:02.1327954Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "39282261-cb52-43f5-9dd0-ffaf66beeaef"
    }
}
```

由於備份作業是長時間執行的作業，因此需要加以追蹤，如[使用 REST API 監視作業文件](./backup-azure-arm-userestapi-managejobs.md#tracking-the-job)所述。

## <a name="next-steps"></a>後續步驟

- 瞭解如何[使用 REST API 還原 Azure 檔案共用](restore-azure-file-share-rest-api.md)。

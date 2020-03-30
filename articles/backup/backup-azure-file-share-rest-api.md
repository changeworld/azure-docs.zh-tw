---
title: 使用 REST API 備份 Azure 檔共用
description: 瞭解如何使用 REST API 在恢復服務保存庫中備份 Azure 檔共用
ms.topic: conceptual
ms.date: 02/16/2020
ms.openlocfilehash: 2cf385830ec1be17cb62432e6ef9cba7d82a9db1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248095"
---
# <a name="backup-azure-file-share-using-azure-backup-via-rest-api"></a>通過休息 API 使用 Azure 備份備份 Azure 檔共用

本文介紹如何使用通過 REST API 的 Azure 備份備份 Azure 檔共用。

本文假定您已經創建了一個恢復服務保存庫和策略，用於為檔共用配置備份。 如果沒有，請參閱[創建保存庫](https://docs.microsoft.com/azure/backup/backup-azure-arm-userestapi-createorupdatevault)並[創建策略](https://docs.microsoft.com/azure/backup/backup-azure-arm-userestapi-createorupdatepolicy)REST API 教程以創建新的保存庫和策略。

在本文中，我們將使用以下資源：

- **恢復服務Vault**： *azure 檔庫*

- **策略：***計畫1*

- **資源組** *：azure 檔*

- **存儲帳戶**：*測試庫2*

- **檔共用**：*測試共用*

## <a name="configure-backup-for-an-unprotected-azure-file-share-using-rest-api"></a>使用 REST API 為未受保護的 Azure 檔共用配置備份

### <a name="discover-storage-accounts-with-unprotected-azure-file-shares"></a>發現具有未受保護的 Azure 檔共用的存儲帳戶

保存庫需要發現訂閱中的所有 Azure 存儲帳戶，並包含可備份到恢復服務保存庫的檔共用。 這會使用[重新整理作業](https://docs.microsoft.com/rest/api/backup/protectioncontainers/refresh) \(英文\) 來觸發。 它是一個非同步*POST*操作，可確保保存庫獲取當前訂閱中所有未受保護的 Azure 檔共用的最新清單，並"緩存"它們。 檔共用被"緩存"後，恢復服務可以訪問檔共用並保護它。

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01&$filter={$filter}
```

POST URI`{subscriptionId}`具有`{vaultName}` `{vaultresourceGroupName}`、和`{fabricName}`參數。 在我們的示例中，不同參數的值如下所示：

- `{fabricName}`是*Azure*

- `{vaultName}`是*azure 檔庫*

- `{vaultresourceGroupName}`是*azure 檔*

- $filter_備份管理類型 eq'Azure 存儲'

由於所有必需的參數都位於 URI 中，因此無需單獨的請求正文。

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/refreshContainers?api-version=2016-12-01&$filter=backupManagementType eq 'AzureStorage'
```

#### <a name="responses"></a>回應

「重新整理」作業為[非同步作業](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations)。 這表示此作業會建立另一項需要個別追蹤的作業。

它返回兩個回應：創建另一個操作時為 202（已接受），當該操作完成時返回 200 （OK）。

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

發現所有 Azure 存儲帳戶後，GET 命令將返回 200（無內容）回應。 保存庫現在能夠發現任何具有可在訂閱中備份的檔共用的存儲帳戶。

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

### <a name="get-list-of-storage-accounts-that-can-be-protected-with-recovery-services-vault"></a>獲取可以使用恢復服務保存庫保護的存儲帳戶清單

要確認"緩存"已完成，請列出訂閱下的所有可保護存儲帳戶。 然後在回應中找到所需的存儲帳戶。 這是使用 GET[可保護容器](https://docs.microsoft.com/rest/api/backup/protectablecontainers/list)操作完成的。

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectableContainers?api-version=2016-12-01&$filter=backupManagementType eq 'AzureStorage'
```

*GET* URI 具備所有必要參數。 不需任何額外的要求本文。

回應正文示例：

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

由於我們可以在回應正文中找到具有易記名稱的*testvault2*存儲帳戶，因此上述執行的刷新操作是成功的。 恢復服務保存庫現在可以在同一訂閱中成功發現具有未受保護的檔共用的存儲帳戶。

### <a name="register-storage-account-with-recovery-services-vault"></a>使用恢復服務保存庫註冊存儲帳戶

僅當之前未向保存庫註冊存儲帳戶時，才需要此步驟。 您可以通過[保護容器-註冊操作](https://docs.microsoft.com/rest/api/backup/protectioncontainers/register)註冊保存庫。

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}?api-version=2016-12-01
```

設置 URI 的變數，如下所示：

- [資源組名稱] - *azure 檔*
- [結構名稱] - *Azure*
- [保存庫名稱] - *azure 檔庫*
- [容器名稱] - 這是 GET 可保護容器操作的回應正文中的名稱屬性。
   在我們的示例中，它是*存儲容器;儲存;Azure 檔;測試庫2*

>[!NOTE]
> 始終採用回應的名稱屬性並填寫此請求。 不要硬編碼或創建容器名稱格式。 如果創建或硬編碼，如果容器名稱格式將來發生更改，API 呼叫將失敗。

<br>

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2?api-version=2016-12-01
```

創建請求正文如下所示：

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

有關請求正文定義和其他詳細資訊的完整清單，請參閱[保護容器註冊](https://docs.microsoft.com/rest/api/backup/protectioncontainers/register#azurestoragecontainer)。

這是非同步作業，並返回兩個回應："202 接受"操作被接受時，在操作完成後返回"200 OK"。  要跟蹤操作狀態，請使用位置標頭獲取操作的最新狀態。

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/operationresults/1a3c8ee7-e0e5-43ed-b8b3-73cc992b6db9?api-version=2016-12-01
```

操作完成後回應正文的示例：

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

可以從回應正文中的*註冊狀態*參數的值驗證註冊是否成功。 在我們的案例中，它顯示了為*testvault2*註冊的狀態，因此註冊操作成功。

### <a name="inquire-all-unprotected-files-shares-under-a-storage-account"></a>查詢存儲帳戶下的所有未受保護的檔共用

您可以使用[保護容器-詢問](https://docs.microsoft.com/rest/api/backup/protectioncontainers/inquire)操作查詢存儲帳戶中的可保護專案。 這是非同步作業，應使用位置標頭跟蹤結果。

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/inquire?api-version=2016-12-01
```

設置上述 URI 的變數，如下所示：

- [保存庫名稱] - *azure 檔庫*
- [結構名稱] - *Azure*
- [容器名稱] - 請參閱 GET 可保護容器操作的回應正文中的名稱屬性。 在我們的示例中，它是*存儲容器;儲存;Azure 檔;測試庫2*

```http
https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/inquire?api-version=2016-12-01
```

請求成功後，它將返回狀態碼"確定"

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

### <a name="select-the-file-share-you-want-to-back-up"></a>選擇要備份的檔共用

您可以在訂閱下列出所有可保護的專案，並找到需要使用[GET 備份保護專案](https://docs.microsoft.com/rest/api/backup/backupprotectableitems/list)操作進行備份的檔共用。

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter={$filter}
```

構造 URI 如下所示：

- [保存庫名稱] - *azure 檔庫*
- [$filter] -*備份管理類型 eq "Azure 存儲"*

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

回應包含所有未受保護的檔共用的清單，並包含 Azure 恢復服務配置備份所需的所有資訊。

### <a name="enable-backup-for-the-file-share"></a>為檔共用啟用備份

相關檔共用被"標識"後，使用易記名稱，選擇策略進行保護。 要瞭解有關保存庫中現有策略的詳細資訊，請參閱[清單策略 API](https://docs.microsoft.com/rest/api/backup/backuppolicies/list)。 然後藉由參考原則名稱來選取[相關的原則](https://docs.microsoft.com/rest/api/backup/protectionpolicies/get) \(英文\)。 若要建立原則，請參閱[建立原則教學課程](https://docs.microsoft.com/azure/backup/backup-azure-arm-userestapi-createorupdatepolicy)。

啟用保護是創建"受保護項"的非同步*PUT*操作。

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

使用 GET 備份可保護項操作的回應正文中的 ID 屬性設置**容器名稱**和**受保護項名稱**變數。

在我們的示例中，我們想要保護的檔共用的 ID 是：

```output
"/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;testvault2/protectableItems/azurefileshare;testshare
```

- [容器名稱] -*存儲容器;存儲;azure檔;測試庫2*
- [受保護的專案名稱] - *azure 檔共用;測試共用*

或者，您可以引用保護容器和可保護項回應**的名稱**屬性。

>[!NOTE]
>始終採用回應的名稱屬性並填寫此請求。 不要硬編碼或創建容器名稱格式或受保護專案名稱格式。 如果創建或硬編碼，如果容器名稱格式或受保護專案名稱格式將來發生更改，API 呼叫將失敗。

<br>

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare?api-version=2016-12-01
```

創建請求正文：

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

**源 ResourceId**是回應 GET 備份保護專案的**父容器 FabricID。**

範例回應

創建受保護項是非同步作業，它創建需要跟蹤的另一個操作。 它返回兩個回應：創建另一個操作時為 202（已接受），當該操作完成時返回 200 （OK）。

提交保護項創建或更新*的 PUT*請求後，初始回應為 202（已接受），具有位置標頭。

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

然後使用*GET*命令使用位置標頭或 Azure-Async操作標頭跟蹤生成的操作。

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
```

當作業完成時，它會在回應本文中傳回 200 (確定) 以及受保護的項目內容。

示例回應正文：

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

這確認為檔共用啟用了保護，並且第一個備份將根據策略計畫觸發。

## <a name="trigger-an-on-demand-backup-for-file-share"></a>觸發檔共用的按需備份

為備份配置 Azure 檔共用後，備份將按計劃運行。 您可以等候第一個排定的備份，或隨時觸發隨選備份。

觸發隨選備份為 POST 作業。

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

[容器名稱] 和 [受保護專案名稱] 在啟用備份時構造為上面構造的。 針對我們的範例，這會轉譯為：

```http
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/backup?api-version=2017-07-01
```

### <a name="create-request-body"></a>建立要求本文

若要觸發隨選備份，以下是要求本文的元件。

| 名稱       | 類型                       | 描述                       |
| ---------- | -------------------------- | --------------------------------- |
| 屬性 | Azure 檔共用備份重新 | 備份請求資源屬性 |

如需要求本文的完整定義清單及其他詳細資訊，請參閱[觸發受保護項目的備份 REST API 文件](https://docs.microsoft.com/rest/api/backup/backups/trigger#request-body) \(英文\)。

請求正文示例

```json
{

  "properties":{

   "objectType":"AzureFileShareBackupRequest",
    "recoveryPointExpiryTimeInUTC":"2020-03-07T18:29:59.000Z"
}

}
```

### <a name="responses"></a>回應

觸發隨選備份為[非同步作業](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations)。 這表示此作業會建立另一項需要個別追蹤的作業。

它返回兩個回應：創建另一個操作時為 202（已接受），當該操作完成時返回 200 （OK）。

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

然後使用*GET*命令使用位置標頭或 Azure-Async操作標頭跟蹤生成的操作。

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

由於備份作業是長時間執行的作業，因此需要加以追蹤，如[使用 REST API 監視作業文件](https://docs.microsoft.com/azure/backup/backup-azure-arm-userestapi-managejobs#tracking-the-job)所述。

## <a name="next-steps"></a>後續步驟

- 瞭解如何使用[Rest API 還原 Azure 檔共用](restore-azure-file-share-rest-api.md)。

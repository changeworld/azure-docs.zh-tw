---
title: 非同步作業的狀態
description: 描述如何在 Azure 中追蹤非同步作業。 其會顯示您用來取得長時間執行作業狀態的值。
ms.topic: conceptual
ms.date: 08/20/2020
ms.custom: seodec18
ms.openlocfilehash: 68a00e50c7d3e0da757ee7a3a09274c5f1dbecad
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2020
ms.locfileid: "88718419"
---
# <a name="track-asynchronous-azure-operations"></a>追蹤非同步 Azure 作業

某些 Azure REST 作業因為無法快速完成，而以非同步方式執行。 本文說明如何透過回應中傳回的值，以追蹤非同步作業的狀態。  

## <a name="status-codes-for-asynchronous-operations"></a>非同步作業的狀態碼

非同步作業最初會傳回下列其中一個 HTTP 狀態碼︰

* 201 (已建立)
* 202 (已接受)

作業成功完成時會傳回下列其中一個狀態碼︰

* 200 (確定)
* 204 (沒有內容)

請參閱 [REST API 文件](/rest/api/azure/)，查看您執行作業的回應。

取得201或202回應碼之後，您就可以開始監視操作的狀態。

## <a name="use-url-to-monitor-status"></a>使用 URL 監視狀態

有兩種不同的方式可以監視非同步作業的狀態。 您可以檢查從原始要求傳回的標頭值，以判斷正確的方法。 首先，尋找：

* `Azure-AsyncOperation` - 用於檢查作業進行中狀態的 URL。 如果您的作業會傳回此值，請使用它來追蹤作業的狀態。
* `Retry-After` - 在檢查非同步作業的狀態之前等待的秒數。

如果 `Azure-AsyncOperation` 不是其中一個標頭值，請尋找：

* `Location` - 用於判斷作業何時完成的 URL。 只有在未傳回 Azure AsyncOperation 時，才使用此值。
* `Retry-After` - 在檢查非同步作業的狀態之前等待的秒數。

## <a name="azure-asyncoperation-request-and-response"></a>Azure-AsyncOperation 要求和回應

如果您有來自 `Azure-AsyncOperation` 標頭值的 url，請將 GET 要求傳送至該 url。 使用的值 `Retry-After` 來排程檢查狀態的頻率。 回應屬性可能會不同，但一律會包含非同步作業的狀態。

```json
{
    "status": "{status-value}"
}
```

下列範例顯示作業可能傳回的其他值：

```json
{
    "id": "{resource path from GET operation}",
    "name": "{operation-id}",
    "status" : "Succeeded | Failed | Canceled | {resource provider values}",
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
    "percentComplete": {double between 0 and 100 },
    "properties": {
        /* Specific resource provider values for successful operations */
    },
    "error" : {
        "code": "{error code}",  
        "message": "{error description}"
    }
}
```

當狀態為 Failed 或 Canceled 時會傳回錯誤物件。 所有其他值都是選擇性的。 您收到的回應看起來可能與範例不同。

## <a name="provisioningstate-values"></a>provisioningState 值

建立、更新或刪除資源的作業 (PUT、PATCH、DELETE) 通常會傳回 `provisioningState` 值。 當作業完成時會傳回下列三個值之一︰

* 成功
* 失敗
* 已取消

其他所有值表示作業仍在執行中。 資源提供者可以傳回自訂值來指出其狀態。 例如，當要求已收到且正在執行時，您可能會收到 **Accepted**。

## <a name="example-requests-and-responses"></a>要求和回應範例

### <a name="start-virtual-machine-202-with-azure-asyncoperation"></a>啟動虛擬機器 (202 與 Azure-AsyncOperation)

此範例示範如何判斷 [虛擬機器的啟動操作](/rest/api/compute/virtualmachines/start)狀態。 初始要求是下列格式︰

```HTTP
POST 
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Compute/virtualMachines/{vm-name}/start?api-version=2019-12-01
```

它傳回狀態碼 202。 在標頭值之中，您會看到：

```HTTP
Azure-AsyncOperation : https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2019-12-01
```

若要檢查非同步作業的狀態，請將另一個要求傳送至該 URL。

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2019-12-01
```

回應內文包含作業的狀態︰

```json
{
  "startTime": "2017-01-06T18:58:24.7596323+00:00",
  "status": "InProgress",
  "name": "9a062a88-e463-4697-bef2-fe039df73a02"
}
```

### <a name="deploy-resources-201-with-azure-asyncoperation"></a>部署資源 (201 與 Azure-AsyncOperation)

此範例說明如何判斷部署資源至 Azure 的 [部署](/rest/api/resources/deployments/createorupdate) 作業狀態。 初始要求是下列格式︰

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/microsoft.resources/deployments/{deployment-name}?api-version=2020-06-01
```

它傳回狀態碼 201。 回應內文包含︰

```json
"provisioningState":"Accepted",
```

在標頭值之中，您會看到：

```HTTP
Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2020-06-01
```

若要檢查非同步作業的狀態，請將另一個要求傳送至該 URL。

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2020-06-01
```

回應內文包含作業的狀態︰

```json
{
    "status": "Running"
}
```

部署完成時，回應包含︰

```json
{
    "status": "Succeeded"
}
```

### <a name="create-storage-account-202-with-location-and-retry-after"></a>建立儲存體帳戶 (202 與 Location 和 Retry-After)

此範例說明如何判斷 [儲存體帳戶的建立](/rest/api/storagerp/storageaccounts/create)作業狀態。 初始要求是下列格式︰

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2019-06-01
```

要求內文包含儲存體帳戶的屬性︰

```json
{
    "location": "South Central US",
    "properties": {},
    "sku": {
        "name": "Standard_LRS"
    },
    "kind": "Storage"
}
```

它傳回狀態碼 202。 在標頭值之中，您會看到下列兩個值︰

```HTTP
Location: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2019-06-01
Retry-After: 17
```

等待 Retry-After 中指定的秒數經過之後，將另一個要求傳送至該 URL，以檢查非同步作業的狀態。

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2019-06-01
```

如果要求仍在執行中，您會收到狀態碼 202。 如果要求已完成，您會收到狀態碼 200，回應內文會包含已建立之儲存體帳戶的屬性。

## <a name="next-steps"></a>後續步驟

* 如需每個 REST 作業的相關文件，請參閱 [REST API 文件](/rest/api/azure/)。
* 如需透過 Resource Manager REST API 部署範本的相關資訊，請參閱 [使用 Resource Manager 範本部署資源和 Resource Manager REST API](../templates/deploy-rest.md)。
---
title: Azure API 管理虛刪除 (預覽版) |Microsoft Docs
description: 虛刪除可讓您復原已刪除的 API 管理實例。
ms.service: api-management
ms.topic: conceptual
author: vladvino
ms.author: apimpm
ms.date: 11/27/2020
ms.openlocfilehash: fca98414a87f3b8a4f3c0969a28ee95c7ed47dc3
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96501469"
---
# <a name="api-management-soft-delete-preview"></a>API 管理虛刪除 (預覽) 

使用 API 管理虛刪除 (預覽版) ，您可以 (APIM) 實例復原和還原最近刪除的 API 管理。

> [!IMPORTANT]
> 只有使用 `2020-01-01-preview` 和更新版本 api 版本刪除的 Api 管理實例，將會使用本文中所述的步驟進行虛刪除和復原。 使用先前 API 版本刪除的 APIM 實例將會繼續遭到永久刪除。 Azure PowerShell 和 Azure CLI 目前不 `2020-06-01-preview` 會使用版本，也會導致實刪除行為。

## <a name="supporting-interfaces"></a>支援的介面

虛刪除功能可透過 [REST API](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/restore)取得。

> [!TIP]
> 如需呼叫 Azure REST Api 的秘訣和工具，請參閱 [Azure REST API 參考](/rest/api/azure/) 。

| 作業 | 描述 | API 管理命名空間 | 最小 API 版本 |
|--|--|--|--|
| [建立或更新](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/createorupdate) | 建立或更新 API 管理服務。  | API 管理服務 | 任意 |
| [Create or Update](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/createorupdate)使用 `restore` 設定為 **true** 的屬性建立或更新 | Undeletes API 管理服務（如果先前已虛刪除）。 如果 `restore` 指定，而且設定為 `true` 其他所有屬性，將會被忽略。  | API 管理服務 |  2020-06-01-預覽 |
| [刪除](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/delete) | 刪除現有的 API 管理服務。 | API 管理服務 | 2020-01-01-預覽|
| [依名稱取得](/rest/api/apimanagement/2020-06-01-preview/deletedservices/getbyname) | 依名稱取得虛刪除的 Api 管理服務。 | 刪除的服務 | 2020-06-01-預覽 |
| [依訂用帳戶列出](/rest/api/apimanagement/2020-06-01-preview/deletedservices/listbysubscription) | 列出指定訂用帳戶可取消刪除的所有虛刪除服務。 | 刪除的服務 | 2020-06-01-預覽
| [清除](/rest/api/apimanagement/2020-06-01-preview/deletedservices/purge) | 清除 API 管理服務 (會將其刪除，且沒有可取消刪除) 的選項。 | 刪除的服務 | 2020-06-01-預覽

## <a name="soft-delete-behavior"></a>虛刪除行為

您可以使用任何 API 版本來建立 API 管理實例，不過您必須使用 `2020-01-01-preview` 或更新版本來虛刪除您的 APIM 實例 (並可選擇將其復原) 。

刪除 API 管理實例時，服務將會處於已刪除狀態，使其無法存取任何 APIM 作業。 處於此狀態時，APIM 實例只能列出、復原或清除 (永久刪除) 。

同時，Azure 會在預先決定的 (48 小時) 保留間隔之後，排程刪除對應于 APIM 實例的基礎資料，以進行執行。 對應至實例的 DNS 記錄也會在保留間隔的持續時間內保留。 您無法重複使用已虛刪除的 API 管理實例的名稱，直到超過保留期限為止。

如果您的 APIM 實例未在48小時內復原，則會被實刪除 (無法復原的) 。 您也可以選擇 [清除](#purge-a-soft-deleted-apim-instance) (永久刪除) APIM 實例，放棄虛刪除保留期限。

## <a name="list-deleted-apim-instances"></a>列出已刪除的 APIM 實例

您可以使用依名稱或依[訂](/deletedservices/listbysubscription)用帳戶作業[取得](/rest/api/apimanagement/2020-06-01-preview/deletedservices/getbyname)的已刪除服務，來確認已虛刪除的 APIM 實例可用於還原 (取消刪除) 。

### <a name="get-a-soft-deleted-instance-by-name"></a>依名稱取得虛刪除的實例

使用 API 管理 [Get By Name](/rest/api/apimanagement/2020-06-01-preview/deletedservices/getbyname) 作業，將 `{subscriptionId}` 、和取代 `{location}` `{serviceName}` 為您的 Azure 訂用帳戶、資源位置和 API 管理實例名稱：

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ApiManagement/locations/{location}/deletedservices/{serviceName}?api-version=2020-06-01-preview
```

如果可以取消刪除，Azure 將會傳回 APIM 實例的記錄，顯示其 `deletionDate` 和 `scheduledPurgeDate` ，例如：

```json
{
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ApiManagement/locations/southcentralus/deletedservices/apimtest",
    "name": "apimtest",
    "type": "Microsoft.ApiManagement/deletedservices",
    "location": "South Central US",
    "properties": {
        "serviceId": "/subscriptions/########-####-####-####-############/resourceGroups/apimtestgroup/providers/Microsoft.ApiManagement/service/apimtest",
        "scheduledPurgeDate": "2020-11-26T19:40:26.3596893Z",
        "deletionDate": "2020-11-24T19:40:50.1013572Z"
    }
}
```

### <a name="list-all-soft-deleted-instances-for-a-given-subscription"></a>列出指定訂用帳戶的所有虛刪除實例

[依訂](/deletedservices/listbysubscription)用帳戶作業使用 API 管理清單， `{subscriptionId}` 並以您的訂用帳戶識別碼取代：

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ApiManagement/deletedservices?api-version=2020-06-01-preview
```

這會傳回一份清單，列出在指定的訂用帳戶下可以取消刪除的所有虛刪除服務， `deletionDate` 並顯示每個服務的和 `scheduledPurgeDate` 。

## <a name="recover-a-deleted-apim-instance"></a>復原已刪除的 APIM 實例

使用 API 管理的 [建立或更新](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/createorupdate) 作業，將 `{subscriptionId}` 、 `{resourceGroup}` 和取代 `{apimServiceName}` 為您的 Azure 訂用帳戶、資源組名和 API 管理名稱：

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.ApiManagement/service/{apimServiceName}?api-version=2020-06-01-preview
```

. . . 並 `restore` `true` 在要求主體中將屬性設為。  (指定此旗標並設定為 *True* 時，將會忽略所有其他屬性 ) 。例如：

```json
{
  "properties": {
    "publisherEmail": "help@contoso.com",
    "publisherName": "Contoso",
    "restore": true
  },
  "sku": {
    "name": "Developer",
    "capacity": 1
  },
  "location": "South Central US"
}
```

## <a name="purge-a-soft-deleted-apim-instance"></a>清除虛刪除的 APIM 實例

使用 API 管理 [清除](/rest/api/apimanagement/2020-06-01-preview/deletedservices/purge) 作業，將 `{subscriptionId}` 、和取代 `{location}` `{serviceName}` 為您的 Azure 訂用帳戶、資源位置和 API 管理名稱：

```rest
DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ApiManagement/locations/{location}/deletedservices/{serviceName}?api-version=2020-06-01-preview
```

這會從 Azure 永久刪除您的 API 管理實例。

## <a name="next-steps"></a>後續步驟

深入瞭解「長期 API 管理」備份和修復選項：

- [如何在 Azure API 管理中使用服務備份和還原來實作災害復原](api-management-howto-disaster-recovery-backup-restore.md)
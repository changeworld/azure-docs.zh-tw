---
title: 要求限制和節流
description: 描述如何在到達訂用帳戶限制時，對 Azure Resource Manager 要求使用節流。
ms.topic: conceptual
ms.date: 03/24/2020
ms.custom: seodec18
ms.openlocfilehash: 4d387749261747eb9ea1ea26629ade4fe8729856
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239370"
---
# <a name="throttling-resource-manager-requests"></a>對 Resource Manager 要求進行節流

本文介紹 Azure 資源管理器如何限制請求。 它演示如何跟蹤在達到限制之前保留的請求數，以及如何在達到限制時做出回應。

限制發生在兩個級別。 Azure 資源管理器限制訂閱和租戶的請求。 如果請求在訂閱和租戶的限制下，資源管理器會將請求路由到資來源提供者。 資來源提供者應用針對其操作量身定制的限制限制。 下圖顯示了當請求從使用者轉到 Azure 資源管理器和資來源提供者時如何應用限制。

![請求限制](./media/request-limits-and-throttling/request-throttling.svg)

## <a name="subscription-and-tenant-limits"></a>訂閱和租戶限制

每個訂閱級和租戶級操作都受限制限制。 訂閱請求涉及傳遞訂閱 ID，例如檢索訂閱中的資源組。 租用戶要求則未包含訂用帳戶識別碼，例如擷取有效的 Azure 位置。

預設每小時限制限制顯示在下表中。

| 影響範圍 | 作業 | 限制 |
| ----- | ---------- | ------- |
| 訂用帳戶 | reads | 12000 |
| 訂用帳戶 | deletes | 15000 |
| 訂用帳戶 | writes | 1200 |
| 租用戶 | reads | 12000 |
| 租用戶 | writes | 1200 |

這些限制會侷限在提出要求的安全性主體 (使用者或應用程式)，以及訂用帳戶識別碼或租用戶識別碼。 如果您的要求來自多個安全性主體，則訂用帳戶或租用戶之間的限制會大於每小時 12,000 個和 1,200 個。

這些限制適用於每個 Azure Resource Manager 執行個體。 每個 Azure 區域中都有多個執行個體，且 Azure Resource Manager 會部署到所有 Azure 區域。  因此，在實踐中，限制高於這些限制。 來自使用者的請求通常由 Azure 資源管理器的不同實例處理。

## <a name="resource-provider-limits"></a>資來源提供者限制

資來源提供者應用自己的限制限制。 由於資源管理器按主體 ID 和資源管理器實例限制，因此資來源提供者收到的請求可能多於上一節中的預設限制。

本節討論一些廣泛使用的資來源提供者的限制限制。

### <a name="storage-throttling"></a>儲存體節流

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="network-throttling"></a>網路節流

Microsoft.網路資來源提供者應用以下限制限制：

| 作業 | 限制 |
| --------- | ----- |
| 寫入/刪除（PUT） | 每5分鐘1000次 |
| 讀取 (GET) | 每5分鐘10000 |

### <a name="compute-throttling"></a>計算限制

有關計算操作限制的資訊，請參閱[排除 API 限制錯誤的故障排除 - 計算](../../virtual-machines/troubleshooting/troubleshooting-throttling-errors.md)。

要檢查虛擬機器規模集中的虛擬機器實例，請使用[虛擬機器縮放集操作](/rest/api/compute/virtualmachinescalesetvms)。 例如，使用[虛擬機器縮放集 VM - 包含參數的清單](/rest/api/compute/virtualmachinescalesetvms/list)來檢查虛擬機器實例的電源狀態。 此 API 減少了請求數。

### <a name="azure-resource-graph-throttling"></a>Azure 資源圖形限制

[Azure 資源圖](../../governance/resource-graph/overview.md)將請求數限制為其操作。 本文中確定剩餘請求的步驟以及達到限制時如何回應也適用于資源圖。 但是，資源圖設置其自己的限制和重置速率。 有關詳細資訊，請參閱[資源圖形限制標頭](../../governance/resource-graph/concepts/guidance-for-throttled-requests.md#understand-throttling-headers)。

## <a name="error-code"></a>錯誤碼

當您到達限制時，您會收到 HTTP 狀態碼 **429 太多要求**。 回應包括 **"重試-後"** 值，該值指定應用程式在發送下一個請求之前應等待（或睡眠）的秒數。 如果重試值沒過您就傳送要求，則不會處理要求，並且會傳回新的重試值。

等待指定時間後，還可以關閉並重新打開與 Azure 的連接。 通過重置連接，可以連接到 Azure 資源管理器的不同實例。

如果使用 Azure SDK，SDK 可能具有自動重試配置。 有關詳細資訊，請參閱[Azure 服務的重試指南](/azure/architecture/best-practices/retry-service-specific)。

某些資來源提供者返回 429 以報告臨時問題。 問題可能是不是由您的請求直接引起的超載情況。 或者，它可以表示有關目標資源或從屬資源狀態的臨時錯誤。 例如，當目標資源被另一個操作鎖定時，網路資來源提供者返回 429，並帶有**可重試 ErrorDueto 另一個操作**錯誤代碼。 要確定錯誤是來自限制還是臨時條件，請查看回應中的錯誤詳細資訊。

## <a name="remaining-requests"></a>剩餘的要求

您可以藉由檢查回應標頭來判斷剩餘的要求數。 讀取請求在標頭中返回剩餘已讀請求數的值。 寫入請求包括剩餘寫入請求數的值。 下表描述可供檢查這些值的回應標頭︰

| 回應標頭 | 描述 |
| --- | --- |
| x-ms-ratelimit-remaining-subscription-reads |受訂用帳戶限制的剩餘讀取。 讀取作業會傳回此值。 |
| x-ms-ratelimit-remaining-subscription-writes |受訂用帳戶限制的剩餘寫入。 寫入作業會傳回此值。 |
| x-ms-ratelimit-remaining-tenant-reads |受租用戶限制的剩餘讀取 |
| x-ms-ratelimit-remaining-tenant-writes |受租用戶限制的剩餘寫入 |
| x-ms-ratelimit-remaining-subscription-resource-requests |受訂用帳戶限制的剩餘資源類型要求。<br /><br />只有在服務已覆寫預設限制時，才會傳回此標頭值。 Resource Manager 會新增此值，而非訂用帳戶讀取或寫入。 |
| x-ms-ratelimit-remaining-subscription-resource-entities-read |受訂用帳戶限制的剩餘資源類型集合要求。<br /><br />只有在服務已覆寫預設限制時，才會傳回此標頭值。 這個值會提供剩餘的集合要求 (列出資源) 數目。 |
| x-ms-ratelimit-remaining-tenant-resource-requests |受租用戶限制的剩餘資源類型要求。<br /><br />只有租用戶層級的要求且在服務已覆寫預設限制時，才會新增此標頭。 Resource Manager 會新增此值，而非租用戶讀取或寫入。 |
| x-ms-ratelimit-remaining-tenant-resource-entities-read |受租用戶限制的剩餘資源類型集合要求。<br /><br />只有租用戶層級的要求且在服務已覆寫預設限制時，才會新增此標頭。 |

資來源提供者還可以返回具有剩餘請求資訊的回應標頭。 有關計算資來源提供者返回的回應標頭的資訊，請參閱[呼叫速率資訊回應標頭](../../virtual-machines/troubleshooting/troubleshooting-throttling-errors.md#call-rate-informational-response-headers)。

## <a name="retrieving-the-header-values"></a>擷取標頭值

在程式碼或指令碼中擷取這些標頭值與擷取任何標頭值並無不同。 

例如，在 **C#** 中，您可以使用下列程式碼從 **HttpWebResponse** 物件 (名為 **response**) 擷取標頭值︰

```cs
response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)
```

在 **PowerShell** 中，您可以從 Invoke-WebRequest 作業擷取標頭值。

```powershell
$r = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
$r.Headers["x-ms-ratelimit-remaining-subscription-reads"]
```

如需完整的 PowerShell 範例，請參閱 [Check Resource Manager Limits for a Subscription](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI) (檢查訂用帳戶的 Resource Manager 限制)。

如果您想要查看可用於偵錯的剩餘要求，您可以在 **PowerShell** Cmdlet 提供 **-Debug** 參數。

```powershell
Get-AzResourceGroup -Debug
```

這會傳回許多值，包括下列回應值︰

```output
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
OK

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-reads: 11999
```

若要取得寫入限制，請使用寫入作業： 

```powershell
New-AzResourceGroup -Name myresourcegroup -Location westus -Debug
```

這會傳回許多值，包括下列值︰

```output
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
Created

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-writes: 1199
```

在 **Azure CLI** 中，您可以使用更詳細的選項擷取標頭值。

```azurecli
az group list --verbose --debug
```

這會傳回許多值，包括下列值︰

```output
msrest.http_logger : Response status: 200
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Content-Encoding': 'gzip'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'Vary': 'Accept-Encoding'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-reads': '11998'
```

若要取得寫入限制，請使用寫入作業： 

```azurecli
az group create -n myresourcegroup --location westus --verbose --debug
```

這會傳回許多值，包括下列值︰

```output
msrest.http_logger : Response status: 201
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Length': '163'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-writes': '1199'
```

## <a name="next-steps"></a>後續步驟

* 如需完整的 PowerShell 範例，請參閱 [Check Resource Manager Limits for a Subscription](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI) (檢查訂用帳戶的 Resource Manager 限制)。
* 有關限制和配額的詳細資訊，請參閱[Azure 訂閱和服務限制、配額和約束](../../azure-resource-manager/management/azure-subscription-service-limits.md)。
* 要瞭解如何處理非同步 REST 請求，請參閱[跟蹤非同步 Azure 操作](async-operations.md)。

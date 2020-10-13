---
title: 要求限制和節流
description: 描述如何在到達訂用帳戶限制時，對 Azure Resource Manager 要求使用節流。
ms.topic: conceptual
ms.date: 03/24/2020
ms.custom: seodec18
ms.openlocfilehash: 4d387749261747eb9ea1ea26629ade4fe8729856
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "80239370"
---
# <a name="throttling-resource-manager-requests"></a>對 Resource Manager 要求進行節流

本文說明 Azure Resource Manager 如何節流要求。 它會示範如何追蹤在達到限制之前保留的要求數目，以及當您達到限制時如何回應。

節流會發生在兩個層級。 Azure Resource Manager 會對訂用帳戶和租使用者的要求進行節流。 如果要求是在訂用帳戶和租使用者的節流限制下，Resource Manager 會將要求路由至資源提供者。 資源提供者會套用針對其作業量身訂做的節流限制。 下圖顯示當要求從使用者傳送至 Azure Resource Manager 和資源提供者時，如何套用節流。

![要求節流](./media/request-limits-and-throttling/request-throttling.svg)

## <a name="subscription-and-tenant-limits"></a>訂用帳戶和租使用者限制

每個訂用帳戶層級和租使用者層級作業受限於節流限制。 訂用帳戶要求是涉及傳遞訂用帳戶識別碼的訂用帳戶，例如在訂用帳戶中取出資源群組。 租用戶要求則未包含訂用帳戶識別碼，例如擷取有效的 Azure 位置。

下表顯示預設的節流限制（每小時）。

| 影響範圍 | 作業 | 限制 |
| ----- | ---------- | ------- |
| 訂用帳戶 | reads | 12000 |
| 訂用帳戶 | deletes | 15000 |
| 訂用帳戶 | writes | 1200 |
| 租用戶 | reads | 12000 |
| 租用戶 | writes | 1200 |

這些限制會侷限在提出要求的安全性主體 (使用者或應用程式)，以及訂用帳戶識別碼或租用戶識別碼。 如果您的要求來自多個安全性主體，則訂用帳戶或租用戶之間的限制會大於每小時 12,000 個和 1,200 個。

這些限制適用於每個 Azure Resource Manager 執行個體。 每個 Azure 區域中都有多個執行個體，且 Azure Resource Manager 會部署到所有 Azure 區域。  因此，在實務上，限制會高於這些限制。 來自使用者的要求通常是由 Azure Resource Manager 的不同實例所處理。

## <a name="resource-provider-limits"></a>資源提供者限制

資源提供者會套用自己的節流限制。 由於 Resource Manager 由主體識別碼和 Resource Manager 的實例進行節流，因此資源提供者可能會收到比上一節中預設限制更多的要求。

本節討論一些廣泛使用之資源提供者的節流限制。

### <a name="storage-throttling"></a>儲存體節流

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="network-throttling"></a>網路節流設定

Microsoft 網路資源提供者會套用下列節流限制：

| 作業 | 限制 |
| --------- | ----- |
| 寫入/刪除 (PUT)  | 每5分鐘1000 |
| 讀取 (GET) | 每5分鐘10000 |

### <a name="compute-throttling"></a>計算節流

如需計算作業節流限制的相關資訊，請參閱 [疑難排解 API 節流錯誤-計算](../../virtual-machines/troubleshooting/troubleshooting-throttling-errors.md)。

若要檢查虛擬機器擴展集內的虛擬機器實例，請使用 [虛擬機器擴展集作業](/rest/api/compute/virtualmachinescalesetvms)。 例如，使用 [虛擬機器擴展集 Vm 清單](/rest/api/compute/virtualmachinescalesetvms/list) 與參數，檢查虛擬機器實例的電源狀態。 此 API 可減少要求數目。

### <a name="azure-resource-graph-throttling"></a>Azure Resource Graph 節流

[Azure Resource Graph](../../governance/resource-graph/overview.md) 會限制對其作業的要求數目。 這篇文章中的步驟可判斷剩餘的要求，以及當達到限制時如何回應，也適用于 Resource Graph。 不過，Resource Graph 會設定自己的限制和重設速率。 如需詳細資訊，請參閱 [Resource Graph 節流標頭](../../governance/resource-graph/concepts/guidance-for-throttled-requests.md#understand-throttling-headers)。

## <a name="error-code"></a>錯誤碼

當您到達限制時，您會收到 HTTP 狀態碼 **429 太多要求**。 回應會包含 [ **重試-後** ] 值，指定您的應用程式在傳送下一個要求之前，應該等候 (或睡眠) 的秒數。 如果重試值沒過您就傳送要求，則不會處理要求，並且會傳回新的重試值。

等候指定的時間之後，您也可以關閉並重新開啟與 Azure 的連線。 藉由重設連接，您可以連接到不同的 Azure Resource Manager 實例。

如果您使用的是 Azure SDK，SDK 可能會有自動重試設定。 如需詳細資訊，請參閱 [Azure 服務的重試指引](/azure/architecture/best-practices/retry-service-specific)。

某些資源提供者會傳回429來回報暫時性問題。 問題可能是因為您的要求未直接造成的多載條件。 或者，它可能代表有關目標資源或相依資源狀態的暫時錯誤。 例如，當其他作業鎖定目標資源時，網路資源提供者會傳回429，並傳回 **RetryableErrorDueToAnotherOperation** 錯誤碼。 若要判斷錯誤是否來自節流或暫時性狀況，請參閱回應中的錯誤詳細資料。

## <a name="remaining-requests"></a>剩餘的要求

您可以藉由檢查回應標頭來判斷剩餘的要求數。 讀取要求會針對剩餘的讀取要求數目，傳回標頭中的值。 寫入要求包含剩餘寫入要求數目的值。 下表描述可供檢查這些值的回應標頭︰

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

資源提供者也可以傳迴響應標頭，以及剩餘要求的相關資訊。 如需計算資源提供者所傳回之回應標頭的詳細資訊，請參閱 [呼叫速率資訊回應標頭](../../virtual-machines/troubleshooting/troubleshooting-throttling-errors.md#call-rate-informational-response-headers)。

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
* 如需有關限制和配額的詳細資訊，請參閱 [Azure 訂用帳戶和服務限制、配額和條件約束](../../azure-resource-manager/management/azure-subscription-service-limits.md)。
* 若要瞭解如何處理非同步 REST 要求，請參閱 [追蹤非同步 Azure 作業](async-operations.md)。

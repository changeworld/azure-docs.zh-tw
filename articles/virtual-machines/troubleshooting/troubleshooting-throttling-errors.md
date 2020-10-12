---
title: 對 Azure 中的節流錯誤進行疑難排解 | Microsoft Docs
description: Azure 計算中的節流錯誤、重試和輪詢。
services: virtual-machines
documentationcenter: ''
author: changov
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: changov
ms.reviewer: vashan, rajraj
ms.openlocfilehash: b1cc8a43423ecd33218948aaa001fc34877eac60
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87074282"
---
# <a name="troubleshooting-api-throttling-errors"></a>針對 API 節流錯誤進行疑難排解 

Azure 計算要求可在訂用帳戶上和個別區域中受到節流，以利提升服務的整體效能。 對於負責管理 Microsoft.Compute 命名空間下各項資源的 Azure 計算資源提供者 (CRP)，我們會確保對其發出的所有呼叫不會超過所允許 API 要求率的上限。 本文件將說明 API 節流，並詳細解說如何對節流問題進行疑難排解，以及避免受到節流的最佳做法。  

## <a name="throttling-by-azure-resource-manager-vs-resource-providers"></a>Azure Resource Manager 與資源提供者的節流  

作為 Azure 的主要進入點，Azure Resource Manager 會對所有內送的 API 要求進行驗證、一級驗證和節流。 Azure Resource Manager 的呼叫率限制和相關的診斷回應 HTTP 標頭說明於[此處](../../azure-resource-manager/management/request-limits-and-throttling.md)。
 
當 Azure API 用戶端發生節流錯誤時，HTTP 狀態會是「429 要求太多」。 若要了解要求節流是由 Azure Resource Manager 還是 CRP 之類的基礎資源提供者所執行，請檢查 GET 要求的 `x-ms-ratelimit-remaining-subscription-reads` 和非 GET 要求的 `x-ms-ratelimit-remaining-subscription-writes` 回應標頭。 如果剩餘的呼叫計數趨近於 0，表示已達到 Azure 資源管理員所定義的訂用帳戶一般呼叫限制。 所有訂用帳戶用戶端的活動會一起計算。 若非如此，表示節流來自於目標資源提供者 (要求 URL 的 `/providers/<RP>` 區段所指出的提供者)。 

## <a name="call-rate-informational-response-headers"></a>呼叫率資訊回應標頭 

| 標頭                            | 值格式                           | 範例                               | 描述                                                                                                                                                                                               |
|-----------------------------------|----------------------------------------|---------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| x-ms-ratelimit-remaining-resource |```<source RP>/<policy or bucket>;<count>```| Microsoft.Compute/HighCostGet3Min;159 | 涵蓋資源貯體或作業群組 (包括此要求的目標) 的節流原則剩餘的 API 呼叫計數                                                                   |
| x-ms-request-charge               | ```<count>```                             | 1                                     | 此 HTTP 要求計入適用原則限制的呼叫計數。 此值通常是 1。 對於批次要求 (例如，用來調整虛擬機器擴展集)，則可能計入多個計數。 |


請注意，一個 API 要求可能受到多個節流原則的限制。 每個原則都會有個別的 `x-ms-ratelimit-remaining-resource` 標頭。 

以下範例顯示對於虛擬機器擴展集刪除要求所做的回應。

```
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet3Min;107 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet30Min;587 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VMScaleSetBatchedVMRequests5Min;3704 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VmssQueuedVMOperations;4720 
```

## <a name="throttling-error-details"></a>節流錯誤詳細資料

429 HTTP 狀態通常用來在已達到呼叫率限制時拒絕要求。 計算資源提供者的一般節流錯誤回應會如下列範例所示 (僅顯示相關標頭)：

```
HTTP/1.1 429 Too Many Requests
x-ms-ratelimit-remaining-resource: Microsoft.Compute/HighCostGet3Min;46
x-ms-ratelimit-remaining-resource: Microsoft.Compute/HighCostGet30Min;0
Retry-After: 1200
Content-Type: application/json; charset=utf-8
{
  "code": "OperationNotAllowed",
  "message": "The server rejected the request because too many requests have been received for this subscription.",
  "details": [
    {
      "code": "TooManyRequests",
      "target": "HighCostGet30Min",
      "message": "{\"operationGroup\":\"HighCostGet30Min\",\"startTime\":\"2018-06-29T19:54:21.0914017+00:00\",\"endTime\":\"2018-06-29T20:14:21.0914017+00:00\",\"allowedRequestCount\":800,\"measuredRequestCount\":1238}"
    }
  ]
}

```

剩餘呼叫計數為 0 的原則，即是導致系統傳回節流錯誤的原則。 在此案例中，該原則為 `HighCostGet30Min`。 回應本文的整體格式是一般 Azure Resource Manager API 錯誤格式 (符合 OData)。 主要錯誤碼 `OperationNotAllowed` 是計算資源提供者用來報告節流錯誤 (以及其他類型的用戶端錯誤) 的錯誤碼。 內部錯誤的 `message` 屬性包含已序列化的 JSON 結構，與節流違規的詳細資料。

如上所示，每個節流錯誤都包含 `Retry-After` 標頭，用以提供用戶端在重試要求之前所應等待的秒數下限。 

## <a name="api-call-rate-and-throttling-error-analyzer"></a>API 呼叫率和節流處理錯誤分析器
「計算」資源提供者的 API 有一個可用的預覽版疑難排解功能。 這些 PowerShell Cmdlet 提供與每一作業每一時間間隔之 API 要求率及每一作業群組 (原則) 之節流處理違規相關的統計資料：
-   [Export-AzLogAnalyticRequestRateByInterval](/powershell/module/az.compute/export-azloganalyticrequestratebyinterval)
-   [匯出-AzLogAnalyticThrottledRequest](/powershell/module/az.compute/export-azloganalyticthrottledrequest)

API 呼叫統計資料可提供訂用帳戶用戶端行為的絕佳深入解析，讓您能夠輕鬆識別出造成節流的呼叫模式。

目前此分析器有一個限制，就是它不會計算磁碟和快照集資源類型 (用以支援受控磁碟) 的要求。 由於它會從 CRP 的遙測收集資料，因此它也無助於識別來自 ARM 的節流處理錯誤。 但是您可以根據獨特的 ARM 回應標頭輕鬆識別這些錯誤，如先前所述。

PowerShell Cmdlet 目前使用 REST 服務 API，這是用戶端可直接輕鬆呼叫的 API (但尚未正式支援)。 若要查看 HTTP 要求格式，請執行 Cmdlet 搭配 -Debug 參數，或使用 Fiddler 在其執行時進行窺探。


## <a name="best-practices"></a>最佳作法 

- 請勿無條件和/或立即地重試 Azure 服務 API 錯誤。 在發生不可重試的錯誤時讓用戶端程式碼進入快速重試迴圈，是經常發生的狀況。 重試最終會耗盡目標作業的群組允許的呼叫限制次數，而對訂用帳戶的其他用戶端造成影響。 
- 在大量 API 自動化的案例中，請在目標作業群組的可用呼叫計數低於某個低閾值時考慮實作主動用戶端自我節流。 
- 在追蹤非同步作業時，請遵循 Retry-After 標頭的提示。 
- 如果用戶端程式碼需要特定虛擬機器的相關資訊，請直接查詢該 VM，而不要列出其所屬資源群組或整個訂用帳戶中的所有 VM，然後選擇用戶端上所需的 VM。 
- 如果用戶端程式碼需要特定 Azure 位置中的 VM、磁碟和快照集，請使用以位置為基礎的查詢表單，而不要查詢所有訂用帳戶 VM，然後在用戶端上依位置進行篩選：對計算資源提供者區域端點發出 `GET /subscriptions/<subId>/providers/Microsoft.Compute/locations/<location>/virtualMachines?api-version=2017-03-30` 查詢。 
-   在建立或更新 API 資源時 (尤其是 VM 與虛擬機器擴展集)，相較於輪詢資源 URL 本身 (根據 `provisioningState`)，追蹤已完成並傳回的非同步作業會有效率得多。

## <a name="next-steps"></a>接下來的步驟

若想進一步了解 Azure 中其他資源的重試指引，請參閱[特定服務的重試指引](/azure/architecture/best-practices/retry-service-specific)

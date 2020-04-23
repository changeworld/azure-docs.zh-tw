---
title: 包含檔案
description: 包含檔案
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 07/22/2019
ms.author: bwren
ms.custom: include file
ms.openlocfilehash: 627b020ce618a2a1f2646a95e143947876bd6a15
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "82072632"
---
### <a name="general-query-limits"></a>一般查詢限制

| 限制 | 描述 |
|:---|:---|
| 查詢語言 | Azure 監視器使用與 Azure 資料資源管理員相同的[函式庫s托查詢語言](/azure/kusto/query/)。 有關 Azure 監視器中不支援的 KQL 語言元素,請參閱[Azure 監視器紀錄查詢語言差異](../articles/azure-monitor/log-query/data-explorer-difference.md)。 |
| Azure 區域 | 當數據跨越多個 Azure 區域中的日誌分析工作區時,日誌查詢可能會遇到過多的開銷。 有關詳細資訊,請參閱[查詢限制](../articles/azure-monitor/log-query/scope.md#query-limits)。 |
| 交叉資源查詢 | 單個查詢中的應用程式見解資源和日誌分析工作區的最大數量限制為 100。<br>視圖設計器不支援跨資源查詢。<br>新的 scheduledQueryRules API 支援記錄警示中的跨資源查詢。<br>有關詳細資訊,請參閱[跨資源查詢限制](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits)。 |

### <a name="user-query-throttling"></a>使用者查詢限制
Azure 監視器具有多個限制限制,以防止使用者發送過多查詢。 此類行為可能會使系統後端資源超載,並危及服務回應能力。 以下限制旨在保護客戶免受中斷,並確保一致的服務級別。 使用者限制和限制設計為僅影響極端使用方案,不應與典型使用情況相關。


| Measure | 每個使用者的限制 | 描述 |
|:---|:---|:---|
| 併發查詢 | 5 | 如果用戶已經運行了 5 個查詢,則任何新查詢都放在每個用戶併發佇列中。 當其中一個正在運行的查詢結束時,下一個查詢將從佇列中拉出並啟動。 這不包括來自警報規則的查詢。
| 併發佇列中的時間 | 2.5 分鐘 | 如果查詢在佇列中存在超過 2.5 分鐘而不啟動,則它將使用代碼 429 的 HTTP 錯誤回應終止。 |
| 併發佇列中查詢總數 | 40 | 一旦佇列中的查詢數達到 40,任何其他查詢都將通過 HTTP 錯誤代碼 429 拒絕。 此數位是可同時運行的 5 個查詢的補充。 |
| 查詢率 | 每 30 秒 200 個查詢 | 這是單個用戶可以將查詢提交到所有工作區的總體速率。  此限制適用於由可視化部分(如 Azure 儀表板和日誌分析工作區摘要頁)啟動的程式設計查詢或查詢。 |

- 優化查詢,如 Azure[監視器 中的「優化日誌查詢」中](../articles/azure-monitor/log-query/query-optimization.md)所述。
- 儀錶板和工作簿可以在單個檢視中包含多個查詢,這些查詢每次載入或刷新時都會生成突發的查詢。 請考慮將它們分解為可按需載入的多個檢視。 
- 在 Power BI 中,請考慮僅提取聚合結果,而不是原始日誌。
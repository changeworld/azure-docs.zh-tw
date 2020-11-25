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
ms.openlocfilehash: ff5d04a2923f16c763e1529ecb365f60d6275ca2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027792"
---
### <a name="general-query-limits"></a>一般查詢限制

| 限制 | 描述 |
|:---|:---|
| 查詢語言 | Azure 監視器使用與 Azure 資料總管相同的 [Kusto 查詢語言](/azure/kusto/query/)。 如需 Azure 監視器中不支援的 KQL 語言元素，請參閱 [Azure 監視器記錄查詢語言差異](/azure/data-explorer/kusto/query/)。 |
| Azure 區域 | 當資料的範圍跨越多個 Azure 區域中的 Log Analytics 工作區時，記錄查詢可能會承受過多的負荷。 如需詳細資訊，請參閱[查詢限制](../articles/azure-monitor/log-query/scope.md#query-scope-limits)。 |
| 跨資源查詢 | 單一查詢中的 Application Insights 資源和 Log Analytics 工作區數目上限為100。<br>View Designer 不支援跨資源查詢。<br>新的 scheduledQueryRules API 支援記錄警示中的跨資源查詢。<br>如需詳細資訊，請參閱[跨資源查詢限制](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits)。 |

### <a name="user-query-throttling"></a>使用者查詢節流
Azure 監視器有數個節流限制，可防止使用者傳送過多的查詢。 這類行為可能會導致系統後端資源多載，並損害服務回應能力。 下列限制旨在保護客戶避免遇到業務中斷，並確保一致的服務層級。 使用者節流和限制旨在影響極端使用案例，並且與一般使用方式無關。


| Measure | 每位使用者限制 | 描述 |
|:---|:---|:---|
| 並行查詢 | 5 | 如果該使用者已有 5 個查詢正在執行，則會將所有新查詢放在每個使用者的並行佇列中。 當其中一個正在執行的查詢結束時，會從佇列中提取下一個查詢並啟動。 這不包含來自警示規則的查詢。
| 並行佇列中的時間 | 3 分鐘 | 如果查詢停留在佇列中的時間超過3分鐘而未啟動，則會以錯誤碼 429 的 HTTP 錯誤回應終止。 |
| 並行佇列中的查詢總數 | 200 | 一旦佇列中的查詢數目達到 200，任何其他查詢都會以 HTTP 錯誤碼 429 拒絕。 此數目是除了可同時執行的 5 個查詢以外的數目。 |
| 查詢速率 | 每 30 秒 200 個查詢 | 這是單一使用者可以向所有工作區提交查詢的整體速率。  這項限制適用於程式設計查詢或由視覺效果組件 (例如 Azure 儀表板和 Log Analytics 工作區摘要頁面) 所起始的查詢。 |

- 如[在 Azure 監視器中最佳化記錄查詢](../articles/azure-monitor/log-query/query-optimization.md)中所述，將查詢最佳化。
- 儀表板和活頁簿可以在單一檢視中包含多個查詢，其會在每次載入或重新整理時產生高載查詢。 請考慮將這些查詢拆分為視需要載入的多個檢視。 
- 在 Power BI 中，請考慮僅擷取匯總的結果，而不是原始記錄。
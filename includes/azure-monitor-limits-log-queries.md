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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82072632"
---
### <a name="general-query-limits"></a>一般查詢限制

| 限制 | 說明 |
|:---|:---|
| 查詢語言 | Azure 監視器使用與 Azure 資料總管相同的[Kusto 查詢語言](/azure/kusto/query/)。 請參閱 Azure 監視器 Azure 監視器中不支援之 KQL 語言元素的[記錄查詢語言差異](../articles/azure-monitor/log-query/data-explorer-difference.md)。 |
| Azure 區域 | 當資料跨越多個 Azure 區域中的 Log Analytics 工作區時，記錄查詢可能會遇到過多的額外負荷。 如需詳細資訊，請參閱[查詢限制](../articles/azure-monitor/log-query/scope.md#query-limits)。 |
| 跨資源查詢 | 單一查詢中的最大 Application Insights 資源和 Log Analytics 工作區數目限制為100。<br>View Designer 不支援跨資源查詢。<br>新的 scheduledQueryRules API 支援記錄警示中的跨資源查詢。<br>如需詳細資訊，請參閱[跨資源查詢限制](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits)。 |

### <a name="user-query-throttling"></a>使用者查詢節流
Azure 監視器有數個節流限制，可防止傳送過多查詢的使用者。 這類行為可能會多載系統後端資源，並危及服務回應能力。 下列限制的設計目的是為了保護客戶免于中斷，並確保一致的服務層級。 使用者節流和限制是設計成隻會影響極端使用案例，而且不應該與一般使用方式相關。


| Measure | 每位使用者限制 | 說明 |
|:---|:---|:---|
| 並行查詢 | 5 | 如果已經有5個查詢正在執行，則所有新的查詢都會放在個別使用者並行佇列中。 當其中一個執行中的查詢結束時，將會從佇列中提取下一個查詢並啟動。 這不包含來自警示規則的查詢。
| 平行存取佇列中的時間 | 2.5 分鐘 | 如果查詢位於佇列中超過2.5 分鐘而不啟動，則會以具有代碼429的 HTTP 錯誤回應來終止。 |
| 平行存取佇列中的查詢總數 | 40 | 一旦佇列中的查詢數目達到40，任何其他查詢都會遭到拒絕，並產生 HTTP 錯誤碼429。 此數目是除了可同時執行的5個查詢以外的數位。 |
| 查詢速率 | 每30秒200個查詢 | 這是單一使用者將查詢提交至所有工作區的整體速率。  這項限制適用于視覺化元件（例如 Azure 儀表板和 Log Analytics 工作區摘要頁面）所起始的程式設計查詢或查詢。 |

- 如[Azure 監視器中優化記錄查詢](../articles/azure-monitor/log-query/query-optimization.md)中所述，將您的查詢優化。
- 儀表板和活頁簿可以在單一視圖中包含多個查詢，以在每次載入或重新整理時產生高載的查詢。 請考慮將它們分解成多個視需要載入的視圖。 
- 在 Power BI 中，請考慮只解壓縮匯總的結果，而不是原始記錄。
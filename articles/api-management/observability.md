---
title: Azure API 管理中的可檢視性 |Microsoft Docs
description: Azure API 管理中所有可檢視性選項的總覽。
services: api-management
documentationcenter: ''
author: begim
manager: alberts
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/01/2020
ms.author: apimpm
ms.openlocfilehash: 1ebebed465952bbb5d3e8f82ae1c7776c441c6b0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096163"
---
# <a name="observability-in-azure-api-management"></a>Azure API 管理中的可檢視性

可檢視性能夠從其產生的資料中瞭解系統的內部狀態，並能夠探索該資料，以回答發生什麼事和原因的問題。 

Azure API 管理可協助組織集中管理所有 Api。 因為它可作為所有 API 流量的單一進入點，所以它是觀察 Api 的理想位置。 

## <a name="observability-tools"></a>可檢視性工具

下表摘要說明 API 管理支援的所有工具，以觀察 Api，每個都適用于一或多個案例：

| 工具        | 適用於    | 資料延遲 | 保留 | 取樣 | 資料類型 | 啟用|
|:------------- |:-------------|:---- |:----|:---- |:--- |:---- 
| **[API 偵測器](api-management-howto-api-inspector.md)** | 測試和偵錯 | 立即 | 最後100追蹤 | 已針對每個要求開啟 | 要求追蹤 | 一律
| **內建分析** | 報告和監控 | 分鐘 | 存留期 | 100% | 報告和記錄 | 一律 |
| **[Azure 監視器計量](api-management-howto-use-azure-monitor.md)** | 報告和監控 | 分鐘 | 93天（升級為擴充） | 100% | 計量 | 一律 |
| **[Azure 監視器記錄](api-management-howto-use-azure-monitor.md)** | 報告、監視和調試 | 分鐘 | 31天/5GB （升級為擴充） | 100% （可調整） | 記錄 | 選擇性 |
| **[Azure Application Insights](api-management-howto-app-insights.md)** | 報告、監視和調試 | 秒 | 90天/5GB （升級為擴充） | 自訂 | 記錄、計量 | 選擇性 |
| **[透過 Azure 事件中樞進行記錄](api-management-howto-log-event-hubs.md)** | 自訂案例 | 秒 | 使用者管理 | 自訂 | 自訂 | 選擇性 |

### <a name="self-hosted-gateway"></a>自我裝載閘道

在雲端中，受管理的閘道支援上述所有工具。 [自我裝載閘道](self-hosted-gateway-overview.md)目前不會將診斷記錄傳送至 Azure 監視器。 不過，您可以在部署自我裝載閘道的本機上設定並保存記錄。 如需詳細資訊，請參閱[設定自我裝載閘道的雲端計量和記錄](how-to-configure-cloud-metrics-logs.md)，以及[設定自我裝載閘道的本機計量和記錄](how-to-configure-local-metrics-logs.md)。

## <a name="next-steps"></a>後續步驟

* [遵循教學課程以深入瞭解 API 管理](import-and-publish.md)

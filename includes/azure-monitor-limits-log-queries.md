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
ms.openlocfilehash: f2092753ab054a639e208aab4a6b7317c1bd5edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "74795934"
---
| 限制 | 描述 |
|:---|:---|
| 查詢語言 | Azure 監視器使用與 Azure 資料資源管理器相同的[庫s托查詢語言](/azure/kusto/query/)。 有關 Azure 監視器中不支援的 KQL 語言元素，請參閱[Azure 監視器日誌查詢語言差異](../articles/azure-monitor/log-query/data-explorer-difference.md)。 |
| Azure 區域 | 當資料跨越多個 Azure 區域中的日誌分析工作區時，日誌查詢可能會遇到過多的開銷。 有關詳細資訊，請參閱[查詢限制](../articles/azure-monitor/log-query/scope.md#query-limits)。 |
| 交叉資源查詢 | 單個查詢中的應用程式見解資源和日誌分析工作區的最大數量限制為 100。<br>視圖設計器不支援跨資源查詢。<br>新的 scheduledQueryRules API 支援記錄警示中的跨資源查詢。<br>有關詳細資訊，請參閱[跨資源查詢限制](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits)。 |
| 查詢限制 | 使用者在任何數量的工作區上每 30 秒只能有 200 個查詢。 此限制適用于程式設計查詢或由視覺化部分（如 Azure 儀表板和日誌分析工作區摘要頁）啟動的查詢。 |

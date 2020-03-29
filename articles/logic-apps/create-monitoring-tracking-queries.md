---
title: 在 Azure 監視器日誌中查看和創建邏輯應用的查詢
description: 在 Azure 監視日誌中查看和創建 Azure 邏輯應用的查詢
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 46989ed2468469443d4e91a1834bc20b12c25a1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76908068"
---
# <a name="view-and-create-queries-for-monitoring-and-tracking-in-azure-monitor-logs-for-azure-logic-apps"></a>查看和創建查詢，以便監視和跟蹤 Azure 邏輯應用的 Azure 監視器日誌

您可以查看從[Azure 監視器日誌](../log-analytics/log-analytics-overview.md)生成結果的基礎查詢，並創建基於特定條件篩選結果的查詢。 例如，您可以找到根據特定交換控制編號的訊息。 查詢使用[Kusto 查詢語言](https://aka.ms/LogAnalyticsLanguageReference)，如果要查看不同結果，可以對其進行編輯。 有關詳細資訊，請參閱[Azure 監視器日誌查詢](../azure-monitor/log-query/query-language.md)。

## <a name="prerequisites"></a>Prerequisites

* Log Analytics 工作區。 如果您沒有 Log Analytics 工作區，請了解[如何建立 Log Analytics 工作區](../azure-monitor/learn/quick-create-workspace.md)。

* 使用 Azure 監視器日誌記錄設置並將該資訊發送到日誌分析工作區的邏輯應用。 [瞭解如何為邏輯應用設置 Azure 監視器日誌](../logic-apps/monitor-logic-apps.md)。

* 如果使用集成帳戶，請確保已使用 Azure 監視器日誌記錄設置帳戶，以將該資訊發送到日誌分析工作區。 瞭解如何[為集成帳戶設置 Azure 監視器日誌記錄](../logic-apps/monitor-b2b-messages-log-analytics.md)。

## <a name="view-queries-behind-results"></a>查看結果後面的查詢

要查看或編輯在工作區摘要中生成結果的查詢，請按照以下步驟操作：

1. 在任何結果頁上，在底部，選擇 **"查看所有**"。

   ![查看所有結果](./media/create-monitoring-tracking-queries/logic-app-see-all.png)

   "日誌"頁將打開並顯示上一個結果頁後面的查詢。

   ![日誌頁 - 查詢檢視](./media/create-monitoring-tracking-queries/view-query-behind-results.png)

1. 在 **"日誌"** 頁上，您可以選擇以下選項：

   * 要將查詢結果視為表，請在查詢編輯器下選擇**表**。

   * 要更改查詢，請更新查詢字串，然後選擇 **"運行"** 以查看表中的結果。

## <a name="create-your-own-query"></a>創建您自己的查詢

要查找或篩選基於特定屬性或值的結果，可以通過從空查詢開始或使用常設查詢來創建自己的查詢。 有關詳細資訊，請參閱 Azure[監視器 中的日誌查詢入門](../azure-monitor/log-query/get-started-queries.md)。

1. 在[Azure 門戶](https://portal.azure.com)中，查找並選擇日誌分析工作區。

1. 在工作區功能表中，在 **"常規"** 下，選擇 **"日誌**"。

1. 從空查詢或任何可用的常設查詢開始。

   * 要檢查是否存在任何常設查詢可用，請在查詢工具列上選擇 **"依例查詢** > **歷史記錄**"（顯示以前查詢運行中的查詢）或選擇顯示預構建查詢的**查詢資源管理器**。

     例如，邏輯應用 B2B 解決方案提供以下預構建查詢：

     ![從"邏輯應用 B2B"解決方案開始預構建查詢](./media/create-monitoring-tracking-queries/b2b-prebuilt-queries.png)

   * 要從空查詢開始，在查詢編輯器中，開始鍵入查詢的[Kusto 查詢語言](../azure-monitor/log-query/query-language.md)。

     ![從空查詢開始](./media/create-monitoring-tracking-queries/create-query-from-blank.png)

## <a name="next-steps"></a>後續步驟

* [AS2 跟蹤架構](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12 跟蹤架構](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [自訂追蹤結構描述](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)
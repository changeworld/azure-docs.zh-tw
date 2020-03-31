---
title: 設置警報和通知（Azure 門戶）
description: 使用 Azure 入口網站建立 SQL Database 的警示，在符合指定條件時觸發通知或自動化。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: 67c47b35e84a93d7d9032ad55b425ae2bb6971fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79209533"
---
# <a name="create-alerts-for-azure-sql-database-and-azure-synapse-analytics-databases-using-azure-portal"></a>使用 Azure 門戶為 Azure SQL 資料庫和 Azure 同步分析資料庫創建警報

## <a name="overview"></a>總覽

本文介紹如何使用 Azure 門戶在 Azure SQL 資料庫和 Azure 突觸分析（以前的 Azure SQL 資料倉儲）中為單個、池和資料倉儲資料庫設置警報。 當某些計量 (例如，資料庫大小或 CPU 使用量) 閾值時，警示可傳送電子郵件給您或呼叫 Webhook。 本文也提供設定警示期間的最佳做法。

> [!IMPORTANT]
> 此功能還無法在受控執行個體中取得。 或者，您可以根據[動態管理檢視](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) \(機器翻譯\)，針對某些計量使用 SQL 代理程式傳送電子郵件警示。

您可以收到以您 Azure 服務的監視計量或事件為基礎的警示。

* **指標值**- 當指定指標的值跨越您按任一方向分配的閾值時，警報將觸發。 也就是說，當先符合條件而之後該條件不再符合時，兩方面皆會觸發。
* **活動日誌事件**- 警報可以*觸發每個*事件，或者，僅當發生一定數量的事件時。

您可以在警示觸發時，設定警示執行下列動作︰

* 傳送電子郵件通知給服務管理員和共同管理員
* 傳送電子郵件給您指定的其他電子郵件。
* 呼叫 Webhook

您可以透過下列方式設定和取得有關警示規則的資訊

* [Azure 門戶](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [電源外殼](../azure-monitor/platform/alerts-classic-portal.md)
* [命令列介面 （CLI）](../azure-monitor/platform/alerts-classic-portal.md)
* [Azure 監視器 REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>使用 Azure 入口網站建立計量的警示規則

1. 在 [入口網站](https://portal.azure.com/)中，找到您要監視的資源並選取。
2. 在"監視"部分中選擇 **"警報**"。 不同資源的文字和圖示會有些許不同。  

   ![監視](media/sql-database-insights-alerts-portal/Alerts.png)
  
3. 選擇 **"新建警報規則"** 按鈕以打開 **"創建規則**"頁。
  ![建立規則](media/sql-database-insights-alerts-portal/create-rule.png)

4. 在 **"條件"** 部分中，按一下"**添加**"。
  ![定義條件](media/sql-database-insights-alerts-portal/create-rule.png)
5. 在 **"配置信號邏輯**"頁中，選擇信號。
  ![選擇信號](media/sql-database-insights-alerts-portal/select-signal.png)。
6. 選擇信號（如 CPU**百分比**）後，將顯示 **"配置信號邏輯**頁"。
  ![設定訊號邏輯](media/sql-database-insights-alerts-portal/configure-signal-logic.png)
7. 在此頁上，配置該閾數值型別、運算子、聚合類型、閾值、聚合細微性和評估頻率。 然後按一下 [完成]****。
8. 在 **"創建"規則**上，選擇現有**操作組**或創建新組。 操作組使您能夠定義發生警示準則時要執行的操作。
  ![定義操作組](media/sql-database-insights-alerts-portal/action-group.png)

9. 定義規則的名稱，提供可選說明，為規則選擇嚴重性級別，選擇是否在創建規則時啟用規則，然後按一下"**創建規則警報**"以創建指標規則警報。

在 10 分鐘內，警報處於活動狀態，並如前面所述觸發。

## <a name="next-steps"></a>後續步驟

* 深入了解 [在警示中設定 webhook](../azure-monitor/platform/alerts-webhooks.md)。

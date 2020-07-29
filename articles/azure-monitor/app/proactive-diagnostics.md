---
title: Azure Application Insights 中的智慧型偵測 | Microsoft Docs
description: Application Insights 會自動深入分析您的 App 遙測，並且警告您有潛在的問題。
ms.topic: conceptual
ms.date: 02/07/2019
ms.openlocfilehash: 24ed8eefb7e07930cbd123fbe4081d555f0c27c3
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87305868"
---
# <a name="smart-detection-in-application-insights"></a>Application Insights 中的智慧型偵測
 「智慧偵測」會自動警告您 Web 應用程式中的可能效能問題和失敗異常。 它會針對您應用程式傳送給 [Application Insights](./app-insights-overview.md) 的遙測執行主動式分析。 如果失敗率急遽上升或是用戶端或伺服器效能出現異常模式，您就會收到警示。 這項功能不需要進行任何設定。 只要您的應用程式傳送的遙測足夠，它就能發揮作用。

您可以從所收到的電子郵件和從 [智慧偵測] 刀鋒視窗，存取「智慧偵測」所發出的偵測。

## <a name="review-your-smart-detections"></a>檢閱智慧型偵測
您可以透過兩種方式探索偵測︰

* **接收來自 Application Insights 的電子郵件** 。 以下是典型範例︰
  
    ![電子郵件警示](./media/proactive-diagnostics/03.png)
  
    按一下大型按鈕以在入口網站中開啟更多詳細資料。
* Application Insights 中的 [智慧偵測] 刀鋒視窗。 選取 [調查] 功能表下的 [智慧偵測]，以查看最近偵測的清單。

![檢視最近的偵測](./media/proactive-diagnostics/04.png)

選取偵測以查看其詳細資料。

## <a name="what-problems-are-detected"></a>可偵測到哪些問題？
「智慧偵測」會偵測並通知各種問題，例如：

* [智慧型偵測 - 失敗異常](./proactive-failure-diagnostics.md)。 我們使用機器學習服務來為應用程式設定預期的失敗要求率，其會與負載和其他因素相互關聯。 如果失敗率超過預期限制，我們便會傳送警示。
* [智慧型偵測 - 效能異常](./proactive-performance-diagnostics.md)。 如果作業或相依性持續時間的回應時間變得比歷史基準慢，或如果我們在回應時間或頁面載入時間發現異常模式，您就會收到通知。   
* 一般的效能降低和問題，例如[追蹤效能降低](./proactive-trace-severity.md)、[記憶體流失](./proactive-potential-memory-leak.md)、[例外狀況數量異常升高](./proactive-exception-volume.md)和[安全性反向模式](./proactive-application-security-detection-pack.md)。

(每個通知中的說明連結會帶您前往相關文章。)

## <a name="smart-detection-email-notifications"></a>智慧型偵測電子郵件通知

除了標示為_預覽_狀態的規則之外，所有智慧偵測規則均預設為發現到偵測時，傳送電子郵件通知。

開啟智慧型偵測 [設定] 刀鋒視窗，然後選取規則，即會開啟 [編輯規則] 刀鋒視窗，然後便可設定特定智慧偵測規則的電子郵件通知。

或者，您也可以使用 Azure Resource Manager 範本來變更設定。 如需詳細資訊，請參閱[使用 Azure Resource Manager 範本來管理 Application Insights 智慧偵測規則](./proactive-arm-config.md)。

## <a name="video"></a>影片

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>後續步驟
這些診斷工具可協助您檢查來自您的應用程式的遙測︰

* [計量瀏覽器](../platform/metrics-charts.md)
* [搜尋總管](./diagnostic-search.md)
* [分析 - 功能強大的查詢語言](../log-query/get-started-portal.md)

「智慧型偵測」是全自動的。 但是，或許您會想要再設定一些警示？

* [手動設定的度量警示](../platform/alerts-log.md)
* [可用性 Web 測試](./monitor-web-app-availability.md) 


---
title: 在 Azure 監視器記錄中查看並建立邏輯應用程式的查詢
description: 在 Azure Logic Apps 的 Azure 監視器記錄中查看並建立查詢
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: cb1af437fc663fcb95c768ec295862209a26064e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87090394"
---
# <a name="view-and-create-queries-for-monitoring-and-tracking-in-azure-monitor-logs-for-azure-logic-apps"></a>在 Azure Logic Apps 的 Azure 監視器記錄中，查看並建立用於監視和追蹤的查詢

您可以查看從 [Azure 監視器記錄](../azure-monitor/log-query/log-query-overview.md) 產生結果的基礎查詢，以及建立根據您的特定準則來篩選結果的查詢。 例如，您可以找到根據特定交換控制編號的訊息。 查詢會使用 [Kusto 查詢語言](https://aka.ms/LogAnalyticsLanguageReference)，如果您想要查看不同的結果，則可加以編輯。 如需詳細資訊，請參閱 [Azure 監視器記錄查詢](../azure-monitor/log-query/query-language.md)。

## <a name="prerequisites"></a>Prerequisites

* Log Analytics 工作區。 如果您沒有 Log Analytics 工作區，請了解[如何建立 Log Analytics 工作區](../azure-monitor/learn/quick-create-workspace.md)。

* 使用 Azure 監視器記錄來設定的邏輯應用程式，並將該資訊傳送至 Log Analytics 工作區。 瞭解 [如何設定邏輯應用程式的 Azure 監視器記錄](../logic-apps/monitor-logic-apps.md)。

* 如果您是使用整合帳戶，請確定您已使用 Azure 監視器記錄來設定帳戶，以將該資訊傳送至 Log Analytics 工作區。 瞭解如何 [設定整合帳戶的 Azure 監視器記錄](../logic-apps/monitor-b2b-messages-log-analytics.md)。

## <a name="view-queries-behind-results"></a>查看結果背後的查詢

若要在您的工作區摘要中查看或編輯產生結果的查詢，請遵循下列步驟：

1. 在 [任何結果] 頁面上，選取底部的 [ **查看全部**]。

   ![查看所有結果](./media/create-monitoring-tracking-queries/logic-app-see-all.png)

   [記錄檔] 頁面隨即開啟，並顯示先前 [結果] 頁面後方的查詢。

   ![記錄頁面-查詢檢視](./media/create-monitoring-tracking-queries/view-query-behind-results.png)

1. 在 [ **記錄** 檔] 頁面上，您可以選取下列選項：

   * 若要以資料表形式來查看查詢結果，請在 [查詢編輯器] 底下選取 [ **資料表**]。

   * 若要變更查詢，請更新查詢字串，然後選取 [ **執行** ] 以查看資料表中的結果。

## <a name="create-your-own-query"></a>建立您自己的查詢

若要根據特定屬性或值來尋找或篩選結果，您可以從空的查詢開始，或使用現有的查詢來建立自己的查詢。 如需詳細資訊，請參閱 [Azure 監視器中的開始使用記錄查詢](../azure-monitor/log-query/get-started-queries.md)。

1. 在 [Azure 入口網站](https://portal.azure.com)中，尋找並選取您的 Log Analytics 工作區。

1. 在工作區功能表上的 **[一般**] 底下，選取 [ **記錄**]。

1. 從空的查詢或任何可用的常設查詢開始。

   * 若要檢查是否有任何現有的查詢可用，請在 [查詢] 工具列上，選取 [**範例查詢**歷程  >  **記錄**] （顯示先前查詢執行的查詢），或選取 [**查詢瀏覽器**]，以顯示預建的查詢。

     例如，Logic Apps B2B 解決方案會提供下列預建查詢：

     ![從「Logic Apps B2B」解決方案預建查詢開始](./media/create-monitoring-tracking-queries/b2b-prebuilt-queries.png)

   * 若要從空的查詢開始，請在查詢編輯器中，開始輸入查詢的 [Kusto 查詢語言](../azure-monitor/log-query/query-language.md) 。

     ![開始使用空白查詢](./media/create-monitoring-tracking-queries/create-query-from-blank.png)

## <a name="next-steps"></a>接下來的步驟

* [AS2 追蹤架構](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12 追蹤架構](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [自訂追蹤結構描述](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)

---
title: 在 Azure 應用程式 Insights 中設定警示
description: 獲知回應時間緩慢、例外狀況，以及 Web 應用程式中的其他效能或使用量變更。
ms.topic: conceptual
ms.date: 01/23/2019
ms.reviewer: lagayhar
ms.subservice: alerts
ms.openlocfilehash: 28fd59556a586b85a6d3caf188d9e02c11d31e3b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80295086"
---
# <a name="set-alerts-in-application-insights"></a>在 Application Insights 中設定警示

[Azure Application Insights][start] 可在 Web 應用程式中發生效能或使用量計量變更時對您發出警示。 

Application Insights 會在[多種平台][platforms]上監視即時應用程式，協助您診斷效能問題，以及了解使用模式。

有多種警示類型：

* 計量[**警示**](../../azure-monitor/platform/alerts-metric-overview.md)會在計量超出某個期間的臨界值（例如回應時間、例外狀況計數、CPU 使用量或頁面流覽）時告訴您。
* [**記錄警示**](../../azure-monitor/platform/alerts-unified-log.md)可用來描述警示信號以自訂 Kusto 查詢為基礎的警示。
* [**Web 測試**][availability]會在您的網站無法在網際網路上使用或回應速度很慢時通知您。 [深入了解][availability]。
* [**主動診斷**](../../azure-monitor/app/proactive-diagnostics.md)會自動設定成通知您異常的效能模式。

## <a name="how-to-set-an-exception-alert-using-custom-log-search"></a>如何使用自訂記錄搜尋來設定例外狀況警示

在本節中，我們將逐步解說如何設定以查詢為基礎的例外狀況警示。 在此範例中，假設在過去24小時內失敗率大於10% 時，我們想要發出警示。

1. 移至 Azure 入口網站中的應用程式深入解析資源。
2. 在左側的 [設定] 下，按一下 [**警示**]。

    ![在左側的 [設定] 按一下 [警示]](./media/alerts/1appinsightalert.png)

3. 在 [警示] 索引標籤的頂端，選取 [**新增警示規則**]。

     ![按一下 [警示] 索引標籤頂端的 [新增警示規則]](./media/alerts/2createalert.png)

4. 應自動選取您的資源。 若要設定條件，請按一下 [**新增條件**]。

    ![按一下 [新增條件]](./media/alerts/3addcondition.png)

5. 在 [設定信號邏輯] 索引標籤中，選取**自訂記錄搜尋**

    ![按一下 [自訂記錄搜尋]](./media/alerts/4customlogsearch.png)

6. 在 [自訂記錄搜尋] 索引標籤的 [搜尋查詢] 方塊中，輸入您的查詢。 在此範例中，我們將使用下列 Kusto 查詢。
    ```kusto
    let percentthreshold = 10;
    let period = 24h;
    requests
    | where timestamp >ago(period)
    | summarize requestsCount = sum(itemCount)
    | project requestsCount, exceptionsCount = toscalar(exceptions | where timestamp >ago(period) | summarize sum(itemCount))
    | extend exceptionsRate = toreal(exceptionsCount)/toreal(requestsCount) * 100
    | where exceptionsRate > percentthreshold

    ```

    ![搜尋查詢方塊中的類型查詢](./media/alerts/5searchquery.png)
    
    > [!NOTE]
    > 您也可以將這些步驟套用至其他類型的查詢式警示。 若要深入瞭解 Kusto 查詢語言，請前往此[Kusto 開始](https://docs.microsoft.com/azure/kusto/concepts/)使用檔或此[SQL to Kusto](https://docs.microsoft.com/azure/kusto/query/sqlcheatsheet)功能提要

7. 在 [警示邏輯] 底下，選擇是否以結果數目或計量量測為基礎。 然後挑選 [條件] （大於、等於、小於）和 [臨界值]。 當您變更這些值時，您可能會注意到條件預覽句子會變更。 在此範例中，我們會使用「等於」。

    ![在 [警示邏輯] 底下，從根據和條件提供的選項中選擇，然後輸入閾值](./media/alerts/6alertlogic.png)

8. 在 [評估依據] 底下，設定 [週期] 和 [頻率]。 此處的期間必須符合我們在上述查詢中為 period 所放的值。 然後按一下 [**完成**]。

    ![在底部設定 [期間] 和 [頻率]，然後按一下 [完成]](./media/alerts/7evaluate.png)

9. 我們現在會看到我們以估計的每月成本來建立的條件。 在[[動作群組]](../platform/action-groups.md)底下，您可以建立新的群組，或選取現有的群組。 如有需要，您可以自訂動作。

    ![按一下 [動作群組] 底下的 [選取或建立] 按鈕](./media/alerts/8actiongroup.png)

10. 最後，新增警示詳細資料（警示規則名稱、描述、嚴重性）。 當您完成時，請按一下底部的 [**建立警示規則**]。

    ![在 [警示詳細資料] 下，輸入您的警示規則名稱，撰寫描述並挑選嚴重性](./media/alerts/9alertdetails.png)

## <a name="how-to-unsubscribe-from-classic-alert-e-mail-notifications"></a>如何取消訂閱傳統警示電子郵件通知

本節適用于**傳統可用性警示**、**傳統 Application Insights 計量警示**和**傳統失敗異常警示**。

如果下列任何一項適用，您就會收到這些傳統警示的電子郵件通知：

* 您的電子郵件地址會列在 [警示規則] 設定的 [通知電子郵件收件者] 欄位中。

* 會啟用將電子郵件通知傳送給持有訂用帳戶特定角色之使用者的選項，而且您會針對該特定 Azure 訂用帳戶保存個別的角色。

![警示通知螢幕擷取畫面](./media/alerts/alert-notification.png)

為了更有效地控制您的安全性和隱私權，我們通常建議您在 [**通知電子郵件**收件者] 欄位中明確指定傳統警示的通知收件者。 針對回溯相容性提供通知所有持有特定角色之使用者的選項。

若要取消訂閱由特定警示規則所產生的電子郵件通知，請從 [**通知電子郵件**收件者] 欄位移除您的電子郵件地址。

如果您的電子郵件地址未明確列出，建議您停用 [自動通知特定角色的所有成員] 選項，然後在 [通知電子郵件收件者] 欄位中列出所有需要接收該警示規則通知的使用者電子郵件。

## <a name="who-receives-the-classic-alert-notifications"></a>誰會收到 (傳統) 警示通知？

本節僅適用於傳統警示，並協助您將警示通知最佳化，以確保只有您所需的收件者會收到通知。 若要深入瞭解[傳統警示](../platform/alerts-classic.overview.md)和新警示體驗之間的差異，請參閱[警示總覽一文](../platform/alerts-overview.md)。 若要控制新警示體驗中的警示通知，請使用[動作群組](../platform/action-groups.md)。

* 我們建議針對傳統警示通知使用特定的收件者。

* 如果已啟用 [大量/群組]**** 核取方塊選項，系統就會將任何 Application Insights 計量 (包括可用性計量) 的警示傳送給訂用帳戶中具有擁有者、參與者或讀者角色的使用者。 實際上，「所有」__ 有權存取 Application Insights 資源訂用帳戶的使用者都在涵蓋範圍內，而且將會收到通知。

> [!NOTE]
> 如果您目前使用 [大量/群組]**** 核取方塊選項並停用它，您將無法還原變更。

如果您需要根據使用者的角色來通知他們，請使用新警示體驗/近乎即時警示。 使用[動作群組](../platform/action-groups.md)，您可以為具有任一個參與者/擁有者/讀者角色 (不會結合來作為單一選項) 的使用者設定電子郵件通知。

## <a name="automation"></a>自動化
* [使用 PowerShell 自動設定警示](../../azure-monitor/app/powershell-alerts.md)
* [使用 Webhook 自動回應警示](../../azure-monitor/platform/alerts-webhooks.md)

## <a name="see-also"></a>請參閱
* [可用性 Web 測試](../../azure-monitor/app/monitor-web-app-availability.md)
* [自動化設定警示](../../azure-monitor/app/powershell-alerts.md)
* [主動診斷](../../azure-monitor/app/proactive-diagnostics.md) 

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[client]: ../../azure-monitor/app/javascript.md
[platforms]: ../../azure-monitor/app/platforms.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
[start]: ../../azure-monitor/app/app-insights-overview.md


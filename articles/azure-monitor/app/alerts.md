---
title: 在 Azure 應用程式見解中設置警報
description: 獲知回應時間緩慢、例外狀況，以及 Web 應用程式中的其他效能或使用量變更。
ms.topic: conceptual
ms.date: 01/23/2019
ms.reviewer: lagayhar
ms.subservice: alerts
ms.openlocfilehash: 28fd59556a586b85a6d3caf188d9e02c11d31e3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295086"
---
# <a name="set-alerts-in-application-insights"></a>在 Application Insights 中設定警示

[Azure Application Insights][start] 可在 Web 應用程式中發生效能或使用量計量變更時對您發出警示。 

Application Insights 會在[多種平台][platforms]上監視即時應用程式，協助您診斷效能問題，以及了解使用模式。

警報有多種類型：

* [**指標警報**](../../azure-monitor/platform/alerts-metric-overview.md)告訴您指標在某些時間段內何時超過閾值 -例如回應時間、異常計數、CPU 使用率或網頁檢視。
* [**日誌警報**](../../azure-monitor/platform/alerts-unified-log.md)用於描述警報信號基於自訂 Kusto 查詢的警報。
* [**Web 測試**][availability]會在您的網站無法在網際網路上使用或回應速度很慢時通知您。 [深入了解][availability]。
* [**主動診斷**](../../azure-monitor/app/proactive-diagnostics.md)會自動設定成通知您異常的效能模式。

## <a name="how-to-set-an-exception-alert-using-custom-log-search"></a>如何使用自訂日誌搜索設置異常警報

在本節中，我們將介紹如何設置基於查詢的異常警報。 對於此示例，假設在過去 24 小時內失敗率大於 10% 時，我們需要警報。

1. 轉到 Azure 門戶中的應用程式見解資源。
2. 在左側，在配置下按一下"**警報**"。

    ![在配置下的左側按一下警報](./media/alerts/1appinsightalert.png)

3. 在警報選項卡的頂部選擇 **"新建警報規則**"。

     ![在警報選項卡的頂部按一下新的警報規則](./media/alerts/2createalert.png)

4. 應自動選擇您的資源。 要設置條件，請按一下"**添加條件**"。

    ![按一下"添加條件"](./media/alerts/3addcondition.png)

5. 在配置信號邏輯選項卡中選擇**自訂日誌搜索**

    ![按一下自訂日誌搜索](./media/alerts/4customlogsearch.png)

6. 在自訂日誌搜索選項卡中，在"搜索查詢"框中輸入查詢。 在此示例中，我們將使用下面的 Kusto 查詢。
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

    ![在搜索查詢框中鍵入查詢](./media/alerts/5searchquery.png)
    
    > [!NOTE]
    > 還可以將這些步驟應用於其他類型的基於查詢的警報。 你可以從這個[Kusto 入門文檔](https://docs.microsoft.com/azure/kusto/concepts/)或此[SQL 到 Kusto 備忘單](https://docs.microsoft.com/azure/kusto/query/sqlcheatsheet)中瞭解有關 Kusto 查詢語言的更多資訊

7. 在"警報邏輯"下，選擇它是基於結果數還是指標度量。 然後選擇條件（大於、等於、小於）和閾值。 更改這些值時，您可能會注意到條件預覽句子更改。 在此示例中，我們使用"等於"。

    ![在"警報"邏輯下，根據 和 條件提供的選項中進行選擇，然後鍵入閾值](./media/alerts/6alertlogic.png)

8. 在"基於評估"下，設置週期和頻率。 此處的期間必須與上面查詢中為期間放置的值匹配。 然後按一下 **"完成**"。

    ![在底部設置週期和頻率，然後按一下"完成"](./media/alerts/7evaluate.png)

9. 現在，我們看到了我們創建的條件與估計的每月成本。 在[下面的"操作組"](../platform/action-groups.md)下，您可以創建一個新組或選擇現有組。 如果需要，可以自訂操作。

    ![按一下操作組下的選擇或創建按鈕](./media/alerts/8actiongroup.png)

10. 最後添加警報詳細資訊（警報規則名稱、描述、嚴重性）。 完成後，按一下底部**的"創建警報規則**"。

    ![在警報詳細資訊下鍵入警報規則名稱，編寫說明並選擇嚴重性](./media/alerts/9alertdetails.png)

## <a name="how-to-unsubscribe-from-classic-alert-e-mail-notifications"></a>如何取消訂閱經典警報電子郵件通知

本節適用于**經典可用性警報**、**經典應用程式見解指標警報**和**經典故障異常警報**。

如果以下任何一項適用，您正在收到這些經典警報的電子郵件通知：

* 您的電子郵件地址列在警報規則設置中的"通知電子郵件收件者"欄位中。

* 將電子郵件通知發送給具有訂閱特定角色的使用者的選項已啟動，並且您持有該特定 Azure 訂閱的相應角色。

![警報通知螢幕截圖](./media/alerts/alert-notification.png)

為了更好地控制您的安全和隱私，我們通常建議您在 **"通知電子郵件收件者"** 欄位中顯式指定經典警報的通知收件者。 提供通知具有特定角色的所有使用者的選項，用於向後相容性。

要取消訂閱由特定警報規則生成的電子郵件通知，請從 **"通知電子郵件收件者"** 欄位中刪除您的電子郵件地址。

如果未明確列出您的電子郵件地址，我們建議您禁用自動通知所有成員某些角色的選項，而是在"通知"電子郵件中列出需要接收該警報規則通知的所有使用者電子郵件收件者欄位。

## <a name="who-receives-the-classic-alert-notifications"></a>誰會收到 (傳統) 警示通知？

本節僅適用於傳統警示，並協助您將警示通知最佳化，以確保只有您所需的收件者會收到通知。 要瞭解有關[經典警報](../platform/alerts-classic.overview.md)和新警報體驗之間的區別的詳細資訊，請參閱[警報概述文章](../platform/alerts-overview.md)。 要控制新警報體驗中的警報通知，請使用[操作組](../platform/action-groups.md)。

* 我們建議針對傳統警示通知使用特定的收件者。

* 如果已啟用 [大量/群組]**** 核取方塊選項，系統就會將任何 Application Insights 計量 (包括可用性計量) 的警示傳送給訂用帳戶中具有擁有者、參與者或讀者角色的使用者。 實際上，「所有」__ 有權存取 Application Insights 資源訂用帳戶的使用者都在涵蓋範圍內，而且將會收到通知。

> [!NOTE]
> 如果您目前使用 [大量/群組]**** 核取方塊選項並停用它，您將無法還原變更。

如果您需要根據使用者的角色來通知他們，請使用新警示體驗/近乎即時警示。 使用[動作群組](../platform/action-groups.md)，您可以為具有任一個參與者/擁有者/讀者角色 (不會結合來作為單一選項) 的使用者設定電子郵件通知。

## <a name="automation"></a>自動化
* [使用 PowerShell 自動設定警示](../../azure-monitor/app/powershell-alerts.md)
* [使用 Webhook 自動回應警示](../../azure-monitor/platform/alerts-webhooks.md)

## <a name="see-also"></a>另請參閱
* [可用性 Web 測試](../../azure-monitor/app/monitor-web-app-availability.md)
* [自動化設定警示](../../azure-monitor/app/powershell-alerts.md)
* [主動診斷](../../azure-monitor/app/proactive-diagnostics.md) 

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[client]: ../../azure-monitor/app/javascript.md
[platforms]: ../../azure-monitor/app/platforms.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
[start]: ../../azure-monitor/app/app-insights-overview.md


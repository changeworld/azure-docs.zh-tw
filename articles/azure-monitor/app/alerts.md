---
title: 在 Azure 應用程式 Insights 中設定警示
description: 獲知回應時間緩慢、例外狀況，以及 Web 應用程式中的其他效能或使用量變更。
ms.topic: conceptual
ms.date: 01/23/2019
ms.reviewer: lagayhar
ms.subservice: alerts
ms.openlocfilehash: 80759c94d7cc5b60b6e38a34b85fb64c3c18fd2e
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77666712"
---
# <a name="set-alerts-in-application-insights"></a>在 Application Insights 中設定警示

[Azure 應用程式 Insights][start]會向您發出 web 應用程式中效能或使用計量變更的警示。 

Application Insights 會在[各種不同的平臺][platforms]上監視您的即時應用程式，以協助您診斷效能問題，並瞭解使用模式。

有多種警示類型：

* 計量[**警示**](../../azure-monitor/platform/alerts-metric-overview.md)會在計量超出某個期間的臨界值（例如回應時間、例外狀況計數、CPU 使用量或頁面流覽）時告訴您。
* [**記錄警示**](../../azure-monitor/platform/alerts-unified-log.md)可用來描述警示信號以自訂 Kusto 查詢為基礎的警示。
* [**Web 測試**][availability]會在您的網站無法在網際網路上使用或回應速度很慢時，告訴您。 [詳細資訊][availability]。
* [**主動診斷**](../../azure-monitor/app/proactive-diagnostics.md)會自動設定成通知您異常的效能模式。

## <a name="set-a-metric-alert"></a>設定計量警示

開啟 [警示規則] 索引標籤，然後使用 [新增] 按鈕。

![在 [警示規則] 索引標籤中，選擇 [新增警示]。 將您的應用程式設定為要測量的資源，提供警示的名稱，然後選擇計量。](./media/alerts/01-set-metric.png)

* 設定其他屬性之前的資源。 **選擇 "(元件)" 資源** 。
* 您提供的警示名稱必須為資源群組 (不只是您的應用程式) 中的唯一名稱。
* 請小心注意系統要求您輸入臨界值時所使用的單位。
* 如果您勾選 [電子郵件擁有者] 方塊，系統會透過電子郵件，將警示傳給每個可以存取此資源群組的人員。 若要展開這一組人員，請將他們新增至 [資源群組或訂用帳戶](../../azure-monitor/app/resources-roles-access-control.md) (而非資源)。
* 如果您指定 [其他電子郵件]，系統會將警示傳送給這些人員或群組 (無論您是否核取 [電子郵件擁有者] 方塊)。 
* 如果您已設定回應通知的 Web 應用程式，請設定 [Webhook 位址](../../azure-monitor/platform/alerts-webhooks.md)。 系統會在警示啟動和解決時加以呼叫。 (不過請注意，查詢參數目前不會當作 Webhook 屬性傳遞)。
* 您可以停用或啟用警示：請參閱頂端的按鈕。

*我看不到 [新增警示] 按鈕。*

* 您是否使用組織帳戶？ 如果您有這個應用程式資源的擁有者或參與者存取權，您可以設定警示。 請查看 [存取控制] 索引標籤。[深入瞭解存取控制][roles]。

> [!NOTE]
> 在 [警示] 刀鋒視窗中，您會看到已經設定警示︰[主動診斷](../../azure-monitor/app/proactive-failure-diagnostics.md)。 自動警示會監視要求失敗率這一個特定度量。 除非您決定要停用主動警示，否則不需要設定自己的要求失敗率警示。
> 
> 

## <a name="see-your-alerts"></a>查看您的警示
當警示在非作用中與作用中之間變更狀態時，您會收到電子郵件。 

每個警示的目前狀態會顯示在 [警示規則] 索引標籤中。

警示下拉式清單中有最近活動的摘要：

![警示下拉式清單](./media/alerts/010-alert-drop.png)

狀態變更的歷程記錄位於活動記錄檔中：

![在 總覽 索引標籤上，按一下 設定、審核記錄](./media/alerts/09-alerts.png)

## <a name="how-alerts-work"></a>警示的運作方式
* 警示有三種狀態：「永遠不啟動」、「已啟動」和「已解決」。 「已啟動」表示您指定的條件在上次評估時為 true。
* 警示狀態變更時，會產生通知。 (如果警示條件在建立警示時已為 true，您可能在條件變為 false 之前都不會得到通知。)
* 如果您已勾選電子郵件方塊或提供電子郵件地址，則每個通知都會產生一封電子郵件。 您也可以於 [通知] 查看下拉式清單。
* 每次度量抵達時都會評估警示，除此之外則無。
* 評估會彙總前一個期間的度量，然後將其與臨界值進行比較，以決定新的狀態。
* 您選擇的期間會指定彙總度量的間隔。 它並不會影響評估警示的頻率：這會根據度量抵達的頻率而定。
* 如果經過一段時間後沒有特定度量的資料到達，此間距會在警示評估上，以及在度量總管的圖表上有不同的效果。 在計量總管中，如果沒看到資料的時間超過圖表的取樣間隔時間，則圖表會顯示 0 值。 但是以相同度量為基礎的警示不會重新接受評估，而且警示的狀態會維持不變。 
  
    當資料終於抵達時，圖表會跳回非零的值。 警示的評估作業會依您指定的期間，並根據可用的資料來進行。 如果新的資料點是期間內唯一可用的資料點，則彙總只會以該資料點為基礎。
* 即使您設定的期間較長，警示也可能會在警示和良好狀態之間經常變動。 如果度量值徘徊在臨界值附近，就會發生這種情形。 臨界值中沒有任何磁滯：轉換為警示時的值會與轉換為良好時的值相同。

## <a name="what-are-good-alerts-to-set"></a>哪些是好的設定警示？
這取決於您的應用程式。 開始時，最好不要設定太多度量。 花點時間查看您的應用程式執行時的度量圖表，以了解正常運作時的情形。 這個做法可協助您找出改善其效能的方式。 然後設定警示，在度量偏離正常區域時通知您。 

熱門的警示包括：

* [瀏覽器計量][client]（尤其是瀏覽器**頁面載入時間**）適用于 web 應用程式。 如果您的分頁有許多指令碼，您應該尋找**瀏覽器例外狀況**。 為了取得這些計量和警示，您必須設定[網頁監視][client]。
* Web 應用程式伺服器端的**伺服器回應時間**。 以及設定警示，注意這些計量，以查看高要求率時的差異是否不成比例：差異可能表示您的應用程式資源不足。 
* **伺服器例外狀況** - 若要查看它們，您只需要進行一些 [額外設定](../../azure-monitor/app/asp-net-exceptions.md)。

別忘了，[主動失敗率診斷](../../azure-monitor/app/proactive-failure-diagnostics.md)會自動監視應用程式以失敗碼回應要求的速率。

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

如果您的電子郵件地址未明確列出，建議您停用 [自動通知特定角色的所有成員] 選項，然後在 [通知電子郵件] 中列出所有需要接收該警示規則通知的使用者電子郵件[收件者] 欄位。

## <a name="who-receives-the-classic-alert-notifications"></a>誰會收到 (傳統) 警示通知？

本節僅適用於傳統警示，並協助您將警示通知最佳化，以確保只有您所需的收件者會收到通知。 若要深入瞭解[傳統警示](../platform/alerts-classic.overview.md)和新警示體驗之間的差異，請參閱[警示總覽一文](../platform/alerts-overview.md)。 若要控制新警示體驗中的警示通知，請使用[動作群組](../platform/action-groups.md)。

* 我們建議針對傳統警示通知使用特定的收件者。

* 如果已啟用 [大量/群組] 核取方塊選項，系統就會將任何 Application Insights 計量 (包括可用性計量) 的警示傳送給訂用帳戶中具有擁有者、參與者或讀者角色的使用者。 實際上，「所有」有權存取 Application Insights 資源訂用帳戶的使用者都在涵蓋範圍內，而且將會收到通知。

> [!NOTE]
> 如果您目前使用 [大量/群組] 核取方塊選項並停用它，您將無法還原變更。

如果您需要根據使用者的角色來通知他們，請使用新警示體驗/近乎即時警示。 使用[動作群組](../platform/action-groups.md)，您可以為具有任一個參與者/擁有者/讀者角色 (不會結合來作為單一選項) 的使用者設定電子郵件通知。

## <a name="automation"></a>Automation
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


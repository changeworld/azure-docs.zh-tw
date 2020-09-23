---
title: 設定計量審查程式警示
titleSuffix: Azure Cognitive Services
description: 如何使用電子郵件、web 和 Azure DevOps 的勾點設定您的計量審查程式警示。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: aahi
ms.openlocfilehash: a68622a921bb58513c7bb2154ccee81162a59c53
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934414"
---
# <a name="how-to-configure-alerts-and-get-notifications-using-a-hook"></a>How to：使用攔截設定警示和取得通知

計量顧問偵測到異常之後，將會根據警示設定，使用攔截來觸發警示通知。 警示設定可以搭配多個偵測設定使用，有各種參數可以自訂警示規則。

## <a name="create-a-hook"></a>建立掛勾

計量審查程式支援三種不同類型的勾點：電子郵件攔截、webhook 和 Azure DevOps。 您可以選擇適用于特定案例的應用程式。 

### <a name="email-hook"></a>電子郵件攔截

> [!Note]
> 計量建議程式資源管理員必須先設定 **電子郵件設定**，並將 SMTP 相關資訊輸入至計量審查程式，才能傳送異常警示。

若要建立電子郵件勾點，可使用下列參數： 

電子郵件攔截是要傳送至 **電子郵件** 中所指定之電子郵件地址的異常警示通道。 將會傳送兩種類型的警示電子郵件： *未提供資料* 摘要的警示，以及包含一或多個異常的 *附隨報告* 。 

|參數 |描述  |
|---------|---------|
| Name | 電子郵件掛勾的名稱 |
| 傳送電子郵件給| 傳送警示的電子郵件地址|
| 外部連結 | 此為選擇性欄位，可啟用自訂的重新導向，例如疑難排解附注。 |
| 自訂異常警示標題 | Title 範本支援 `${severity}` 、 `${alertSettingName}` 、 `${datafeedName}` 、 `${metricName}` 、 `${detectConfigName}` 、 `${timestamp}` `${topDimension}` 、、 `${incidentCount}` 、 `${anomalyCount}`

按一下 **[確定]** 之後，就會建立電子郵件勾點。 您可以在任何警示設定中使用它來接收異常警示。 

### <a name="web-hook"></a>Webhook

> [!Note]
> * 使用 **POST** 要求方法。
> * 要求本文會如下所示：  
    `{"timestamp":"2019-09-11T00:00:00Z","alertSettingGuid":"49635104-1234-4c1c-b94a-744fc920a9eb"}`
> * 建立或修改網頁攔截器時，會將 API 稱為空白要求本文的測試。 您的 API 必須傳回 200 HTTP 代碼。

Webhook 是計量顧問服務所提供之所有資訊的進入點，並會在觸發警示時呼叫使用者提供的 api。 所有警示都可以透過網路攔截傳送。

若要建立 webhook，您將需要新增下列資訊：

|參數 |描述  |
|---------|---------|
|端點     | 觸發警示時所要呼叫的 API 位址。        |
|使用者名稱/密碼 | 用於驗證 API 位址。 如果不需要驗證，請將此為黑色。         |
|標頭     | API 呼叫中的自訂標頭。        |

:::image type="content" source="../media/alerts/create-web-hook.png" alt-text="webhook 建立視窗。":::

當透過 webhook 推播通知時，您可以使用下列 Api 來取得警示的詳細資料。 設定要推送至的 API 服務中的 *時間戳記* 和 *alertSettingGuid* ，然後使用下列查詢： 
- `query_alert_result_anomalies`
- `query_alert_result_incidents`

### <a name="azure-devops"></a>Azure DevOps

計量審查程式也支援在 Azure DevOps 中自動建立工作專案，以在偵測到任何異常狀況時追蹤問題/bug。 所有警示都可以透過 Azure DevOps 勾點傳送。

若要建立 Azure DevOps 攔截，您將需要新增下列資訊：

|參數 |描述  |
|---------|---------|
| Name | 攔截的名稱 |
| 組織 | 您的 DevOps 所屬的組織 |
| Project | DevOps 中的特定專案。 |
| 存取權杖 |  用來驗證 DevOps 的權杖。 | 

> [!Note]
> 如果您想要讓計量建議程式根據異常警示建立工作專案，您需要授與寫入權限。 建立勾點之後，您可以在任何警示設定中使用它們。 在 [攔截設定] 頁面中管理您的 **勾** 點。

## <a name="add-or-edit-alert-settings"></a>新增或編輯警示設定

移至 [計量詳細資料] 頁面，以尋找 [計量詳細資料] 頁面左下角的 [ **警示設定** ] 區段。 它會列出所有適用于所選偵測設定的警示設定。 建立新的偵測設定時，不會有警示設定，也不會傳送任何警示。  
您可以使用 [ **新增**]、[ **編輯** ] 和 [ **刪除** ] 圖示來修改警示設定。

:::image type="content" source="../media/alerts/alert-setting.png" alt-text="警示設定功能表項目。":::

按一下 [ **新增** ] 或 [ **編輯** ] 按鈕，以取得可新增或編輯警示設定的視窗。

:::image type="content" source="../media/alerts/edit-alert.png" alt-text="新增或編輯警示設定":::

**警示設定名稱**：此警示設定的名稱。 它會顯示在警示電子郵件標題中。

攔截 **：要**傳送警示的勾點清單。

上述螢幕擷取畫面中標示的區段是一個偵測設定的設定。 您可以針對不同的偵測設定設定不同的警示設定。 在此視窗中，使用第三個下拉式清單來選擇目標設定。 

### <a name="filter-settings"></a>篩選設定 

以下是一個偵測設定的篩選設定。

**的警示** 有4個篩選異常選項：

* **所有數列的異常**狀況：警示中會包含所有異常。         
* **數列群組中的異常**：依維度值篩選數列。 設定某些維度的特定值。 只有當數列符合指定的值時，警示才會包含異常。       
* 我的**最愛系列中的異常**狀況：警示中只會包含標示為我的最愛的數列。        |
* **所有數列的前 N 個異常**：此篩選準則適用于您只在意值是在前 n 個數列中的數列。我們將回頭查看一些時間戳記，並檢查這些時間戳記的數列值是否在前 N 個。如果 "in top n" 計數大於指定的數目，則會在警示中包含異常。        |

**篩選異常選項** 是具有下列選項的額外篩選準則：

- **嚴重性** ：只有當異常嚴重性在指定的範圍內時，才會包含異常。
- 延遲 **：當**警示中觸發警示時，請暫時停止下一個 N 點 (期間) 的異常警示。
    - 延遲**類型**：設定為 [**數列**] 時，觸發的異常只會延遲其數列。 針對 **計量，觸發的異常**會將此計量中的所有序列延遲。
    - 延遲**數目**：) 延遲的 (期間點數目。
    - **重設為非連續** ：若選取此選項，觸發的異常將只會延遲接下來的 n 個後續異常。 如果下列其中一個資料點不是異常，將會從該時間點重設延遲;如果未選取，則觸發的異常會將下 n 個點 (期間) ，即使連續的資料點不是異常。
- **值** (選擇性) ：依值篩選。 只有符合條件的點值會包含異常。 如果您使用另一個度量的對應值，則兩個度量的維度名稱應該是一致的。

未篩選掉的異常會在警示中傳送。

### <a name="add-cross-metric-settings"></a>新增交叉度量設定

按一下 [警示設定] 頁面中的 [ **+ 新增交叉計量設定** ]，以新增另一個區段。

**操作員**選取器是每個區段的邏輯關聯性，以判斷是否傳送警示。


|運算子  |描述  |
|---------|---------|
|AND     | 只有當數列符合每個警示區段，而且所有資料點都是異常時，才傳送警示。 如果計量具有不同的維度名稱，則永遠不會觸發警示。         |
|或者     | 如果至少有一個區段包含異常，則傳送警示。         |

:::image type="content" source="../media/alerts/alert-setting-operator.png" alt-text="多個警示設定區段的運算子":::

## <a name="next-steps"></a>下一步

- [使用意見反應來調整異常偵測](anomaly-feedback.md)
- [診斷事件](diagnose-incident.md)。
- [設定計量和微調偵測組態](configure-metrics.md)
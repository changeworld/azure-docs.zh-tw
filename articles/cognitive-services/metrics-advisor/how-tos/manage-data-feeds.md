---
title: 管理計量審查程式中的資料摘要
titleSuffix: Azure Cognitive Services
description: 瞭解如何管理您已新增至計量審查程式的資料摘要。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: aahi
ms.openlocfilehash: 22a1be7231b730c83ca97b0f128e43258ed24533
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90933984"
---
# <a name="how-to-manage-your-data-feeds"></a>如何：管理您的資料摘要

瞭解如何在計量 Advisor 中管理您的上線資料摘要。 本文會引導您在計量監視器中管理資料摘要。

## <a name="edit-a-data-feed"></a>編輯資料摘要

> [!NOTE]
> 建立資料摘要之後，就無法變更下列詳細資料。 
> * 資料摘要識別碼
> * 建立時間
> * 尺寸
> * 來源類型
> * 細微度

只有資料摘要的系統管理員可以進行變更。 

若要暫停或重新啟用資料摘要：

1. 在 [資料摘要清單] 頁面上，按一下您想要在資料摘要上執行的作業。

2. 在 [資料摘要詳細資料] 頁面上，按一下 [ **狀態** ] 切換按鈕。

若要刪除資料摘要： 

1. 在 [資料摘要清單] 頁面上，按一下 [資料摘要] 上的 [ **刪除** ]。

2. 在 [資料摘要詳細資料] 頁面中，按一下 [ **刪除**]。

變更開始時間時，您必須再次驗證架構。 您可以使用 **編輯參數**來變更它。

##  <a name="backfill-your-data-feed"></a>回填您的資料摘要

選取 [  **回填** ] 按鈕以在時間戳記上觸發立即內嵌，以修正失敗的內嵌或覆寫現有的資料。
- 開始時間是內含的。
- 結束時間是獨佔的。
- 只有選取的範圍會重新觸發異常偵測。

:::image type="content" source="../media/datafeeds/backfill-datafeed.png" alt-text="回填資料摘要":::

## <a name="manage-permission-of-a-data-feed"></a>管理資料摘要的許可權

工作區存取是由計量建議程式資源所控制，該資源使用 Azure Active Directory 進行驗證。 另一層的許可權控制會套用至度量資料。

計量建議程式可讓您將許可權授與不同資料摘要的不同人員群組。 目前有兩種角色類型： 

- 系統管理員：擁有完整許可權可管理資料摘要的人員，包括修改和刪除。
- 檢視器：誰可以存取資料摘要的唯讀視圖。
 

## <a name="advanced-settings"></a>進階設定

建立新的資料摘要時，有數個選擇性的 advanced 設定，可以在 [資料摘要詳細資料] 頁面中加以修改。

### <a name="ingestion-options"></a>內嵌選項

* 內嵌**時間位移**：根據預設，會根據指定的資料細微性內嵌資料。 例如，具有 *每日* 時間戳記的度量將會在其時間戳記之後的一天內嵌。 您可以使用位移來延遲使用 *正數* 進行內嵌的時間，或使用 *負數* 來向前移動。

* **最大並行**存取：如果您的資料來源支援限制並行，請設定此參數。 否則，請保留預設設定。

* **停止重試**：如果資料內嵌失敗，則會在一段期間內自動重試。 句號的開頭是第一個資料內嵌發生的時間。 期間的長度是根據資料細微性來定義。 如果將預設值保留 (-1) ，則會根據以下的資料細微性來決定值。
    
    | 細微度       | 停止重試           |
    | :------------ | :--------------- |
    | 每日，自訂 ( # B0 = 1 天) 、每週、每月、每年     | 7 天          |
    | 每小時，自訂 ( # A0 1 天)        | 72小時 |

* **最小重試間隔**：您可以在重試從來源提取資料時，指定最小間隔。 如果將預設值保留 (-1) ，將會根據以下的資料細微性來決定重試間隔。
    
    | 細微度       | 最小重試間隔           |
    | :------------ | :--------------- |
    | 每日，自訂 ( # B0 = 1 天) 、每週、每月     | 30 分鐘          |
    | 每小時，自訂 ( # A0 1 天)       | 10 分鐘 |
    | 每年 | 1 日          |
 
### <a name="fill-gap-when-detecting"></a>偵測時填滿間距： 

> [!NOTE]
> 此設定不會影響您的資料來源，且不會影響入口網站上顯示的資料圖表。 自動填入只會在異常偵測期間發生。

某些時間序列不是連續的。 遺漏資料點時，計量審查程式會使用指定的值來填滿異常偵測，以獲得更好的精確度。
其選項包括： 

* 使用先前實際資料點的值。 預設會使用這個。
* 使用特定值。

### <a name="action-link-template"></a>動作連結範本： 

動作連結範本可用來預先定義可採取動作的 HTTP url，其中包含預留位置 `%datafeed` 、 `%metric` 、、 `%timestamp` `%detect_config` 和 `%tagset` 。 您可以使用此範本，從異常或事件重新導向至特定的 URL，以向下切入。

:::image type="content" source="../media/action-link-template.png" alt-text="回填資料摘要" lightbox="../media/action-link-template.png":::

當您填入 [動作] 連結之後，請按一下事件清單的 [動作] 選項以及事件樹狀目錄的滑鼠右鍵功能表上的 [ **移至動作] 連結** 。 以異常或事件的對應值取代動作連結範本中的預留位置。

| 預留位置 | 範例 | 留言 |
| ---------- | -------- | ------- |
| `%datafeed` | - | 資料摘要識別碼 |
| `%metric` | - | 度量識別碼 |
| `%detect_config` | - | 偵測設定識別碼 |
| `%timestamp` | - | 持續性事件異常或結束時間的時間戳記 |
| `%tagset` | `%tagset`, <br> `[%tagset.get("Dim1")]`, <br> `[ %tagset.get("Dim1", "filterVal")]` | 事件異常或前異常的維度值。   <br> `filterVal`用來篩選出方括弧內的相符值。   |

例子：

* 如果動作連結範本為 `https://action-link/metric/%metric?detectConfigId=%detect_config` ，
  * [動作] 連結 `https://action-link/metric/1234?detectConfigId=2345` 會移至 [計量] 下的異常或事件並偵測設定 `1234` `2345` 。

* 如果動作連結範本為 `https://action-link?[Dim1=%tagset.get('Dim1','')&][Dim2=%tagset.get('Dim2','')]` ， 
    * 當異常狀況時，動作連結會是 `https://action-link?Dim1=Val1&Dim2=Val2` `{ "Dim1": "Val1", "Dim2": "Val2" }` 。 
    * `https://action-link?Dim2=Val2`當異常為時，動作連結會是 `{ "Dim1": "", "Dim2": "Val2" } ` ，因為 `[Dim1=***&]` 針對維度值的空字串略過。 

* 如果動作連結範本為 `https://action-link?filter=[Name/Dim1 eq '%tagset.get('Dim1','')' and ][Name/Dim2 eq '%tagset.get('Dim2','')']` ， 
    * `https://action-link?filter=Name/Dim1 eq 'Val1' and Name/Dim2 eq 'Val2'`當異常狀況為時，動作連結會是 `{ "Dim1": "Val1", "Dim2": "Val2" }` ， 
    * `https://action-link?filter=Name/Dim2 eq 'Val2'`當異常是 `{ "Dim1": "", "Dim2": "Val2" }` 因為 `[Name/Dim1 eq '***' and ]` 維度值為空字串而略過時，動作連結就會是。 
   
### <a name="data-feed-not-available-alert-settings"></a>「資料摘要無法使用」警示設定

如果在從資料摘要開始內嵌所指定的寬限期內，未從來源內嵌資料，則資料摘要會被視為無法使用。 在此情況下，會觸發警示。

若要設定警示，您必須先 [建立](alerts.md#create-a-hook) 攔截。 警示會透過設定的勾點傳送。

* **寬限期**：如果沒有內嵌資料點，則會使用寬限期設定來決定何時傳送警示。 參考點是第一次內嵌的時間。 如果內嵌失敗，計量建議程式會繼續以資料細微性指定的固定間隔進行嘗試。 如果持續發生超過寬限期，則會傳送警示。

* **自動**延遲：當此選項設定為零時，每個 *無法使用* 的時間戳記都會觸發警示。 指定零以外的設定時，不會根據指定的設定來觸發第一個 *無法* 使用的時間戳記之後的連續時間戳記。

## <a name="next-steps"></a>後續步驟
- [設定計量和微調偵測組態](configure-metrics.md)
- [使用意見反應來調整異常偵測](anomaly-feedback.md)
- [診斷事件](diagnose-incident.md)。

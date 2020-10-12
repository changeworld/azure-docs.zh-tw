---
title: 使用 Azure 監視器來查看度量
titleSuffix: Azure Digital Twins
description: 瞭解如何在 Azure 監視器中查看 Azure 數位 Twins 計量。
author: baanders
ms.author: baanders
ms.date: 8/4/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: 084a823571281c91419a56b6212ddf6c44dd80bb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91322626"
---
# <a name="troubleshooting-azure-digital-twins-metrics"></a>針對 Azure 數位 Twins 進行疑難排解：計量

本文所述的計量會提供您 Azure 訂用帳戶中 Azure 數位 Twins 資源狀態的相關資訊。 Azure 數位 Twins 計量可協助您評估 Azure 數位 Twins 服務及其連線資源的整體健康情況。 這些使用者對應的統計資料可協助您瞭解 Azure 數位 Twins 的狀況，並協助您對問題進行根本原因分析，而不需要聯繫 Azure 支援。

預設會啟用計量。 您可以從 [Azure 入口網站](https://portal.azure.com)查看 Azure 數位 Twins 計量。

## <a name="how-to-view-azure-digital-twins-metrics"></a>如何查看 Azure 數位 Twins 計量

1. 建立 Azure 數位 Twins 實例。 您可以在 how [*to：設定實例和驗證*](how-to-set-up-instance-portal.md)中找到如何設定 Azure 數位 Twins 實例的指示。

2. 在 [Azure 入口網站](https://portal.azure.com) 中尋找您的 Azure 數位 Twins 實例 (您可以在入口網站的搜尋列) 中輸入其名稱，以開啟該實例的頁面。 

    從實例的功能表中，選取 [ **計量**]。
   
    :::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="顯示 Azure 數位 Twins 計量頁面的螢幕擷取畫面":::

    此頁面會顯示 Azure 數位 Twins 實例的計量。 您也可以從清單中選取您想要查看的計量，以建立其自訂的觀點。
    
3. 您可以選擇將計量資料傳送到事件中樞端點或 Azure 儲存體帳戶，方法是從功能表選取 [ **診斷設定** ]，然後 **新增診斷設定**。

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="顯示 Azure 數位 Twins 計量頁面的螢幕擷取畫面":::

    如需此程式的詳細資訊，請參閱 [*疑難排解：設定診斷*](troubleshoot-diagnostics.md)。

4. 您可以選擇從功能表選取 [ **警示** ]，然後選取 [ **+ 新增警示規則**]，以設定計量資料的警示。
    :::image type="content" source="media/troubleshoot-alerts/alerts-pre.png" alt-text="顯示 Azure 數位 Twins 計量頁面的螢幕擷取畫面":::

    如需此程式的詳細資訊，請參閱 [*疑難排解：設定警示*](troubleshoot-alerts.md)。

## <a name="azure-digital-twins-metrics-and-how-to-use-them"></a>Azure 數位 Twins 計量和使用方式

Azure 數位 Twins 提供數個計量，可讓您瞭解實例的健康情況和其相關聯的資源。 您也可以結合多個計量的資訊，來繪製更大的實例狀態圖。 

下表說明每個 Azure 數位 Twins 實例所追蹤的計量，以及每個計量與實例整體狀態的關聯。

#### <a name="api-request-metrics"></a>API 要求計量

需要處理 API 要求的計量：

| 計量 | 度量顯示名稱 | Unit | 彙總類型| 描述 | 維度 |
| --- | --- | --- | --- | --- | --- |
| ApiRequests | API 要求 (預覽)  | Count | 總計 | 針對數位 Twins 的讀取、寫入、刪除和查詢作業所提出的 API 要求數目。 |  認證 <br>操作 <br>協定 <br>狀態碼， <br>狀態碼類別， <br>狀態文字 |
| ApiRequestsFailureRate | API 要求失敗率 (預覽)  | 百分比 | Average | 服務針對您的實例所收到的 API 要求百分比，可提供內部錯誤 (500) Twins 讀取、寫入、刪除和查詢作業的回應碼。 | 認證 <br>操作 <br>協定 <br>狀態碼， <br>狀態碼類別， <br>狀態文字
| ApiRequestsLatency |  (預覽版的 API 要求延遲)  | 毫秒 | Average | API 要求的回應時間。 這指的是 Azure 數位 Twins 收到要求的時間，直到服務傳送數位 Twins 讀取、寫入、刪除和查詢作業的成功/失敗結果為止。 | 認證 <br>操作 <br>通訊協定 |

#### <a name="billing-metrics"></a>帳單計量

需要計費的計量：

>[!NOTE]
> 在預覽期間， **計費為零成本**。 雖然這些計量仍會顯示在可選取的清單中，但不會在預覽期間套用，而且會維持為零，直到服務移至預覽之外。

| 計量 | 度量顯示名稱 | Unit | 彙總類型| 描述 | 維度 |
| --- | --- | --- | --- | --- | --- |
| BillingApiOperations |  (預覽) 的計費 API 作業 | Count | 總計 | 針對 Azure 數位 Twins 服務提出之所有 API 要求計數的計費計量。 | 計量識別碼 |
| BillingMessagesProcessed |  (預覽) 處理的帳單訊息 | Count | 總計 | 從 Azure 數位 Twins 送出至外部端點之訊息數目的計費度量。<br><br>若要將帳單視為單一訊息，承載必須不超過 1 KB。 大於此的承載會以 1 KB 的遞增量計算為額外訊息 (因此1到 2 KB 之間的訊息將會計算為2則訊息，介於2到 3 KB 之間將會是3則訊息，依此類推) 。<br>這項限制也適用于回應，因此在回應本文中傳回 1.5 KB 的呼叫將會以2個作業計費。 | 計量識別碼 |
| BillingQueryUnits | 帳單查詢單位 (預覽)  | Count | 總計 | 查詢單位的數目，這是服務資源使用量的內部計算量值，用來執行查詢。 另外還有可用於測量查詢單位的協助程式 API： [QueryChargeHelper 類別](https://docs.microsoft.com/dotnet/api/azure.digitaltwins.core.querychargehelper?view=azure-dotnet-preview&preserve-view=true) | 計量識別碼 |

#### <a name="ingress-metrics"></a>輸入計量

與資料輸入有關的計量：

| 計量 | 度量顯示名稱 | Unit | 彙總類型| 描述 | 維度 |
| --- | --- | --- | --- | --- | --- |
| IngressEvents | 輸入事件 (預覽)  | Count | 總計 | 傳入的遙測事件到 Azure 數位 Twins 的數目。 | 結果 |
| IngressEventsFailureRate | 輸入事件失敗率 (預覽)  | 百分比 | Average | 服務傳回內部錯誤 (500) 回應碼的傳入遙測事件百分比。 | 結果 |
| IngressEventsLatency | 輸入事件延遲 (預覽)  | 毫秒 | Average | 當事件已準備好要由 Azure 數位 Twins 輸出時，從該事件到達時的時間，服務會在該時間點傳送成功/失敗結果。 | 結果 |

#### <a name="routing-metrics"></a>路由計量

使用路由的計量：

| 計量 | 度量顯示名稱 | Unit | 彙總類型| 描述 | 維度 |
| --- | --- | --- | --- | --- | --- |
| MessagesRouted | 路由 (預覽) 的訊息 | Count | 總計 | 路由至端點 Azure 服務（例如事件中樞、服務匯流排或事件方格）的訊息數目。 | 端點類型， <br>結果 |
| RoutingFailureRate | 路由失敗率 (預覽)  | 百分比 | Average | 從 Azure 數位 Twins 路由傳送至端點 Azure 服務（例如事件中樞、服務匯流排或事件方格）時，會產生錯誤的事件百分比。 | 端點類型， <br>結果 |
| RoutingLatency |  (預覽版的路由延遲)  | 毫秒 | Average | 從 Azure 數位 Twins 路由傳送至端點 Azure 服務（例如事件中樞、服務匯流排或事件方格）的事件之間所經過的時間。 | 端點類型， <br>結果 |

## <a name="dimensions"></a>維度

維度有助於找出更多關於計量的詳細資料。 某些路由計量會提供每個端點的資訊。 下表列出這些維度的可能值。

| 尺寸 | 值 |
| --- | --- |
| 驗證 | OAuth |
|  (API 要求的作業)  | DigitalTwins/DigitalTwins/delete、 <br>DigitalTwins/DigitalTwins/write， <br>DigitalTwins/DigitalTwins/read、 <br>DigitalTwins/eventroutes/read、 <br>DigitalTwins/eventroutes/write， <br>DigitalTwins/eventroutes/delete、 <br>DigitalTwins/模型/讀取、 <br>DigitalTwins/模型/撰寫、 <br>DigitalTwins/模型/刪除、 <br>DigitalTwins/query/action |
| 端點類型 | 事件方格 <br>事件中樞、 <br>服務匯流排 |
| 通訊協定 | HTTPS |
| 結果 | 成功 <br>失敗 |
| 狀態碼 | 200、404、500等等。 |
| 狀態碼類別 | 2xx、4xx、5xx 等等。 |
| 狀態文字 | 內部伺服器錯誤、找不到等等。 |

## <a name="next-steps"></a>後續步驟

若要深入瞭解如何管理 Azure 數位 Twins 的已記錄計量，請參閱 [*疑難排解：設定診斷*](troubleshoot-diagnostics.md)。

---
title: 使用 Azure 監視器來查看計量
titleSuffix: Azure Digital Twins
description: 請參閱如何在 Azure 監視器中查看 Azure 數位 Twins 計量。
author: baanders
ms.author: baanders
ms.date: 8/4/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: cc270ebb12b27c6461b00a4f7042bc3c829d02ef
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2020
ms.locfileid: "87812277"
---
# <a name="troubleshooting-azure-digital-twins-metrics"></a>針對 Azure 數位 Twins 進行疑難排解：計量

本文中所述的計量會提供您 Azure 訂用帳戶中 Azure 數位 Twins 資源狀態的相關資訊。 Azure 數位 Twins 計量可協助您評估 Azure 數位 Twins 服務及其連線資源的整體健康情況。 這些使用者對應的統計資料可協助您瞭解 Azure 數位 Twins 的情況，並協助對問題執行根本原因分析，而不需要聯絡 Azure 支援。

預設會啟用計量。 您可以從[Azure 入口網站](https://portal.azure.com)查看 Azure 數位 Twins 計量。

## <a name="how-to-view-azure-digital-twins-metrics"></a>如何查看 Azure 數位 Twins 計量

1. 建立 Azure 數位 Twins 實例。 您可以在[*如何：設定實例和驗證*](how-to-set-up-instance-scripted.md)中找到如何設定 Azure 數位 Twins 實例的指示。

2. 在[Azure 入口網站](https:/portal.azure.com)中尋找您的 Azure 數位 Twins 實例 (您可以在入口網站的搜尋列) 中輸入其名稱，以開啟它的頁面。 

    從實例的功能表中，選取 [**計量**]。
   
    :::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="顯示 Azure 數位 Twins 的 [計量] 頁面的螢幕擷取畫面":::

    此頁面會顯示您的 Azure 數位 Twins 實例的計量。 您也可以從清單中選取您想要查看的計量，以建立其自訂的觀點。
    
3. 您可以從功能表中按一下 [**診斷設定**]，然後選取 [**新增診斷設定**]，以選擇將計量資料傳送至事件中樞端點或 Azure 儲存體帳戶。

    :::image type="content" source="media/troubleshoot-metrics/diagnostic-settings.png" alt-text="顯示 [診斷設定] 頁面和要新增之按鈕的螢幕擷取畫面":::

    如需此流程的詳細資訊，請參閱[*疑難排解：設定診斷*](troubleshoot-diagnostics.md)。

## <a name="azure-digital-twins-metrics-and-how-to-use-them"></a>Azure 數位 Twins 計量和使用方式

Azure 數位 Twins 提供數個計量，可讓您大致瞭解實例和其相關聯的資源的健全狀況。 您也可以結合多個計量的資訊，來繪製更大的實例狀態圖片。 

下表描述每個 Azure 數位 Twins 實例所追蹤的計量，以及每個度量如何與實例的整體狀態產生關聯。

#### <a name="api-request-metrics"></a>API 要求計量

需要使用 API 要求的計量：

| 計量 | 度量顯示名稱 | 單位 | 彙總類型| 描述 | 維度 |
| --- | --- | --- | --- | --- | --- |
| ApiRequests |  (預覽的 API 要求)  | Count | 總計 | 針對數位 Twins 讀取、寫入、刪除和查詢作業所提出的 API 要求數目。 |  Authentication <br>運營 <br>Protocol <br>狀態碼、 <br>狀態碼類別， <br>狀態文字 |
| ApiRequestsFailureRate | API 要求失敗率 (預覽)  | 百分比 | Average | 服務為您的實例所接收的 API 要求百分比，其提供內部錯誤 (500) 回應碼，以進行數位 Twins 讀取、寫入、刪除和查詢作業。 | Authentication <br>運營 <br>Protocol <br>狀態碼、 <br>狀態碼類別， <br>狀態文字
| ApiRequestsLatency | API 要求延遲 (預覽)  | 毫秒 | Average | API 要求的回應時間。 這是指 Azure 數位 Twins 收到要求的時間，直到服務傳送數位 Twins 讀取、寫入、刪除和查詢作業的成功/失敗結果為止。 | Authentication <br>運營 <br>通訊協定 |

#### <a name="billing-metrics"></a>計費計量

計量必須使用計費：

>[!NOTE]
> 在預覽期間，**計費為零成本**。 雖然這些計量仍會顯示在可選取的清單中，但它們不會在預覽期間套用，且會維持為零，直到服務移動超過預覽為止。

| 計量 | 度量顯示名稱 | 單位 | 彙總類型| 描述 | 維度 |
| --- | --- | --- | --- | --- | --- |
| BillingApiOperations | 計費 API 作業 (預覽)  | Count | 總計 | 針對 Azure 數位 Twins 服務提出的所有 API 要求計數的計費計量。 | 計量識別碼 |
| BillingMessagesProcessed | 已處理 (預覽的帳單訊息)  | Count | 總計 | 從 Azure 數位 Twins 送出至外部端點的訊息數目計費計量。 | 計量識別碼 |
| BillingQueryUnits | 計費查詢單位 (預覽)  | Count | 總計 | 查詢單位的數目，即內部計算的服務資源使用量測量，用於執行查詢。 還有一個協助程式 API 可用於測量查詢單位： [QueryChargeHelper 類別](https://docs.microsoft.com/dotnet/api/azure.digitaltwins.core.querychargehelper?view=azure-dotnet-preview) | 計量識別碼 |

#### <a name="ingress-metrics"></a>輸入計量

與資料輸入有關的計量：

| 計量 | 度量顯示名稱 | 單位 | 彙總類型| 描述 | 維度 |
| --- | --- | --- | --- | --- | --- |
| IngressEvents |  (Preview) 的輸入事件 | Count | 總計 | 傳入的遙測事件數目到 Azure 數位 Twins。 | 結果 |
| IngressEventsFailureRate |  (預覽) 的輸入事件失敗率 | 百分比 | Average | 服務傳回內部錯誤 (500) 回應碼的傳入遙測事件百分比。 | 結果 |
| IngressEventsLatency | 輸入事件延遲 (預覽)  | 毫秒 | Average | 當事件已準備好由 Azure 數位 Twins 輸出時的時間，此時服務會傳送成功/失敗的結果。 | 結果 |

#### <a name="routing-metrics"></a>路由計量

與路由有關的計量：

| 計量 | 度量顯示名稱 | 單位 | 彙總類型| 描述 | 維度 |
| --- | --- | --- | --- | --- | --- |
| MessagesRouted |  (預覽路由傳送的訊息)  | Count | 總計 | 路由傳送至端點 Azure 服務的訊息數目，例如事件中樞、服務匯流排或事件方格。 | 運營 <br>結果 |
| RoutingFailureRate |  (預覽) 的路由失敗率 | 百分比 | Average | 當事件從 Azure 數位 Twins 路由傳送至端點 Azure 服務（例如事件中樞、服務匯流排或事件方格）時，會導致錯誤的事件百分比。 | 運營 <br>結果 |
| RoutingLatency |  (預覽) 的路由延遲 | 毫秒 | Average | 在事件從 Azure 數位 Twins 發佈到端點 Azure 服務（例如事件中樞、服務匯流排或事件方格）之間經過的時間。 | 運營 <br>結果 |

## <a name="dimensions"></a>維度

維度有助於識別有關計量的更多詳細資料。 某些路由計量會提供每個端點的資訊。 下表列出這些維度的可能值。

| 維度 | 值 |
| --- | --- |
| 驗證 | OAuth |
| API 要求的作業 ()  | 選取/選取/delete、 <br>選取/選取/write， <br>選取/選取/read、 <br>選取/eventroutes/read、 <br>選取/eventroutes/write， <br>選取/eventroutes/delete、 <br>選取/型號/讀取、 <br>選取/機型/write， <br>選取/模型/刪除、 <br>選取/查詢/動作 |
| 路由) 的作業 ( | 事件方格、 <br>事件中樞、 <br>服務匯流排 |
| 通訊協定 | HTTPS |
| 結果 | Success <br>失敗 |
| 狀態碼 | 200、404、500等等。 |
| 狀態碼類別 | 2xx、4xx、5xx 等等。 |
| 狀態文字 | 內部伺服器錯誤、找不到等等。 |

## <a name="next-steps"></a>後續步驟

若要深入瞭解如何管理 Azure 數位 Twins 的已記錄計量，請參閱[*疑難排解：設定診斷*](troubleshoot-diagnostics.md)。
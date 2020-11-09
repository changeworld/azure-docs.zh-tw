---
title: 顯示和設定適用于 FHIR (preview) 計量的 Azure IoT Connector
description: 本文說明如何顯示及設定適用于 FHIR (preview) 計量的 Azure IoT Connector。
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 10/29/2020
ms.author: jasteppe
ms.openlocfilehash: 9a4e2c4dfe8a9de28688afe0dd036cecb7ce2b39
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/09/2020
ms.locfileid: "94381213"
---
# <a name="display-and-configure-azure-iot-connector-for-fhir-preview-metrics"></a>顯示和設定適用于 FHIR (preview) 計量的 Azure IoT Connector 

在本文中，您將瞭解如何顯示和設定適用于快速健康照護互通資源 (FHIR&#174;) * 計量的 Azure IoT Connector。

> [!TIP]
> 若要瞭解如何設定匯出計量資料，請遵循 [透過診斷設定匯出 Azure IoT Connector FOR FHIR (preview) 計量](./iot-metrics-diagnostics-export.md)中的指引。

## <a name="display-metrics-for-azure-iot-connector-for-fhir-preview"></a>顯示適用于 FHIR 的 Azure IoT Connector (preview 的計量) 

1. 登入 Azure 入口網站，然後選取您的 Azure API for FHIR 服務。 

2. 在左窗格中，選取 [ **計量** ]。 

3. 選取 [ **IoT Connector** ] 索引標籤。

   :::image type="content" source="media/iot-metrics-display/iot-metrics-main.png" alt-text="[IoT Connector] 窗格的螢幕擷取畫面，其中包含顯示傳入和正規化訊息數目的折線圖。" lightbox="media/iot-metrics-display/iot-metrics-main.png"::: 

4. 選取 IoT Connector 來查看其度量。 例如，有四個 IoT 連接器 ( *連接器 1* 、 *連接器 2* 等) 與此 Azure API for FHIR 服務相關聯的。

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-connector.png" alt-text="顯示 IoT Connector 索引標籤1、2、3和4的 [IoT Connector] 窗格螢幕擷取畫面。" lightbox="media/iot-metrics-display/iot-metrics-select-connector.png"::: 

5. 選取您要顯示之 IoT Connector 計量的時間週期 (例如 **1 小時** 、 **24 小時** 、 **7 天** 或 **自訂** ) 。 藉由選取 [ **自訂** ] 索引標籤，您可以建立特定的時間/日期組合以顯示 IoT Connector 計量。

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-time.png" alt-text="[IoT Connector] 窗格的螢幕擷取畫面，顯示「1小時」的「連接器1」時間週期折線圖。" lightbox="media/iot-metrics-display/iot-metrics-select-time.png"::: 
 
## <a name="metric-types-for-azure-iot-connector-for-fhir-preview"></a>適用于 FHIR (preview) 的 Azure IoT Connector 計量類型 

下表列出您可以顯示的 IoT Connector 計量：

|度量類型|度量用途| 
|-----------|--------------|
|傳入的訊息數目|顯示接收的原始內送訊息數目 (例如，裝置事件) 。|
|正規化訊息的數目|顯示正規化訊息的數目。|
|訊息群組數目|顯示在指定的時間範圍內匯總訊息的群組數目。|
|平均正規化階段延遲|顯示正規化階段的平均延遲。 正規化階段會針對未經處理的傳入訊息執行正規化。|
|平均群組階段延遲|顯示群組階段的平均延遲。 群組階段會針對正規化訊息執行緩衝、匯總和群組。| 
|Total Error Count|顯示錯誤總數。| 

## <a name="focus-on-and-configure-azure-iot-connector-for-fhir-preview-metrics"></a>專注于並設定 Azure IoT Connector 以 FHIR (preview) 計量

在此範例中，讓我們將焦點放在 **傳入訊息** 計量的數目。

1. 選取您想要關注的時間點。

   :::image type="content" source="media/iot-metrics-display/iot-metrics-focus.png" alt-text="[內送訊息數目] 計量窗格的螢幕擷取畫面，其中會反白顯示折線圖上的單一時間點。" lightbox="media/iot-metrics-display/iot-metrics-focus.png"::: 

2. 在 [ **內送訊息** ] 窗格中，您可以選取 [ **新增度量** ]、[ **新增篩選** ] 或 [套用 **分割** ]，進一步自訂度量。 

   :::image type="content" source="media/iot-metrics-display/iot-metrics-add-options.png" alt-text="[輸入訊息數目] 計量窗格的螢幕擷取畫面，其中反白顯示 [新增度量]、[新增篩選] 和 [套用分割] 按鈕。" lightbox="media/iot-metrics-display/iot-metrics-add-options.png"::: 

## <a name="conclusion"></a>結論 
擁有資料平面度量的存取權，對於監視和疑難排解而言是不可或缺的。 適用于 FHIR 的 Azure IoT Connector 可透過計量協助您執行這些動作。 

## <a name="next-steps"></a>後續步驟

取得 Azure IoT Connector for FHIR 常見問題的解答。

>[!div class="nextstepaction"]
>[適用于 FHIR 的 Azure IoT Connector 常見問題](fhir-faq.md)

* 在 Azure 入口網站中，Azure IoT Connector for FHIR 稱為 IoT Connector (preview) 。 FHIR 是 HL7 的注冊商標，可搭配 HL7 的許可權使用。 

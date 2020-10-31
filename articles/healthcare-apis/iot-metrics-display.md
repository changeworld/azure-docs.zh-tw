---
title: 查看及設定適用于 FHIR (preview) 計量的 Azure IoT Connector
description: 本文說明如何顯示及設定 Azure IoT Connector for FHIR (preview) 計量
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 10/29/2020
ms.author: jasteppe
ms.openlocfilehash: 76166fc5c525d36474a585179472e93b22dad647
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93133506"
---
# <a name="view-and-configure-azure-iot-connector-for-fhir-preview-metrics"></a>查看及設定適用于 FHIR (preview) 計量的 Azure IoT Connector 

在本文中，您將瞭解如何查看和設定適用于 FHIR * 計量的 Azure IoT Connector。 

> [!TIP]
> 遵循 [透過診斷設定將 Azure IoT Connector FOR FHIR (preview) 計量](https://docs.microsoft.com/azure/healthcare-apis/iot-metrics-diagnostics-export) 中的指引，以瞭解如何設定計量資料的匯出。

## <a name="view-metrics-for-azure-iot-connector-for-fhir-preview"></a>查看適用于 FHIR (preview 的 Azure IoT Connector 計量) 
1. 若要查看 IoT 連接器的計量，請在 Azure 入口網站中選取您的 Azure API for FHIR 服務。 

2. 流覽至 **計量** 

3. 選取 [ **IoT Connector** ] 索引標籤。

   :::image type="content" source="media/iot-metrics-display/iot-metrics-main.png" alt-text="IoT Connector1" lightbox="media/iot-metrics-display/iot-metrics-main.png"::: 

4. 選取 IoT Connector 來查看其計量 (例如：與此 Azure API for FHIR 服務) 相關聯的 (4) IoT 連接器。

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-connector.png" alt-text="IoT Connector1" lightbox="media/iot-metrics-display/iot-metrics-select-connector.png"::: 

> [!NOTE]
> [ **自訂** ] 索引標籤可讓您建立用於觀看 IoT Connector 計量的特定時間/日期組合。

5. 選取要顯示 IoT Connector 計量的時間週期 (例如：1小時、24小時、7天或自訂) 。

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-time.png" alt-text="IoT Connector1" lightbox="media/iot-metrics-display/iot-metrics-select-time.png"::: 
 
## <a name="metrics-types-for-azure-iot-connector-for-fhir-preview"></a>適用于 FHIR (preview) 的 Azure IoT Connector 計量類型 
顯示的 IoT Connector 計量如下：

|計量類型|計量用途| 
|-----------|--------------|
|傳入的訊息數目|收到的原始內送訊息數目 (例如：裝置事件) 。|
|正規化訊息的數目|正規化訊息的數目。|
|訊息群組數目|在指定的時間範圍內匯總訊息的群組數目。|
|平均正規化階段延遲|標準化階段的平均延遲。 正規化階段是針對未經處理的傳入訊息執行正規化。|
|平均群組階段延遲|群組階段的平均延遲。 群組階段是針對正規化訊息執行緩衝、匯總和群組。| 
|Total Error Count|錯誤總數。| 

## <a name="focusing-and-configuring-azure-iot-connector-for-fhir-preview-metrics"></a>專注于設定 Azure IoT Connector 以 FHIR (預覽版) 計量
在此範例中，我們將著重于 **傳入訊息** 計量的數目。

1. 選取您想要關注的時間點。

   :::image type="content" source="media/iot-metrics-display/iot-metrics-focus.png" alt-text="IoT Connector1" lightbox="media/iot-metrics-display/iot-metrics-focus.png"::: 

2. 在此畫面中，您可以 **新增度量** 、 **新增篩選準則** ，以及套用 **分割** 以進行進一步的自訂。 

   :::image type="content" source="media/iot-metrics-display/iot-metrics-add-options.png" alt-text="IoT Connector1" lightbox="media/iot-metrics-display/iot-metrics-add-options.png"::: 

## <a name="conclusion"></a>結論 
擁有資料平面度量的存取權，對於監視和疑難排解而言是不可或缺的。  適用于 FHIR 的 Azure IoT Connector 可協助您透過計量進行這些動作。 

## <a name="next-steps"></a>後續步驟

查看 Azure IoT Connector for FHIR 的常見問題。

>[!div class="nextstepaction"]
>[適用于 FHIR 的 Azure IoT Connector 常見問題](fhir-faq.md)

*在 Azure 入口網站中，Azure IoT Connector for FHIR 稱為 IoT 連接器 (預覽)。

FHIR 是 HL7 的註冊商標，必須搭配 HL7 權限方可使用。
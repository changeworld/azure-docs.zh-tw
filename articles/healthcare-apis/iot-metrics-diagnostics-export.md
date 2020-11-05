---
title: 透過診斷設定為 FHIR (預覽) 計量匯出 Azure IoT Connector
description: 本文說明如何透過診斷設定，將 Azure IoT Connector for FHIR (preview) 計量匯出
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 10/30/2020
ms.author: jasteppe
ms.openlocfilehash: 5a57b13cdb8c7c1144815543e03b970de32b4369
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93394249"
---
# <a name="export-azure-iot-connector-for-fhir-preview-metrics-through-diagnostic-settings"></a>透過診斷設定為 FHIR (預覽) 計量匯出 Azure IoT Connector

在本文中，您將瞭解如何匯出適用于 FHIR * 計量記錄的 Azure IoT Connector。 啟用計量記錄的功能是 Azure 入口網站中的 [**診斷設定**](../azure-monitor/platform/diagnostic-settings.md) 。 

> [!TIP]
> 遵循 Azure API for FHIR 中的「 [啟用診斷記錄」和「Azure IoT Connector FOR FHIR](enable-diagnostic-logging.md#enable-diagnostic-logging-in-azure-api-for-fhir) 」中的指引來設定「審核記錄」。

## <a name="enable-metrics-logging-for-the-azure-iot-connector-for-fhir-preview"></a>針對適用于 FHIR (preview 的 Azure IoT Connector 啟用計量記錄) 
1. 若要啟用適用于 FHIR 的 Azure IoT Connector 的計量記錄，請在 Azure 入口網站中選取您的 Azure API for FHIR 服務 

2. 流覽至 **診斷設定** 

3. 選取 [ **+ 新增診斷設定** ]

   :::image type="content" source="media/iot-metrics-export/diagnostic-settings-main.png" alt-text="IoT Connector1" lightbox="media/iot-metrics-export/diagnostic-settings-main.png"::: 

4. 在 [ **診斷設定名稱** ] 對話方塊中輸入名稱。

5. 選取您要用來存取診斷記錄的方法：

    1. 封存 **至儲存體帳戶** 進行審核或手動檢查。 您要使用的儲存體帳戶必須已經建立。
    2. **串流至事件中樞** ，以供協力廠商服務或自訂分析解決方案進行內嵌。 您需要先建立事件中樞命名空間和事件中樞原則，才能設定此步驟。
    3. 在 Azure 監視器中 **串流至 Log Analytics** 工作區。 您需要先建立記錄分析工作區，才能選取此選項。

6. 選取 Azure IoT Connector for FHIR 的 **錯誤、流量和延遲** 。  選取您要為 Azure API for FHIR 捕捉的任何其他計量類別。

7. 選取 [儲存]。

   :::image type="content" source="media/iot-metrics-export/diagnostic-setting-add.png" alt-text="IoT Connector2" lightbox="media/iot-metrics-export/diagnostic-setting-add.png":::

> [!Note] 
> 最多可能需要15分鐘的時間，第一個計量記錄才會顯示在您選擇的儲存機制中。  
 
如需如何使用診斷記錄的詳細資訊，請參閱 [Azure 資源記錄檔](../azure-monitor/platform/platform-logs-overview.md)

## <a name="conclusion"></a>結論 
您必須能夠存取計量記錄，才能進行監視和疑難排解。  適用于 FHIR 的 Azure IoT Connector 可讓您透過計量記錄進行這些動作。 

## <a name="next-steps"></a>後續步驟

查看 Azure IoT Connector for FHIR 的常見問題。

>[!div class="nextstepaction"]
>[適用于 FHIR 的 Azure IoT Connector 常見問題](fhir-faq.md)

*在 Azure 入口網站中，Azure IoT Connector for FHIR 稱為 IoT 連接器 (預覽)。

FHIR 是 HL7 的註冊商標，必須搭配 HL7 權限方可使用。
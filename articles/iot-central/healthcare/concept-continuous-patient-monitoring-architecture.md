---
title: Azure IoT Central 中的持續性患者監視架構 |Microsoft Docs
description: 了解持續性患者監視解決方案架構。
author: philmea
ms.author: philmea
ms.date: 10/24/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 92eb4157abb55b7056952d1fb064c7c7d7500335
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "77021691"
---
# <a name="continuous-patient-monitoring-architecture"></a>持續性患者監視架構



您可以使用隨附的應用程式範本，並以下列架構作為指引，來建立持續性患者監視解決方案。

>[!div class="mx-imgBorder"] 
>![CPM 架構](media/cpm-architecture.png)

1. 醫療裝置使用藍牙低功耗 (BLE) 進行通訊
1. 行動電話閘道接收 BLE 資料並傳送至 IoT Central
1. 將患者的健康情況資料持續匯出至 Azure API for FHIR&reg;
1. 根據互通的資料進行機器學習
1. 依據 FHIR 資料建置護理小組儀表板

## <a name="details"></a>詳細資料
本節將進一步詳述架構圖表的各個部分。

### <a name="ble-medical-devices"></a>BLE 醫療裝置
醫療保健 IoT 空間中使用的許多醫療穿戴式裝置都是藍牙低功耗裝置。 其無法直接與雲端通訊，而必須通過閘道來進行。 在此架構中，建議使用行動電話應用程式作為此閘道。

### <a name="mobile-phone-gateway"></a>行動電話閘道
行動電話應用程式的主要功能是從醫療裝置擷取 BLE 資料，並將其傳輸至 Azure IoT Central。 此外，應用程式可引導患者完成裝置設定和佈建流程，並協助他們查看其個人健康情況資料的檢視。 在醫院的病房內，其他解決方案可能會使用平板電腦閘道或靜態閘道來執行相同的通訊流程。

### <a name="export-to-azure-api-for-fhirreg"></a>匯出至 Azure API for FHIR&reg;
Azure IoT Central 符合 HIPAA 規範，且已通過 HITRUST&reg; 認證，但您也可以將患者的健康情況相關資料傳送至 Azure API for FHIR。 [Azure API for FHIR](../../healthcare-apis/overview.md) 專為處理臨床健康情況資料而設計，是完全受控且符合規範的標準 API，可讓您建立新的健康情況資料處理系統。 它可讓您透過 FHIR API 快速交換資料，且在雲端中受到受控平台即服務 (PaaS) 供應項目的支援。 您可以使用 IoT Central 的持續資料匯出功能，將資料傳送至 Azure API for FHIR。

### <a name="machine-learning"></a>機器學習服務
在彙總資料並將其轉譯成 FHIR 格式之後，您可以建置機器學習模型，以擴充深入解析，並協助護理小組做出更明智的決策。 有多種不同的服務都可用來建置、定型和部署機器學習模型。 如需如何使用 Azure 機器學習供應項目的詳細資訊，請參閱我們的[機器學習文件](../../machine-learning/index.yml)。

### <a name="provider-dashboard"></a>提供者儀表板
位於 Azure API for FHIR 中的資料可用來建置患者深入解析儀表板，也可以直接整合到 EMR 中，以協助小組將患者的健康狀態視覺化。 護理小組可使用此儀表板來照顧需要協助的患者，並在病情惡化前及早發現警告徵兆。 若要了解如何建置 Power BI 即時提供者儀表板，請遵循我們的[操作說明指南](howto-health-data-triage.md)。

## <a name="next-steps"></a>後續步驟
* [了解如何部署持續性患者監視應用程式範本](tutorial-continuous-patient-monitoring.md)
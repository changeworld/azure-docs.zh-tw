---
title: Azure IoT Central 中的持續性患者監視架構 |Microsoft Docs
description: 教學課程 - 了解持續性患者監視解決方案架構。
author: philmea
ms.author: philmea
ms.date: 12/11/2020
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: dad6327acd24fe7642bf1033b1527ef3e40b772e
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97590098"
---
# <a name="continuous-patient-monitoring-architecture"></a>持續性患者監視架構

本文說明從 **持續性患者監視** 應用程式範本建置的解決方案架構：

您可以使用隨附的應用程式範本，並以下列架構作為指導方針，來建立持續性患者監視解決方案。

:::image type="content" source="media/cpm-architecture.png" alt-text="持續性患者監視架構":::

## <a name="details"></a>詳細資料

本節將進一步詳述架構圖表的各個部分：

### <a name="bluetooth-low-energy-ble-medical-devices"></a>低功耗藍牙 (BLE) 醫療裝置

在醫療保健 IoT 解決方案中使用的許多醫療穿戴式裝置是 BLE 裝置。 這些裝置無法直接與雲端通訊，需要使用閘道來與您的雲端解決方案交換資料。 此架構使用行動電話應用程式作為閘道。

### <a name="mobile-phone-gateway"></a>行動電話閘道

行動電話應用程式的主要功能是從醫療裝置收集 BLE 資料，並將其傳輸至 IoT Central。 應用程式也會透過裝置設定引導患者，並讓他們檢視其個人健康情況資料。 其他解決方案可以使用平板電腦閘道或醫院病房中的靜態閘道。 適用於 Android 和 iOS 的開放原始碼範例行動應用程式可以作為起點，開始進行應用程式開發工作。 若要深入了解，請參閱 [IoT Central 持續性患者監視行動應用程式](/samples/iot-for-all/iotc-cpm-sample/iotc-cpm-sample/)。

### <a name="export-to-azure-api-for-fhirreg"></a>匯出至 Azure API for FHIR&reg;

Azure IoT Central 符合 HIPAA 規範且通過 HITRUST&reg; 認證。 您也可以使用 [Azure API for FHIR](../../healthcare-apis/overview.md)，將患者健康情況資料傳送給其他服務。 Azure API for FHIR 是以標準為基礎的 API，適用於臨床健康情況資料。 [適用於 FHIR 的 Azure IoT 連接器](../../healthcare-apis/iot-fhir-portal-quickstart.md)可讓您使用 Azure API for FHIR 作為 IoT Central 中的連續資料匯出目的地。

### <a name="machine-learning"></a>機器學習服務

使用機器學習模型與您的 FHIR 資料來產生深入解析，並支援您的護理小組所做出的決策。 若要深入了解，請參閱 [Azure 機器學習文件](../../machine-learning/index.yml)。

### <a name="provider-dashboard"></a>提供者儀表板

使用 Azure API for FHIR 資料來建置患者深入解析儀表板，或將其直接整合到護理小組所使用的電子醫療記錄。 護理小組可以使用儀表板來協助患者，並識別病情惡化的早期警告徵兆。 若要深入了解，請參閱[建置 Power BI 提供者儀表板](howto-health-data-triage.md)教學課程。

## <a name="next-steps"></a>下一步

建議的下一個步驟是[了解如何部署持續性患者監視應用程式範本](tutorial-continuous-patient-monitoring.md)。
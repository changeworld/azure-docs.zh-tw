---
title: Azure IoT Central 的架構概念 - 智慧型電表 | Microsoft Docs
description: 此文章介紹與 Azure IoT Central 能源應用程式範本架構相關的重要概念
author: op-ravi
ms.author: omravi
ms.date: 10/22/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 9f5e1ba52390191409d8da4bc4c41faaa859a4a8
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92125606"
---
# <a name="azure-iot-central---smart-meter-app-architecture"></a>Azure IoT Central - 智慧型電表應用程式架構



此文章提供智慧型電表監視應用程式範本架構的概觀。 下圖顯示在 Azure 上使用 IoT Central 平台之智慧型電表應用程式的常用架構。

> [!div class="mx-imgBorder"]
> ![智慧型電表架構](media/concept-iot-central-smart-meter/smart-meter-app-architecture.png)

此架構由下列元件組成。 有些解決方案可能不需要此處所列的每個元件。

## <a name="smart-meters-and-connectivity"></a>智慧型電表與連線能力 

智慧型電表是所有能源資產中最重要的裝置之一。 它會記錄能源耗用量資料並將其傳遞到公用程式，以進行監視並用於其他使用案例，例如計費和需求回應。 根據電表類型，其可以使用閘道或其他中繼裝置或系統 (例如邊緣裝置和前端系統) 連線到 IoT Central。 建置 IoT Central 裝置橋接器來連線無法直接連線的裝置。 IoT Central 裝置橋接器是一個開放原始碼解決方案，您可以[在這裡](../core/howto-build-iotc-device-bridge.md) \(部分機器翻譯\) 找到完整詳細資料。 


## <a name="iot-central-platform"></a>IoT Central 平台

Azure IoT Central 是一個平台，能簡化 IoT 解決方案的建置，有助於降低 IoT 管理、作業與開發的負擔及費用。 利用 IoT Central，您可以輕鬆地大規模連線、監視並管理您的物聯網 (IoT) 資產。 將智慧型電表連線到 IoT Central 之後，應用程式範本就會使用裝置模型、命令和儀表板等內建功能。 應用程式範本也會針對暖路徑案例使用 IoT Central 儲存體，例如，近乎即時的電表資料監視、分析、規則和視覺效果。 


## <a name="extensibility-options-to-build-with-iot-central"></a>透過 IoT Central 建置的擴充性選項
IoT Central 平台提供兩個擴充性選項：連續資料匯出 (CDE) 和 API。 客戶與夥伴可以在這些選項之間進行選擇，以根據特定需求自訂解決方案。 例如，我們的其中一個夥伴使用 Azure Data Lake Storage (ADLS) 設定了 CDE。 他們使用 ADLS 來進行長期資料保留和其他冷路徑儲存案例，例如批次處理、稽核和報告用途。 

## <a name="next-steps"></a>後續步驟

* 既然您已了解架構，請[免費建立智慧型電表應用程式](https://apps.azureiotcentral.com/build/new/smart-meter-monitoring) \(英文\)
* 若要深入了解 IoT Central，請參閱 [IoT Central 概觀](../index.yml)
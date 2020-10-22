---
title: 使用 Azure IoT Central 所建置的連線廢棄物管理解決方案參考架構 | Microsoft Docs
description: 了解使用 Azure IoT Central 所建置的連線廢棄物管理解決方案概念。
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 9b061a6d3d7d2a6598d8ed7c48428d756db6e97d
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92126270"
---
# <a name="connected-waste-monitoring-reference-architecture"></a>連線廢棄物監視參考架構 



您可以使用 **Azure IoT Central 應用程式範本**來建立連線廢棄物管理解決方案，作為入門 IoT 應用程式。 本文提供建置端對端解決方案的高階參考架構指引。 

![連線廢棄物管理架構](./media/concepts-connectedwastemanagement-architecture/concepts-connectedwastemanagement-architecture1.png)


概念：

1. 裝置與連線能力  
1. IoT 中心 
2. 擴充性與整合
3. 商務應用程式

讓我們看看經常在耗水量監控解決方案中發揮作用的主要元件。

## <a name="devices-and-connectivity"></a>裝置與連線能力 
在開放式環境中使用的裝置 (例如垃圾桶) 可經由第三方網路操作員，透過低電力廣域網路 (LPWAN) 進行連線。 針對這些類型的裝置，您可以使用 [Azure IoT Central 裝置橋接器](../core/howto-build-iotc-device-bridge.md)，將裝置資料傳送至 Azure IoT Central 中的 IoT 應用程式。 或者，您可能有具備 IP 功能的裝置閘道，而且可以直接連線到 IoT Central。

## <a name="iot-central"></a>IoT 中心 
Azure IoT Central 是 IoT 應用程式平台，可讓您快速啟動並在 IoT 解決方案上執行。 您可以使用第三方服務來建立品牌、自訂及整合您的解決方案。
將您的智慧型水資源裝置連線到 IoT Central 之後，您就可以取得裝置命令和控制、監視和警示、具有內建 RBAC 的使用者介面、可設定的深入解析儀表板，以及擴充性選項。 

## <a name="extensibility-and-integrations"></a>擴充性與整合 
您可以在 IoT Central 中擴充 IoT 應用程式，並選擇性地執行下列動作：
* 透過從 IoT Central 應用程式將資料連續匯出，以轉換和整合您的 IoT 資料並進行進階分析，例如訓練機器學習模型。 
* 使用 Microsoft Flow 或來自 IoT Central 應用程式的 Webhook 觸發動作，將其他系統中的工作流程自動化
* 透過 IoT Central API 在 IoT Central 中以程式設計方式存取您的 IoT 應用程式。

## <a name="business-applications"></a>商務應用程式 
IoT 資料可用來為廢棄物利用內的各種商務應用程式提供強大的功能。 若要深入了解如何使用現場服務連線 IoT Central 連線廢棄物管理應用程式，請遵循[如何整合 Dynamics 365 現場服務](./how-to-configure-connected-field-services.md)的教學課程 

## <a name="next-steps"></a>後續步驟
* 深入了解如何[建立連線廢棄物管理](./tutorial-connected-waste-management.md) IoT Central 應用程式
* 深入了解 [IoT Central 政府機關範本](./overview-iot-central-government.md)
* 若要深入了解 IoT Central，請參閱 [IoT Central 概觀](../core/overview-iot-central.md)
---
title: 將您的 Azure IoT Central 應用程式與 Dynamics 365 Field Services 連線 | Microsoft Docs
description: 了解如何使用 Azure IoT Central 和 Dynamics 365 Field Service 建置端對端解決方案
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 2b3e006f717ed1c66c7db29dbd70c226d2d75ea8
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92127211"
---
# <a name="build-end-to-end-solution-with-azure-iot-central-and-dynamics-365-field-service"></a>了解如何使用 Azure IoT Central 和 Dynamics 365 Field Service 建置端對端解決方案 



作為建置者，您可以將 Azure IoT Central 應用程式整合到其他商務系統。 


例如，在聯網廢棄物管理解決方案中，您可以將垃圾收集車的派遣最佳化。 您可以根據來自 connected 垃圾桶的 IoT 感應器資料來進行優化。在您 [IoT Central 連線的垃圾管理應用程式](./tutorial-connected-waste-management.md) 中，您可以設定規則和動作，並將其設定為觸發 Dynamics Field Service 中的警示建立。 此案例是使用 Microsoft Flow 來完成，您可以直接在 IoT Central 中設定此服務，以自動化應用程式和服務之間的工作流程。 此外，根據 Field Service 中的服務活動，資訊也可以送回 Azure IoT Central。 

## <a name="how-to-connect-your-azure-iot-central-application-with-dynamics-365-field-services"></a>如何建立 Azure IoT Central 應用程式與 Dynamics 365 Field Services 的連線 

您可以根據純粹的設定體驗來輕鬆實作下列整合程序：
* Azure IoT Central 可以將裝置異常的相關資訊傳送至連線的 Field Service (以 IoT 警示的形式) 來進行診斷。
* 連線的 Field Service 可以建立裝置異常所觸發的案例或工單。
* 連線的 Field Service 可以排定技術人員進行檢查，以避免停機事件。
* 您可以使用相關的服務和排程資訊來更新 Azure IoT Central 裝置儀表板。


## <a name="next-steps"></a>下一步
* 深入了解 [IoT Central 政府機關範本](./overview-iot-central-government.md)
* 深入了解 [IoT Central](../core/overview-iot-central.md)。
* 深入了解 [Dynamics 365 Field Services](/dynamics365/field-service/cfs-iot-overview)
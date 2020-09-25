---
title: 快速入門：設定和啟用 Azure RTOS 的安全性模組
description: 了解如何在您的 Azure IoT 中樞上線及啟用適用於 Azure RTOS 服務的資訊安全中心模組。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2020
ms.author: mlottner
ms.openlocfilehash: 6f438bd7dd0b3e45fd292947a3b08db80401b825
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943795"
---
# <a name="quickstart-security-module-for-azure-rtos-preview"></a>快速入門：適用於 Azure RTOS 的安全性模組 (預覽)

本文將說明如何在開始使用之前提供必要條件，並說明如何在 IoT 中樞上啟用 Azure RTOS 服務的安全性模組。 如果您目前沒有 IoT 中樞，請參閱[使用 Azure 入口網站建立 IoT 中樞](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal)來著手。

> [!NOTE]
> 只有標準層 IoT 中樞才支援 Azure RTOS 的安全性模組。

## <a name="prerequisites"></a>先決條件 

### <a name="supported-devices"></a>支援的裝置

- ST STM32F746G Discovery Kit
- NXP i.MX RT1060 EVK
- Microchip SAM E54 Xplained Pro EVK

從[適用於 Azure RTOS GitHub 資源的安全性模組](hhtps://github.com/azure-rtos/azure-iot-preview/releases)下載、編譯及執行您選擇之特定主機板和工具 (IAR、半導體 IDE 或電腦) 的其中一個 .zip 檔案。

### <a name="azure-resources"></a>Azure 資源

開始使用的下一個階段是準備您的 Azure 資源。 您需要 IoT 中樞，我們建議使用 Log Analytics 工作區。 針對 IoT 中樞，您需要 IoT 中樞連接字串，才能連線到您的裝置。 
  
### <a name="iot-hub-connection"></a>IoT 中樞連線

需要 IoT 中樞連線才能開始使用。 

1. 在 Azure 入口網站中開啟您的 **IoT 中樞**。
1. 將 IoT 連接字串儲存到[組態檔](how-to-azure-rtos-security-module.md)。


連線認證取自使用者應用程式組態 **HOST_NAME**、**DEVICE_ID** 和 **DEVICE_SYMMETRIC_KEY**。

適用於 Azure RTO 的安全性模組會使用以 **MQTT** 通訊協定為基礎的 Azure IoT 中介軟體連線。


### <a name="log-analytics-workspace"></a>Log Analytics 工作區

IoT 解決方案的 Defender 預設會關閉 IoT 中樞內的 Log Analytics 擷取。 若要啟用以使用適用於 Azure RTOS 的安全性模組，請執行下列動作： 
1. 在 Azure 入口網站中，移至您的 IoT 中樞。
1. 在**安全性**功能表下，選取 [設定]。
   :::image type="content" source="media/quickstart/azure-rtos-hub-settings.png" alt-text="Azure RTOS 的存取資料收集選項"::: 
1. 選取 [資料收集]。 
1. 從 [工作區組態] 選項，將交換器切換至**開啟**。 
1. 建立新的 Log Analytics 工作區，或附加現有的 Log Analytics 工作區。 請確定已選取 [存取原始安全性資料] 選項。 
 :::image type="content" source="media/quickstart/azure-rtos-data-collection-on.png" alt-text="Azure RTOS 組態顯示同時選取的資料收集選項和原始安全性資料選項":::
1. 選取 [儲存]。
1. 返回您的 Azure 資源清單，確認您已為 IoT 中樞啟用所建立或附加的 Log Analytics 工作區。
    :::image type="content" source="media/quickstart/verify-azure-resource-list.png" alt-text="檢查您的 Azure 資源清單，確認為 IoT 中樞新增了正確的 Log Analytics 工作區"::: 

## <a name="next-steps"></a>後續步驟

前往下一篇文章，完成設定並自訂您的解決方案。

> [!div class="nextstepaction"]
> [設定適用於 Azure RTOS 的安全性模組](how-to-azure-rtos-security-module.md)

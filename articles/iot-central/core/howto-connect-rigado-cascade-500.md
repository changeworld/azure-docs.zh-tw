---
title: 在 Azure IoT 中心連接里加多級聯 500 |微軟文檔
description: 瞭解如何將里加多級聯 500 閘道設備連接到 IoT 中央應用程式。
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 11/27/2019
ms.openlocfilehash: d52366684d772f91b53a1ab385b51ae4f11f0a5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158326"
---
# <a name="connect-a-rigado-cascade-500-gateway-device-to-your-azure-iot-central-application"></a>將里加多級聯 500 閘道設備連接到 Azure IoT 中央應用程式


本文介紹作為解決方案產生器，如何將 Rigado Cascade 500 閘道設備連接到 Microsoft Azure IoT 中央應用程式。 

## <a name="what-is-cascade-500"></a>什麼是級聯 500？

級聯 500 IoT 閘道是 Rigado 提供的硬體產品，包含在其級聯邊緣即服務解決方案中。 它為商業物聯網專案和產品團隊提供了靈活的邊緣計算能力、強大的容器化應用環境以及各種無線設備連接選項，包括藍牙 5、LTE、& Wi-Fi。

Cascade 500 已為 Azure IoT 隨插即用（預覽）進行了預認證，允許我們的解決方案構建者輕鬆地將設備送入端到端解決方案。 Cascade 閘道允許您以無線方式連接到靠近閘道設備的各種狀態監控感應器。 這些感應器可通過閘道設備進入 IoT 中心。

## <a name="prerequisites"></a>Prerequisites
要逐步完成本操作指南，您需要以下資源：

* 里加多級聯 500 設備。 欲瞭解更多資訊，請訪問[里加多](https://www.rigado.com/)。
* Azure IoT Central 應用程式。 有關詳細資訊，請參閱[創建新應用程式](./quick-deploy-iot-central.md)。

## <a name="add-a-device-template"></a>新增裝置範本

為了將 Cascade 500 閘道設備引入 Azure IoT 中央應用程式實例，您需要在應用程式中配置相應的設備範本。

要添加級聯 500 設備範本， 

1. 導航到左側窗格中的 ***"設備範本"*** 選項卡，選擇 **"新建**"：![創建新設備範本](./media/howto-connect-rigado-cascade-500/device-template-new.png)
1. 該頁面為您提供了***創建自訂範本***或使用***預配置設備範本***的選項
1. 從預配置的設備範本清單中選擇 C500 設備範本，如下所示：![選擇 C500 設備範本](./media/howto-connect-rigado-cascade-500/device-template-preconfigured.png)
1. 選擇 ***"下一步"：自訂***以繼續下一步。 
1. 在下一個螢幕上，選擇 ***"創建***"以將 C500 設備範本納入 IoT 中央應用程式。

## <a name="retrieve-application-connection-details"></a>檢索應用程式連接詳細資訊

現在，您需要檢索 Azure IoT 中央應用程式**的範圍 ID**和**主金鑰**，以便連接 Cascade 500 設備。 

1. 在左側窗格中導航到 **"管理****"，然後按一下"設備連接**"。 
2. 記下 IoT 中央應用程式**的範圍 ID。**
![應用範圍 ID](./media/howto-connect-rigado-cascade-500/app-scope-id.png)
3. 現在按一下 **"查看鍵**"並記下**主鍵主鍵**
 ![](./media/howto-connect-rigado-cascade-500/primary-key-sas.png)  

## <a name="contact-rigado-to-connect-the-gateway"></a>聯繫里加多連接閘道 

為了將 Cascade 500 設備連接到 IoT 中心應用程式，您需要聯繫 Rigado，並向他們提供上述步驟中的應用程式連接詳細資訊。 

一旦設備連接到互聯網，Rigado 將能夠通過安全通道向下推送到 Cascade 500 閘道設備的配置更新。 

此更新將在 Cascade 500 設備上應用 IoT 中央連接詳細資訊，並將顯示在您的設備清單中。 

![主索引鍵](./media/howto-connect-rigado-cascade-500/devices-list-c500.png)  

現在，您已準備好在 IoT 中央應用程式中使用 C500 設備！

## <a name="next-steps"></a>後續步驟

現在，您已經瞭解如何將 Rigado Cascade 500 連接到 Azure IoT 中央應用程式，建議的下一步是瞭解如何[創建店內分析應用程式](../retail/tutorial-in-store-analytics-create-app-pnp.md)以構建端到端解決方案。 
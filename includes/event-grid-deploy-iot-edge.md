---
title: 包含檔案
description: 包含檔案
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 10/10/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: b453a04a170764a037eed7415eaf71e5a4d37526
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844578"
---
## <a name="deploy-event-grid-iot-edge-module"></a>部署事件網格 IoT 邊緣模組

有幾種方法可以將模組部署到 IoT 邊緣設備，並且所有這些模組都適用于 IoT Edge 上的 Azure 事件網格。 本文介紹從 Azure 門戶在 IoT 邊緣上部署事件網格的步驟。

>[!NOTE]
> 在本教程中，您將部署事件網格模組，而不進行持久性。 這意味著在重新部署模組時，您在本教程中創建的任何主題和訂閱都將被刪除。 有關如何設置持久性的詳細資訊，請參閱以下文章[：Linux 中的持久狀態](../articles/event-grid/edge/persist-state-linux.md)或[Windows 中的持久狀態](../articles/event-grid/edge/persist-state-windows.md)。 對於生產工作負載，我們建議您安裝具有持久性的事件網格模組。

>[!IMPORTANT]
> 在本教程中，事件網格模組將在用戶端身份驗證關閉的情況下部署，並允許 HTTP 訂閱者。 對於生產工作負載，我們建議您僅啟用啟用用戶端身份驗證的 HTTPS 請求和訂閱者。 有關如何安全地配置事件網格模組的詳細資訊，請參閱[安全性和身份驗證](../articles/event-grid/edge/security-authentication.md)。
 
### <a name="select-your-iot-edge-device"></a>選擇 IoT 邊緣設備

1. 登錄到 Azure[門戶](https://portal.azure.com)
1. 瀏覽至您的 IoT 中樞。
1. 從 **"自動裝置管理**"部分中的功能表中選擇**IoT 邊緣**。 
1. 從設備清單中按一下目標設備的 ID
1. 選擇**設置模組**。 保持頁面打開。 您將繼續下一節中的步驟。

### <a name="configure-a-deployment-manifest"></a>設定部署資訊清單

部署資訊清單為 JSON 文件，說明應部署的模組、資料如何在模組之間流動，以及想要的模組對應項需要的屬性。 Azure 門戶具有一個嚮導，該嚮導引導您完成創建部署清單，而不是手動構建 JSON 文檔。  它有三個步驟：**新增模組**、**指定路由**和**檢閱部署**。

### <a name="add-modules"></a>新增模組

1. 在 **"部署模組"** 部分中，選擇 **"添加**
1. 從下拉清單中的模組類型中，選擇**IoT 邊緣模組**
1. 提供容器的名稱、圖像、容器創建選項：

[!INCLUDE [event-grid-edge-module-version-update](event-grid-edge-module-version-update.md)]

   * **名稱**： 事件網格模組
   * **圖像 URI**：`mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **容器建立選項**：

    ```json
        {
          "Env": [
            "inbound__clientAuth:clientCert__enabled=false",
            "outbound__webhook__httpsOnly=false"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                  "HostPort": "4438"
                }
              ]
            }
          }
        }
    ```

 1. 按一下 [儲存]****。
 1. 按一下 **"下一步**"以繼續到路徑部分

    > [!NOTE]
    > 如果使用 Azure VM 作為邊緣設備，請添加入站連接埠規則以允許埠 4438 上的入站流量。 有關添加規則的說明，請參閱[如何將埠打開到 VM](../articles/virtual-machines/windows/nsg-quickstart-portal.md)。


### <a name="setup-routes"></a>設置路由

 保留預設路由，然後選擇 **"下一步**"以繼續查看部分

### <a name="review-deployment"></a>檢閱部署

1. 檢閱區段會顯示 JSON 部署資訊清單，該清單會根據您在前兩個區段中的選項而建立。 確認您看到清單中的兩個模組 **：$edgeAgent**和 **$edgeHub**。 這兩個模組組成 IoT Edge 執行階段，在每個部署中都是必要的預設值。
1. 檢閱您的部署資訊，然後選取 [提交]****。

### <a name="verify-your-deployment"></a>驗證部署

1. 提交部署之後，您會返回您 IoT 中樞的 [IoT Edge] 頁面。
1. 選擇部署時針對的**IoT Edge 設備**以打開其詳細資訊。
1. 在設備詳細資訊中，驗證事件網格模組是否列為 **"部署中指定**"和 **"按設備報告**"。

模組在裝置上啟動然後向 IoT 中樞回報可能需要一點時間。 重新整理頁面來查看更新狀態。
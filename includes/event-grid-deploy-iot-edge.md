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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76844578"
---
## <a name="deploy-event-grid-iot-edge-module"></a>IoT Edge 模組部署事件方格

有數種方式可以將模組部署到 IoT Edge 裝置，並在 IoT Edge 的 Azure 事件方格中使用它們。 本文說明在 Azure 入口網站的 IoT Edge 上部署事件方格的步驟。

>[!NOTE]
> 在本教學課程中，您將會部署事件方格模組，而不會持續保留。 這表示當您重新部署模組時，將會刪除您在本教學課程中建立的任何主題和訂用帳戶。 如需如何設定持續性的詳細資訊，請參閱下列文章：[在 Linux 中保存狀態](../articles/event-grid/edge/persist-state-linux.md)或[在 Windows 中保存狀態](../articles/event-grid/edge/persist-state-windows.md)。 對於生產工作負載，我們建議您安裝具有持續性的事件方格模組。

>[!IMPORTANT]
> 在本教學課程中，將會在用戶端驗證關閉時部署事件方格模組，並允許 HTTP 訂閱者。 對於生產工作負載，建議您只啟用已啟用用戶端驗證的 HTTPS 要求和訂閱者。 如需有關如何安全地設定 Event Grid 模組的詳細資訊，請參閱[安全性和驗證](../articles/event-grid/edge/security-authentication.md)。
 
### <a name="select-your-iot-edge-device"></a>選取您的 IoT Edge 裝置

1. 登入 [Azure 入口網站](https://portal.azure.com)
1. 瀏覽至您的 IoT 中樞。
1. 從 [**自動裝置管理**] 區段的功能表中選取 [ **IoT Edge** ]。 
1. 從裝置清單中按一下目標裝置的識別碼
1. 選取 [**設定模組**]。 保持開啟頁面。 您將繼續進行下一節中的步驟。

### <a name="configure-a-deployment-manifest"></a>設定部署資訊清單

部署資訊清單為 JSON 文件，說明應部署的模組、資料如何在模組之間流動，以及想要的模組對應項需要的屬性。 Azure 入口網站有一個 wizard，會逐步引導您建立部署資訊清單，而不是手動建立 JSON 檔。  它有三個步驟：**新增模組**、**指定路由**和**檢閱部署**。

### <a name="add-modules"></a>新增模組

1. 在 [**部署模組**] 區段中，選取 [**新增**]
1. 從下拉式清單中的模組類型，選取 [ **IoT Edge 模組**]
1. 提供容器的 [名稱]、[映射]、[容器] 建立選項：

[!INCLUDE [event-grid-edge-module-version-update](event-grid-edge-module-version-update.md)]

   * **名稱**： eventgridmodule
   * **映射 URI**：`mcr.microsoft.com/azure-event-grid/iotedge:latest`
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

 1. 按一下 [儲存] 
 1. 按 **[下一步]** 繼續前往 [路由] 區段

    > [!NOTE]
    > 如果您使用 Azure VM 做為 edge 裝置，請新增輸入連接埠規則，以允許埠4438上的輸入流量。 如需新增規則的指示，請參閱[如何開啟 VM 的埠](../articles/virtual-machines/windows/nsg-quickstart-portal.md)。


### <a name="setup-routes"></a>設定路由

 保留預設路由，然後選取 **[下一步]** 以繼續前往 [審核] 區段

### <a name="review-deployment"></a>檢閱部署

1. 檢閱區段會顯示 JSON 部署資訊清單，該清單會根據您在前兩個區段中的選項而建立。 確認您在清單中看到兩個模組： [ **$edgeAgent** ] 和 [ **$edgeHub**]。 這兩個模組組成 IoT Edge 執行階段，在每個部署中都是必要的預設值。
1. 檢閱您的部署資訊，然後選取 [提交]****。

### <a name="verify-your-deployment"></a>驗證您的部署

1. 提交部署之後，您會返回您 IoT 中樞的 [IoT Edge] 頁面。
1. 選取您以部署為目標的**IoT Edge 裝置**，以開啟其詳細資料。
1. 在 [裝置詳細資料] 中，確認 [事件方格] 模組已列為 [**部署中**] 和 [**由裝置回報**]。

模組在裝置上啟動然後向 IoT 中樞回報可能需要一點時間。 重新整理頁面來查看更新狀態。
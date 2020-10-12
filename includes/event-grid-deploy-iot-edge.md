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
ms.openlocfilehash: 0fc6a62a94f31617209bcc60bfaa95bc8927551a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86050429"
---
## <a name="deploy-event-grid-iot-edge-module"></a>部署事件方格 IoT Edge 模組

有幾種方式可以將模組部署到 IoT Edge 裝置，而且所有的方法都適用于 IoT Edge 上的 Azure 事件方格。 本文說明從 Azure 入口網站在 IoT Edge 上部署事件方格的步驟。

>[!NOTE]
> 在本教學課程中，您將部署不具持續性的事件方格模組。 這表示當您重新部署模組時，將會刪除您在本教學課程中建立的任何主題和訂閱。 如需有關如何設定持續性的詳細資訊，請參閱下列文章：在 [Linux 中保存狀態](../articles/event-grid/edge/persist-state-linux.md) 或 [在 Windows 中保存](../articles/event-grid/edge/persist-state-windows.md)狀態。 針對生產工作負載，我們建議您安裝具有持續性的事件方格模組。

>[!IMPORTANT]
> 在本教學課程中，會使用關閉的用戶端驗證來部署事件方格模組，並允許 HTTP 訂閱者。 針對生產工作負載，建議您只啟用已啟用用戶端驗證的 HTTPS 要求和訂閱者。 如需如何安全地設定事件方格模組的詳細資訊，請參閱 [安全性和驗證](../articles/event-grid/edge/security-authentication.md)。
 
### <a name="select-your-iot-edge-device"></a>選取您的 IoT Edge 裝置

1. 登入 [Azure 入口網站](https://portal.azure.com)
1. 瀏覽至您的 IoT 中樞。
1. 在 [**自動裝置管理**] 區段中，從功能表選取 [ **IoT Edge** ]。 
1. 從裝置清單中按一下目標裝置的識別碼
1. 選取 [ **設定模組**]。 讓頁面保持開啟。 您將會繼續進行下一節中的步驟。

### <a name="configure-a-deployment-manifest"></a>設定部署資訊清單

部署資訊清單為 JSON 文件，說明應部署的模組、資料如何在模組之間流動，以及想要的模組對應項需要的屬性。 此 Azure 入口網站有一個嚮導，可逐步引導您建立部署資訊清單，而不是手動建立 JSON 檔。  它有三個步驟：**新增模組**、**指定路由**和**檢閱部署**。

### <a name="add-modules"></a>新增模組

1. 在 [**部署模組**] 區段中，選取 [**新增**]
1. 從下拉式清單中的模組類型，選取 [ **IoT Edge 模組**]
1. 提供容器的名稱、映射、容器建立選項：

[!INCLUDE [event-grid-edge-module-version-update](event-grid-edge-module-version-update.md)]

   * **名稱**： eventgridmodule
   * **映射 URI**： `mcr.microsoft.com/azure-event-grid/iotedge:latest`
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
    > 如果您使用 Azure VM 作為邊緣裝置，請新增輸入連接埠規則，以允許埠4438上的輸入流量。 如需新增規則的指示，請參閱 [如何開啟 VM 的埠](../articles/virtual-machines/windows/nsg-quickstart-portal.md)。


### <a name="setup-routes"></a>設定路由

 保留預設路由，然後選取 **[下一步]** 繼續進行審核區段

### <a name="review-deployment"></a>檢閱部署

1. 檢閱區段會顯示 JSON 部署資訊清單，該清單會根據您在前兩個區段中的選項而建立。 確認您在清單中看到兩個模組： **$edgeAgent** 並 **$edgeHub**。 這兩個模組組成 IoT Edge 執行階段，在每個部署中都是必要的預設值。
1. 檢閱您的部署資訊，然後選取 [提交]****。

### <a name="verify-your-deployment"></a>驗證您的部署

1. 提交部署之後，您會返回您 IoT 中樞的 [IoT Edge] 頁面。
1. 選取您要部署的目標 **IoT Edge 裝置** ，以開啟其詳細資料。
1. 在 [裝置詳細資料] 中，確認已在 [部署] 和 [**依裝置報告**]**中指定**事件方格模組。

模組在裝置上啟動然後向 IoT 中樞回報可能需要一點時間。 重新整理頁面來查看更新狀態。
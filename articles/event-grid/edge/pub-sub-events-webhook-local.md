---
title: 在本地發佈、訂閱事件 - Azure 事件網格 IoT 邊緣 |微軟文檔
description: 使用 IoT 邊緣的事件網格在本地發佈、訂閱事件
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ba82b1bea4753cd51e275a78b248247032d79a01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280998"
---
# <a name="tutorial-publish-subscribe-to-events-locally"></a>教程：在本地發佈、訂閱事件

本文將引導您完成使用 IoT Edge 上的事件網格發佈和訂閱事件所需的所有步驟。

> [!NOTE]
> 要瞭解 Azure 事件網格主題和訂閱，請參閱[事件網格概念](concepts.md)。

## <a name="prerequisites"></a>Prerequisites 
若要完成這個教學課程，您將需要：

* **Azure 訂閱**- 如果尚未創建[免費帳戶](https://azure.microsoft.com/free)，則創建免費帳戶。 
* **Azure IoT 中心和 IoT 邊緣設備**- 如果尚未運行 Linux[或](../../iot-edge/quickstart-linux.md)Windows 設備，請按照 Linux 或[Windows 設備](../../iot-edge/quickstart.md)的快速入門步驟操作。

## <a name="deploy-event-grid-iot-edge-module"></a>部署事件網格 IoT 邊緣模組

有幾種方法可以將模組部署到 IoT 邊緣設備，並且所有這些模組都適用于 IoT Edge 上的 Azure 事件網格。 本文介紹從 Azure 門戶在 IoT 邊緣上部署事件網格的步驟。

>[!NOTE]
> 在本教程中，您將部署事件網格模組，而不進行持久性。 這意味著在重新部署模組時，您在本教程中創建的任何主題和訂閱都將被刪除。 有關如何設置持久性的詳細資訊，請參閱以下文章[：Linux 中的持久狀態](persist-state-linux.md)或[Windows 中的持久狀態](persist-state-windows.md)。 對於生產工作負載，我們建議您安裝具有持久性的事件網格模組。


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

   * **名稱**： 事件網格模組
   * **圖像 URI**：`mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **容器建立選項**：

   [!INCLUDE [event-grid-edge-module-version-update](../../../includes/event-grid-edge-module-version-update.md)]

    ```json
        {
          "Env": [
            "inbound__clientAuth__clientCert__enabled=false"
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
 1. 繼續下一節以添加 Azure 事件網格訂閱伺服器模組，然後再將它們一起部署。

    >[!IMPORTANT]
    > 在本教程中，您將部署禁用用戶端身份驗證的事件網格模組。 對於生產工作負載，我們建議您啟用用戶端身份驗證。 有關如何安全地配置事件網格模組的詳細資訊，請參閱[安全性和身份驗證](security-authentication.md)。
    > 
    > 如果使用 Azure VM 作為邊緣設備，請添加入站連接埠規則以允許埠 4438 上的入站流量。 有關添加規則的說明，請參閱[如何將埠打開到 VM](../../virtual-machines/windows/nsg-quickstart-portal.md)。
    

## <a name="deploy-event-grid-subscriber-iot-edge-module"></a>部署事件網格訂閱者 IoT 邊緣模組

本節介紹如何部署另一個 IoT 模組，該模組將作為事件處理常式，將事件傳遞到該模組。

### <a name="add-modules"></a>新增模組

1. 在 **"部署模組"** 部分中，選擇"再次**添加**"。 
1. 從下拉清單中的模組類型中，選擇**IoT 邊緣模組**
1. 提供容器的名稱、圖像和容器創建選項：

   * **名稱**： 訂閱者
   * **圖像 URI**：`mcr.microsoft.com/azure-event-grid/iotedge-samplesubscriber:latest`
   * **容器創建選項**： 無
1. 按一下 [儲存]****。
1. 按一下 **"下一步**"以繼續到路徑部分

 ### <a name="setup-routes"></a>設置路由

保留預設路由，然後選擇 **"下一步**"以繼續查看部分

### <a name="submit-the-deployment-request"></a>提交部署請求

1. 審核部分顯示基於上一節中的選擇創建的 JSON 部署清單。 確認您同時看到 JSON 中列出的模組：**事件網格模組**和**訂閱者**。 
1. 檢閱您的部署資訊，然後選取 [提交]****。 提交部署後，您將返回到**設備**頁面。
1. 在 **"模組"部分**中，驗證**是否同時列出了事件網格**和**訂閱伺服器**模組。 此外，請驗證**部署中的指定**和**按設備列報告的**設置為 **"是**"。

    模組在裝置上啟動然後向 IoT 中樞回報可能需要一點時間。 重新整理頁面來查看更新狀態。

## <a name="create-a-topic"></a>建立主題

作為事件的發行者，您需要創建事件網格主題。 在 Azure 事件網格中，主題是指發行者可以向其發送事件的終結點。

1. 使用以下內容創建主題.json。 有關有效負載的詳細資訊，請參閱我們的[API 文檔](api.md)。

    ```json
        {
          "name": "sampleTopic1",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```

1. 運行以下命令以創建事件網格主題。 確認您看到 HTTP 狀態碼為`200 OK`。

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```

1. 運行以下命令以驗證主題已成功創建。 應返回 200 OK 的 HTTP 狀態碼。

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```

   範例輸出：

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic1",
            "name": "sampleTopic1",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic1/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-an-event-subscription"></a>建立事件訂閱

訂閱者可以註冊發佈到主題的事件。 要接收任何事件，您需要為感興趣的主題創建事件網格訂閱。

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. 使用以下內容創建訂閱.json。 有關有效負載的詳細資訊，請參閱我們的[API 文檔](api.md)

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
    ```

    >[!NOTE]
    > **終結點類型**屬性指定訂閱者是**Webhook**。  **終結點 Url**指定訂閱者偵聽事件的 URL。 此 URL 對應于前面部署的 Azure 訂閱伺服器示例。
2. 運行以下命令為主題創建訂閱。 確認您看到 HTTP 狀態碼為`200 OK`。

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/eventSubscriptions/sampleSubscription1?api-version=2019-01-01-preview
    ```
3. 運行以下命令以驗證已成功創建訂閱。 應返回 200 OK 的 HTTP 狀態碼。

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/eventSubscriptions/sampleSubscription1?api-version=2019-01-01-preview
    ```

    範例輸出：

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic1/eventSubscriptions/sampleSubscription1",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription1",
          "properties": {
            "Topic": "sampleTopic1",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
    ```

## <a name="publish-an-event"></a>發佈事件

1. 使用以下內容創建 event.json。 有關有效負載的詳細資訊，請參閱我們的[API 文檔](api.md)。

    ```json
        [
          {
            "id": "eventId-func-0",
            "eventType": "recordInserted",
            "subject": "myapp/vehicles/motorcycles",
            "eventTime": "2019-07-28T21:03:07+00:00",
            "dataVersion": "1.0",
            "data": {
              "make": "Ducati",
              "model": "Monster"
            }
          }
        ]
    ```
1. 運行以下命令以發佈事件。

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>驗證事件傳遞

1. SSH 或 RDP 進入 IoT 邊緣 VM。
1. 檢查訂閱者日誌：

    在 Windows 上，運行以下命令：

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine container logs subscriber
    ```

   在 Linux 上，運行以下命令：

    ```sh
    sudo docker logs subscriber
    ```

    範例輸出：

    ```sh
        Received Event:
            {
              "id": "eventId-func-0",
              "topic": "sampleTopic1",
              "subject": "myapp/vehicles/motorcycles",
              "eventType": "recordInserted",
              "eventTime": "2019-07-28T21:03:07+00:00",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "make": "Ducati",
                "model": "Monster"
              }
            }
    ```

## <a name="cleanup-resources"></a>清除資源

* 運行以下命令以刪除主題及其所有訂閱。

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```
* 從 IoT 邊緣設備中刪除訂閱者模組。


## <a name="next-steps"></a>後續步驟
在本教程中，您創建了事件網格主題、訂閱和已發佈的事件。 現在您已經瞭解了基本步驟，請參閱以下文章： 

- 要解決在 IoT 邊緣上使用 Azure 事件網格的問題，請參閱[故障排除指南](troubleshoot.md)。
- 使用篩選器創建/更新[訂閱](advanced-filtering.md)。
- 在[Linux](persist-state-linux.md)或[Windows](persist-state-windows.md)上啟用事件網格模組的持久性
- 按照[文檔](configure-client-auth.md)配置用戶端身份驗證
- 按照[本教程](pub-sub-events-webhook-cloud.md)將事件轉發到雲中的 Azure 函數
- [回應 IoT 邊緣的 Blob 存儲事件](react-blob-storage-events-locally.md)
- [監視邊緣的主題和訂閱](monitor-topics-subscriptions.md)


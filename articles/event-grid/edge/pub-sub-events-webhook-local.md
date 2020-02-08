---
title: 發佈，在本機訂閱事件-Azure Event Grid IoT Edge |Microsoft Docs
description: 在 IoT Edge 上使用 Webhook 搭配事件方格來發佈、訂閱事件（在本機上）
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ba82b1bea4753cd51e275a78b248247032d79a01
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086632"
---
# <a name="tutorial-publish-subscribe-to-events-locally"></a>教學課程：在本機發佈、訂閱事件

本文會逐步引導您完成在 IoT Edge 上使用事件方格發佈和訂閱事件所需的所有步驟。

> [!NOTE]
> 若要瞭解 Azure 事件方格主題和訂用帳戶，請參閱[事件方格概念](concepts.md)。

## <a name="prerequisites"></a>Prerequisites 
若要完成這個教學課程，您將需要：

* **Azure 訂**用帳戶-如果您還沒有帳戶，請建立一個[免費帳戶](https://azure.microsoft.com/free)。 
* **Azure IoT 中樞和 IoT Edge 裝置**-遵循[Linux](../../iot-edge/quickstart-linux.md)或[Windows 裝置](../../iot-edge/quickstart.md)的快速入門中的步驟（如果您還沒有的話）。

## <a name="deploy-event-grid-iot-edge-module"></a>IoT Edge 模組部署事件方格

有數種方式可以將模組部署到 IoT Edge 裝置，並在 IoT Edge 的 Azure 事件方格中使用它們。 本文說明在 Azure 入口網站的 IoT Edge 上部署事件方格的步驟。

>[!NOTE]
> 在本教學課程中，您將會部署事件方格模組，而不會持續保留。 這表示當您重新部署模組時，將會刪除您在本教學課程中建立的任何主題和訂用帳戶。 如需如何設定持續性的詳細資訊，請參閱下列文章：[在 Linux 中保存狀態](persist-state-linux.md)或[在 Windows 中保存狀態](persist-state-windows.md)。 對於生產工作負載，我們建議您安裝具有持續性的事件方格模組。


### <a name="select-your-iot-edge-device"></a>選取您的 IoT Edge 裝置

1. 登入 [Azure 入口網站](https://portal.azure.com)
1. 瀏覽至您的 IoT 中樞。
1. 從 [**自動裝置管理**] 區段的功能表中選取 [ **IoT Edge** ]。 
1. 從裝置清單中按一下目標裝置的識別碼
1. 選取 [設定模組]。 保持開啟頁面。 您將繼續進行下一節中的步驟。

### <a name="configure-a-deployment-manifest"></a>設定部署資訊清單

部署資訊清單為 JSON 文件，說明應部署的模組、資料如何在模組之間流動，以及想要的模組對應項需要的屬性。 Azure 入口網站有一個 wizard，會逐步引導您建立部署資訊清單，而不是手動建立 JSON 檔。  它有三個步驟：**新增模組**、**指定路由**和**檢閱部署**。

### <a name="add-modules"></a>新增模組

1. 在 [**部署模組**] 區段中，選取 [**新增**]
1. 從下拉式清單中的模組類型，選取 [ **IoT Edge 模組**]
1. 提供容器的 [名稱]、[映射]、[容器] 建立選項：

   * **名稱**： eventgridmodule
   * **映射 URI**： `mcr.microsoft.com/azure-event-grid/iotedge:latest`
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
 1. 按一下 [儲存]
 1. 繼續進行下一節，以新增 Azure Event Grid 訂閱者模組，然後再將它們部署在一起。

    >[!IMPORTANT]
    > 在本教學課程中，您將會部署已停用用戶端驗證的事件方格模組。 對於生產工作負載，我們建議您啟用用戶端驗證。 如需有關如何安全地設定 Event Grid 模組的詳細資訊，請參閱[安全性和驗證](security-authentication.md)。
    > 
    > 如果您使用 Azure VM 做為 edge 裝置，請新增輸入連接埠規則，以允許埠4438上的輸入流量。 如需新增規則的指示，請參閱[如何開啟 VM 的埠](../../virtual-machines/windows/nsg-quickstart-portal.md)。
    

## <a name="deploy-event-grid-subscriber-iot-edge-module"></a>IoT Edge 模組部署事件方格訂閱者

本節說明如何部署另一個 IoT 模組，做為可傳遞事件的事件處理常式。

### <a name="add-modules"></a>新增模組

1. 在 [**部署模組**] 區段中，再次選取 [**新增**]。 
1. 從下拉式清單中的模組類型，選取 [ **IoT Edge 模組**]
1. 提供容器的 [名稱]、[映射] 和 [容器] 建立選項：

   * **名稱**：訂閱者
   * **映射 URI**： `mcr.microsoft.com/azure-event-grid/iotedge-samplesubscriber:latest`
   * **容器建立選項**：無
1. 按一下 [儲存]
1. 按 **[下一步]** 繼續前往 [路由] 區段

 ### <a name="setup-routes"></a>設定路由

保留預設路由，然後選取 **[下一步]** 以繼續前往 [審核] 區段

### <a name="submit-the-deployment-request"></a>提交部署要求

1. [審查] 區段會顯示根據您在上一節中的選擇所建立的 JSON 部署資訊清單。 確認您看到的是 JSON 中所列的兩個模組： **eventgridmodule**和**訂閱者**。 
1. 檢閱您的部署資訊，然後選取 [提交]。 提交部署之後，您會返回 [**裝置**] 頁面。
1. 在 [**模組] 區段**中，確認已列出 [ **eventgrid** ] 和 [**訂閱者**] 模組。 此外，確認 [**部署中指定**的] 和 [**由裝置報告**] 資料行設定為 **[是]** 。

    模組在裝置上啟動然後向 IoT 中樞回報可能需要一點時間。 重新整理頁面來查看更新狀態。

## <a name="create-a-topic"></a>建立主題

身為事件的發行者，您必須建立事件方格主題。 在 Azure Event Grid 中，主題是指發行者可以將事件傳送至其中的端點。

1. 使用下列內容建立主題. json。 如需裝載的詳細資訊，請參閱我們的[API 檔](api.md)。

    ```json
        {
          "name": "sampleTopic1",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```

1. 執行下列命令來建立事件方格主題。 確認您看到 [HTTP 狀態碼] 為 [`200 OK`]。

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```

1. 執行下列命令，以確認已成功建立主題。 應傳回200正常的 HTTP 狀態碼。

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

訂閱者可以註冊發佈至主題的事件。 若要接收任何事件，您必須為感關注的主題建立 Event Grid 訂用帳戶。

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. 建立具有下列內容的訂用帳戶. json。 如需裝載的詳細資訊，請參閱我們的[API 檔](api.md)

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
    > **EndpointType**屬性會指定訂閱者為**Webhook**。  **EndpointUrl**會指定訂閱者接聽事件的 URL。 此 URL 會對應至您稍早部署的 Azure 訂閱者範例。
2. 執行下列命令來建立主題的訂用帳戶。 確認您看到 [HTTP 狀態碼] 為 [`200 OK`]。

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/eventSubscriptions/sampleSubscription1?api-version=2019-01-01-preview
    ```
3. 執行下列命令，確認已成功建立訂用帳戶。 應傳回200正常的 HTTP 狀態碼。

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

1. 建立包含下列內容的事件 json。 如需裝載的詳細資訊，請參閱我們的[API 檔](api.md)。

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
1. 執行下列命令以發行事件。

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>驗證事件傳遞

1. SSH 或 RDP 進入您的 IoT Edge VM。
1. 檢查訂閱者記錄檔：

    在 Windows 上，執行下列命令：

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine container logs subscriber
    ```

   在 Linux 上，執行下列命令：

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

* 執行下列命令來刪除主題及其所有訂用帳戶。

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```
* 從您的 IoT Edge 裝置刪除訂閱者模組。


## <a name="next-steps"></a>後續步驟
在本教學課程中，您已建立事件方格主題、訂用帳戶和已發佈的事件。 現在您已瞭解基本步驟，請參閱下列文章： 

- 若要針對在 IoT Edge 上使用 Azure 事件方格的問題進行疑難排解，請參閱[疑難排解指南](troubleshoot.md)。
- 使用[篩選器](advanced-filtering.md)建立/更新訂用帳戶。
- 在[Linux](persist-state-linux.md)或[Windows](persist-state-windows.md)上啟用事件方格模組的持續性
- 遵循[檔](configure-client-auth.md)以設定用戶端驗證
- 遵循本[教學](pub-sub-events-webhook-cloud.md)課程，將事件轉送到雲端中的 Azure Functions
- [回應 IoT Edge 上的 Blob 儲存體事件](react-blob-storage-events-locally.md)
- [監視邊緣上的主題和訂用帳戶](monitor-topics-subscriptions.md)


---
title: 回應 Blob 儲存體模組事件-Azure 事件方格 IoT Edge |Microsoft Docs
description: 回應 Blob 儲存體模組事件
author: arduppal
manager: brymat
ms.author: arduppal
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 230e158a970f8c815b1575403c013e30749124c5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005056"
---
# <a name="tutorial-react-to-blob-storage-events-on-iot-edge-preview"></a>教學課程：在 IoT Edge (Preview 上回應 Blob 儲存體事件) 
本文說明如何在 IoT 模組上部署 Azure Blob 儲存體，其可作為事件方格發行者，以將 Blob 的建立和刪除的事件傳送至事件方格。  

如需 IoT Edge 的 Azure Blob 儲存體總覽，請參閱 IoT Edge 和其功能 [上的 Azure Blob 儲存體](../../iot-edge/how-to-store-data-blob.md) 。

> [!WARNING]
> IoT Edge 與事件方格整合的 Azure Blob 儲存體目前為預覽狀態

若要完成這個教學課程，您將需要：

* **Azure 訂** 用帳戶-如果您還沒有帳戶，請建立一個 [免費帳戶](https://azure.microsoft.com/free) 。 
* **Azure IoT 中樞和 IoT Edge 裝置** -如果您還沒有 [Linux](../../iot-edge/quickstart-linux.md) 或 [Windows 裝置](../../iot-edge/quickstart.md) 的快速入門中的步驟，請遵循這些步驟。

## <a name="deploy-event-grid-iot-edge-module"></a>部署事件方格 IoT Edge 模組

有幾種方式可以將模組部署到 IoT Edge 裝置，而且所有的方法都適用于 IoT Edge 上的 Azure 事件方格。 本文說明從 Azure 入口網站在 IoT Edge 上部署事件方格的步驟。

>[!NOTE]
> 在本教學課程中，您將部署不具持續性的事件方格模組。 這表示當您重新部署模組時，將會刪除您在本教學課程中建立的任何主題和訂閱。 如需有關如何設定持續性的詳細資訊，請參閱下列文章：在 [Linux 中保存狀態](persist-state-linux.md) 或 [在 Windows 中保存](persist-state-windows.md)狀態。 針對生產工作負載，我們建議您安裝具有持續性的事件方格模組。


### <a name="select-your-iot-edge-device"></a>選取您的 IoT Edge 裝置

1. 登入 [Azure 入口網站](https://portal.azure.com)
1. 瀏覽至您的 IoT 中樞。
1. 在 [**自動裝置管理**] 區段中，從功能表選取 [ **IoT Edge** ]。 
1. 從裝置清單中按一下目標裝置的識別碼
1. 選取 [ **設定模組**]。 讓頁面保持開啟。 您將會繼續進行下一節中的步驟。

### <a name="configure-a-deployment-manifest"></a>設定部署資訊清單

部署資訊清單為 JSON 文件，說明應部署的模組、資料如何在模組之間流動，以及想要的模組對應項需要的屬性。 此 Azure 入口網站有一個嚮導，可逐步引導您建立部署資訊清單，而不是手動建立 JSON 檔。  它有三個步驟：**新增模組**、**指定路由** 和 **檢閱部署**。

### <a name="add-modules"></a>新增模組

1. 在 [**部署模組**] 區段中，選取 [**新增**]
1. 從下拉式清單中的模組類型，選取 [ **IoT Edge 模組**]
1. 提供容器的名稱、映射、容器建立選項：

   * **名稱**： eventgridmodule
   * **映射 URI**： `mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **容器建立選項**：

    ```json
        {
          "Env": [
           "inbound__serverAuth__tlsPolicy=enabled",
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
 1. 繼續進行下一節，以新增 Azure Event Grid 訂閱者模組，然後將其部署在一起。

    >[!IMPORTANT]
    > 在本教學課程中，您將瞭解如何部署事件方格模組，以允許 HTTP/HTTPs 要求和用戶端驗證已停用。 針對生產工作負載，建議您只啟用已啟用用戶端驗證的 HTTPs 要求和訂閱者。 如需如何安全地設定事件方格模組的詳細資訊，請參閱 [安全性和驗證](security-authentication.md)。
    

## <a name="deploy-event-grid-subscriber-iot-edge-module"></a>將事件方格訂閱者部署 IoT Edge 模組

本節說明如何部署另一個 IoT 模組，作為事件可傳遞的事件處理常式。

### <a name="add-modules"></a>新增模組

1. 在 [ **部署模組** ] 區段中，再次選取 [ **新增** ]。 
1. 從下拉式清單中的模組類型，選取 [ **IoT Edge 模組**]
1. 提供容器的名稱、映射和容器建立選項：

   * **名稱**：訂閱者
   * **映射 URI**： `mcr.microsoft.com/azure-event-grid/iotedge-samplesubscriber:latest`
   * **容器建立選項**：無
1. 按一下 [儲存] 
1. 繼續進行下一節以新增 Azure Blob 儲存體模組

## <a name="deploy-azure-blob-storage-module"></a>部署 Azure Blob 儲存體模組

本節說明如何部署 Azure Blob 儲存體模組，該模組會作為事件方格發行者發佈 Blob 的建立和刪除事件。

### <a name="add-modules"></a>新增模組

1. 在 [**部署模組**] 區段中，選取 [**新增**]
2. 從下拉式清單中的模組類型，選取 [ **IoT Edge 模組**]
3. 提供容器的名稱、映射和容器建立選項：

   * **名稱**： azureblobstorageoniotedge
   * **映射 URI**： mcr.microsoft.com/azure-blob-storage:latest
   * **容器建立選項**：

   ```json
       {
         "Env":[
           "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
           "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>",
           "EVENTGRID_ENDPOINT=http://<event grid module name>:5888"
         ],
         "HostConfig":{
           "Binds":[
               "<storage mount>"
           ],
           "PortBindings":{
             "11002/tcp":[{"HostPort":"11002"}]
           }
         }
       }
   ```

   > [!IMPORTANT]
   > - Blob 儲存體模組可以使用 HTTPS 和 HTTP 發佈事件。 
   > - 如果您已針對 EventGrid 啟用以用戶端為基礎的驗證，請務必將 EVENTGRID_ENDPOINT 的值更新為允許 HTTPs，如下所示： `EVENTGRID_ENDPOINT=https://<event grid module name>:4438` 。
   > - 也請將另一個環境變數新增 `AllowUnknownCertificateAuthority=true` 至上述 Json。 透過 HTTPS 與 EventGrid 說話時， **AllowUnknownCertificateAuthority** 允許存放裝置模組信任自我簽署的 EventGrid 伺服器憑證。

4. 使用下列資訊來更新您複製的 JSON：

   - 將 `<your storage account name>` 取代為您可以記住的名稱。 帳戶名稱的長度必須介於3到24個字元之間，並包含小寫字母和數位。 無空格。

   - 使用 64 位元組 base64 金鑰取代 `<your storage account key>`。 您可以 [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64) 之類的工具產生金鑰。 您將使用這些認證，從其他模組存取 Blob 儲存體。

   - 取代 `<event grid module name>` 為您的事件方格模組名稱。
   - `<storage mount>`根據您的容器作業系統來取代。
     - 針對 Linux 容器， **我的磁片區：/blobroot**
     - 針對 Windows 容器，**我的磁片區： C：/BlobRoot**

5. 按一下 [儲存] 
6. 按 **[下一步]** 繼續前往 [路由] 區段

    > [!NOTE]
    > 如果您使用 Azure VM 作為 edge 裝置，請新增輸入連接埠規則，以允許本教學課程所使用之主機埠上的輸入流量：4438、5888、8080和11002。 如需新增規則的指示，請參閱 [如何開啟 VM 的埠](../../virtual-machines/windows/nsg-quickstart-portal.md)。

### <a name="setup-routes"></a>設定路由

保留預設路由，然後選取 **[下一步]** 繼續進行審核區段

### <a name="review-deployment"></a>檢閱部署

1. [審核] 區段會顯示根據您在上一節中所做選擇所建立的 JSON 部署資訊清單。 確認您看到下列四個模組： **$edgeAgent**、 **$edgeHub**、 **eventgridmodule**、 **訂閱者** 和 **azureblobstorageoniotedge** 都已部署。
2. 檢閱您的部署資訊，然後選取 [提交]。

## <a name="verify-your-deployment"></a>驗證您的部署

1. 提交部署之後，您會返回您 IoT 中樞的 [IoT Edge] 頁面。
2. 選取您要部署的目標 **IoT Edge 裝置** ，以開啟其詳細資料。
3. 在 [裝置詳細資料] 中，確認 [eventgridmodule]、[訂閱者] 和 [azureblobstorageoniotedge] 模組都列為 [部署] 和 [**由裝置報告**]**中所指定**。

   模組在裝置上啟動然後向 IoT 中樞回報可能需要一點時間。 重新整理頁面來查看更新狀態。

## <a name="publish-blobcreated-and-blobdeleted-events"></a>發行 BlobCreated 和 BlobDeleted 事件

1. 此課程模組會自動建立主題 **MicrosoftStorage**。 確認它存在
    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview
    ```

    範例輸出：

    ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/MicrosoftStorage",
            "name": "MicrosoftStorage",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/MicrosoftStorage/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
    ```

    > [!IMPORTANT]
    > - 針對 HTTPS 流程，如果透過 SAS 金鑰啟用用戶端驗證，則應該將稍早指定的 SAS 金鑰新增為標頭。 因此，捲曲的要求將會是： `curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview`
    > - 若是 HTTPS 流程，如果透過憑證啟用用戶端驗證，則捲曲要求將會是： `curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview`

2. 訂閱者可以註冊發行至主題的事件。 若要接收任何事件，您必須建立 **MicrosoftStorage** 主題的 event Grid 訂用帳戶。
    1. 使用下列內容建立 blobsubscription.js。 如需承載的詳細資訊，請參閱我們的 [API 檔](api.md)

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
       > **EndpointType** 屬性會指定訂閱者為 **Webhook**。  **EndpointUrl** 會指定訂閱者接聽事件的 URL。 此 URL 會對應至您稍早部署的 Azure Function 範例。

    2. 執行下列命令來建立主題的訂用帳戶。 確認您看到 HTTP 狀態碼為 `200 OK` 。

       ```sh
       curl -k -H "Content-Type: application/json" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
       ```

       > [!IMPORTANT]
       > - 針對 HTTPS 流程，如果透過 SAS 金鑰啟用用戶端驗證，則應該將稍早指定的 SAS 金鑰新增為標頭。 因此，捲曲的要求將會是： `curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview` 
       > - 若是 HTTPS 流程，如果透過憑證啟用用戶端驗證，則捲曲要求將會是：`curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`

    3. 執行下列命令以確認已成功建立訂用帳戶。 應傳回 HTTP 狀態碼 200 OK。

       ```sh
       curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
       ```

       範例輸出：

       ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription5",
          "properties": {
            "Topic": "MicrosoftStorage",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
       ```

       > [!IMPORTANT]
       > - 針對 HTTPS 流程，如果透過 SAS 金鑰啟用用戶端驗證，則應該將稍早指定的 SAS 金鑰新增為標頭。 因此，捲曲的要求將會是： `curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`
       > - 若是 HTTPS 流程，如果透過憑證啟用用戶端驗證，則捲曲要求將會是： `curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`

3. 下載 [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/) ，並 [將其連線至您的本機儲存體](../../iot-edge/how-to-store-data-blob.md#connect-to-your-local-storage-with-azure-storage-explorer)

## <a name="verify-event-delivery"></a>確認事件傳遞

### <a name="verify-blobcreated-event-delivery"></a>確認 BlobCreated 事件傳遞

1. 從 Azure 儲存體總管將檔案以區塊 blob 的形式上傳至本機儲存體，模組會自動發佈建立事件。 
2. 查看建立事件的訂閱者記錄。 遵循下列步驟來 [確認事件傳遞](pub-sub-events-webhook-local.md#verify-event-delivery)

    範例輸出：

    ```json
            Received Event:
            {
              "id": "d278f2aa-2558-41aa-816b-e6d8cc8fa140",
              "topic": "MicrosoftStorage",
              "subject": "/blobServices/default/containers/cont1/blobs/Team.jpg",
              "eventType": "Microsoft.Storage.BlobCreated",
              "eventTime": "2019-10-01T21:35:17.7219554Z",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "api": "PutBlob",
                "clientRequestId": "00000000-0000-0000-0000-000000000000",
                "requestId": "ef1c387b-4c3c-4ac0-8e04-ff73c859bfdc",
                "eTag": "0x8D746B740DA21FB",
                "url": "http://azureblobstorageoniotedge:11002/myaccount/cont1/Team.jpg",
                "contentType": "image/jpeg",
                "contentLength": 858129,
                "blobType": "BlockBlob"
              }
            }
    ```

### <a name="verify-blobdeleted-event-delivery"></a>確認 BlobDeleted 事件傳遞

1. 使用 Azure 儲存體總管從本機儲存體刪除 blob，模組會自動發行刪除事件。 
2. 查看刪除事件的訂閱者記錄。 遵循下列步驟來 [確認事件傳遞](pub-sub-events-webhook-local.md#verify-event-delivery)

    範例輸出：
    
    ```json
            Received Event:
            {
              "id": "ac669b6f-8b0a-41f3-a6be-812a3ce6ac6d",
              "topic": "MicrosoftStorage",
              "subject": "/blobServices/default/containers/cont1/blobs/Team.jpg",
              "eventType": "Microsoft.Storage.BlobDeleted",
              "eventTime": "2019-10-01T21:36:09.2562941Z",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "api": "DeleteBlob",
                "clientRequestId": "00000000-0000-0000-0000-000000000000",
                "requestId": "2996bbfb-c819-4d02-92b1-c468cc67d8c6",
                "eTag": "0x8D746B740DA21FB",
                "url": "http://azureblobstorageoniotedge:11002/myaccount/cont1/Team.jpg",
                "contentType": "image/jpeg",
                "contentLength": 858129,
                "blobType": "BlockBlob"
              }
            }
    ```

恭喜！ 您已完成本教學課程。 下列各節提供事件屬性的詳細資料。

### <a name="event-properties"></a>事件屬性

以下是支援的事件屬性及其類型與描述的清單。 

| 屬性 | 類型 | 描述 |
| -------- | ---- | ----------- |
| 主題 | 字串 | 事件來源的完整資源路徑。 此欄位不可寫入。 Event Grid 提供此值。 |
| subject | 字串 | 發行者定義事件主旨的路徑。 |
| eventType | 字串 | 此事件來源已註冊的事件類型之一。 |
| eventTime | 字串 | 事件產生的時間，以提供者之 UTC 時間為準。 |
| id | 字串 | 事件的唯一識別碼。 |
| data | 物件 (object) | blob 儲存體帳戶。 |
| dataVersion | 字串 | 資料物件的結構描述版本。 發行者會定義結構描述版本。 |
| metadataVersion | 字串 | 事件中繼資料的結構描述版本。 「事件方格」會定義最上層屬性的結構描述。 「事件方格」提供此值。 |

資料物件具有下列屬性：

| 屬性 | 類型 | 描述 |
| -------- | ---- | ----------- |
| api | 字串 | 觸發事件的作業。 它可能是下列其中一個值： <ul><li>BlobCreated-允許的值為： `PutBlob` 和 `PutBlockList`</li><li>BlobDeleted-允許的值為 `DeleteBlob` 、 `DeleteAfterUpload` 和 `AutoDelete` 。 <p>`DeleteAfterUpload`當 blob 因為 deleteAfterUpload 所需屬性設定為 true 而自動刪除時，就會產生此事件。 </p><p>`AutoDelete` 當 blob 因為 deleteAfterMinutes 所需屬性值過期而自動刪除時，就會產生事件。</p></li></ul>|
| clientRequestId | 字串 | 用戶端提供的儲存體 API 作業要求識別碼。 您可以使用此識別碼，在記錄檔中使用 "client-request-id" 欄位將 Azure 儲存體診斷記錄相互關聯，並可在用戶端要求中使用 "x------------------------- 如需詳細資訊，請參閱 [記錄檔格式](/rest/api/storageservices/storage-analytics-log-format)。 |
| requestId | 字串 | 服務產生的儲存體 API 作業要求識別碼。 可用於利用記錄中的 "request-id-header" 欄位與 Azure 儲存體診斷記錄建立關聯，並從 'x-ms-request-id' 標頭中的 API 呼叫初始化傳回。 請參閱[記錄格式](/rest/api/storageservices/storage-analytics-log-format)。 |
| etag | 字串 | 此值可讓您依條件執行作業。 |
| ContentType | 字串 | 為 blob 指定內容類型。 |
| contentLength | integer | Blob 大小 (以位元組為單位)。 |
| blobType | 字串 | Blob 的類型。 有效值為 "BlockBlob" 或 "PageBlob"。 |
| url | 字串 | blob 的路徑。 <br>如果用戶端使用 Blob REST API，則 url 會有下列結構： *\<storage-account-name\> . blob.core.windows.net/ \<container-name\> / \<file-name\>*。 <br>如果用戶端使用 Data Lake Storage REST API，則 url 會有下列結構： *\<storage-account-name\> . dfs.core.windows.net/ \<file-system-name\> / \<file-name\>*。 |


## <a name="next-steps"></a>後續步驟

請參閱下列 Blob 儲存體檔中的文章：

- [篩選 Blob 儲存體事件](../../storage/blobs/storage-blob-event-overview.md#filtering-events)
- [使用 Blob 儲存體事件的建議做法](../../storage/blobs/storage-blob-event-overview.md#practices-for-consuming-events)

在本教學課程中，您已透過在 Azure Blob 儲存體中建立或刪除 blob 來發佈事件。 請參閱其他教學課程，以瞭解如何將事件轉送至雲端 (Azure 事件中樞或 Azure IoT 中樞) ： 

- [將事件轉送至 Azure 事件方格](forward-events-event-grid-cloud.md)
- [將事件轉送至 Azure IoT 中樞](forward-events-iothub.md)

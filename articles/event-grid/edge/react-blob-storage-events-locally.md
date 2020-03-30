---
title: 回應 Blob 存儲模組事件 - Azure 事件網格 IoT 邊緣 |微軟文檔
description: 回應 Blob 存儲模組事件
author: arduppal
manager: brymat
ms.author: arduppal
ms.reviewer: spelluru
ms.date: 12/13/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 3360b92a1b71adcbf0364a16c197aecdab5700db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086597"
---
# <a name="tutorial-react-to-blob-storage-events-on-iot-edge-preview"></a>教程：對 IoT 邊緣上的 Blob 存儲事件做出反應（預覽）
本文介紹如何在 IoT 模組上部署 Azure Blob 存儲，該模組將充當事件網格發行者，將有關 Blob 創建和 Blob 刪除的事件發送到事件網格。  

有關 IoT Edge 上的 Azure Blob 存儲的概述，請參閱[IoT 邊緣上的 Azure Blob 存儲](../../iot-edge/how-to-store-data-blob.md)及其功能。

> [!WARNING]
> IoT 邊緣與事件網格集成上的 Azure Blob 存儲處於預覽狀態

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

 1. 按一下 [儲存]****。
 1. 繼續下一節以添加 Azure 事件網格訂閱伺服器模組，然後再將它們一起部署。

    >[!IMPORTANT]
    > 在本教程中，您將學習部署事件網格模組，以允許同時禁用 HTTP/HTTP 要求，禁用用戶端身份驗證。 對於生產工作負載，我們建議您僅啟用啟用用戶端身份驗證的 HTTP 要求和訂閱者。 有關如何安全地配置事件網格模組的詳細資訊，請參閱[安全性和身份驗證](security-authentication.md)。
    

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
1. 繼續下一節以添加 Azure Blob 存儲模組

## <a name="deploy-azure-blob-storage-module"></a>部署 Azure Blob 存儲模組

本節介紹如何部署 Azure Blob 存儲模組，該模組將充當發佈創建和刪除的 Blob 的事件網格發行者。

### <a name="add-modules"></a>新增模組

1. 在 **"部署模組"** 部分中，選擇 **"添加**
2. 從下拉清單中的模組類型中，選擇**IoT 邊緣模組**
3. 提供容器的名稱、圖像和容器創建選項：

   * **名稱**： azure blob 存儲oniotedge
   * **圖像 URI**： mcr.microsoft.com/azure-blob-storage:latest
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
   > - Blob 存儲模組可以使用 HTTPS 和 HTTP 發佈事件。 
   > - 如果已為 EventGrid 啟用基於用戶端的身份驗證，請確保更新EVENTGRID_ENDPOINT的值以允許 HTTPs，如下所示： `EVENTGRID_ENDPOINT=https://<event grid module name>:4438`。
   > - 此外，向上述`AllowUnknownCertificateAuthority=true`Json 添加另一個環境變數。 通過 HTTPS 與 EventGrid 對話時，**允許未知憑證授權單位**允許存儲模組信任自簽名的 EventGrid 伺服器憑證。

4. 使用下列資訊來更新您複製的 JSON：

   - 將 `<your storage account name>` 取代為您可以記住的名稱。 帳戶名稱應為 3 到 24 個字元長，帶有小寫字母和數位。 無空格。

   - 使用 64 位元組 base64 金鑰取代 `<your storage account key>`。 您可以 [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64) 之類的工具產生金鑰。 您將使用這些認證，從其他模組存取 Blob 儲存體。

   - 替換為`<event grid module name>`事件網格模組的名稱。
   - 根據`<storage mount>`容器作業系統進行更換。
     - 對於 Linux 容器，**我的卷：/blobroot**
     - 對於 Windows 容器，**我的卷：C：/BlobRoot**

5. 按一下 [儲存]****。
6. 按一下 **"下一步**"以繼續到路徑部分

    > [!NOTE]
    > 如果使用 Azure VM 作為邊緣設備，請添加入站連接埠規則，以允許本教程中使用的主機埠上的入站流量：4438、5888、8080 和 11002。 有關添加規則的說明，請參閱[如何將埠打開到 VM](../../virtual-machines/windows/nsg-quickstart-portal.md)。

### <a name="setup-routes"></a>設置路由

保留預設路由，然後選擇 **"下一步**"以繼續查看部分

### <a name="review-deployment"></a>檢閱部署

1. 審核部分顯示基於上一節中的選擇創建的 JSON 部署清單。 確認您將看到以下四個模組 **：$edgeAgent、$edgeHub、****事件網格模組**、**訂閱者**和**Azureblob 存儲oniotedge，** 所有這些模組都已部署。 **$edgeHub**
2. 檢閱您的部署資訊，然後選取 [提交]****。

## <a name="verify-your-deployment"></a>驗證部署

1. 提交部署之後，您會返回您 IoT 中樞的 [IoT Edge] 頁面。
2. 選擇部署時針對的**IoT Edge 設備**以打開其詳細資訊。
3. 在設備詳細資訊中，驗證事件網格模組、訂閱者和 Azureblobstorageonioedge 模組是否同時列為 **"部署中指定**"和 **"按設備報告**"。

   模組在裝置上啟動然後向 IoT 中樞回報可能需要一點時間。 重新整理頁面來查看更新狀態。

## <a name="publish-blobcreated-and-blobdeleted-events"></a>發佈 Blob 創建和 Blob 刪除事件

1. 此模組會自動創建主題**微軟存儲**。 驗證它是否存在
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
    > - 對於 HTTPS 流，如果通過 SAS 金鑰啟用用戶端身份驗證，則應將前面指定的 SAS 金鑰添加為標頭。 因此，捲曲請求將是：`curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview`
    > - 對於 HTTPS 流，如果通過證書啟用用戶端身份驗證，則 curl 請求將為：`curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview`

2. 訂閱者可以註冊發佈到主題的事件。 要接收任何事件，您需要為**Microsoft 存儲**主題創建事件網格訂閱。
    1. 使用以下內容創建 blob 訂閱.json。 有關有效負載的詳細資訊，請參閱我們的[API 文檔](api.md)

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
       > **終結點類型**屬性指定訂閱者是**Webhook**。  **終結點 Url**指定訂閱者偵聽事件的 URL。 此 URL 對應于前面部署的 Azure 函數示例。

    2. 運行以下命令為主題創建訂閱。 確認您看到 HTTP 狀態碼為`200 OK`。

       ```sh
       curl -k -H "Content-Type: application/json" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
       ```

       > [!IMPORTANT]
       > - 對於 HTTPS 流，如果通過 SAS 金鑰啟用用戶端身份驗證，則應將前面指定的 SAS 金鑰添加為標頭。 因此，捲曲請求將是：`curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview` 
       > - 對於 HTTPS 流，如果通過證書啟用用戶端身份驗證，則 curl 請求將為：`curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`

    3. 運行以下命令以驗證已成功創建訂閱。 應返回 200 OK 的 HTTP 狀態碼。

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
       > - 對於 HTTPS 流，如果通過 SAS 金鑰啟用用戶端身份驗證，則應將前面指定的 SAS 金鑰添加為標頭。 因此，捲曲請求將是：`curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`
       > - 對於 HTTPS 流，如果通過證書啟用用戶端身份驗證，則 curl 請求將為：`curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`

3. 下載[Azure 存儲資源管理器](https://azure.microsoft.com/features/storage-explorer/)[並將其連接到本機存放區](../../iot-edge/how-to-store-data-blob.md#connect-to-your-local-storage-with-azure-storage-explorer)

## <a name="verify-event-delivery"></a>驗證事件傳遞

### <a name="verify-blobcreated-event-delivery"></a>驗證 Blob 創建的事件傳遞

1. 將檔作為塊 Blob 上載到 Azure 存儲資源管理器的本機存放區，模組將自動發佈創建事件。 
2. 查看創建事件的訂閱者日誌。 按照步驟[驗證事件傳遞](pub-sub-events-webhook-local.md#verify-event-delivery)

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

### <a name="verify-blobdeleted-event-delivery"></a>驗證 Blob 已刪除的事件傳遞

1. 使用 Azure 存儲資源管理器從本機存放區中刪除 blob，模組將自動發佈刪除事件。 
2. 查看刪除事件的訂閱者日誌。 按照步驟[驗證事件傳遞](pub-sub-events-webhook-local.md#verify-event-delivery)

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

恭喜！ 您已完成本教程。 以下各節提供有關事件屬性的詳細資訊。

### <a name="event-properties"></a>事件屬性

下面是支援的事件屬性及其類型和說明的清單。 

| 屬性 | 類型 | 描述 |
| -------- | ---- | ----------- |
| 主題 | 字串 | 事件來源的完整資源路徑。 此欄位不可寫入。 Event Grid 提供此值。 |
| subject | 字串 | 發行者定義事件主體的路徑。 |
| eventType | 字串 | 此事件來源已註冊的事件類型之一。 |
| eventTime | 字串 | 事件產生的時間，以提供者之 UTC 時間為準。 |
| id | 字串 | 事件的唯一識別碼。 |
| data | 物件 (object) | blob 儲存體帳戶。 |
| dataVersion | 字串 | 資料物件的結構描述版本。 發行者會定義結構描述版本。 |
| metadataVersion | 字串 | 事件中繼資料的結構描述版本。 Event Grid 會定義最上層屬性的結構描述。 Event Grid 提供此值。 |

資料物件具有下列屬性：

| 屬性 | 類型 | 描述 |
| -------- | ---- | ----------- |
| api | 字串 | 觸發事件的作業。 它可能是下列其中一個值： <ul><li>Blob 創建 - 允許`PutBlob`的值是：`PutBlockList`</li><li>BlobDelete- 允許的值為`DeleteBlob``DeleteAfterUpload`和`AutoDelete`。 <p>當`DeleteAfterUpload`Blob 自動刪除時，將生成該事件，因為 DeleteAfterUpload 所需的屬性設置為 true。 </p><p>`AutoDelete`當 Blob 自動刪除時建置事件，因為刪除"刪除後分鐘"所需的屬性值已過期。</p></li></ul>|
| clientRequestId | 字串 | 存儲 API 操作的用戶端提供的請求 ID。 此 ID 可用於使用日誌中的"用戶端-請求-id"欄位與 Azure 存儲診斷日誌相關聯，並且可以使用"x-ms-用戶端-請求-id"標頭在用戶端請求中提供。 有關詳細資訊，請參閱[日誌格式](/rest/api/storageservices/storage-analytics-log-format)。 |
| requestId | 字串 | 存儲 API 操作的服務生成的請求 ID。 可用於利用記錄中的 "request-id-header" 欄位與 Azure 儲存體診斷記錄建立關聯，並從 'x-ms-request-id' 標頭中的 API 呼叫初始化傳回。 請參閱[記錄格式](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format)。 |
| etag | 字串 | 此值可讓您依條件執行作業。 |
| ContentType | 字串 | 為 blob 指定內容類型。 |
| contentLength | integer | Blob 大小 (以位元組為單位)。 |
| blobType | 字串 | Blob 的類型。 有效值為 "BlockBlob" 或 "PageBlob"。 |
| url | 字串 | blob 的路徑。 <br>如果用戶端使用 Blob REST API，則 URL 具有此結構：*\<存儲帳戶名稱\>\<.blob.core.windows.net/容器名稱\>/\<檔案名\>*。 <br>如果用戶端使用 Data Lake 存儲 REST API，則 URL 具有此結構：*\<存儲\>帳戶名稱\<.dfs.core.windows.net/檔案系統名稱\>/\<檔案名\>*。 |


## <a name="next-steps"></a>後續步驟

請參閱 Blob 存儲文檔中的以下文章：

- [篩選 Blob 存儲事件](../../storage/blobs/storage-blob-event-overview.md#filtering-events)
- [用於使用 Blob 存儲事件的推薦做法](../../storage/blobs/storage-blob-event-overview.md#practices-for-consuming-events)

在本教程中，通過創建或刪除 Azure Blob 存儲中的 Blob 來發佈事件。 請參閱其他教程，瞭解如何將事件轉發到雲（Azure 事件中心或 Azure IoT 中心）： 

- [將事件轉送至 Azure 事件方格](forward-events-event-grid-cloud.md)
- [將事件轉送至 Azure IoT 中樞](forward-events-iothub.md)

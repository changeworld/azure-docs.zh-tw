---
title: 將模組上的 blob 儲存體部署至您的裝置-Azure IoT Edge
description: 將 Azure Blob 儲存體模組部署到您的 IoT Edge 裝置，即可在邊緣儲存資料。
author: kgremban
ms.author: kgremban
ms.date: 3/10/2020
ms.topic: conceptual
ms.service: iot-edge
ms.reviewer: arduppal
ms.openlocfilehash: 12f0af5f051d02945eeb9b1f7d4bfc50ef98f281
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2020
ms.locfileid: "93027630"
---
# <a name="deploy-the-azure-blob-storage-on-iot-edge-module-to-your-device"></a>將 IoT Edge 模組上的 Azure Blob 儲存體部署至您的裝置

有幾種方式可以將模組部署到 IoT Edge 裝置，而且所有模組都適用于 IoT Edge 模組上的 Azure Blob 儲存體。 兩個最簡單的方法是使用 Azure 入口網站或 Visual Studio Code 範本。

## <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶中的 [IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)。
- IoT Edge 裝置。

  如果您沒有設定 IoT Edge 裝置，可以在 Azure 虛擬機器中建立一個。 遵循其中一個快速入門文章中的步驟， [建立虛擬 Linux 裝置](quickstart-linux.md) 或 [建立虛擬 Windows 裝置](quickstart.md)。

- 如果從 Visual Studio Code 部署， [Visual Studio Code](https://code.visualstudio.com/)和[Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 。

## <a name="deploy-from-the-azure-portal"></a>從 Azure 入口網站部署

Azure 入口網站會引導您建立部署資訊清單，並將部署推送至 IoT Edge 裝置。

### <a name="select-your-device"></a>選取您的裝置

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後瀏覽至 IoT 中樞。
1. 選取功能表中的 [IoT Edge]  。
1. 按一下裝置清單中目標裝置的識別碼。
1. 選取 [ **設定模組** ]。

### <a name="configure-a-deployment-manifest"></a>設定部署資訊清單

部署資訊清單為 JSON 文件，說明應部署的模組、資料如何在模組之間流動，以及想要的模組對應項需要的屬性。 此 Azure 入口網站有一個嚮導，可逐步引導您建立部署資訊清單。 它會將三個步驟分成多個索引標籤： **模組** 、 **路由** 和 **審核 + 建立** 。

#### <a name="add-modules"></a>新增模組

1. 在頁面的 [ **IoT Edge 模組** ] 區段中，按一下 [ **新增** ] 下拉式清單，然後選取 [ **IoT Edge 模組** ]，以顯示 [ **新增 IoT Edge 模組** ] 頁面。

2. 在 [ **模組設定** ] 索引標籤上，提供模組的名稱，然後指定容器映射 URI：

   範例：
  
   - **IoT Edge 模組名稱** ： `azureblobstorageoniotedge`
   - **映射 URI** ： `mcr.microsoft.com/azure-blob-storage:latest`

   ![螢幕擷取畫面顯示 [新增 I o T Edge 模組] 頁面的 [模組設定] 索引標籤。](./media/how-to-deploy-blob/addmodule-tab1.png)

   除非您已在 [ **模組設定** ]、[ **容器建立選項** ] 和 [模組對應項 **設定** ] 索引標籤上指定值，如下列程式所述，請勿選取 [ **新增** ]。

   > [!IMPORTANT]
   > 當您對模組進行呼叫時，Azure IoT Edge 會區分大小寫，而且儲存體 SDK 也會預設為小寫。 雖然 [Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) 中的模組名稱是 **AzureBlobStorageonIoTEdge** ，但是將名稱變更為小寫有助於確保您與 IoT Edge 模組上 Azure Blob 儲存體的連接不會中斷。

3. 開啟 [ **容器建立選項** ] 索引標籤。

   ![螢幕擷取畫面顯示 [新增 I o T Edge 模組] 頁面的 [容器建立選項] 索引標籤。](./media/how-to-deploy-blob/addmodule-tab3.png)

   將下列 JSON 複製並貼到方塊中，以提供儲存體帳戶資訊，以及您裝置上的儲存體掛接。
  
   ```json
   {
     "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
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

4. 使用下列資訊，將您複製的 JSON 更新為 **容器建立選項** ：

   - 將 `<your storage account name>` 取代為您可以記住的名稱。 帳戶名稱的長度必須介於3到24個字元之間，並包含小寫字母和數位。 無空格。

   - 使用 64 位元組 base64 金鑰取代 `<your storage account key>`。 您可以 [GeneratePlus](https://generate.plus/en/base64) 之類的工具產生金鑰。 您將使用這些認證，從其他模組存取 Blob 儲存體。

   - `<storage mount>`根據您的容器作業系統來取代。 提供 [磁片](https://docs.docker.com/storage/volumes/) 區的名稱，或您 IoT Edge 裝置上的現有目錄的絕對路徑，blob 模組將在此儲存其資料。 儲存體掛接會將您提供的裝置上的位置對應至模組中的設定位置。

     - 針對 Linux 容器，格式為 **\<your storage path or volume> ：/blobroot** 。 例如：
         - 使用 [磁片區掛接](https://docs.docker.com/storage/volumes/)： `my-volume:/blobroot`
         - 使用系結 [掛接](https://docs.docker.com/storage/bind-mounts/)： `/srv/containerdata:/blobroot` 。 請務必遵循下列步驟來[授與容器使用者目錄存取](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)權
     - 針對 Windows 容器，格式為 **\<your storage path or volume> ： C：/BlobRoot** 。 例如：
         - 使用 [磁片區掛接](https://docs.docker.com/storage/volumes/)： `my-volume:C:/BlobRoot` 。
         - 使用系結 [掛接](https://docs.docker.com/storage/bind-mounts/)： `C:/ContainerData:C:/BlobRoot` 。
         - 您可以對應 SMB 網路位置，而不是使用本機磁片磁碟機，如需詳細資訊，請參閱 [使用 SMB 共用作為本機儲存體](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)

     > [!IMPORTANT]
     > 請勿變更儲存體掛接值的後半部，以指向 IoT Edge 模組上 Blob 儲存體中的特定位置。 儲存體裝載的結尾一律必須是 **：/blobroot** （適用于 Linux 容器）和 **： C：/blobroot** （適用于 Windows 容器）。

5. 在 [ **模組** 對應項設定] 索引標籤上，複製下列 JSON，並將它貼到方塊中。

   ![螢幕擷取畫面顯示 [新增 I o T Edge 模組] 頁面的 [模組對應項設定] 索引標籤。](./media/how-to-deploy-blob/addmodule-tab4.png)

   設定每個具有適當值的屬性，如預留位置所指示。 如果您使用 IoT Edge 模擬器，請將這些屬性的值設定為相關的環境變數，如 [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) 和 [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties)所述。

   ```json
   {
     "deviceAutoDeleteProperties": {
       "deleteOn": <true, false>,
       "deleteAfterMinutes": <timeToLiveInMinutes>,
       "retainWhileUploading": <true,false>
     },
     "deviceToCloudUploadProperties": {
       "uploadOn": <true, false>,
       "uploadOrder": "<NewestFirst, OldestFirst>",
       "cloudStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>; EndpointSuffix=<your end point suffix>",
       "storageContainersForUpload": {
         "<source container name1>": {
           "target": "<target container name1>"
         }
       },
       "deleteAfterUpload": <true,false>
     }
   }
   ```

   如需在部署模組之後設定 deviceToCloudUploadProperties 和 deviceAutoDeleteProperties 的相關資訊，請參閱 [編輯模組](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin)對應項。 如需有關所需屬性的詳細資訊，請參閱 [定義或更新所需屬性](module-composition.md#define-or-update-desired-properties)。

6. 選取 [新增]。

7. 選取 **[下一步：路由]** 以繼續前往 [路由] 區段。

#### <a name="specify-routes"></a>指定路由

保留預設路由，然後選取 **[下一步：檢查 + 建立]** 以繼續進行審核區段。

#### <a name="review-deployment"></a>檢閱部署

檢閱區段會顯示 JSON 部署資訊清單，該清單會根據您在前兩個區段中的選項而建立。 另外還有兩個模組宣告您未新增： **$edgeAgent** 和 **$edgeHub** 。 這兩個模組組成 [IoT Edge 執行階段](iot-edge-runtime.md)，在每個部署中都是必要的預設值。

檢查您的部署資訊，然後選取 [ **建立** ]。

### <a name="verify-your-deployment"></a>驗證您的部署

建立部署之後，您會返回 IoT 中樞的 [ **IoT Edge** ] 頁面。

1. 選取您使用部署設定為目標的 IoT Edge 裝置，以開啟其詳細資料。
1. 在裝置詳細資料中，確認 Blob 儲存體模組列為 [指定於部署中]  和 [由裝置回報]  。

模組在裝置上啟動然後向 IoT 中樞回報可能需要一點時間。 重新整理頁面來查看更新狀態。

## <a name="deploy-from-visual-studio-code"></a>從 Visual Studio Code 部署

Azure IoT Edge 提供 Visual Studio Code 中的範本協助您開發解決方案。 使用下列步驟來建立具有 blob 儲存體模組的新 IoT Edge 解決方案，以及設定部署資訊清單。

1. 選取 [檢視]   > [命令調色盤]  。

1. 在命令選擇區中，輸入並執行命令 Azure IoT Edge： **新增 IoT Edge 解決方案** 。

   ![執行新的 IoT Edge 解決方案](./media/how-to-develop-csharp-module/new-solution.png)

   依照命令選擇區中的提示建立解決方案。

   | 欄位 | 值 |
   | ----- | ----- |
   | 選取資料夾 | 選擇您開發電腦上的位置，以 Visual Studio Code 建立方案檔。 |
   | 提供解決方案名稱 | 輸入解決方案的描述性名稱或接受預設值 **EdgeSolution** 。 |
   | 選取模組範本 | 選擇 [現有模組 (輸入完整映像 URL)]  。 |
   | 提供模組名稱 | 為您的模組輸入全部小寫的名稱，例如 **azureblobstorageoniotedge** 。<br/><br/>針對 IoT Edge 模組上的 Azure Blob 儲存體，請務必使用小寫名稱。 IoT Edge 在參考模組時會區分大小寫，且儲存體 SDK 預設為小寫。 |
   | 提供模組的 Docker 映像 | 提供映像 URI： **mcr.microsoft.com/azure-blob-storage:latest** |

   Visual Studio Code 會採用您提供的資訊、建立 IoT Edge 解決方案，然後將其載入至新的視窗。 解決方案範本會建立部署資訊清單範本，其中包含 Blob 儲存體模組映像，但是您需要設定模組的建立選項。

1. 在新的解決方案工作區中開啟 [deployment.template.json]  ，並找出 [模組]  區段。 進行下列設定變更：

   1. 刪除 **SimulatedTemperatureSensor** 模組，因為這不是此部署的必要步驟。

   1. 將下列程式碼複製並貼到 `createOptions` 欄位中：

      ```json
      "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
      ],
      "HostConfig":{
        "Binds": ["<storage mount>"],
        "PortBindings":{
          "11002/tcp": [{"HostPort":"11002"}]
        }
      }
      ```

      ![更新模組 createOptions-Visual Studio Code](./media/how-to-deploy-blob/create-options.png)

1. 將 `<your storage account name>` 取代為您可以記住的名稱。 帳戶名稱的長度必須介於3到24個字元之間，並包含小寫字母和數位。 無空格。

1. 使用 64 位元組 base64 金鑰取代 `<your storage account key>`。 您可以 [GeneratePlus](https://generate.plus/en/base64) 之類的工具產生金鑰。 您將使用這些認證，從其他模組存取 Blob 儲存體。

1. `<storage mount>`根據您的容器作業系統來取代。 提供[磁碟區](https://docs.docker.com/storage/volumes/)的名稱，或您想要 Blob 模組儲存其資料的 IoT Edge 裝置目錄絕對路徑。 儲存體掛接會將您提供的裝置上的位置對應至模組中的設定位置。  

     - 針對 Linux 容器，格式為 **\<your storage path or volume> ：/blobroot** 。 例如：
         - 使用 [磁片區掛接](https://docs.docker.com/storage/volumes/)： `my-volume:/blobroot`
         - 使用系結 [掛接](https://docs.docker.com/storage/bind-mounts/)： `/srv/containerdata:/blobroot` 。 請務必遵循下列步驟來[授與容器使用者目錄存取](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)權
     - 針對 Windows 容器，格式為 **\<your storage path or volume> ： C：/BlobRoot** 。 例如：
         - 使用 [磁片區掛接](https://docs.docker.com/storage/volumes/)： `my-volume:C:/BlobRoot` 。
         - 使用系結 [掛接](https://docs.docker.com/storage/bind-mounts/)： `C:/ContainerData:C:/BlobRoot` 。
         - 您可以對應 SMB 網路位置，而不是使用本機磁片磁碟機。 如需詳細資訊，請參閱 [使用 SMB 共用作為本機儲存體](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)。

     > [!IMPORTANT]
     > 請勿變更儲存體掛接值的後半部，以指向 IoT Edge 模組上 Blob 儲存體中的特定位置。 儲存體裝載的結尾一律必須是 **：/blobroot** （適用于 Linux 容器）和 **： C：/blobroot** （適用于 Windows 容器）。

1. 藉由將下列 JSON 新增至 *deployment.template.json* 檔案，為您的模組設定 [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties)和 [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) 。 使用適當的值來設定每個屬性，並儲存檔案。 如果您使用 IoT Edge 模擬器，請將這些屬性的值設定為相關的環境變數，您可以在[deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties)和[deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties)的 [說明] 區段中找到這些屬性。

   ```json
   "<your azureblobstorageoniotedge module name>":{
     "properties.desired": {
       "deviceAutoDeleteProperties": {
         "deleteOn": <true, false>,
         "deleteAfterMinutes": <timeToLiveInMinutes>,
         "retainWhileUploading": <true, false>
       },
       "deviceToCloudUploadProperties": {
         "uploadOn": <true, false>,
         "uploadOrder": "<NewestFirst, OldestFirst>",
         "cloudStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
         "storageContainersForUpload": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         },
         "deleteAfterUpload": <true, false>
       }
     }
   }
   ```

   ![設定 azureblobstorageoniotedge 所需的屬性-Visual Studio Code](./media/how-to-deploy-blob/devicetocloud-deviceautodelete.png)

   如需在部署模組之後設定 deviceToCloudUploadProperties 和 deviceAutoDeleteProperties 的相關資訊，請參閱 [編輯模組](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin)對應項。 如需容器建立選項、重新開機原則及所需狀態的詳細資訊，請參閱 [EdgeAgent 所需屬性](module-edgeagent-edgehub.md#edgeagent-desired-properties)。

1. 儲存 *deployment.template.json* 檔案。

1. 以滑鼠右鍵按一下  。

1. Visual Studio Code 會取得您在 *deployment.template.js* 中提供的資訊，並使用它來建立新的部署資訊清單檔。 隨即在解決方案工作區新 **config** 資料夾中建立部署資訊清單。 您有該檔案時，就可以依照[從 Visual Studio Code 部署 Azure IoT Edge 模組](how-to-deploy-modules-vscode.md)或[使用 Azure CLI 2.0 部署 Azure IoT Edge 模組](how-to-deploy-modules-cli.md)中的步驟進行。

## <a name="deploy-multiple-module-instances"></a>部署多個模組實例

如果您想要在 IoT Edge 模組上部署 Azure Blob 儲存體的多個實例，您必須提供不同的儲存路徑，並變更模組所系結的 `HostPort` 值。 Blob 儲存體模組一律會公開容器的 11002 連接埠，但是您可以宣告哪個連接埠繫結至主機。

在 Azure 入口網站) 中編輯 **容器建立選項** (，或deployment.template.jsVisual Studio Code 中的檔案 *)* 中的 [ **createOptions** ] 欄位 (，以變更 `HostPort` 值：

```json
"PortBindings":{
  "11002/tcp": [{"HostPort":"<port number>"}]
}
```

您連接到其他 Blob 儲存體模組時，請變更端點來指向已更新的主機連接埠。

## <a name="configure-proxy-support"></a>設定 Proxy 支援

如果您的組織使用 proxy 伺服器，您將需要設定 edgeAgent 和 edgeHub 執行時間模組的 proxy 支援。 此套裝程式含兩個工作：

- 設定裝置上的執行時間守護程式和 IoT Edge 代理程式。
- 針對部署資訊清單 JSON 檔案中的模組設定 HTTPS_PROXY 環境變數。

[設定 IoT Edge 裝置以透過 proxy 伺服器通訊](how-to-configure-proxy-support.md)時，會說明此程式。

此外，blob 儲存體模組也需要資訊清單部署檔案中的 HTTPS_PROXY 設定。 您可以直接編輯部署資訊清單檔，也可以使用 Azure 入口網站。

1. 在 Azure 入口網站中流覽至您的 Iot 中樞，然後從左窗格功能表中選取 [ **IoT Edge** ]。

1. 選取包含要設定之模組的裝置。

1. 選取 [ **設定模組** ]。

1. 在頁面的 [ **IoT Edge 模組** ] 區段中，選取 blob 儲存體模組。

1. 在 [ **更新 IoT Edge 模組** ] 頁面上，選取 [ **環境變數** ] 索引標籤。

1. `HTTPS_PROXY`針對 **名稱** 和您的 Proxy URL 新增 **值** 。

      ![螢幕擷取畫面顯示 [Update I o T Edge 模組] 窗格，您可以在其中輸入指定的值。](./media/how-to-deploy-blob/https-proxy-config.png)

1. 按一下 [ **更新** ]，然後 **參閱 [+ 建立** ]。

1. 請注意，proxy 會新增至部署資訊清單中的模組，然後選取 [ **建立** ]。

1. 從 [裝置詳細資料] 頁面選取模組來驗證設定，然後在 **IoT Edge 模組詳細資料** 頁面的下半部選取 [ **環境變數** ] 索引標籤。

      ![螢幕擷取畫面：顯示 [環境變數] 索引標籤。](./media/how-to-deploy-blob/verify-proxy-config.png)

## <a name="next-steps"></a>後續步驟

深入瞭解 [IoT Edge 上的 Azure Blob 儲存體](how-to-store-data-blob.md)。

如需部署資訊清單的功能，以及如何建立此類清單的詳細資訊，請參閱[了解如何使用、設定以及重複使用 IoT Edge 模組](module-composition.md) (英文)。

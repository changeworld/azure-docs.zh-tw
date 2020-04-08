---
title: 將模組上的 Blob 儲存部署到您的裝置 - Azure IoT 邊緣
description: 將 Azure Blob 儲存體模組部署到您的 IoT Edge 裝置，即可在邊緣儲存資料。
author: kgremban
ms.author: kgremban
ms.date: 3/10/2020
ms.topic: conceptual
ms.service: iot-edge
ms.reviewer: arduppal
ms.openlocfilehash: da163e902d06bd98ac47a24256cb809cb222173b
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804617"
---
# <a name="deploy-the-azure-blob-storage-on-iot-edge-module-to-your-device"></a>將 IoT Edge 模組上的 Azure Blob 儲存體部署至您的裝置

有幾種方法可以將模組部署到 IoT 邊緣設備,並且所有這些模組都適用於 IoT Edge 模組上的 Azure Blob 儲存。 兩個最簡單的方法是使用 Azure 入口網站或 Visual Studio Code 範本。

## <a name="prerequisites"></a>Prerequisites

- Azure 訂閱中的[IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。
- 已安裝 IoT Edge 執行階段的 [IoT Edge 裝置](how-to-register-device.md)。
- 如果從視覺化工作室碼部署,[則可視化工作室碼](https://code.visualstudio.com/)與[Azure IoT 工具](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)。

## <a name="deploy-from-the-azure-portal"></a>從 Azure 門戶部署

Azure 門戶將引導您創建部署清單並將部署推送到 IoT Edge 設備。

### <a name="select-your-device"></a>選取您的裝置

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後瀏覽至 IoT 中樞。
1. 選取功能表中的 [IoT Edge]****。
1. 按一下裝置清單中的目標設備的識別碼。
1. 選擇**設定模組**。

### <a name="configure-a-deployment-manifest"></a>設定部署資訊清單

部署資訊清單為 JSON 文件，說明應部署的模組、資料如何在模組之間流動，以及想要的模組對應項需要的屬性。 Azure 門戶具有引導您創建部署清單的嚮導。 它有三個步驟組織到選項卡:**模組**,**路由**,和審查 **+ 建立**。

#### <a name="add-modules"></a>新增模組

1. 在頁面的**IoT 邊緣模組**部分中,按下 **「添加**下拉清單」並選擇**IoT 邊緣模組**以顯示 **「添加 IoT 邊緣模組」** 頁。

2. 在**模組設定「 選項**卡上,提供模組的名稱,然後指定容器映像 URI:

   範例：
  
   - **IoT 邊緣模組名稱**:`azureblobstorageoniotedge`
   - **影像 URI**:`mcr.microsoft.com/azure-blob-storage:latest`

   ![模組雙組設定](./media/how-to-deploy-blob/addmodule-tab1.png)

   在此過程中所述的 **「模組設定**」、「**容器建立選項**」 「和 **「模組孿生設定」** 選項卡上指定值之前,不要選擇 **「添加**」。

   > [!IMPORTANT]
   > 對模組進行調用時,Azure IoT Edge 區分大小寫,存儲 SDK 也默認為小寫。 儘管[Azure 應用商店](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace)中的模組名稱是 Azure **Blob 儲存onIoTEdge,** 但將名稱更改為小寫有助於確保與 IoT Edge 模組上的 Azure Blob 儲存的連接不會中斷。

3. 開啟**容器建立選項**選項卡。

   ![模組雙組設定](./media/how-to-deploy-blob/addmodule-tab3.png)

   複製下面的 JSON 並將其貼上到框中,以提供儲存帳戶資訊和設備上儲存的裝載。
  
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

4. 使用以下資訊更新複製到**容器建立選項**的 JSON:

   - 將 `<your storage account name>` 取代為您可以記住的名稱。 帳戶名稱應為 3 到 24 個字元長,帶有小寫字母和數位。 無空格。

   - 使用 64 位元組 base64 金鑰取代 `<your storage account key>`。 您可以 [GeneratePlus](https://generate.plus/en/base64) 之類的工具產生金鑰。 您將使用這些認證，從其他模組存取 Blob 儲存體。

   - 根據`<storage mount>`容器操作系統進行更換。 提供[卷](https://docs.docker.com/storage/volumes/)的名稱或IoT Edge設備上現有目錄的絕對路徑,其中Blob模組將存儲其資料。 儲存載入將您提供的裝置上的位置映射到模組中的設定位置。

     - 對 Linux 容器,格式是**\<儲存路徑或卷>:/blobroot**。 例如：
         - 使用[捲入](https://docs.docker.com/storage/volumes/):`my-volume:/blobroot`
         - 使用[繫結安裝](https://docs.docker.com/storage/bind-mounts/)`/srv/containerdata:/blobroot`: 。 確保依步驟[向容器使用者的目錄存取權限](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - 對 Windows 容器,格式是**\<儲存路徑或卷>:C:/BlobRoot**。 例如：
         - 使用[捲入](https://docs.docker.com/storage/volumes/)`my-volume:C:/BlobRoot`:
         - 使用[繫結安裝](https://docs.docker.com/storage/bind-mounts/)`C:/ContainerData:C:/BlobRoot`: 。
         - 您可以映射 SMB 網路位置,而不是使用本地驅動程式,有關詳細資訊,請參閱[使用 SMB 分享為本地存儲](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)

     > [!IMPORTANT]
     > 不要更改儲存裝載值的後半部分,該值指向 IoT Edge 模組上的 Blob 儲存中的特定位置。 儲存載入必須始終以 Linux 容器的 **:/blobroot**和 **:C:/BlobRoot**為 Windows 容器結束。

5. 在 **「模組孿生設置」** 選項卡上,複製以下 JSON 並將其粘貼到框中。

   ![模組雙組設定](./media/how-to-deploy-blob/addmodule-tab4.png)

   配置每個屬性具有適當的值,如占位符所示。 如果您使用的是 IoT 邊緣模擬器,請將這些屬性的值設置為相關環境變數,如[設備 ToCloudUpload 屬性](how-to-store-data-blob.md#devicetoclouduploadproperties)和設備[AutoDelete 屬性](how-to-store-data-blob.md#deviceautodeleteproperties)所述。

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

   有關在部署模組後設定裝置ToCloudUpload屬性和裝置自動刪除屬性的資訊,請參閱[編輯模組孿生](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin)。 有關所需屬性的詳細資訊,請參閱[定義或更新所需屬性](module-composition.md#define-or-update-desired-properties)。

6. 選取 [新增]  。

7. 選擇 **「下一步」:要繼續**到路線部分的路由。

#### <a name="specify-routes"></a>指定路由

保留預設路線並選擇 **「下一步:審閱 + 創建**」以繼續查看部分。

#### <a name="review-deployment"></a>檢閱部署

檢閱區段會顯示 JSON 部署資訊清單，該清單會根據您在前兩個區段中的選項而建立。 還有兩個模組聲明您沒有新增 **:$edgeAgent**和 **$edgeHub**。 這兩個模組組成 [IoT Edge 執行階段](iot-edge-runtime.md)，在每個部署中都是必要的預設值。

查看部署資訊,然後選擇 **"創建**"。

### <a name="verify-your-deployment"></a>驗證部署

創建部署後,您將返回到 IoT 中心**IoT 邊緣**頁面。

1. 選取您使用部署設定為目標的 IoT Edge 裝置，以開啟其詳細資料。
1. 在裝置詳細資料中，確認 Blob 儲存體模組列為 [指定於部署中]**** 和 [由裝置回報]****。

模組在裝置上啟動然後向 IoT 中樞回報可能需要一點時間。 重新整理頁面來查看更新狀態。

## <a name="deploy-from-visual-studio-code"></a>從視覺化工作室碼部署

Azure IoT Edge 提供 Visual Studio Code 中的範本協助您開發解決方案。 使用以下步驟使用 Blob 儲存模組創建新的 IoT Edge 解決方案,並設定部署清單。

1. 選擇 **「查看** > **命令調色板**」 。

1. 在命令選擇區中，輸入並執行命令 Azure IoT Edge：**新增 IoT Edge 解決方案**。

   ![執行新的 IoT Edge 解決方案](./media/how-to-develop-csharp-module/new-solution.png)

   依照命令選擇區中的提示建立解決方案。

   | 欄位 | 值 |
   | ----- | ----- |
   | 選取資料夾 | 為可視化工作室代碼選擇開發電腦上的位置以創建解決方案檔。 |
   | 提供解決方案名稱 | 輸入解決方案的描述性名稱或接受預設值 **EdgeSolution**。 |
   | 選取模組範本 | 選擇 [現有模組 (輸入完整映像 URL)]****。 |
   | 提供模組名稱 | 輸入模組的全小寫名稱,如**Azureblob 儲存oniotedge**。<br/><br/>針對 IoT Edge 模組上的 Azure Blob 儲存體，請務必使用小寫名稱。 IoT Edge 在參考模組時會區分大小寫，且儲存體 SDK 預設為小寫。 |
   | 提供模組的 Docker 映像 | 提供映像 URI：**mcr.microsoft.com/azure-blob-storage:latest** |

   Visual Studio Code 會採用您提供的資訊、建立 IoT Edge 解決方案，然後將其載入至新的視窗。 解決方案範本會建立部署資訊清單範本，其中包含 Blob 儲存體模組映像，但是您需要設定模組的建立選項。

1. 在新的解決方案工作區中開啟 [deployment.template.json]**，並找出 [模組]**** 區段。 進行下列設定變更：

   1. 刪除**模擬溫度感測器**模組,因為它不需要進行此部署。

   1. 複製並貼上以下代碼到`createOptions`欄位中:

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

      ![更新模組建立選項 - 視覺化工作室代碼](./media/how-to-deploy-blob/create-options.png)

1. 將 `<your storage account name>` 取代為您可以記住的名稱。 帳戶名稱應為 3 到 24 個字元長,帶有小寫字母和數位。 無空格。

1. 使用 64 位元組 base64 金鑰取代 `<your storage account key>`。 您可以 [GeneratePlus](https://generate.plus/en/base64) 之類的工具產生金鑰。 您將使用這些認證，從其他模組存取 Blob 儲存體。

1. 根據`<storage mount>`容器操作系統進行更換。 提供[磁碟區](https://docs.docker.com/storage/volumes/)的名稱，或您想要 Blob 模組儲存其資料的 IoT Edge 裝置目錄絕對路徑。 儲存載入將您提供的裝置上的位置映射到模組中的設定位置。  

     - 對 Linux 容器,格式是**\<儲存路徑或卷>:/blobroot**。 例如：
         - 使用[捲入](https://docs.docker.com/storage/volumes/):`my-volume:/blobroot`
         - 使用[繫結安裝](https://docs.docker.com/storage/bind-mounts/)`/srv/containerdata:/blobroot`: 。 確保依步驟[向容器使用者的目錄存取權限](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - 對 Windows 容器,格式是**\<儲存路徑或卷>:C:/BlobRoot**。 例如：
         - 使用[捲入](https://docs.docker.com/storage/volumes/)`my-volume:C:/BlobRoot`:
         - 使用[繫結安裝](https://docs.docker.com/storage/bind-mounts/)`C:/ContainerData:C:/BlobRoot`: 。
         - 您可以使用本地驅動程式來映射 SMB 網路位置,有關詳細資訊,請參閱[使用 SMB 分享為本地存儲](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)

     > [!IMPORTANT]
     > 不要更改儲存裝載值的後半部分,該值指向 IoT Edge 模組上的 Blob 儲存中的特定位置。 儲存載入必須始終以 Linux 容器的 **:/blobroot**和 **:C:/BlobRoot**為 Windows 容器結束。

1. 以將以下 JSON 加入*至部署.template.json*檔,為模組設定[裝置到CloudUpload屬性](how-to-store-data-blob.md#devicetoclouduploadproperties)和裝置[自動刪除屬性](how-to-store-data-blob.md#deviceautodeleteproperties)。 使用適當的值配置每個屬性並保存檔。 如果您使用的是 IoT Edge 模擬器,請將這些屬性的值設定為相關環境變數,您可以在[裝置ToCloudUpload屬性](how-to-store-data-blob.md#devicetoclouduploadproperties)和裝置[AutoDelete 屬性](how-to-store-data-blob.md#deviceautodeleteproperties)的說明部分找到這些變數

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

   ![為 Azureblob 儲存oniotedge 設定所需的屬性 - 視覺化工作室代碼](./media/how-to-deploy-blob/devicetocloud-deviceautodelete.png)

   有關在部署模組後設定裝置ToCloudUpload屬性和裝置自動刪除屬性的資訊,請參閱[編輯模組孿生](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin)。 有關容器建立選項、重新啟動策略和所需狀態的詳細資訊,請參閱[EdgeAgent 所需的屬性](module-edgeagent-edgehub.md#edgeagent-desired-properties)。

1. 保存*部署.template.json*檔。

1. 以滑鼠右鍵按一下 **deployment.template.json**，然後選取 [產生 IoT Edge 部署資訊清單]****。

1. 可視化工作室代碼獲取您在*部署.template.json*中提供的資訊,並用它來創建新的部署清單檔。 隨即在解決方案工作區新 **config** 資料夾中建立部署資訊清單。 您有該檔案時，就可以依照[從 Visual Studio Code 部署 Azure IoT Edge 模組](how-to-deploy-modules-vscode.md)或[使用 Azure CLI 2.0 部署 Azure IoT Edge 模組](how-to-deploy-modules-cli.md)中的步驟進行。

## <a name="deploy-multiple-module-instances"></a>部署多個模組實體

如果要在 IoT Edge 模組上部署 Azure Blob 儲存的多個實例,則需要提供不同的儲存`HostPort`路徑並更改模組綁定 到的值。 Blob 儲存體模組一律會公開容器的 11002 連接埠，但是您可以宣告哪個連接埠繫結至主機。

編輯**容器建立選項**(在 Azure 閘戶中)或**建立選項**欄位(在 Visual Studio 代碼中的*部署.template.json*檔案中`HostPort`)以更改值:

```json
"PortBindings":{
  "11002/tcp": [{"HostPort":"<port number>"}]
}
```

您連接到其他 Blob 儲存體模組時，請變更端點來指向已更新的主機連接埠。

## <a name="configure-proxy-support"></a>設定 Proxy 支援

如果您的組織正在使用代理伺服器,則需要為邊緣代理和邊緣 Hub 運行時模組設定代理支援。 此過程涉及兩個工作:

- 配置設備上的運行時守護程序和IoT邊緣代理。
- 為部署清單 JSON 檔中的模組設置HTTPS_PROXY環境變數。

此過程在[配置 IoT Edge 設備以透過代理伺服器進行通訊中](how-to-configure-proxy-support.md)進行了描述。

此外,blob 儲存模組還需要在清單部署檔中設置HTTPS_PROXY。 您可以直接編輯部署清單檔,或使用 Azure 門戶。

1. 導航到 Azure 門戶中的 Iot 中心,然後從左側窗格功能表中選擇**Iot Edge。**

1. 選擇要配置模組的設備。

1. 選擇**設定模組**。

1. 在頁面的**IoT 邊緣模組**部分中,選擇 blob 儲存模組。

1. 在 **"更新 IoT 邊緣模組"** 頁上,選擇 **「環境變數」** 選項卡。

1. 為`HTTPS_PROXY`**值**的名稱與代理 URL**Value**加入 。

      ![設定HTTPS_PROXY環境變數](./media/how-to-deploy-blob/https-proxy-config.png)

1. 點選 **「更新**」,然後**檢視 + 建立**。

1. 請注意,代理將添加到部署清單中的模組,並選擇 **「創建**」。

1. 通過從裝置詳細資訊頁面中選擇模組來驗證設置,並在**IoT 邊緣模組詳細資訊**頁面的下半部分選擇「**環境變數**」選項卡。

      ![設定HTTPS_PROXY環境變數](./media/how-to-deploy-blob/verify-proxy-config.png)

## <a name="next-steps"></a>後續步驟

瞭解有關[IoT 邊緣上的 Azure Blob 儲存的詳細資訊](how-to-store-data-blob.md)。

如需部署資訊清單的功能，以及如何建立此類清單的詳細資訊，請參閱[了解如何使用、設定以及重複使用 IoT Edge 模組](module-composition.md) (英文)。

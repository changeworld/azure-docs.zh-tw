---
title: 將區塊 Blob 儲存在裝置上 - Azure IoT Edge | Microsoft Docs
description: 瞭解分層和存留時間功能，請參閱支援的 blob 儲存體作業，並連接到您的 blob 儲存體帳戶。
author: kgremban
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 2ee4e313366bafdd2f6e3bd0e104abd9f11b7776
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98108665"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge"></a>在 IoT Edge 使用 Azure Blob 儲存體，以便在邊緣儲存資料

IoT Edge 上的 Azure Blob 儲存體會在邊緣提供 [區塊 blob](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) 和 [附加 Blob](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs) 儲存體解決方案。 您 IoT Edge 裝置上的 blob 儲存體模組的行為就像 Azure blob 服務，不同之處在于 blob 會儲存在本機 IoT Edge 裝置上。 您可以使用您已經用過的相同 Azure 儲存體 SDK 方法或 blob API 呼叫來存取 blob。 本文說明在 IoT Edge 裝置上執行 Blob 服務的 IoT Edge 容器 Azure Blob 儲存體相關概念。

此模組在案例中很有用：

* 資料必須儲存在本機，才能處理或傳輸至雲端。 此資料可以是影片、影像、財務資料、醫院資料或任何其他非結構化資料。
* 當裝置位於連線能力有限的位置時。
* 當您想要有效率地在本機處理資料，以取得資料的低延遲存取時，您可以儘快回應緊急情況。
* 當您想要降低頻寬成本，並避免將數 tb 的資料傳輸到雲端時。 您可以在本機處理資料，並只將處理過的資料傳送至雲端。

觀看影片以取得快速簡介
> [!VIDEO https://www.youtube.com/embed/xbwgMNGB_3Y]

此課程模組隨附 **deviceToCloudUpload** 和 **deviceAutoDelete** 功能。

**deviceToCloudUpload** 是可設定的功能。 此函式會自動將本機 blob 儲存體中的資料上傳至 Azure，並提供間歇性的網際網路連線支援。 它可讓您：

* 開啟/關閉 deviceToCloudUpload 功能。
* 選擇資料複製到 Azure 的順序，例如 NewestFirst 或 OldestFirst。
* 指定您想要上傳資料的 Azure 儲存體帳戶。
* 指定您想要上傳至 Azure 的容器。 此模組可讓您指定來源和目標容器名稱。
* 選擇在上傳至雲端儲存體完成後立即刪除 blob 的功能
* 使用作業) 進行完整的 blob 上傳 (，以及 `Put Blob` 使用 `Put Block` `Put Block List` 和 `Append Block` 作業) 的區塊層級上傳 (。

當您的 blob 由區塊組成時，此模組會使用區塊層級上傳。 以下是一些常見案例：

* 您的應用程式會更新先前上傳的區塊 blob 的一些區塊，或將新的區塊附加至附加 blob，此模組只會上傳更新的區塊，而不會上傳整個 blob。
* 模組正在上傳 blob 和網際網路連線消失，當連線恢復時，就只會上傳其餘的區塊，而不會上傳整個 blob。

如果在 blob 上傳期間發生非預期的進程終止 (例如電源失敗) ，則在模組重新上線後，就會再次上傳所有到期的區塊。

**deviceAutoDelete** 是可設定的功能。 此函式會在指定的持續時間 (以分鐘為單位來測量) 到期時，自動從本機儲存體刪除您的 blob。 它可讓您：

* 開啟/關閉 deviceAutoDelete 功能。
* 以分鐘為單位指定將會自動刪除 blob (deleteAfterMinutes) 時間。
* 如果 deleteAfterMinutes 值過期，請選擇在上傳 blob 時保留 blob 的功能。

## <a name="prerequisites"></a>必要條件

Azure IoT Edge 裝置：

* 您可以遵循 [Linux](quickstart-linux.md) 或 [Windows 裝置](quickstart.md)快速入門中的步驟，使用開發電腦或虛擬機器作為 IoT Edge 裝置。

* 請參閱 [Azure IoT Edge 支援的系統](support.md#operating-systems) ，以取得支援的作業系統和架構清單。 IoT Edge 模組上的 Azure Blob 儲存體支援下列架構：
  * Windows AMD64
  * Linux AMD64
  * Linux ARM32
  * Linux ARM64 (preview) 

雲端資源：

Azure 中的標準層 [IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)。

## <a name="devicetocloudupload-and-deviceautodelete-properties"></a>deviceToCloudUpload 和 deviceAutoDelete 屬性

使用模組所需的屬性來設定 **deviceToCloudUploadProperties** 和 **deviceAutoDeleteProperties**。 您可以在部署期間設定所需的屬性，或在稍後編輯模組對應項來變更，而不需要重新部署。 建議您檢查和的「模組對應項」， `reported configuration` `configurationValidation` 以確定值已正確傳播。

### <a name="devicetoclouduploadproperties"></a>deviceToCloudUploadProperties

這項設定的名稱是 `deviceToCloudUploadProperties` 。 如果您使用 IoT Edge 模擬器，請將這些屬性的值設定為相關的環境變數，您可以在 [說明] 區段中找到這些屬性。

| 屬性 | 可能的值 | 說明 |
| ----- | ----- | ---- |
| uploadOn | true、false | 預設會設定為 `false` 。 如果您想要開啟此功能，請將此欄位設定為 `true` 。 <br><br> 環境變數：`deviceToCloudUploadProperties__uploadOn={false,true}` |
| uploadOrder | NewestFirst、OldestFirst | 可讓您選擇將資料複製到 Azure 的順序。 預設會設定為 `OldestFirst` 。 順序取決於上次修改 Blob 的時間。 <br><br> 環境變數：`deviceToCloudUploadProperties__uploadOrder={NewestFirst,OldestFirst}` |
| cloudStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"` 是連接字串，可讓您指定您想要上傳資料的儲存體帳戶。 指定 `Azure Storage Account Name` 、 `Azure Storage Account Key` 、 `End point suffix` 。 新增適當的 Azure EndpointSuffix，以將資料上傳到其中，全域 Azure、政府 Azure 和 Microsoft Azure Stack 會有所不同。 <br><br> 您可以選擇在這裡指定 Azure 儲存體 SAS 連接字串。 但是，當這個屬性過期時，您必須更新它。 SAS 許可權可能包括建立容器的存取權，以及建立、寫入和新增 blob 的存取權。  <br><br> 環境變數：`deviceToCloudUploadProperties__cloudStorageConnectionString=<connection string>` |
| storageContainersForUpload | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | 可讓您指定想要上傳至 Azure 的容器名稱。 此模組可讓您指定來源和目標容器名稱。 如果您未指定目標容器名稱，它會自動將容器名稱指派為 `<IoTHubName>-<IotEdgeDeviceID>-<ModuleName>-<SourceContainerName>` 。 您可以建立目標容器名稱的範本字串，查看可能的值資料行。 <br>*% h-> IoT 中樞名稱 (3-50 個字元) 。 <br>*% d-> IoT Edge 裝置識別碼 (1 到129個字元) 。 <br>*% m-> 模組名稱 (1 到64個字元) 。 <br>*% c-> 來源容器名稱 (3 到63個字元) 。 <br><br>容器名稱的大小上限為63個字元，當容器的大小超過63個字元時，會自動指派目標容器名稱，它會 (IoTHubName、IotEdgeDeviceID、ModuleName、SourceContainerName) 為15個字元來修剪每個區段。 <br><br> 環境變數：`deviceToCloudUploadProperties__storageContainersForUpload__<sourceName>__target=<targetName>` |
| deleteAfterUpload | true、false | 預設會設定為 `false` 。 當其設定為時 `true` ，會在上傳至雲端儲存體完成時自動刪除資料。 <br><br> **注意**：如果您使用附加 blob，此設定將會在成功上傳之後，從本機儲存體刪除附加 blob，而且這些 blob 的任何未來附加區塊作業都會失敗。 請小心使用此設定，如果您的應用程式不常附加作業或不支援連續附加作業，請不要啟用此功能<br><br> 環境變數： `deviceToCloudUploadProperties__deleteAfterUpload={false,true}` 。 |

### <a name="deviceautodeleteproperties"></a>deviceAutoDeleteProperties

這項設定的名稱是 `deviceAutoDeleteProperties` 。 如果您使用 IoT Edge 模擬器，請將這些屬性的值設定為相關的環境變數，您可以在 [說明] 區段中找到這些屬性。

| 屬性 | 可能的值 | 說明 |
| ----- | ----- | ---- |
| deleteOn | true、false | 預設會設定為 `false` 。 如果您想要開啟此功能，請將此欄位設定為 `true` 。 <br><br> 環境變數：`deviceAutoDeleteProperties__deleteOn={false,true}` |
| deleteAfterMinutes | `<minutes>` | 指定時間（以分鐘為單位）。 當這個值過期時，模組會自動從本機儲存體刪除您的 blob。 目前允許的最大分鐘數為35791。 <br><br> 環境變數：`deviceAutoDeleteProperties__ deleteAfterMinutes=<minutes>` |
| retainWhileUploading | true、false | 依預設，它會設定為 `true` ，而且如果 deleteAfterMinutes 過期，則會保留 blob，並在其上傳至雲端儲存體。 您可以將它設定為 `false` ，它會在 deleteAfterMinutes 到期時立即刪除資料。 注意：若要讓此屬性運作，uploadOn 應該設定為 true。  <br><br> **注意**：如果您使用附加 blob，此設定將會在值過期時，從本機儲存體刪除附加 blob，而且這些 blob 的任何未來附加區塊作業都會失敗。 您可能會想要確保到期值夠大，以滿足應用程式所執行的附加作業預期頻率。<br><br> 環境變數：`deviceAutoDeleteProperties__retainWhileUploading={false,true}`|

## <a name="using-smb-share-as-your-local-storage"></a>使用 SMB 共用作為本機儲存體

當您在 Windows 主機上部署此模組的 Windows 容器時，您可以提供 SMB 共用作為本機儲存體路徑。

請確定 SMB 共用和 IoT 裝置位於互相信任的網域中。

您可以執行 `New-SmbGlobalMapping` PowerShell 命令，以將 SMB 共用對應到執行 Windows 的 IoT 裝置本機。

設定步驟如下：

```PowerShell
$creds = Get-Credential
New-SmbGlobalMapping -RemotePath <remote SMB path> -Credential $creds -LocalPath <Any available drive letter>
```

例如：

```powershell
$creds = Get-Credential
New-SmbGlobalMapping -RemotePath \\contosofileserver\share1 -Credential $creds -LocalPath G:
```

此命令將使用認證來向遠端 SMB 伺服器進行驗證。 然後，將遠端共用路徑對應至 G: 磁碟機代號 (可以是任何其他可用的磁碟機代號)。 IoT 裝置現在已將資料磁片區對應到 G：磁片磁碟機上的路徑。

請確定 IoT 裝置中的使用者可以讀取/寫入遠端 SMB 共用。

針對您的部署，的值 `<storage mount>` 可以是 **G：/ContainerData： C：/BlobRoot**。

## <a name="granting-directory-access-to-container-user-on-linux"></a>將目錄存取權授與 Linux 上的容器使用者

如果您已在 Linux 容器的建立選項中使用 [磁片區掛接](https://docs.docker.com/storage/volumes/) 進行儲存，則您不需要執行任何額外的步驟，但如果您使用系結 [掛接](https://docs.docker.com/storage/bind-mounts/) ，則必須執行這些步驟，才能正確地執行服務。

遵循最低許可權原則，以將使用者的存取權限限制為使用者執行其工作所需的最低許可權，此模組包含使用者 (名稱： absie、識別碼： 11000) 和使用者群組 (名稱： absie，識別碼： 11000) 。 如果容器以 **root** 的形式啟動 (預設使用者是 **根**) ，我們的服務將會以低許可權 **absie** 使用者的形式啟動。

此行為可讓主機路徑系結的許可權設定，以確保服務正常運作，否則服務會因為拒絕存取錯誤而損毀。 目錄系結中使用的路徑必須可供容器使用者存取 (範例： absie 11000) 。 您可以藉由執行下列主機上的命令，授與容器使用者對目錄的存取權：

```terminal
sudo chown -R 11000:11000 <blob-dir>
sudo chmod -R 700 <blob-dir>
```

例如：

```terminal
sudo chown -R 11000:11000 /srv/containerdata
sudo chmod -R 700 /srv/containerdata
```

如果您需要以非 **absie** 的使用者來執行服務，您可以在部署資訊清單中的 "user" 屬性下，于 createOptions 中指定您的自訂使用者識別碼。 在這種情況下，您需要使用預設或根群組識別碼 `0` 。

```json
"createOptions": {
  "User": "<custom user ID>:0"
}
```

現在，授與容器使用者對目錄的存取權

```terminal
sudo chown -R <user ID>:<group ID> <blob-dir>
sudo chmod -R 700 <blob-dir>
```

## <a name="configure-log-files"></a>設定記錄檔

如需為模組設定記錄檔的相關資訊，請參閱這些實際執行的 [最佳作法](./production-checklist.md#set-up-logs-and-diagnostics)。

## <a name="connect-to-your-blob-storage-module"></a>連接到您的 Blob 儲存體模組

您可以使用帳戶名稱和您為模組設定的帳戶金鑰來存取 IoT Edge 裝置上的 Blob 儲存體。

指定 IoT Edge 裝置作為您提出的任何儲存體要求所用的 Blob 端點。 您可以使用 IoT Edge 裝置資訊和您設定的帳戶名稱，[建立明確儲存體端點的連接字串](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint)。

* 針對部署在相同裝置上的模組，Azure Blob 儲存體在 IoT Edge 模組上執行的位置，Blob 端點為： `http://<module name>:11002/<account name>` 。
* 針對在不同裝置上執行的模組或應用程式，您必須為您的網路選擇正確的端點。 根據您的網路設定，選擇端點格式，讓外部模組或應用程式的資料流量可以觸達 IoT Edge 模組上執行 Azure Blob 儲存體的裝置。 此案例的 blob 端點是下列其中一項：
  * `http://<device IP >:11002/<account name>`
  * `http://<IoT Edge device hostname>:11002/<account name>`
  * `http://<fully qualified domain name>:11002/<account name>`
 
 > [!IMPORTANT]
 > 當您對模組進行呼叫時，Azure IoT Edge 會區分大小寫，而且儲存體 SDK 也會預設為小寫。 雖然 [Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) 中的模組名稱是 **AzureBlobStorageonIoTEdge**，但是將名稱變更為小寫有助於確保您與 IoT Edge 模組上 Azure Blob 儲存體的連接不會中斷。
 
## <a name="azure-blob-storage-quickstart-samples"></a>Azure Blob 儲存體快速入門範例

Azure Blob 儲存體檔包含數種語言的快速入門範例程式碼。 您可以藉由變更 Blob 端點以連接到本機 Blob 儲存體模組，來執行這些範例以測試 IoT Edge 上的 Azure Blob 儲存體。

下列快速入門範例使用 IoT Edge 也支援的語言，因此您可以將它們部署為與 blob 儲存體模組 IoT Edge 的模組：

* [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Python](../storage/blobs/storage-quickstart-blobs-python.md)
  * Python SDK 2.1 之前的版本有已知問題，模組不會傳回 blob 建立時間。 基於這個問題，某些方法（例如清單 blob）無法運作。 解決方法是將 blob 用戶端上的 API 版本明確設定為 ' 2017-04-17 '。 範例：`block_blob_service._X_MS_VERSION = '2017-04-17'`
  * [附加 Blob 範例](https://github.com/Azure/azure-storage-python/blob/master/samples/blob/append_blob_usage.py)
* [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs-legacy.md)
* [JS/HTML](../storage/blobs/storage-quickstart-blobs-javascript-client-libraries-legacy.md)
* [Ruby](../storage/blobs/storage-quickstart-blobs-ruby.md)
* [Go](../storage/blobs/storage-quickstart-blobs-go.md)
* [PHP](../storage/blobs/storage-quickstart-blobs-php.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>使用 Azure 儲存體總管連接到您的本機儲存體

您可以使用 [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/) 連接到您的本機儲存體帳戶。

1. 下載並安裝 Azure 儲存體總管

1. 使用連接字串連接到 Azure 儲存體

1. 提供連接字串： `DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`

1. 進行連線的步驟。

1. 在您的本機儲存體帳戶內建立容器

1. 開始將檔案上傳為區塊 blob 或附加 Blob。
   > [!NOTE]
   > 此模組不支援分頁 blob。

1. 您也可以選擇在儲存體總管中連接您的 Azure 儲存體帳戶。 這項設定可讓您單一查看本機儲存體帳戶和 Azure 儲存體帳戶

## <a name="supported-storage-operations"></a>支援的儲存體作業

IoT Edge 上的 Blob 儲存體模組會使用 Azure 儲存體 Sdk，而且會與適用于區塊 blob 端點的2017-04-17 版 Azure 儲存體 API 一致。

由於並非所有 Azure Blob 儲存體作業都是由 IoT Edge 上的 Azure Blob 儲存體所支援，因此本節會列出每個作業的狀態。

### <a name="account"></a>帳戶

支援：

* 列出容器

不支援：

* 取得和設定 Blob 服務屬性
* 預檢 Blob 要求
* 取得 Blob 服務統計資料
* 取得帳戶資訊

### <a name="containers"></a>容器

支援：

* 建立和刪除容器
* 取得容器屬性和中繼資料
* 列出 Blob
* 取得和設定容器 ACL
* 設定容器中繼資料

不支援：

* 租用容器

### <a name="blobs"></a>Blob

支援：

* 放置、取得和刪除 Blob
* 取得和設定 Blob 屬性
* 取得和設定 Blob 中繼資料

不支援：

* 租用 Blob
* 拍攝 Blob 的快照
* 複製和中止複製 Blob
* 取消刪除 Blob
* 設定 Blob 層

### <a name="block-blobs"></a>區塊 Blob

支援：

* 放置區塊
* 放置和取得區塊清單

不支援：

* 從 URL 放置區塊

### <a name="append-blobs"></a>附加 Blob

支援：

* 附加區塊

不支援：

* 從 URL 附加區塊

## <a name="event-grid-on-iot-edge-integration"></a>IoT Edge 整合的事件方格

> [!CAUTION]
> IoT Edge 的事件方格整合目前為預覽狀態

這 Azure Blob 儲存體在 IoT Edge 模組上，現在提供與 IoT Edge 上事件方格的整合。 如需這種整合的詳細資訊，請參閱 [部署模組、發佈事件和驗證事件傳遞的教學](../event-grid/edge/react-blob-storage-events-locally.md)課程。

## <a name="release-notes"></a>版本資訊

以下是此課程模組的 [docker hub 版本](https://hub.docker.com/_/microsoft-azure-blob-storage) 資訊

## <a name="suggestions"></a>建議

您的意見反應對我們非常重要，讓此課程模組和其功能實用且容易使用。 請分享您的意見反應，並讓我們知道可以如何改善。

您可以在這裡找到 absiotfeedback@microsoft.com

## <a name="next-steps"></a>後續步驟

瞭解如何 [在 IoT Edge 上部署 Azure Blob 儲存體](how-to-deploy-blob.md)

在[IoT Edge blog 的 Azure Blob 儲存體](https://aka.ms/abs-iot-blogpost)中隨時掌握最新的更新和公告

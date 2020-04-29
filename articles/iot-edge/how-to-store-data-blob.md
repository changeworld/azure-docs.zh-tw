---
title: 將區塊 Blob 儲存在裝置上 - Azure IoT Edge | Microsoft Docs
description: 瞭解分層和存留時間功能，請參閱支援的 blob 儲存體作業，並聯機至您的 blob 儲存體帳戶。
author: kgremban
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: bea00f429f31f2be62ee6a9c00f88873c595d94c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76509813"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge"></a>在 IoT Edge 使用 Azure Blob 儲存體，以便在邊緣儲存資料

IoT Edge 上的 Azure Blob 儲存體會在邊緣提供[區塊 blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs)和[附加 Blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs)儲存體解決方案。 IoT Edge 裝置上的 blob 儲存體模組的運作方式類似 Azure blob 服務，但 blob 會儲存在您的 IoT Edge 裝置本機上。 您可以使用您已使用的相同 Azure 儲存體 SDK 方法或 blob API 呼叫來存取 blob。 本文說明在 IoT Edge 裝置上執行 Blob 服務 IoT Edge 容器 Azure Blob 儲存體相關概念。

此模組在案例中很有用：

* 資料必須儲存在本機，直到可以處理或傳輸到雲端為止。 此資料可以是影片、影像、財務資料、醫院資料，或任何其他非結構化資料。
* 當裝置位於連線能力有限的位置時。
* 當您想要有效率地在本機處理資料，以取得資料的低延遲存取時，您可以儘快回應緊急情況。
* 當您想要降低頻寬成本，並避免將數 tb 的資料傳輸到雲端時。 您可以在本機處理資料，並只將已處理的資料傳送至雲端。

觀賞快速簡介影片
> [!VIDEO https://www.youtube.com/embed/xbwgMNGB_3Y]

此課程模組隨附**deviceToCloudUpload**和**deviceAutoDelete**功能。

**deviceToCloudUpload**是可設定的功能。 此函式會自動將資料從您的本機 blob 儲存體上傳至 Azure，並提供間歇性的網際網路連線支援。 它可讓您：

* 開啟/關閉 deviceToCloudUpload 功能。
* 選擇資料複製到 Azure 的順序，例如 NewestFirst 或 OldestFirst。
* 指定您要上傳資料的 Azure 儲存體帳戶。
* 指定您想要上傳至 Azure 的容器。 此模組可讓您指定來源和目標容器名稱。
* 選擇在上傳至雲端儲存體完成後，立即刪除 blob 的功能
* 執行完整 blob 上傳（ `Put Blob`使用操作）和區塊層級上`Put Block`傳`Put Block List` （ `Append Block`使用、和作業）。

當您的 blob 包含區塊時，此模組會使用區塊層級上傳。 以下是一些常見案例：

* 您的應用程式會更新先前上傳之區塊 blob 的某些區塊，或將新區塊附加至附加 blob，此模組只會上傳更新的區塊，而不是整個 blob。
* 模組正在上傳 blob，而網際網路連線已中斷，當連線重新時，它只會上傳其餘區塊，而不是整個 blob。

如果在 blob 上傳期間發生非預期的進程終止（例如電源中斷），則在模組重新上線之後，將會再次上傳所有已到期的區塊。

**deviceAutoDelete**是可設定的功能。 此函式會在指定的持續時間（以分鐘為單位）到期時，自動從本機儲存體中刪除您的 blob。 它可讓您：

* 開啟/關閉 deviceAutoDelete 功能。
* 指定將自動刪除 blob 的時間（以分鐘為單位）（deleteAfterMinutes）。
* 如果 deleteAfterMinutes 值過期，請選擇在其上傳時保留 blob 的功能。

## <a name="prerequisites"></a>先決條件

Azure IoT Edge 裝置：

* 您可以遵循[Linux](quickstart-linux.md)或[Windows 裝置](quickstart.md)快速入門中的步驟，使用您的開發電腦或虛擬機器作為 IoT Edge 裝置。

* 如需支援的作業系統和架構清單，請參閱[Azure IoT Edge 支援的系統](support.md#operating-systems)。 IoT Edge 模組上的 Azure Blob 儲存體支援下列架構：
  * Windows AMD64
  * Linux AMD64
  * Linux ARM32
  * Linux ARM64 （預覽）

雲端資源：

Azure 中的標準層 [IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)。

## <a name="devicetocloudupload-and-deviceautodelete-properties"></a>deviceToCloudUpload 和 deviceAutoDelete 屬性

使用模組所需的屬性來設定**deviceToCloudUploadProperties**和**deviceAutoDeleteProperties**。 您可以在部署期間設定所需的屬性，或在稍後藉由編輯模組對應項而不需要重新部署來進行變更。 我們建議您檢查和`reported configuration` `configurationValidation`的「模組對應項」，以確保值已正確傳播。

### <a name="devicetoclouduploadproperties"></a>deviceToCloudUploadProperties

此設定的名稱為`deviceToCloudUploadProperties`。 如果您使用 IoT Edge 模擬器，請將這些屬性的值設定為相關的環境變數，您可以在 [說明] 區段中找到這些屬性。

| 屬性 | 可能的值 | 說明 |
| ----- | ----- | ---- |
| uploadOn | true、false | 預設設定`false`為。 如果您想要開啟此功能，請將此欄位設定`true`為。 <br><br> 環境變數：`deviceToCloudUploadProperties__uploadOn={false,true}` |
| uploadOrder | NewestFirst、OldestFirst | 可讓您選擇將資料複製到 Azure 的順序。 預設設定`OldestFirst`為。 順序取決於 Blob 的上次修改時間。 <br><br> 環境變數：`deviceToCloudUploadProperties__uploadOrder={NewestFirst,OldestFirst}` |
| cloudStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"`這是一個連接字串，可讓您指定要上傳資料的目標儲存體帳戶。 指定`Azure Storage Account Name`、 `Azure Storage Account Key`、 `End point suffix`。 新增適當的 Azure EndpointSuffix，以將資料上傳到其中，全域 Azure、政府 Azure 和 Microsoft Azure Stack 會有所不同。 <br><br> 您可以選擇在這裡指定 Azure 儲存體 SAS 連接字串。 但當此屬性過期時，您就必須更新它。 <br><br> 環境變數：`deviceToCloudUploadProperties__cloudStorageConnectionString=<connection string>` |
| storageContainersForUpload | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | 可讓您指定您想要上傳至 Azure 的容器名稱。 此模組可讓您指定來源和目標容器名稱。 如果您未指定目標容器名稱，它會自動將容器名稱指派為`<IoTHubName>-<IotEdgeDeviceID>-<ModuleName>-<SourceContainerName>`。 您可以建立目標容器名稱的範本字串，請查看可能的值資料行。 <br>*% h-> IoT 中樞名稱（3-50 個字元）。 <br>*% d-> IoT Edge 裝置識別碼（1到129個字元）。 <br>*% m-> 模組名稱（1到64個字元）。 <br>*% c-> 來源容器名稱（3到63個字元）。 <br><br>容器名稱的大小上限為63個字元，而如果容器的大小超過63個字元，則會自動指派目標容器名稱，它會將每個區段（IoTHubName、IotEdgeDeviceID、ModuleName、SourceContainerName）修剪為15個字元。 <br><br> 環境變數：`deviceToCloudUploadProperties__storageContainersForUpload__<sourceName>__target=<targetName>` |
| deleteAfterUpload | true、false | 預設設定`false`為。 當其設定為`true`時，會在上傳至雲端儲存體完成時自動刪除資料。 <br><br> **注意**：如果您使用附加 blob，此設定將會在成功上傳之後，從本機儲存體刪除附加 blob，而這些 blob 的任何未來附加區塊作業將會失敗。 使用此設定時請小心，如果您的應用程式不常附加作業或不支援連續附加作業，請勿啟用此功能<br><br> 環境變數： `deviceToCloudUploadProperties__deleteAfterUpload={false,true}`。 |

### <a name="deviceautodeleteproperties"></a>deviceAutoDeleteProperties

此設定的名稱為`deviceAutoDeleteProperties`。 如果您使用 IoT Edge 模擬器，請將這些屬性的值設定為相關的環境變數，您可以在 [說明] 區段中找到這些屬性。

| 屬性 | 可能的值 | 說明 |
| ----- | ----- | ---- |
| deleteOn | true、false | 預設設定`false`為。 如果您想要開啟此功能，請將此欄位設定`true`為。 <br><br> 環境變數：`deviceAutoDeleteProperties__deleteOn={false,true}` |
| deleteAfterMinutes | `<minutes>` | 指定以分鐘為單位的時間。 此模組會在此值到期時，自動從本機儲存體中刪除您的 blob。 <br><br> 環境變數：`deviceAutoDeleteProperties__ deleteAfterMinutes=<minutes>` |
| retainWhileUploading | true、false | 根據預設，它會設定`true`為，而且如果 deleteAfterMinutes 過期，它會在將 blob 上傳至雲端儲存體時保留。 您可以將它設定`false`為，它會在 deleteAfterMinutes 到期時立即刪除資料。 注意：若要讓這個屬性能夠正常執行，uploadOn 應該設定為 true。  <br><br> **注意**：如果您使用附加 blob，則此設定會在值過期時，從本機儲存體刪除附加 blob，而這些 blob 的任何未來附加區塊作業將會失敗。 您可能想要確定到期值夠大，足以應付應用程式所執行之附加作業的預期頻率。<br><br> 環境變數：`deviceAutoDeleteProperties__retainWhileUploading={false,true}`|

## <a name="using-smb-share-as-your-local-storage"></a>使用 SMB 共用作為本機儲存體

當您在 Windows 主機上部署此模組的 Windows 容器時，您可以提供 SMB 共用作為本機儲存體路徑。

請確定 SMB 共用和 IoT 裝置位於互相信任的網域中。

您可以執行`New-SmbGlobalMapping` PowerShell 命令，在執行 Windows 的 IoT 裝置上本機對應 SMB 共用。

以下是設定步驟：

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

針對您的部署，的`<storage mount>`值可以是**G：/ContainerData： C：/BlobRoot**。

## <a name="granting-directory-access-to-container-user-on-linux"></a>將目錄存取權授與 Linux 上的容器使用者

如果您已在 Linux 容器的建立選項中使用[磁片區掛接](https://docs.docker.com/storage/volumes/)來儲存，則不需要執行任何額外的步驟，但如果您使用了[bind 掛接](https://docs.docker.com/storage/bind-mounts/)，則需要這些步驟，才能正確執行服務。

遵循最低許可權原則，將使用者的存取權限限制為執行工作所需的最低許可權，此模組包含使用者（名稱： absie，識別碼：11000）和使用者群組（名稱： absie，識別碼：11000）。 如果容器是以**root** （預設使用者為**root**）啟動，我們的服務就會以低許可權**absie**使用者的身分啟動。

這種行為會讓主機路徑的許可權設定系結至服務正常運作的關鍵，否則服務將會因為拒絕存取錯誤而損毀。 目錄系結中使用的路徑必須可供容器使用者存取（範例： absie 11000）。 您可以在主機上執行下列命令，以授與容器使用者對目錄的存取權：

```terminal
sudo chown -R 11000:11000 <blob-dir>
sudo chmod -R 700 <blob-dir>
```

例如：

```terminal
sudo chown -R 11000:11000 /srv/containerdata
sudo chmod -R 700 /srv/containerdata
```

如果您需要以非**absie**的使用者身分執行服務，您可以在部署資訊清單中的 "user" 屬性下，于 createOptions 中指定您的自訂使用者識別碼。 在這種情況下，您必須使用預設或根`0`群組識別碼。

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

如需為模組設定記錄檔的詳細資訊，請參閱這些[生產環境最佳做法](https://docs.microsoft.com/azure/iot-edge/production-checklist#set-up-logs-and-diagnostics)。

## <a name="connect-to-your-blob-storage-module"></a>連接到您的 Blob 儲存體模組

您可以使用帳戶名稱和您為模組設定的帳戶金鑰來存取 IoT Edge 裝置上的 Blob 儲存體。

指定 IoT Edge 裝置作為您提出的任何儲存體要求所用的 Blob 端點。 您可以使用 IoT Edge 裝置資訊和您設定的帳戶名稱，[建立明確儲存體端點的連接字串](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint)。

* 對於部署在 IoT Edge 模組上之 Azure Blob 儲存體所在的相同裝置上的模組，Blob 端點為： `http://<module name>:11002/<account name>`。
* 對於在不同裝置上執行的模組或應用程式，您必須為您的網路選擇正確的端點。 根據您的網路設定，選擇端點格式，讓來自外部模組或應用程式的資料流量可以連接到在 IoT Edge 模組上執行 Azure Blob 儲存體的裝置。 此案例的 blob 端點為下列其中一項：
  * `http://<device IP >:11002/<account name>`
  * `http://<IoT Edge device hostname>:11002/<account name>`
  * `http://<fully qualified domain name>:11002/<account name>`

## <a name="azure-blob-storage-quickstart-samples"></a>Azure Blob 儲存體快速入門範例

Azure Blob 儲存體檔包含幾種語言的快速入門範例程式碼。 您可以藉由變更 Blob 端點以連接到本機 Blob 儲存體模組，來執行這些範例以測試 IoT Edge 上的 Azure Blob 儲存體。

下列快速入門範例會使用 IoT Edge 也支援的語言，因此您可以將它們部署為與 blob 儲存體模組相同的 IoT Edge 模組：

* [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Python](../storage/blobs/storage-quickstart-blobs-python.md)
  * 在 Python SDK 2.1 之前的版本有已知的問題，此模組不會傳回 blob 建立時間。 基於這個問題，某些方法（例如清單 blob）無法運作。 因應措施是將 blob 用戶端上的 API 版本明確設定為 ' 2017-04-17 '。 範例：`block_blob_service._X_MS_VERSION = '2017-04-17'`
  * [附加 Blob 範例](https://github.com/Azure/azure-storage-python/blob/master/samples/blob/append_blob_usage.py)
* [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs-legacy.md)
* [JS/HTML](../storage/blobs/storage-quickstart-blobs-javascript-client-libraries-legacy.md)
* [Ruby](../storage/blobs/storage-quickstart-blobs-ruby.md)
* [Go](../storage/blobs/storage-quickstart-blobs-go.md)
* [PHP](../storage/blobs/storage-quickstart-blobs-php.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>使用 Azure 儲存體總管連接到您的本機儲存體

您可以使用[Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)來連接到本機儲存體帳戶。

1. 下載並安裝 Azure 儲存體總管

1. 使用連接字串連接到 Azure 儲存體

1. 提供連接字串：`DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`

1. 完成連線的步驟。

1. 在您的本機儲存體帳戶內建立容器

1. 開始將檔案上傳為區塊 blob 或附加 Blob。
   > [!NOTE]
   > 此模組不支援分頁 blob。

1. 您也可以選擇在儲存體總管中連接您的 Azure 儲存體帳戶。 此設定可為您提供本機儲存體帳戶和 Azure 儲存體帳戶的單一視圖

## <a name="supported-storage-operations"></a>支援的儲存體作業

IoT Edge 上的 Blob 儲存體模組會使用 Azure 儲存體 Sdk，且與適用于區塊 blob 端點的2017-04-17 版 Azure 儲存體 API 一致。

因為 IoT Edge 上的 Azure Blob 儲存體不支援所有的 Azure Blob 儲存體作業，所以此區段會列出每一個的狀態。

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
> IoT Edge 的事件格線整合目前為預覽狀態

IoT Edge 模組上的這個 Azure Blob 儲存體現在提供與 IoT Edge 上事件方格的整合。 如需這種整合的詳細資訊，請參閱[部署模組、發佈事件和驗證事件傳遞教學](../event-grid/edge/react-blob-storage-events-locally.md)課程。

## <a name="release-notes"></a>版本資訊

以下是此課程模組的[docker hub 版本](https://hub.docker.com/_/microsoft-azure-blob-storage)資訊

## <a name="feedback"></a>意見反應

您的意見反應對我們來說非常重要，因為它的功能很實用且容易使用。 請分享您的意見反應，並讓我們知道如何改進。

您可以在這裡找到absiotfeedback@microsoft.com

## <a name="next-steps"></a>後續步驟

瞭解如何[在 IoT Edge 上部署 Azure Blob 儲存體](how-to-deploy-blob.md)

在[IoT Edge blog 的 Azure Blob 儲存體](https://aka.ms/abs-iot-blogpost)中隨時掌握最新的更新和公告

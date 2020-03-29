---
title: 將區塊 Blob 儲存在裝置上 - Azure IoT Edge | Microsoft Docs
description: 瞭解分層和即時功能，查看支援的 Blob 存儲操作，並連接到 Blob 存儲帳戶。
author: kgremban
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: bea00f429f31f2be62ee6a9c00f88873c595d94c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76509813"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge"></a>在 IoT Edge 使用 Azure Blob 儲存體，以便在邊緣儲存資料

IoT Edge 上的 Azure Blob 存儲在邊緣提供[塊 Blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs)和[追加 Blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs)存儲解決方案。 IoT Edge 設備上的 Blob 存儲模組與 Azure Blob 服務類似，但 blob 存儲在 IoT Edge 設備上本地。 您可以使用已習慣的相同 Azure 存儲 SDK 方法或 Blob API 呼叫訪問 Blob。 本文介紹了與在 IoT Edge 設備上運行 Blob 服務的 IoT Edge 容器上的 Azure Blob 存儲相關的概念。

此模組在以下情況下非常有用：

* 需要本機存放區資料，直到可以處理或傳輸到雲。 此資料可以是視頻、圖像、財務資料、醫院資料或任何其他非結構化資料。
* 當設備位於連接受限的地方時。
* 當您希望有效地在本地處理資料以獲得對資料的低延遲訪問時，您可以儘快回應緊急情況。
* 當您希望降低頻寬成本並避免將 TB 的資料傳輸到雲時。 您可以在本地處理資料，並且僅將處理過的資料發送到雲。

觀看視頻，快速介紹
> [!VIDEO https://www.youtube.com/embed/xbwgMNGB_3Y]

此模組附帶**設備到雲上傳****和設備自動刪除**功能。

**設備ToCloudUpload**是一種可配置的功能。 此功能通過間歇性互聯網連接支援，自動將資料從本地 Blob 存儲上載到 Azure。 它允許您：

* 打開/關閉設備到雲上傳功能。
* 選擇將資料複製到 Azure 的順序，如"最新優先"或"最舊優先"。
* 指定要將資料上載到的 Azure 存儲帳戶。
* 指定要上載到 Azure 的容器。 此模組允許您指定源容器和目標容器名稱。
* 選擇在上載到雲存儲完成後立即刪除 Blob 的能力
* 執行完全 blob 上載`Put Blob`（使用操作）和塊級上載`Put Block`（`Put Block List`使用`Append Block`和 操作）。

當 blob 由塊組成時，此模組使用塊級上載。 下面是一些常見方案：

* 應用程式更新以前上載的塊 Blob 的某些塊，或將新塊追加到追加 Blob，此模組僅上載更新的塊，而不是整個 Blob。
* 模組正在上載 Blob，互聯網連接消失，當連接再次恢復時，它只上載剩餘的塊，而不是整個 Blob。

如果在 Blob 上載期間發生意外的進程終止（如電源故障），則一旦模組重新連線，將重新上載應上載上載之前的所有塊。

**設備自動刪除**是一個可配置的功能。 當指定的持續時間（以分鐘為單位衡量）過期時，此功能會自動從本機存放區中刪除 blob。 它允許您：

* 打開/關閉設備自動刪除功能。
* 指定自動刪除 blob 的時間（以分鐘（刪除後分鐘）。
* 如果 DeleteAfterThe分鐘值過期，請選擇在上載 Blob 時保留 Blob 的能力。

## <a name="prerequisites"></a>Prerequisites

Azure IoT Edge 裝置：

* 您可以按照[Linux](quickstart-linux.md)或[Windows 設備](quickstart.md)快速入門中的步驟，將開發電腦或虛擬機器用作 IoT 邊緣設備。

* 有關支援的作業系統和體系結構的清單，請參閱[Azure IoT Edge 支援的系統](support.md#operating-systems)。 IoT Edge 上的 Azure Blob 存儲模組支援以下體系結構：
  * Windows AMD64
  * Linux AMD64
  * Linux ARM32
  * Linux ARM64（預覽版）

雲端資源：

Azure 中的標準層 [IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)。

## <a name="devicetocloudupload-and-deviceautodelete-properties"></a>設備到雲上傳和設備自動刪除屬性

使用模組所需的屬性將**設備設置為"雲上傳屬性****"和設備自動刪除屬性**。 可以在部署期間設置所需的屬性，或者稍後通過編輯模組孿生來更改所需的屬性，而無需重新部署。 我們建議檢查"模組孿生"，`reported configuration`並確保`configurationValidation`值正確傳播。

### <a name="devicetoclouduploadproperties"></a>設備到雲上傳屬性

此設置的名稱為`deviceToCloudUploadProperties`。 如果使用 IoT Edge 模擬器，則將這些屬性的值設置為相關環境變數，您可以在說明部分中找到這些變數。

| 屬性 | 可能的值 | 說明 |
| ----- | ----- | ---- |
| 上傳上 | true、false | 設置為`false`預設。 如果要打開該功能，請將此欄位設置為`true`。 <br><br> 環境變數：`deviceToCloudUploadProperties__uploadOn={false,true}` |
| 上傳訂單 | 最新第一，最老的第一 | 允許您選擇將資料複製到 Azure 的順序。 設置為`OldestFirst`預設。 順序由 Blob 的最後修改時間確定。 <br><br> 環境變數：`deviceToCloudUploadProperties__uploadOrder={NewestFirst,OldestFirst}` |
| 雲存儲連接字串 |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"`是一個連接字串，允許您指定要將資料上載到的存儲帳戶。 指定`Azure Storage Account Name` `Azure Storage Account Key`， `End point suffix`， . 在其中將上載資料的位置添加 Azure 的相應終結點Suffix，它因全域 Azure、政府 Azure 和 Microsoft Azure 堆疊而異。 <br><br> 您可以在此處選擇指定 Azure 存儲 SAS 連接字串。 但是，在此屬性過期時，必須更新此屬性。 <br><br> 環境變數：`deviceToCloudUploadProperties__cloudStorageConnectionString=<connection string>` |
| 存儲容器用於上傳 | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | 允許您指定要上載到 Azure 的容器名稱。 此模組允許您指定源容器和目標容器名稱。 如果不指定目標容器名稱，它將自動將容器名稱指定為`<IoTHubName>-<IotEdgeDeviceID>-<ModuleName>-<SourceContainerName>`。 可以為目標容器名稱創建範本字串，簽出可能的值列。 <br>• %h -> IoT 中心名稱（3-50 個字元）。 <br>• %d -> IoT 邊緣裝置識別碼（1 到 129 個字元）。 <br>• %m -> 模組名稱（1 到 64 個字元）。 <br>• %c ->源容器名稱（3 到 63 個字元）。 <br><br>容器名稱的最大大小為 63 個字元，而如果容器大小超過 63 個字元，將自動分配目標容器名稱，它將將每個部分（IoTHubName、IotDeviceID、模組名稱、源容器名稱）修剪為 15 個字元字元。 <br><br> 環境變數：`deviceToCloudUploadProperties__storageContainersForUpload__<sourceName>__target=<targetName>` |
| 刪除上傳後 | true、false | 設置為`false`預設。 當它設置為`true`時，它將在上載到雲存儲時自動刪除資料。 <br><br> **注意**：如果使用追加 Blob，此設置將在成功上載後從本機存放區中刪除追加 Blob，並且將來對這些 blob 的任何追加塊操作都將失敗。 請謹慎使用此設置，如果應用程式執行不頻繁的追加操作或不支援連續追加操作，請不要啟用此設置<br><br> 環境變數： `deviceToCloudUploadProperties__deleteAfterUpload={false,true}`. |

### <a name="deviceautodeleteproperties"></a>設備自動刪除屬性

此設置的名稱為`deviceAutoDeleteProperties`。 如果使用 IoT Edge 模擬器，則將這些屬性的值設置為相關環境變數，您可以在說明部分中找到這些變數。

| 屬性 | 可能的值 | 說明 |
| ----- | ----- | ---- |
| 刪除On | true、false | 設置為`false`預設。 如果要打開該功能，請將此欄位設置為`true`。 <br><br> 環境變數：`deviceAutoDeleteProperties__deleteOn={false,true}` |
| 刪除後分鐘 | `<minutes>` | 指定以分鐘表示的時間。 當此值過期時，模組將自動從本機存放區中刪除您的 Blob。 <br><br> 環境變數：`deviceAutoDeleteProperties__ deleteAfterMinutes=<minutes>` |
| 載入時保留 | true、false | 預設情況下，它設置為`true`，如果 Delete 後分鐘過期，它將在上載到雲存儲時保留 Blob。 您可以將其設置為，`false`並且在刪除後分鐘過期時，它將刪除資料。 注意：對於此屬性的工作上傳應設置為 true。  <br><br> **注意**：如果使用追加 Blob，此設置將在值過期時從本機存放區中刪除追加 Blob，並且這些 blob 的任何將來追加塊操作都將失敗。 您可能希望確保到期值足夠大，以滿足應用程式執行的追加操作的預期頻率。<br><br> 環境變數：`deviceAutoDeleteProperties__retainWhileUploading={false,true}`|

## <a name="using-smb-share-as-your-local-storage"></a>使用 SMB 共用作為本機存放區

在 Windows 主機上部署此模組的 Windows 容器時，可以將 SMB 共用作為本機存放區路徑提供。

確保 SMB 共用和 IoT 設備位於相互信任的域中。

您可以運行`New-SmbGlobalMapping`PowerShell 命令，在運行 Windows 的 IoT 設備上本機對應 SMB 共用。

以下是配置步驟：

```PowerShell
$creds = Get-Credential
New-SmbGlobalMapping -RemotePath <remote SMB path> -Credential $creds -LocalPath <Any available drive letter>
```

例如：

```powershell
$creds = Get-Credential
New-SmbGlobalMapping -RemotePath \\contosofileserver\share1 -Credential $creds -LocalPath G:
```

此命令將使用憑據與遠端 SMB 伺服器進行身份驗證。 然後，將遠端共用路徑對應至 G: 磁碟機代號 (可以是任何其他可用的磁碟機代號)。 IoT 設備現在將資料卷映射到 G： 磁碟機上的路徑。

確保 IoT 設備中的使用者可以讀取/寫入遠端 SMB 共用。

對於您的部署，的值`<storage mount>`可以是**G：/容器資料：C：/BlobRoot**。

## <a name="granting-directory-access-to-container-user-on-linux"></a>向 Linux 上的容器使用者授予目錄存取權限

如果在 Linux 容器的創建選項中使用[卷裝載](https://docs.docker.com/storage/volumes/)存儲，則無需執行任何額外的步驟，但如果使用[綁定裝載](https://docs.docker.com/storage/bind-mounts/)，則需要執行這些步驟才能正確運行服務。

遵循將使用者存取權限限制為執行其工作所需的最低許可權的最低許可權的原則，此模組包括使用者（名稱：absie，ID：11000）和使用者組（名稱：absie，ID：11000）。 如果容器以**root**身份啟動（預設使用者是**root），** 我們的服務將作為低特權**absie**使用者啟動。

此行為使主機路徑上的許可權配置綁定對服務正常工作至關重要，否則服務將崩潰，導致訪問被拒絕錯誤。 容器使用者需要訪問目錄綁定中使用的路徑（例如：absie 11000）。 您可以通過在主機上執行以下命令來授予容器使用者對目錄的存取權限：

```terminal
sudo chown -R 11000:11000 <blob-dir>
sudo chmod -R 700 <blob-dir>
```

例如：

```terminal
sudo chown -R 11000:11000 /srv/containerdata
sudo chmod -R 700 /srv/containerdata
```

如果需要以**absie**以外的使用者身份運行服務，則可以在部署清單中的"使用者"屬性下在 createOptions 中指定自訂使用者 ID。 在這種情況下，您需要使用預設組 ID 或根組`0`ID 。

```json
"createOptions": {
  "User": "<custom user ID>:0"
}
```

現在，授予容器使用者對目錄的存取權限

```terminal
sudo chown -R <user ID>:<group ID> <blob-dir>
sudo chmod -R 700 <blob-dir>
```

## <a name="configure-log-files"></a>配置日誌檔

有關為模組配置日誌檔的資訊，請參閱這些[生產最佳實踐](https://docs.microsoft.com/azure/iot-edge/production-checklist#set-up-logs-and-diagnostics)。

## <a name="connect-to-your-blob-storage-module"></a>連接到您的 Blob 儲存體模組

您可以使用帳戶名稱和您為模組設定的帳戶金鑰來存取 IoT Edge 裝置上的 Blob 儲存體。

指定 IoT Edge 裝置作為您提出的任何儲存體要求所用的 Blob 端點。 您可以使用 IoT Edge 裝置資訊和您設定的帳戶名稱，[建立明確儲存體端點的連接字串](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint)。

* 對於部署在同一設備上部署的模組，與 IoT Edge 模組上的 Azure Blob 存儲運行位置相同，Blob 終結點為： `http://<module name>:11002/<account name>`。
* 對於在不同的設備上運行的模組或應用程式，您必須為網路選擇正確的終結點。 根據網路設置，選擇終結點格式，以便來自外部模組或應用程式的資料流量可以到達在 IoT Edge 模組上運行 Azure Blob 存儲的設備。 此方案的 blob 終結點是：
  * `http://<device IP >:11002/<account name>`
  * `http://<IoT Edge device hostname>:11002/<account name>`
  * `http://<fully qualified domain name>:11002/<account name>`

## <a name="azure-blob-storage-quickstart-samples"></a>Azure Blob 存儲快速啟動示例

Azure Blob 存儲文檔包括多種語言的快速入門示例代碼。 可以通過更改 Blob 終結點連接到本地 Blob 存儲模組，運行這些示例以測試 IoT Edge 上的 Azure Blob 存儲。

以下快速入門示例使用 IoT Edge 也支援的語言，因此您可以將它們部署為 IT Edge 模組以及 blob 存儲模組：

* [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Python](../storage/blobs/storage-quickstart-blobs-python.md)
  * Python SDK 的 V2.1 之前的版本存在已知問題，即模組不返回 Blob 創建時間。 由於此問題，某些方法（如清單 blob）不起作用。 作為解決方法，顯式將 blob 用戶端上的 API 版本設置為"2017-04-17"。 範例：`block_blob_service._X_MS_VERSION = '2017-04-17'`
  * [追加 Blob 示例](https://github.com/Azure/azure-storage-python/blob/master/samples/blob/append_blob_usage.py)
* [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs-legacy.md)
* [JS/HTML](../storage/blobs/storage-quickstart-blobs-javascript-client-libraries-legacy.md)
* [紅寶石](../storage/blobs/storage-quickstart-blobs-ruby.md)
* [移至](../storage/blobs/storage-quickstart-blobs-go.md)
* [Php](../storage/blobs/storage-quickstart-blobs-php.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>使用 Azure 存儲資源管理器連接到本機存放區

您可以使用[Azure 存儲資源管理器](https://azure.microsoft.com/features/storage-explorer/)連接到本機存放區帳戶。

1. 下載並安裝 Azure 儲存體總管

1. 使用連接字串連接到 Azure 存儲

1. 提供連接字串：`DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`

1. 完成連接步驟。

1. 在本機存放區帳戶內創建容器

1. 啟動將檔上載為塊 Blob 或追加 Blob。
   > [!NOTE]
   > 此模組不支援頁面 blob。

1. 您也可以選擇在存儲資源管理器中連接 Azure 存儲帳戶。 此配置為本機存放區帳戶和 Azure 存儲帳戶提供單個視圖

## <a name="supported-storage-operations"></a>支援的儲存體作業

IoT Edge 上的 Blob 存儲模組使用 Azure 存儲 SDK，並且與塊 Blob 終結點的 Azure 存儲 API 的 2017-04-17 版本一致。

由於並非所有 Azure Blob 存儲操作都由 IoT Edge 上的 Azure Blob 存儲支援，因此本節列出了每個操作的狀態。

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

* 追加塊

不支援：

* URL 中的追加塊

## <a name="event-grid-on-iot-edge-integration"></a>IoT 邊緣集成上的事件網格

> [!CAUTION]
> 與 IoT 邊緣上的事件網格的集成處於預覽狀態

此 IoT 邊緣模組上的 Azure Blob 存儲現在提供與 IoT 邊緣上的事件網格的集成。 有關此集成的詳細資訊，請參閱[部署模組、發佈事件和驗證事件傳遞的教程](../event-grid/edge/react-blob-storage-events-locally.md)。

## <a name="release-notes"></a>版本資訊

以下是此模組[的 Docker 集線器中的版本資訊](https://hub.docker.com/_/microsoft-azure-blob-storage)

## <a name="feedback"></a>意見反應

您的回饋對我們非常重要，使本模組及其功能有用且便於使用。 請分享您的回饋，並告訴我們如何改進。

您可以聯繫我們：absiotfeedback@microsoft.com

## <a name="next-steps"></a>後續步驟

瞭解如何在[IoT 邊緣部署 Azure Blob 存儲](how-to-deploy-blob.md)

在[IoT Edge 博客上](https://aka.ms/abs-iot-blogpost)的 Azure Blob 存儲中隨時瞭解最新更新和公告

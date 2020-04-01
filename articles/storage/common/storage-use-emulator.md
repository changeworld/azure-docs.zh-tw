---
title: 使用 Azure 儲存體模擬器進行開發和測試 | Microsoft Docs
description: Azure 儲存體模擬器提供免費的本機開發環境，針對 Azure 儲存體應用程式進行開發和測試。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/21/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: 997aa9d96f2f52331865fd15d97443d74bb8bc1f
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398005"
---
# <a name="use-the-azure-storage-emulator-for-development-and-testing"></a>使用 Azure 儲存體模擬器進行開發和測試

Microsoft Azure 儲存模擬器是一種工具,用於類比 Azure Blob、佇列和表服務,用於本地開發目的。 您可以在本地根據儲存服務測試應用程式,而無需創建 Azure 訂閱或產生任何成本。 當您對應用程式在模擬器中的工作方式感到滿意時,切換到在雲中使用 Azure 儲存帳戶。

## <a name="get-the-storage-emulator"></a>取得儲存體模擬器

儲存體模擬器隨 [Microsoft Azure SDK](https://azure.microsoft.com/downloads/)提供。 您也可以使用[獨立安裝程式](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409) (直接下載) 來安裝儲存體模擬器。 若要安裝儲存體模擬器，您必須具有電腦上的系統管理權限。

儲存體模擬器目前只能在 Windows 上執行。 如果您需要 Linux 的儲存模擬器,一個選項是維護的社區,開源記憶體模擬器[Azurite](https://github.com/azure/azurite)。

> [!NOTE]
> 在某個儲存體模擬器版本中建立的資料不保證可在使用不同版本時加以存取。 如果您需要長期保存資料，建議您將該資料儲存於 Azure 儲存體帳戶中 (而不是儲存體模擬器中)。
> 
> 儲存體模擬器取決於特定的 OData 程式庫版本。 不支援將儲存體模擬器所使用的 OData DLL 更換為其他版本，而且可能會造成非預期的行為。 不過，儲存體服務支援的任何版本 OData 可能用來將要求傳送至模擬器。

## <a name="how-the-storage-emulator-works"></a>儲存體模擬器的運作方式

存儲模擬程式使用本地 Microsoft SQL Server 2012 Express 本地資料庫實例來類比 Azure 儲存服務。 您可以選擇設定儲存體模擬器存取 SQL Server 本機執行個體，而非 LocalDB 執行個體。 請參閱本文後面的[「開始並初始化儲存模擬器」](#start-and-initialize-the-storage-emulator)部分以瞭解更多資訊。

儲存體模擬器會使用 Windows 驗證，連接到 SQL Server 或 LocalDB。

儲存體模擬器與 Azure 儲存體服務的功能有所差異。 如需有關這些差異的詳細資訊，請參閱本文稍後的[儲存體模擬器和 Azure 儲存體之間的差異](#differences-between-the-storage-emulator-and-azure-storage)一節。

## <a name="start-and-initialize-the-storage-emulator"></a>啟動及初始化儲存體模擬器

啟動 Azure 儲存體模擬器：

1. 選取 [開始]**** 按鈕或按下 [Windows]**** 鍵。
2. 開始輸入 `Azure Storage Emulator`。
3. 從顯示的應用程式清單中選取模擬器。

當儲存體模擬器啟動時，將會出現 [命令列提示字元] 視窗。 您可以使用此主控台視窗啟動和停止記憶體模擬器。 您還可以從命令提示符中清除資料、獲取狀態和初始化模擬器。 如需詳細資訊，請參閱本文稍後的[儲存體模擬器命令列工具參考](#storage-emulator-command-line-tool-reference)一節。

> [!NOTE]
> 如果系統上正在運行另一個記憶體比器(如 Azurite),則 Azure 儲存模擬器可能無法正確啟動。

如果模擬器正在執行，您就會在 Windows 工作列通知區域看到圖示。

當您關閉儲存體模擬器的 [命令提示字元] 視窗時，儲存體模擬器將會繼續執行。 若要再次顯示 [儲存體模擬器] 主控台視窗，請遵循先前步驟，如同啟動儲存體模擬器一樣。

當您第一次執行儲存體模擬器時，系統會為您初始化本機儲存體環境。 初始化處理程序會在 LocalDB 中建立資料庫，並為每個本機儲存體服務保留 HTTP 連接埠。

預設會將儲存體模擬器安裝至 `C:\Program Files (x86)\Microsoft SDKs\Azure\Storage Emulator`。

> [!TIP]
> 您可以使用 [Microsoft Azure 儲存體總管](https://storageexplorer.com)來使用本機儲存體模擬器資源。 安裝並啟動儲存模擬器後,在儲存資源管理器資源樹中的「本地&連接」下查找「(模擬器 - 預設埠)(密鑰)。
>

### <a name="initialize-the-storage-emulator-to-use-a-different-sql-database"></a>初始化儲存體模擬器以使用不同的 SQL 資料庫

您可以使用儲存體模擬器命令列工具來初始化儲存體模擬器，以指向預設 LocalDB 執行個體以外的 SQL 資料庫執行個體：

1. 開啟 [儲存體模擬器] 主控台視窗，如[啟動及初始化儲存體模擬器](#start-and-initialize-the-storage-emulator)一節中所述。
1. 在主控台視窗中，輸入下列命令，其中 `<SQLServerInstance>` 是 SQL Server 執行個體的名稱。 若要使用 LocalDB，請指定 `(localdb)\MSSQLLocalDb` 做為 SQL Server 執行個體。

   `AzureStorageEmulator.exe init /server <SQLServerInstance>`

   您也可以使用下列命令，引導模擬器使用預設的 SQL Server 執行個體：

   `AzureStorageEmulator.exe init /server .`

   或者，您也可以使用下列命令，將資料庫重新初始化至預設 LocalDB 執行個體：

   `AzureStorageEmulator.exe init /forceCreate`

如需有關這些命令的詳細資訊，請參閱[儲存體模擬器命令列工具參考](#storage-emulator-command-line-tool-reference)。

> [!TIP]
> 您可以使用 [Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) 來管理您的 SQL Server 執行個體，包括 LocalDB 安裝。 在 SMSS [連接到伺服器]**** 對話方塊中，於 [伺服器名稱:]**** 欄位中指定 `(localdb)\MSSQLLocalDb`，以連接到 LocalDB 執行個體。

## <a name="authenticating-requests-against-the-storage-emulator"></a>對儲存體模擬器的驗證要求

一旦您安裝並啟動儲存體模擬器之後，就可以針對它測試您的程式碼。 您針對儲存模擬程式提出的每個請求都必須獲得授權,除非它是匿名請求。 您可以使用共用金鑰驗證或共用存取簽章 (SAS)，授權傳送給儲存體模擬器的要求。

### <a name="authorize-with-shared-key-credentials"></a>使用共用金鑰認證進行授權

[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

如需連接字串的詳細資訊，請參閱[設定 Azure 儲存體連接字串](../storage-configure-connection-string.md)。

### <a name="authorize-with-a-shared-access-signature"></a>使用共用存取簽章進行授權

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

某些 Azure 儲存體用戶端程式庫 (例如 Xamarin 程式庫)，僅支援使用共用存取簽章 (SAS) 權杖進行的驗證。 您可以使用[儲存資源管理器](https://storageexplorer.com/)或其他支援共享密鑰身份驗證的應用程式創建 SAS 權杖。

您也可以使用 Azure PowerShell 來產生 SAS 權杖。 下列範例會產生 SAS 權杖且對 blob 容器具有完整權限：

1. 安裝 Azure PowerShell (如果您尚未安裝，建議使用 Azure PowerShell Cmdlet 的最新版本)。 如需安裝指示，請參閱[安裝和設定 Azure PowerShell](/powershell/azure/install-Az-ps)。
2. 開啟 Azure PowerShell 並執行下列命令，使用您所選的名稱來取代 `CONTAINER_NAME`：

```powershell
$context = New-AzStorageContext -Local

New-AzStorageContainer CONTAINER_NAME -Permission Off -Context $context

$now = Get-Date

New-AzStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri
```

針對新容器產生的共用存取簽章 URI 應該類似下列項目：

```
http://127.0.0.1:10000/devstoreaccount1/sascontainer?sv=2012-02-12&se=2015-07-08T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss
```

使用此範例建立的共用存取簽章的效期為一天。 簽章會將完整存取權限 (讀取、寫入、刪除、列出) 授與容器內的 Blob。

有關共享存取簽章的詳細資訊,請參閱[使用共享存取簽章 (SAS) 授予對 Azure 資源的有限存取權限](storage-sas-overview.md)。

## <a name="addressing-resources-in-the-storage-emulator"></a>在儲存體模擬器中為資源定址

存儲模擬器的服務終結點與 Azure 儲存帳戶的終結點不同。 本地電腦不執行功能變數名稱解析,要求儲存模擬器終結點為本地位址。

當您在 Azure 儲存體帳戶中定址資源時，可使用下列配置。 帳戶名稱是 URI 主機名稱的一部分，而要定址的資源是 URI 路徑的一部分：

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

例如，下列 URI 是 Azure 儲存體帳戶中的有效 blob 位址：

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

由於本地電腦不執行功能變數名稱解析,因此帳戶名稱是URI路徑的一部分,而不是主機名。 針對儲存體模擬器中的資源使用下列 URI 格式：

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

例如，下列位址可能會用於存取儲存體模擬器中的 Blob：

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

儲存體模擬器的服務端點包括：

* Blob 服務：`http://127.0.0.1:10000/<account-name>/<resource-path>`
* 佇列服務：`http://127.0.0.1:10001/<account-name>/<resource-path>`
* 表格服務：`http://127.0.0.1:10002/<account-name>/<resource-path>`

### <a name="addressing-the-account-secondary-with-ra-grs"></a>使用 RA-GRS 為帳戶次要位址定址

從 3.1 版開始，儲存體模擬器就支援讀取存取地理備援複寫 (RA-GRS)。 您可以通過將 -輔助位置追加到帳戶名稱來造訪輔助位置。 例如，下列位址可能會用於在儲存體模擬器中使用唯讀的次要位置來存取 Blob：

`http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt`

> [!NOTE]
> 如需以程式設計方式使用儲存體模擬器來存取次要位置，請使用 Storage Client Library for.NET 3.2 版或更新版本。 如需詳細資訊，請參閱 [Microsoft Azure Storage Client Library for .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx) (適用於 .NET 的 Microsoft Azure 儲存體用戶端程式庫)。
>
>

## <a name="storage-emulator-command-line-tool-reference"></a>儲存體模擬器命令列工具參考

從 3.0 版開始，當您啟動儲存體模擬器時，就會顯示主控台視窗。 使用主控台視窗中的命令行啟動和停止模擬器。 您還可以查詢狀態並從命令行執行其他操作。

> [!NOTE]
> 如果您已安裝 Microsoft Azure 計算模擬器，當您啟動儲存體模擬器時，就會出現系統匣圖示。 使用滑鼠右鍵按一下圖示即可顯示功能表，提供圖形化方式來啟動和停止儲存體模擬器。
>
>

### <a name="command-line-syntax"></a>命令列語法

`AzureStorageEmulator.exe [start] [stop] [status] [clear] [init] [help]`

### <a name="options"></a>選項。

若要檢視選項清單，請在命令提示字元輸入 `/help` 。

| 選項 | 描述 | Command | 引數 |
| --- | --- | --- | --- |
| **啟動** |啟動儲存體模擬器。 |`AzureStorageEmulator.exe start [-inprocess]` |*-重新處理*:在當前進程中啟動模擬器,而不是創建新進程。 |
| **停止** |停止儲存體模擬器。 |`AzureStorageEmulator.exe stop` | |
| **狀態** |列印儲存體模擬器的狀態。 |`AzureStorageEmulator.exe status` | |
| **清楚** |清除命令列上指定的所有服務中的資料。 |`AzureStorageEmulator.exe clear [blob] [table] [queue] [all]` |*blob*：清除 blob 資料。 <br/>*queue*：清除佇列資料。 <br/>*table*：清除資料表資料。 <br/>*all*：清除所有服務中的所有資料。 |
| **Init** |執行一次性初始化以設置模擬器。 |<code>AzureStorageEmulator.exe init [-server serverName] [-sqlinstance instanceName] [-forcecreate&#124;-skipcreate] [-reserveports&#124;-unreserveports] [-inprocess]</code> |*-server serverName\instanceName*︰指定裝載 SQL 執行個體的伺服器。 <br/>*-sqlinstance instanceName*：指定在預設伺服器執行個體中使用之 SQL 執行個體的名稱。 <br/>*-forcecreate*：強制建立 SQL 資料庫，即使它已經存在。 <br/>*-skipcreate*︰略過建立 SQL 資料庫。 其優先順序高於 -forcecreate。<br/>*-reserveports*︰嘗試保留與服務相關聯的 HTTP 連接埠。<br/>*-unreserveports*︰嘗試移除服務相關聯 HTTP 連接埠的保留。 其優先順序高於 -reserveports。<br/>*-inprocess*：在目前的處理序中執行初始化，而不是繁衍新的處理序。 如果變更連接埠保留，必須以提高權限啟動目前的處理程序。 |

## <a name="differences-between-the-storage-emulator-and-azure-storage"></a>儲存體模擬器和 Azure 儲存體之間的差異

由於儲存模擬器是本地模擬環境,因此在雲端中使用模擬器和 Azure 儲存帳戶之間存在差異:

* 儲存體模擬器僅支援單一固定帳戶及已知的驗證金鑰。
* 存儲模擬程式不是可擴展的存儲服務,並且不支援大量併發用戶端。
* 如 [在儲存體模擬器中定址資源](#addressing-resources-in-the-storage-emulator)中所述，資源在儲存體模擬器與在 Azure 儲存體帳戶中的定址方式不同。 區別在於,功能變數名稱解析在雲中可用,但在本地電腦上不可用。
* 從 3.1 版開始，儲存體模擬器帳戶就支援讀取存取地理備援複寫 (RA-GRS)。 在模擬器中,所有帳戶都啟用了 RA-GRS,主副本和輔助副本之間永遠不會有任何延遲。 次要帳戶支援 Get Blob Service Stats、Get Queue Service Stats 和 Get Table Service Stats 作業，且這些作業一律會根據基礎 SQL 資料庫傳回 `LastSyncTime` 回應元素的值當成目前的時間。
* 儲存模擬器當前不支援檔服務和 SMB 協定服務終結點。
* 如果使用模擬程式不支援的儲存服務版本,模擬程式將返回版本不支援的「密碼」檢查器錯誤(HTTP 狀態代碼 400 - 錯誤請求)。

### <a name="differences-for-blob-storage"></a>Blob 儲存體的差異

下列差異適用於模擬器中的 Blob 儲存體：

* 儲存體模擬器最多只支援 2 GB 的 blob 大小。
* 儲存體模擬器中的 Blob 名稱長度上限是 256 個字元，而 Azure 儲存體中的 Blob 名稱長度上限是 1024 個字元。
* 增量複本會讓來自覆寫 blob 的快照進行複製，其會在服務時傳回失敗。
* 獲取頁面範圍差異在使用增量複製 Blob 複製的快照之間不起作用。
* Put Blob 操作可能會針對存在於具有活動租約的儲存模擬器中的 Blob 成功,即使請求中未指定租約 ID 也是如此。
* 模擬器不支援附加 Blob 作業。 在附加 Blob 上嘗試作業會傳回 FeatureNotSupportedByEmulator 錯誤 (HTTP 狀態碼 400-不正確的要求)。

### <a name="differences-for-table-storage"></a>資料表儲存體的差異

下列差異適用於模擬器中的資料表儲存體：

* 儲存模擬器中的表服務中的日期屬性僅支援 SQL Server 2005 支援的範圍(它們要求晚於 1753 年 1 月 1 日)。 1753 年 1 月 1 日之前的所有日期都會變成此值。 日期的精確度受限於 SQL Server 2005 的精確度，也就是會精確度達到 1/300 秒。
* 儲存體模擬器支援屬性值小於 512 個位元組的資料分割索引鍵與資料列索引鍵。 帳戶名稱、表名稱和鍵屬性名稱的總大小不能超過 900 位元組。
* 在儲存體模擬器中，資料表的資料列大小總計限制為小於 1 MB。
* 在儲存體模擬器中，資料類型 `Edm.Guid` 或 `Edm.Binary` 的屬性只支援查詢篩選字串中的 `Equal (eq)` 與 `NotEqual (ne)` 比較運算子。

### <a name="differences-for-queue-storage"></a>佇列儲存體的差異

模擬器中的佇列儲存體沒有特定差異。

## <a name="storage-emulator-release-notes"></a>儲存體模擬器版本資訊

### <a name="version-510"></a>版本 5.10

* 儲存模擬器不會拒絕 Blob、佇列和表服務終結點上的存儲服務版本 2019-07-07。

### <a name="version-59"></a>版本 5.9

* 儲存模擬器不會拒絕 Blob、佇列和表服務終結點上的存儲服務版本 2019-02-02。

### <a name="version-58"></a>版本 5.8

* 儲存模擬器不會拒絕 Blob、佇列和表服務終結點上的存儲服務版本 2018-11-09。

### <a name="version-57"></a>5.7 版

* 已修正啟用記錄時會造成當機的錯誤。

### <a name="version-56"></a>5.6 版

* 儲存體模擬器現在支援 Blob、佇列和資料表服務端點上 2018-03-28 版的儲存體服務。

### <a name="version-55"></a>版本 5.5

* 儲存體模擬器現在支援 Blob、佇列和資料表服務端點上 2017-11-09 版的儲存體服務。
* 已新增 Blob **Created** 屬性的支援，它會傳回 Blob 建立時間。

### <a name="version-54"></a>版本 5.4

* 若要改善安裝穩定性，模擬器不會嘗試在安裝時保留連接埠。 如果需要埠預留,請使用**init**命令的 *-預留埠*選項來指定它們。

### <a name="version-53"></a>版本 5.3

* 儲存體模擬器現在支援 Blob、佇列和資料表服務端點上 2017-07-29 版的儲存體服務。

### <a name="version-52"></a>5.2 版

* 儲存體模擬器現在支援 Blob、佇列和表格服務端點上 2017-04-17 版的儲存體服務。
* 修復了未正確編碼表屬性值的錯誤。

### <a name="version-51"></a>版本 5.1

* 修正了儲存體模擬器在某些回應中傳回 `DataServiceVersion` 標頭 (但服務未傳回) 的問題。

### <a name="version-50"></a>版本 5.0

* 儲存體模擬器安裝程式不再會檢查現有的 MSSQL 和 .NET Framework 安裝。
* 儲存體模擬器安裝程式不再會隨著安裝建立資料庫。 必要時仍會隨著啟動建立資料庫。
* 建立資料庫不再需要提高權限。
* 啟動不再需要保留連接埠。
* 將下列選項新增至 `init`：`-reserveports` (需要提高權限)、`-unreserveports` (需要提高權限)、`-skipcreate`。
* 系統托盤圖示上的儲存模擬器 UI 選項現在啟動命令列介面。 已不再使用舊 GUI。
* 某些 DLL 已移除或重新命名。

### <a name="version-46"></a>版本 4.6

* 儲存體模擬器現在支援 Blob、佇列和資料表服務端點上的 2016-05-31 版儲存體服務。

### <a name="version-45"></a>版本 4.5

* 修復了在重命名備份資料庫時導致安裝和初始化失敗的錯誤。

### <a name="version-44"></a>4.4 版

* 儲存體模擬器現在支援 Blob、佇列和資料表服務端點上 2015-12-11 版的儲存體服務。
* 現在，處理大量 blob 時，儲存體模擬器的 blob 資料記憶體回收更有效率。
* 修正了造成容器 ACL XML 的驗證方式與儲存體服務的運作方式稍有不同的錯誤。
* 修正了有時造成報告的日期時間上限和下限值時區不正確的錯誤。

### <a name="version-43"></a>版本 4.3

* 儲存體模擬器現在支援 Blob、佇列和表格服務端點上的 2015-07-08 版儲存體服務。

### <a name="version-42"></a>4.2 版

* 儲存體模擬器現在支援 Blob、佇列和資料表服務端點上 2015-04-05 版的儲存體服務。

### <a name="version-41"></a>4.1 版

* 存儲模擬程式現在支援 Blob、佇列和表服務終結點上的存儲服務版本 2015-02-21。 它不支援新的追加 Blob 功能。
* 模擬程式現在返回一條有意義的錯誤消息,用於不支援的存儲服務版本。 我們建議使用最新版本的模擬器。 如果收到版本不支援的「密碼」錯誤(HTTP 狀態代碼 400 - 錯誤請求),請下載最新版本的模擬程式。
* 修正競爭情形造成資料表實體資料在並行合併作業期間會不正確的 bug。

### <a name="version-40"></a>4.0 版

* 儲存體模擬器的可執行檔已重新命名為 *AzureStorageEmulator.exe*。

### <a name="version-32"></a>3.2 版

* 儲存體模擬器現在支援 Blob、佇列和資料表服務端點上的 2014-02-14 版儲存體服務。 儲存模擬器當前不支援檔服務終結點。 如需有關 2014-02-14 版本的詳細資訊，請參閱 [為 Microsoft Azure 中的 Blob、佇列和表格服務進行版本設定](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) 。

### <a name="version-31"></a>3.1 版

* 儲存體模擬器現在支援讀取權限異地備援儲存體 (RA-GRS)。 帳戶`Get Blob Service Stats`輔助`Get Queue Service Stats`資料庫支援`Get Table Service Stats`和 API,並且始終根據基礎 SQL 資料庫將 LastSyncTime 回應元素的值作為當前時間返回。 如需以程式設計方式使用儲存體模擬器來存取次要位置，請使用 Storage Client Library for.NET 3.2 版或更新版本。 如需詳細資訊，請參閱「Microsoft Azure Storage Client Library for .NET」。

### <a name="version-30"></a>3.0 版

* Azure 儲存體模擬器不再隨附於計算模擬器封裝。
* 儲存模擬器圖形使用者介面被棄用。 它已被可編寫腳本的命令行介面替換。 如需有關命令列介面的詳細資訊，請參閱儲存體模擬器命令列工具參考。 3.0 版仍將提供圖形化介面，除非在系統匣圖示按一下滑鼠右鍵並選取 [顯示儲存體模擬器 UI] 的方式安裝計算模擬器，否則無法存取圖形化介面。
* 現在完全支援 2013-08-15 版的 Azure 儲存體服務。 (先前只有儲存體模擬器 2.2.1 版預覽才支援此版本)。

## <a name="next-steps"></a>後續步驟

* 評估跨平台、社區維護的開源儲存模擬器[Azurite](https://github.com/azure/azurite)。 
* [使用 .NET 的 Azure 儲存體範例](../storage-samples-dotnet.md)包含開發應用程式時您可以使用的數個程式碼範例的連結。
* 您可以使用 [Microsoft Azure 儲存體總管](https://storageexplorer.com)，來使用雲端儲存體帳戶和儲存體模擬器中的資源。

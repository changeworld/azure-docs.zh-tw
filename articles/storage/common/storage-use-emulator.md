---
title: 使用 Azure 儲存體模擬器進行開發和測試
description: Azure 儲存體模擬器提供免費的本機開發環境，可用於開發和測試您的 Azure 儲存體應用程式。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/16/2020
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.openlocfilehash: b51746326c2df81e2dd2bdc72bf2a9ab72b649b5
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92790554"
---
# <a name="use-the-azure-storage-emulator-for-development-and-testing"></a>使用 Azure 儲存體模擬器進行開發和測試

Microsoft Azure 儲存體模擬器是一種可模擬 Azure Blob、佇列和表格服務的工具，供本機開發之用。 您可以針對儲存體服務在本機測試應用程式，而不需建立 Azure 訂用帳戶，也不會產生任何費用。 當您滿意應用程式在模擬器中的運作方式時，請切換到使用雲端中的 Azure 儲存體帳戶。

> [!IMPORTANT]
> 不再積極開發 Azure 儲存體模擬器。 [**Azurite**](storage-use-azurite.md) 是即將前進的儲存體模擬器平臺。 Azurite 會取代 Azure 儲存體模擬器。 Azurite 將繼續更新以支援最新版本的 Azure 儲存體 Api。 如需詳細資訊，請參閱 [**使用 Azurite 模擬器進行本機 Azure 儲存體開發**](storage-use-azurite.md)。

## <a name="get-the-storage-emulator"></a>取得儲存體模擬器

儲存體模擬器可作為 [MICROSOFT AZURE SDK](https://azure.microsoft.com/downloads/)的一部分。 您也可以使用 [獨立安裝程式](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409) (直接下載) 來安裝儲存體模擬器。 若要安裝儲存體模擬器，您必須具備電腦的系統管理許可權。

儲存體模擬器目前只能在 Windows 上執行。 如果您需要適用于 Linux 的儲存體模擬器，有一個選項是已維護的「開放原始碼儲存體模擬器 [Azurite](https://github.com/azure/azurite)」。

> [!NOTE]
> 使用不同的版本時，不保證可存取在某個儲存體模擬器版本中建立的資料。 如果您需要長期保存資料，建議您將該資料儲存在 Azure 儲存體帳戶中，而不是儲存在儲存體模擬器中。
> 
> 儲存體模擬器相依于特定版本的 OData 程式庫。 不支援將儲存體模擬器所使用的 OData Dll 取代為其他版本，而且可能會造成非預期的行為。 不過，儲存體服務支援的任何版本 OData 可能用來將要求傳送至模擬器。

## <a name="how-the-storage-emulator-works"></a>儲存體模擬器的運作方式

儲存體模擬器會使用本機的 Microsoft SQL Server 2012 Express LocalDB 實例來模擬 Azure 儲存體服務。 您可以選擇將儲存體模擬器設定為存取 SQL Server 的本機實例，而不是 LocalDB 實例。 若要深入瞭解，請參閱本文稍後的 [「啟動及初始化儲存體模擬器」](#start-and-initialize-the-storage-emulator) 一節。

儲存體模擬器會使用 Windows 驗證連接到 SQL Server 或 LocalDB。

儲存體模擬器和 Azure 儲存體服務之間有一些功能上的差異。 如需有關這些差異的詳細資訊，請參閱本文稍後的 [儲存體模擬器與 Azure 儲存體](#differences-between-the-storage-emulator-and-azure-storage) 一節之間的差異。

## <a name="start-and-initialize-the-storage-emulator"></a>啟動及初始化儲存體模擬器

若要啟動 Azure 儲存體模擬器：

1. 選取 [開始]  按鈕或按下 [Windows]  鍵。
2. 開始輸入 `Azure Storage Emulator`。
3. 從顯示的應用程式清單中選取模擬器。

當儲存體模擬器啟動時，會出現命令提示字元視窗。 您可以使用此主控台視窗來啟動和停止儲存體模擬器。 您也可以從命令提示字元清除資料、取得狀態，以及初始化模擬器。 如需詳細資訊，請參閱本文稍後的 [儲存體模擬器命令列工具參考](#storage-emulator-command-line-tool-reference) 一節。

> [!NOTE]
> 如果另一個儲存體模擬器（例如 Azurite）正在系統上執行，則 Azure 儲存體模擬器可能無法正確啟動。

如果模擬器正在執行，您就會在 Windows 工作列通知區域看到圖示。

當您關閉儲存體模擬器的命令提示字元視窗時，儲存體模擬器會繼續執行。 若要再次顯示儲存體模擬器主控台視窗，請遵循上述步驟，如同啟動儲存體模擬器一樣。

當您第一次執行儲存體模擬器時，系統會為您初始化本機儲存體環境。 初始化處理程序會在 LocalDB 中建立資料庫，並為每個本機儲存體服務保留 HTTP 連接埠。

儲存體模擬器預設會安裝至 `C:\Program Files (x86)\Microsoft SDKs\Azure\Storage Emulator` 。

> [!TIP]
> 您可以使用 [Microsoft Azure 儲存體總管](https://storageexplorer.com) 來處理本機儲存體模擬器資源。 當您安裝並啟動儲存體模擬器之後，請在 & 資源樹狀結構中，尋找 [本機儲存體總管附加] 下的 [ (模擬器-預設埠)  (金鑰) ]。
>

### <a name="initialize-the-storage-emulator-to-use-a-different-sql-database"></a>初始化儲存體模擬器以使用不同的 SQL 資料庫

您可以使用儲存體模擬器命令列工具來初始化儲存體模擬器，以指向預設 LocalDB 實例以外的 SQL 資料庫實例：

1. 開啟 [儲存體模擬器] 主控台視窗，如 [啟動及初始化儲存體模擬器](#start-and-initialize-the-storage-emulator) 一節中所述。
1. 在主控台視窗中，輸入下列命令，其中 `<SQLServerInstance>` 是 SQL Server 執行個體的名稱。 若要使用 LocalDB，請指定 `(localdb)\MSSQLLocalDb` 做為 SQL Server 執行個體。

   `AzureStorageEmulator.exe init /server <SQLServerInstance>`

   您也可以使用下列命令，引導模擬器使用預設的 SQL Server 執行個體：

   `AzureStorageEmulator.exe init /server .`

   或者，您可以使用下列命令，將資料庫初始化為預設 LocalDB 實例：

   `AzureStorageEmulator.exe init /forceCreate`

如需這些命令的詳細資訊，請參閱 [儲存體模擬器命令列工具參考](#storage-emulator-command-line-tool-reference)。

> [!TIP]
> 您可以使用 [Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) 來管理您的 SQL Server 執行個體，包括 LocalDB 安裝。 在 SMSS [連接到伺服器]  對話方塊中，於 [伺服器名稱:]  欄位中指定 `(localdb)\MSSQLLocalDb`，以連接到 LocalDB 執行個體。

## <a name="authenticating-requests-against-the-storage-emulator"></a>針對儲存體模擬器驗證要求

當您安裝並啟動儲存體模擬器之後，就可以對其測試您的程式碼。 您對儲存體模擬器提出的每個要求都必須獲得授權，除非它是匿名要求。 您可以使用共用金鑰驗證或共用存取簽章 (SAS) 來對儲存體模擬器授權要求。

### <a name="authorize-with-shared-key-credentials"></a>使用共用金鑰認證進行授權

[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

如需連接字串的詳細資訊，請參閱[設定 Azure 儲存體連接字串](./storage-configure-connection-string.md)。

### <a name="authorize-with-a-shared-access-signature"></a>使用共用存取簽章進行授權

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

某些 Azure 儲存體用戶端程式庫 (例如 Xamarin 程式庫)，僅支援使用共用存取簽章 (SAS) 權杖進行的驗證。 您可以使用 [儲存體總管](https://storageexplorer.com/) 或另一個支援共用金鑰驗證的應用程式來建立 SAS 權杖。

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

如需共用存取簽章的詳細資訊，請參閱 [使用共用存取簽章將有限存取權授與 Azure 儲存體資源 (SAS) ](storage-sas-overview.md)。

## <a name="addressing-resources-in-the-storage-emulator"></a>定址儲存體模擬器中的資源

儲存體模擬器的服務端點與 Azure 儲存體帳戶的端點不同。 本機電腦不會進行功能變數名稱解析，要求儲存體模擬器端點必須是本機位址。

當您在 Azure 儲存體帳戶中定址資源時，可使用下列配置。 帳戶名稱是 URI 主機名稱的一部分，而要定址的資源是 URI 路徑的一部分：

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

例如，下列 URI 是 Azure 儲存體帳戶中的有效 blob 位址：

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

因為本機電腦不會進行功能變數名稱解析，所以帳戶名稱是 URI 路徑的一部分，而不是主機名稱。 針對儲存體模擬器中的資源使用下列 URI 格式：

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

例如，下列位址可能會用於存取儲存體模擬器中的 blob：

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

儲存體模擬器的服務端點如下：

* Blob 服務：`http://127.0.0.1:10000/<account-name>/<resource-path>`
* 佇列服務：`http://127.0.0.1:10001/<account-name>/<resource-path>`
* 表格服務：`http://127.0.0.1:10002/<account-name>/<resource-path>`

### <a name="addressing-the-account-secondary-with-ra-grs"></a>使用 RA-GRS 為帳戶次要位址定址

從3.1 版開始，儲存體模擬器支援 (GRS) 的讀取權限異地冗余複寫。 您可以將次要位置附加至帳戶名稱，以存取次要位置。 例如，下列位址可能用於在儲存體模擬器中使用唯讀次要資料庫存取 blob：

`http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt`

> [!NOTE]
> 若要使用儲存體模擬器以程式設計方式存取次要資料庫，請使用適用于 .NET 3.2 版或更新版本的儲存體用戶端程式庫。 如需詳細資訊，請參閱 [Microsoft Azure Storage Client Library for .NET](/previous-versions/azure/dn261237(v=azure.100)) (適用於 .NET 的 Microsoft Azure 儲存體用戶端程式庫)。
>
>

## <a name="storage-emulator-command-line-tool-reference"></a>儲存體模擬器命令列工具參考

從 3.0 版開始，當您啟動儲存體模擬器時，就會顯示主控台視窗。 在主控台視窗中使用命令列來啟動和停止模擬器。 您也可以查詢狀態，並從命令列進行其他作業。

> [!NOTE]
> 如果您已安裝 Microsoft Azure 計算模擬器，當您啟動儲存體模擬器時，就會出現系統匣圖示。 使用滑鼠右鍵按一下圖示即可顯示功能表，提供圖形化方式來啟動和停止儲存體模擬器。
>
>

### <a name="command-line-syntax"></a>命令列語法

`AzureStorageEmulator.exe [start] [stop] [status] [clear] [init] [help]`

### <a name="options"></a>選項

若要檢視選項清單，請在命令提示字元輸入 `/help` 。

| 選項 | 描述 | Command | 引數 |
| --- | --- | --- | --- |
| **開始** |啟動儲存體模擬器。 |`AzureStorageEmulator.exe start [-inprocess]` |*-* 重新處理：在目前的進程中啟動模擬器，而不是建立新的進程。 |
| **停止** |停止儲存體模擬器。 |`AzureStorageEmulator.exe stop` | |
| **狀態** |列印儲存體模擬器的狀態。 |`AzureStorageEmulator.exe status` | |
| **清除** |清除命令列上指定的所有服務中的資料。 |`AzureStorageEmulator.exe clear [blob] [table] [queue] [all]` |*blob* ：清除 blob 資料。 <br/>*queue* ：清除佇列資料。 <br/>*table* ：清除資料表資料。 <br/>*all* ：清除所有服務中的所有資料。 |
| **Init** |進行一次性初始化以設定模擬器。 |<code>AzureStorageEmulator.exe init [-server serverName] [-sqlinstance instanceName] [-forcecreate&#124;-skipcreate] [-reserveports&#124;-unreserveports] [-inprocess]</code> |*-server serverName\instanceName* ︰指定裝載 SQL 執行個體的伺服器。 <br/>*-sqlinstance instanceName* ：指定在預設伺服器執行個體中使用之 SQL 執行個體的名稱。 <br/>*-forcecreate* ：強制建立 SQL 資料庫，即使它已經存在。 <br/>*-skipcreate* ︰略過建立 SQL 資料庫。 其優先順序高於 -forcecreate。<br/>*-reserveports* ︰嘗試保留與服務相關聯的 HTTP 連接埠。<br/>*-unreserveports* ︰嘗試移除服務相關聯 HTTP 連接埠的保留。 其優先順序高於 -reserveports。<br/>*-inprocess* ：在目前的處理序中執行初始化，而不是繁衍新的處理序。 如果變更連接埠保留，必須以提高權限啟動目前的處理程序。 |

## <a name="differences-between-the-storage-emulator-and-azure-storage"></a>儲存體模擬器與 Azure 儲存體之間的差異

因為儲存體模擬器是本機模擬環境，所以在雲端使用模擬器和 Azure 儲存體帳戶之間會有一些差異：

* 儲存體模擬器僅支援單一固定帳戶和知名的驗證金鑰。
* 儲存體模擬器不是可擴充的儲存體服務，也不支援大量的並行用戶端。
* 如在 [儲存體模擬器中定址資源](#addressing-resources-in-the-storage-emulator)所述，儲存體模擬器和 Azure 儲存體帳戶的資源定址方式不同。 差別在於，網域名稱解析可在雲端中使用，但無法在本機電腦上使用。
* 從3.1 版開始，儲存體模擬器帳戶支援「讀取權限地理區域冗余複寫」 (GRS) 。 在模擬器中，所有帳戶都會啟用 GRS，而且主要和次要複本之間永遠不會有任何延遲。 次要帳戶支援 Get Blob Service Stats、Get Queue Service Stats 和 Get Table Service Stats 作業，且這些作業一律會根據基礎 SQL 資料庫傳回 `LastSyncTime` 回應元素的值當成目前的時間。
* 儲存體模擬器目前不支援檔案服務和 SMB 通訊協定服務端點。
* 如果您使用模擬器不支援的儲存體服務版本，模擬器會傳回 VersionNotSupportedByEmulator 錯誤 (HTTP 狀態碼 400-錯誤的要求) 。

### <a name="differences-for-blob-storage"></a>Blob 儲存體的差異

下列差異適用於模擬器中的 Blob 儲存體：

* 儲存體模擬器最多隻支援 2 GB 的 blob 大小。
* 儲存體模擬器中 blob 名稱的最大長度是256個字元，而 Azure 儲存體中 blob 名稱的最大長度是1024個字元。
* 增量複本會讓來自覆寫 blob 的快照進行複製，其會在服務時傳回失敗。
* 在使用增量複製 Blob 複製的快照之間，取得頁面範圍差異無法正常運作。
* 即使要求中未指定租用識別碼，對具有作用中租用的儲存體模擬器中的 blob 進行 Put Blob 作業可能會成功。
* 模擬器不支援附加 blob 作業。 在附加 Blob 上嘗試作業會傳回 FeatureNotSupportedByEmulator 錯誤 (HTTP 狀態碼 400-不正確的要求)。

### <a name="differences-for-table-storage"></a>資料表儲存體的差異

下列差異適用於模擬器中的資料表儲存體：

* 儲存體模擬器中的表格服務的日期屬性只支援 SQL Server 2005 所支援的範圍 (它們必須晚于 1753) 的1月1日。 1753 年 1 月 1 日之前的所有日期都會變成此值。 日期的精確度受限於 SQL Server 2005 的精確度，也就是會精確度達到 1/300 秒。
* 儲存體模擬器支援每個小於512位元組的資料分割索引鍵和資料列索引鍵屬性值。 帳戶名稱、資料表名稱和索引鍵屬性名稱的總大小不能超過900個位元組。
* 在儲存體模擬器中，資料表的資料列大小總計限制為小於 1 MB。
* 在儲存體模擬器中，資料類型的屬性 `Edm.Guid` 或 `Edm.Binary` 僅支援 `Equal (eq)` `NotEqual (ne)` 查詢篩選字串中的和比較運算子。

### <a name="differences-for-queue-storage"></a>佇列儲存體的差異

模擬器中的佇列儲存體沒有特定差異。

## <a name="storage-emulator-release-notes"></a>儲存體模擬器版本資訊

### <a name="version-510"></a>版本5.10

* 儲存體模擬器不會拒絕 Blob、佇列和表格服務端點上的2019-07-07 版儲存體服務。

### <a name="version-59"></a>版本5。9

* 儲存體模擬器不會拒絕 Blob、佇列和表格服務端點上的2019-02-02 版儲存體服務。

### <a name="version-58"></a>版本5。8

* 儲存體模擬器不會拒絕 Blob、佇列和表格服務端點上的2018-11-09 版儲存體服務。

### <a name="version-57"></a>5.7 版

* 已修正啟用記錄時會造成當機的錯誤。

### <a name="version-56"></a>5.6 版

* 儲存體模擬器現在支援 Blob、佇列和資料表服務端點上的2018-03-28 版儲存體服務。

### <a name="version-55"></a>版本 5.5

* 儲存體模擬器現在支援 Blob、佇列和資料表服務端點上的2017-11-09 版儲存體服務。
* 已新增 Blob **Created** 屬性的支援，它會傳回 Blob 建立時間。

### <a name="version-54"></a>版本 5.4

* 若要改善安裝穩定性，模擬器不會嘗試在安裝時保留連接埠。 如果您想要保留埠，請使用 **init** 命令的 *-reserveports* 選項來指定它們。

### <a name="version-53"></a>版本 5.3

* 儲存體模擬器現在支援 Blob、佇列和資料表服務端點上的2017-07-29 版儲存體服務。

### <a name="version-52"></a>5.2 版

* 儲存體模擬器現在支援 Blob、佇列和資料表服務端點上的2017-04-17 版儲存體服務。
* 修正資料表屬性值未正確編碼的錯誤（bug）。

### <a name="version-51"></a>版本 5.1

* 修正了儲存體模擬器 `DataServiceVersion` 在服務不在某些回應中傳回標頭的錯誤（bug）。

### <a name="version-50"></a>版本 5.0

* 儲存體模擬器安裝程式不會再檢查現有的 MSSQL 和 .NET Framework 安裝。
* 儲存體模擬器安裝程式在安裝過程中不會再建立資料庫。 必要時仍會隨著啟動建立資料庫。
* 建立資料庫不再需要提高權限。
* 啟動不再需要保留連接埠。
* 將下列選項新增至 `init`：`-reserveports` (需要提高權限)、`-unreserveports` (需要提高權限)、`-skipcreate`。
* 系統匣圖示上的儲存體模擬器 UI 選項現在會啟動命令列介面。 已不再使用舊 GUI。
* 某些 DLL 已移除或重新命名。

### <a name="version-46"></a>版本 4.6

* 儲存體模擬器現在支援 Blob、佇列和資料表服務端點上的2016-05-31 版儲存體服務。

### <a name="version-45"></a>版本 4.5

* 修正當重新命名備份資料庫時，導致安裝和初始化失敗的 bug。

### <a name="version-44"></a>4.4 版

* 儲存體模擬器現在支援 Blob、佇列和資料表服務端點上的2015-12-11 版儲存體服務。
* 在處理大量 blob 時，儲存體模擬器的 blob 資料的垃圾收集現在更有效率。
* 修正了造成容器 ACL XML 的驗證方式與儲存體服務的運作方式稍有不同的錯誤。
* 修正了有時造成報告的日期時間上限和下限值時區不正確的錯誤。

### <a name="version-43"></a>版本 4.3

* 儲存體模擬器現在支援 Blob、佇列和資料表服務端點上的2015-07-08 版儲存體服務。

### <a name="version-42"></a>4.2 版

* 儲存體模擬器現在支援 Blob、佇列和資料表服務端點上的2015-04-05 版儲存體服務。

### <a name="version-41"></a>4.1 版

* 儲存體模擬器現在支援 Blob、佇列和資料表服務端點上的2015-02-21 版儲存體服務。 它不支援新的附加 blob 功能。
* 模擬器現在會針對不支援的儲存體服務版本傳回有意義的錯誤訊息。 我們建議使用最新版本的模擬器。 如果您收到 VersionNotSupportedByEmulator 錯誤 (HTTP 狀態碼 400-錯誤的要求) ，請下載最新版本的模擬器。
* 修正競爭情形造成資料表實體資料在並行合併作業期間會不正確的 bug。

### <a name="version-40"></a>4.0 版

* 儲存體模擬器可執行檔已重新命名為 *AzureStorageEmulator.exe* 。

### <a name="version-32"></a>3.2 版

* 儲存體模擬器現在支援 Blob、佇列和資料表服務端點上的2014-02-14 版儲存體服務。 儲存體模擬器目前不支援檔案服務端點。 如需有關 2014-02-14 版本的詳細資訊，請參閱 [為 Microsoft Azure 中的 Blob、佇列和表格服務進行版本設定](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) 。

### <a name="version-31"></a>3.1 版

* 儲存體模擬器現在支援讀取權限異地冗余儲存體 (RA-GRS) 。 `Get Blob Service Stats`、 `Get Queue Service Stats` 和 `Get Table Service Stats` api 支援次要帳戶，並且一律會根據基礎 SQL 資料庫，將 LastSyncTime response 元素的值傳回為目前時間。 若要使用儲存體模擬器以程式設計方式存取次要資料庫，請使用適用于 .NET 3.2 版或更新版本的儲存體用戶端程式庫。 如需詳細資訊，請參閱「Microsoft Azure Storage Client Library for .NET」。

### <a name="version-30"></a>3.0 版

* Azure 儲存體模擬器不再隨附于與計算模擬器相同的套件中。
* 儲存體模擬器圖形化使用者介面已淘汰。 它已由可編寫腳本的命令列介面取代。 如需有關命令列介面的詳細資訊，請參閱儲存體模擬器命令列工具參考。 3.0 版仍將提供圖形化介面，除非在系統匣圖示按一下滑鼠右鍵並選取 [顯示儲存體模擬器 UI] 的方式安裝計算模擬器，否則無法存取圖形化介面。
* 現在完全支援 2013-08-15 版的 Azure 儲存體服務。 (先前只有儲存體模擬器 2.2.1 版預覽才支援此版本)。

## <a name="next-steps"></a>後續步驟

* 評估跨平臺、以社區維護的開放原始碼儲存體模擬器 [Azurite](https://github.com/azure/azurite)。 
* [使用 .NET 的 Azure 儲存體範例](./storage-samples-dotnet.md)包含開發應用程式時您可以使用的數個程式碼範例的連結。
* 您可以使用 [Microsoft Azure 儲存體總管](https://storageexplorer.com) 來處理雲端儲存體帳戶和儲存體模擬器中的資源。

## <a name="see-also"></a>另請參閱

* [使用 Azurite、Azure Sdk 和 Azure 儲存體總管進行本機 Azure 儲存體開發](https://blog.jongallant.com/2020/04/local-azure-storage-development-with-azurite-azuresdks-storage-explorer/)
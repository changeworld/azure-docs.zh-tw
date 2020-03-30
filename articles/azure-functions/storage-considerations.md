---
title: Azure 函數的存儲注意事項
description: 瞭解 Azure 函數的存儲要求和加密存儲的資料。
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 3bacc93ad6c1851d9165e8efb7d27b427050e6f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276578"
---
# <a name="storage-considerations-for-azure-functions"></a>Azure 函數的存儲注意事項

創建函數應用實例時，Azure 函數需要 Azure 存儲帳戶。 您的功能應用可以使用以下存儲服務：


|存儲服務  | 函數使用  |
|---------|---------|
| [Azure Blob 儲存體](../storage/blobs/storage-blobs-introduction.md)     | 維護綁定狀態和功能鍵。  <br/>也由[持久函數中的任務中心](durable/durable-functions-task-hubs.md)使用。 |
| [Azure 檔案](../storage/files/storage-files-introduction.md)  | 用於在[消耗計畫中](functions-scale.md#consumption-plan)存儲和運行函數應用代碼的檔共用。 |
| [Azure 佇列存儲](../storage/queues/storage-queues-introduction.md)     | 由[持久函數中的任務中心](durable/durable-functions-task-hubs.md)使用。   |
| [Azure 資料表儲存體](../storage/tables/table-storage-overview.md)  |  由[持久函數中的任務中心](durable/durable-functions-task-hubs.md)使用。       |

> [!IMPORTANT]
> 當使用「使用情況主控方案」時，您的函式程式碼和繫結組態檔會儲存在主要儲存體帳戶中的 Azure 檔案儲存體中。 當您刪除主要儲存體帳戶時，會刪除此內容且無法復原。

## <a name="storage-account-requirements"></a>儲存體帳戶的需求

創建函數應用時，必須創建或連結到支援 Blob、佇列和表存儲的通用 Azure 存儲帳戶。 這是因為函數依賴于 Azure 存儲來執行諸如管理觸發器和日誌記錄函數執行等操作。 某些存儲帳戶不支援佇列和表。 這些帳戶包括僅 Blob 存儲帳戶、Azure 高級存儲和具有 ZRS 複製的通用存儲帳戶。 創建函數應用時，這些不支援的帳戶將從"存儲帳戶"邊欄選項卡中篩選出來。

要瞭解有關存儲帳戶類型詳細資訊，請參閱[介紹 Azure 存儲服務](../storage/common/storage-introduction.md#azure-storage-services)。 

雖然您可以將現有存儲帳戶用於函數應用，但必須確保它滿足這些要求。 作為功能應用創建流的一部分創建的存儲帳戶保證滿足這些存儲帳戶要求。  

## <a name="storage-account-guidance"></a>存儲帳戶指南

每個函式應用程式都需要有儲存體帳戶才能運作。 如果該帳戶已刪除，則函數應用將無法運行。 要排除與存儲相關的問題，請參閱[如何解決與存儲相關的問題](functions-recover-storage-account.md)。 以下其他注意事項適用于功能應用使用的存儲帳戶。

### <a name="storage-account-connection-setting"></a>存儲帳戶連接設置

存儲帳戶連接在[AzureWeb作業存儲應用程式設定](./functions-app-settings.md#azurewebjobsstorage)中維護。 

重新生成存儲金鑰時，必須更新存儲帳戶連接字串。 [在此處閱讀有關存儲金鑰管理的更多內容](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)。

### <a name="shared-storage-accounts"></a>共用存儲帳戶

多個功能應用可以共用同一個存儲帳戶，沒有任何問題。 例如，在視覺化工作室中，可以使用 Azure 存儲模擬器開發多個應用。 在這種情況下，模擬程式的作用類似于單個存儲帳戶。 功能應用使用的相同存儲帳戶也可用於存儲應用程式資料。 但是，這種方法在生產環境中並不總是一個好主意。

### <a name="optimize-storage-performance"></a>將儲存體效能最佳化

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

## <a name="storage-data-encryption"></a>存儲資料加密

Azure 存儲在靜態存儲帳戶中加密所有資料。 有關詳細資訊，請參閱[靜態資料的 Azure 存儲加密](../storage/common/storage-service-encryption.md)。

預設情況下，資料使用 Microsoft 管理的金鑰進行加密。 為了對加密金鑰進行其他控制，可以提供客戶管理的金鑰，用於加密 Blob 和檔資料。 這些金鑰必須存在於 Azure 金鑰保存庫中，以便函數能夠訪問存儲帳戶。 要瞭解更多資訊，請參閱[使用 Azure 門戶使用 Azure 金鑰保存庫配置客戶管理金鑰](../storage/common/storage-encryption-keys-portal.md)。  

## <a name="mount-file-shares-linux"></a>裝載檔共用（Linux）

您可以將現有的 Azure 檔共用裝載到 Linux 函數應用。 通過將共用安裝到 Linux 函數應用，您可以利用函數中的現有機器學習模型或其他資料。 可以使用 命令[`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add)將現有共用裝載到 Linux 函數應用。 

在此命令中，`share-name`是現有 Azure 檔共用的名稱，`custom-id`可以是裝載到函數應用時唯一定義共用的任何字串。 此外，`mount-path`在函數應用中訪問共用的路徑。 `mount-path`必須採用格式`/dir-name`，並且不能從`/home`開始。

有關完整示例，請參閱創建 Python[函數應用中的腳本並裝載 Azure 檔共用](scripts/functions-cli-mount-files-storage-linux.md)。 

目前，僅支援`storage-type``AzureFiles`的 一個。 您只能將五個共用裝載到給定的函數應用。 安裝檔共用可能會將冷啟動時間至少增加 200-300 毫秒，或者當存儲帳戶位於其他區域時甚至更多。

裝載的共用在指定的功能代碼處`mount-path`可用。 例如，何時`mount-path`是`/path/to/mount`，可以通過檔案系統 API 訪問目標目錄，如以下 Python 示例所示：

```python
import os
...

files_in_share = os.listdir("/path/to/mount")
```

## <a name="next-steps"></a>後續步驟

詳細瞭解 Azure 函數託管選項。

> [!div class="nextstepaction"]
> [Azure Functions 的規模調整和主控](functions-scale.md)



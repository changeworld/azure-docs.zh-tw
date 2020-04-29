---
title: Azure Functions 的儲存體考慮
description: 瞭解 Azure Functions 的儲存體需求，以及如何加密儲存的資料。
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 48ff2dedd997cccb76b13acdadc895504f656ea3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80984158"
---
# <a name="storage-considerations-for-azure-functions"></a>Azure Functions 的儲存體考慮

當您建立函數應用程式實例時，Azure Functions 需要 Azure 儲存體帳戶。 您的函數應用程式可能會使用下列儲存體服務：


|儲存體服務  | 函數使用方式  |
|---------|---------|
| [Azure Blob 儲存體](../storage/blobs/storage-blobs-introduction.md)     | 維護系結狀態和功能金鑰。  <br/>[Durable Functions 中](durable/durable-functions-task-hubs.md)的工作中樞也會使用此功能。 |
| [Azure 檔案](../storage/files/storage-files-introduction.md)  | 用來在取用[方案](functions-scale.md#consumption-plan)中儲存和執行函式應用程式程式碼的檔案共用。 |
| [Azure 佇列儲存體](../storage/queues/storage-queues-introduction.md)     | 由[Durable Functions 中](durable/durable-functions-task-hubs.md)的工作中樞使用。   |
| [Azure 表格儲存體](../storage/tables/table-storage-overview.md)  |  由[Durable Functions 中](durable/durable-functions-task-hubs.md)的工作中樞使用。       |

> [!IMPORTANT]
> 當使用「使用情況主控方案」時，您的函式程式碼和繫結組態檔會儲存在主要儲存體帳戶中的 Azure 檔案儲存體中。 當您刪除主要儲存體帳戶時，會刪除此內容且無法復原。

## <a name="storage-account-requirements"></a>儲存體帳戶的需求

建立函數應用程式時，您必須建立或連結至支援 Blob、佇列和資料表儲存體的一般用途 Azure 儲存體帳戶。 這是因為函式依賴 Azure 儲存體進行作業，例如管理觸發程式和記錄函數執行。 有些儲存體帳戶不支援佇列和資料表。 這些帳戶包括僅限 blob 的儲存體帳戶、Azure 進階儲存體，以及具有 ZRS 複寫的一般用途儲存體帳戶。 建立函數應用程式時，會從 [儲存體帳戶] 分頁中篩選出這些不受支援的帳戶。

若要深入瞭解儲存體帳戶類型，請參閱[Azure 儲存體服務簡介](../storage/common/storage-introduction.md#core-storage-services)。 

雖然您可以在函式應用程式中使用現有的儲存體帳戶，但您必須確定它符合這些需求。 在函式應用程式建立流程中建立的儲存體帳戶，保證會符合這些儲存體帳戶的需求。  

## <a name="storage-account-guidance"></a>儲存體帳戶指引

每個函式應用程式都需要有儲存體帳戶才能運作。 如果該帳戶遭到刪除，您的函式應用程式將不會執行。 若要針對儲存體相關的問題進行疑難排解，請參閱[如何針對儲存體相關的問題進行疑難排解](functions-recover-storage-account.md)。 下列其他考慮適用于函數應用程式所使用的儲存體帳戶。

### <a name="storage-account-connection-setting"></a>儲存體帳戶連接設定

儲存體帳戶連線會保留在[AzureWebJobsStorage 應用程式設定](./functions-app-settings.md#azurewebjobsstorage)中。 

當您重新產生儲存體金鑰時，必須更新儲存體帳戶連接字串。 [在這裡閱讀更多關於儲存體金鑰管理的資訊](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)。

### <a name="shared-storage-accounts"></a>共用的儲存體帳戶

有可能會有多個函式應用程式共用相同的儲存體帳戶，而不會有任何問題。 例如，在 Visual Studio 您可以使用 Azure 儲存體模擬器開發多個應用程式。 在此情況下，模擬器的作用就像單一儲存體帳戶。 您的函數應用程式所使用的相同儲存體帳戶也可以用來儲存您的應用程式資料。 不過，在生產環境中，這種方法不一定是好主意。

### <a name="optimize-storage-performance"></a>將儲存體效能最佳化

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

## <a name="storage-data-encryption"></a>儲存體資料加密

Azure 儲存體會加密待用儲存體帳戶中的所有資料。 如需詳細資訊，請參閱待用[資料的加密 Azure 儲存體](../storage/common/storage-service-encryption.md)。

根據預設，資料會使用 Microsoft 管理的金鑰進行加密。 若要進一步控制加密金鑰，您可以提供客戶管理的金鑰，以用於加密 blob 和檔案資料。 這些金鑰必須存在於 Azure Key Vault 中，函式才能存取儲存體帳戶。 若要深入瞭解，請參閱[使用 Azure 入口網站設定 Azure Key Vault 的客戶管理金鑰](../storage/common/storage-encryption-keys-portal.md)。  

## <a name="mount-file-shares-linux"></a>掛接檔案共用（Linux）

您可以將現有的 Azure 檔案儲存體共用掛接至 Linux 函數應用程式。 藉由將共用裝載至您的 Linux 函式應用程式，您可以利用現有的機器學習模型或函式中的其他資料。 您可以使用[`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add)命令，將現有的共用掛接至您的 Linux 函式應用程式。 

在此命令中`share-name` ，是現有 Azure 檔案儲存體共用的名稱，而且`custom-id`可以是任何可在裝載至函式應用程式時，唯一定義共用的字串。 此外， `mount-path`也是函式應用程式中用來存取共用的路徑。 `mount-path`的格式`/dir-name`必須是，而且開頭不能是`/home`。

如需完整範例，請參閱[建立 Python 函式應用程式和掛接 Azure 檔案儲存體共用](scripts/functions-cli-mount-files-storage-linux.md)中的腳本。 

目前僅支援`storage-type`的`AzureFiles` 。 您只能將五個共用掛接至指定的函式應用程式。 若儲存體帳戶位於不同的區域，掛接檔案共用可能會使冷啟動時間增加至少 200-300 毫秒，或甚至更多。

裝載的共用可供您的函式程式碼`mount-path`在指定的處使用。 例如，當是`mount-path` `/path/to/mount`時，您可以透過檔案系統 api 存取目標目錄，如下列 Python 範例所示：

```python
import os
...

files_in_share = os.listdir("/path/to/mount")
```

## <a name="next-steps"></a>後續步驟

深入瞭解 Azure Functions 裝載選項。

> [!div class="nextstepaction"]
> [Azure Functions 的規模調整和主控](functions-scale.md)



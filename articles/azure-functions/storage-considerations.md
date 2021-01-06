---
title: Azure Functions 的儲存考量事項
description: 了解 Azure Functions 的儲存體需求，以及加密儲存資料的相關資訊。
ms.topic: conceptual
ms.date: 07/27/2020
ms.openlocfilehash: 66bfded384be47224e86ee8e0a2999fe3d4ed5d9
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936153"
---
# <a name="storage-considerations-for-azure-functions"></a>Azure Functions 的儲存考量事項

當您建立函數應用程式執行個體時，Azure Functions 需要 Azure 儲存體帳戶。 您的函數應用程式可使用下列儲存體服務：


|儲存體服務  | 函式使用方式  |
|---------|---------|
| [Azure Blob 儲存體](../storage/blobs/storage-blobs-introduction.md)     | 維護繫結狀態和函式金鑰。  <br/>[Durable Functions 的工作中樞](durable/durable-functions-task-hubs.md)也會使用。 |
| [Azure 檔案](../storage/files/storage-files-introduction.md)  | 用來在取用 [方案](consumption-plan.md) 和 [Premium 方案](functions-premium-plan.md)中儲存和執行函數應用程式程式碼的檔案共用。 |
| [Azure 佇列儲存體](../storage/queues/storage-queues-introduction.md)     | 由 [Durable Functions 的工作中樞](durable/durable-functions-task-hubs.md)使用。   |
| [Azure 資料表儲存體](../storage/tables/table-storage-overview.md)  |  由 [Durable Functions 的工作中樞](durable/durable-functions-task-hubs.md)使用。       |

> [!IMPORTANT]
> 使用耗用量/高階主控方案時，您的函式程式碼和系結設定檔會儲存在主要儲存體帳戶的 Azure 檔案儲存體中。 當您刪除主要儲存體帳戶時，會刪除此內容且無法復原。

## <a name="storage-account-requirements"></a>儲存體帳戶的需求

建立函數應用程式時，您必須建立或連結支援 Blob、「佇列」及「表格」儲存體的一般用途「Azure 儲存體」帳戶。 這是因為 Functions 依賴「Azure 儲存體」來進行作業，例如管理觸發程序和記錄函數執行。 某些儲存體帳戶不支援佇列和資料表。 這些帳戶包括僅限 Blob 的儲存體帳戶、Azure 進階儲存體和搭配 ZRS 複寫的一般用途儲存體帳戶。

若要深入了解儲存體帳戶類型，請參閱 [Azure 儲存體服務簡介](../storage/common/storage-introduction.md#core-storage-services)。 

雖然您可以在函數應用程式中使用現有的儲存體帳戶，但您必須確定它符合這些需求。 在 Azure 入口網站中建立為函數應用程式建立流程一部分的儲存體帳戶，保證符合這些儲存體帳戶需求。 在入口網站中，當您在建立函數應用程式時選擇現有的儲存體帳戶時，不支援的帳戶會被篩選掉。 在此流程中，您只允許在與您建立的函式應用程式相同的區域中，選擇現有的儲存體帳戶。 若要深入瞭解，請參閱 [儲存體帳戶位置](#storage-account-location)。

<!-- JH: Does using a Premium Storage account improve perf? -->

## <a name="storage-account-guidance"></a>儲存體帳戶指引

每個函式應用程式都需要有儲存體帳戶才能運作。 如果該帳戶已刪除，您的函數應用程式就不會運作。 若要為儲存體相關問題疑難排解，請參閱[如何為儲存體相關問題疑難排解](functions-recover-storage-account.md)。 下列其他考慮適用于函數應用程式所使用的儲存體帳戶。

### <a name="storage-account-location"></a>儲存體帳戶位置

為了達到最佳效能，您的函數應用程式應該使用相同區域中的儲存體帳戶，以減少延遲。 Azure 入口網站會強制執行這種最佳做法。 如果基於某些原因，您需要在與函式應用程式不同的區域中使用儲存體帳戶，您必須在入口網站外部建立函數應用程式。 

### <a name="storage-account-connection-setting"></a>儲存體帳戶連線設定

儲存體帳戶連線會在 [AzureWebJobsStorage application setting](./functions-app-settings.md#azurewebjobsstorage) 中維護。 

當您重新產生儲存體金鑰時，必須更新儲存體帳戶連接字串。 [在此深入了解儲存體金鑰管理](../storage/common/storage-account-create.md)。

### <a name="shared-storage-accounts"></a>共用的儲存體帳戶

可能有多個函數應用程式共用相同的儲存體帳戶，而沒有任何問題。 例如，在 Visual Studio 中，您可以使用 Azure 儲存體模擬器開發多個應用程式。 在此情況下，模擬器的作用就像單一儲存體帳戶。 您的函數應用程式所使用的相同儲存體帳戶也可用來儲存您的應用程式資料。 不過，在實際執行環境中，這個方法不一定理想。

### <a name="optimize-storage-performance"></a>最佳化儲存體效能

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

## <a name="storage-data-encryption"></a>儲存體資料加密

[!INCLUDE [functions-storage-encryption](../../includes/functions-storage-encryption.md)]

### <a name="in-region-data-residency"></a>區域內資料落地

當所有客戶資料都必須保留在單一區域內時，與函數應用程式相關聯的儲存體帳戶必須是具有 [區域內冗余](../storage/common/storage-redundancy.md)的儲存體帳戶。 區域內多餘的儲存體帳戶也必須搭配 [Azure Durable Functions](./durable/durable-functions-perf-and-scale.md#storage-account-selection)使用。

當裝載于內部負載平衡的 App Service 環境 (ASE) 時，其他平臺管理的客戶資料只會儲存在區域內。 若要深入瞭解，請參閱 [ASE 區域冗余](../app-service/environment/zone-redundancy.md#in-region-data-residency)。

## <a name="mount-file-shares"></a>掛接檔案共用

_這項功能僅適用于在 Linux 上執行時的最新狀態。_ 

您可以將現有的 Azure 檔案儲存體共用掛接至 Linux 函數應用程式。 藉由將共用掛接至 Linux 函式應用程式，您可以利用現有的機器學習模型或函式中的其他資料。 您可以使用 [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) 命令，將現有的共用掛接到您的 Linux 函數應用程式。 

在此命令中，`share-name` 是現有 Azure 檔案儲存體共用的名稱，而且 `custom-id` 可以是掛接到函數應用程式時，唯一定義共用的任何字串。 此外，`mount-path` 是在函數應用程式中存取共用的路徑。 `mount-path` 的格式必須是 `/dir-name`，而且開頭不可為 `/home`。

如需完整範例，請參閱[建立 Python 函數應用程式並掛接 Azure 檔案儲存體共用](scripts/functions-cli-mount-files-storage-linux.md)中的指令碼。 

目前，僅支援 `AzureFiles` 的 `storage-type`。 您只能將五個共用掛接至指定的函數應用程式。 當儲存體帳戶位於不同區域時，掛接檔案共用可能會讓冷啟動時間至少增加 200-300 毫秒，甚至更多。

掛接的共用可供 `mount-path` 指定的函式程式碼使用。 例如，當 `mount-path` 為 `/path/to/mount` 時，您可以根據檔案系統 API 存取目標目錄，如以下 Python 範例所示：

```python
import os
...

files_in_share = os.listdir("/path/to/mount")
```

## <a name="next-steps"></a>後續步驟

深入了解 Azure Functions 裝載選項。

> [!div class="nextstepaction"]
> [Azure Functions 的級別和裝載](functions-scale.md)

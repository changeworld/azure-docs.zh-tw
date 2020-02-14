---
title: Azure Functions 的儲存體考慮
description: 瞭解 Azure Functions 的儲存體需求，以及如何加密儲存的資料。
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: f094996ca44ec36d46330e54eac56b28794ef22e
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190292"
---
# <a name="storage-considerations-for-azure-functions"></a>Azure Functions 的儲存體考慮

當您建立函數應用程式實例時，Azure Functions 需要 Azure 儲存體帳戶。 您的函數應用程式可能會使用下列儲存體服務：


|儲存體服務  | 函數使用方式  |
|---------|---------|
| [Azure Blob 儲存體](../storage/blobs/storage-blobs-introduction.md)     | 維護系結狀態和功能金鑰。  <br/>[Durable Functions 中](durable/durable-functions-task-hubs.md)的工作中樞也會使用此功能。 |
| [Azure 檔案](../storage/files/storage-files-introduction.md)  | 用來在取用[方案](functions-scale.md#consumption-plan)中儲存和執行函式應用程式程式碼的檔案共用。 |
| [Azure 佇列儲存體](../storage/queues/storage-queues-introduction.md)     | 由[Durable Functions 中](durable/durable-functions-task-hubs.md)的工作中樞使用。   |
| [Azure 資料表儲存體](../storage/tables/table-storage-overview.md)  |  由[Durable Functions 中](durable/durable-functions-task-hubs.md)的工作中樞使用。       |

> [!IMPORTANT]
> 當使用「使用情況主控方案」時，您的函式程式碼和繫結組態檔會儲存在主要儲存體帳戶中的 Azure 檔案儲存體中。 當您刪除主要儲存體帳戶時，會刪除此內容且無法復原。

## <a name="storage-account-requirements"></a>儲存體帳戶的需求

建立函數應用程式時，您必須建立或連結至支援 Blob、佇列和資料表儲存體的一般用途 Azure 儲存體帳戶。 這是因為函式依賴 Azure 儲存體進行作業，例如管理觸發程式和記錄函數執行。 有些儲存體帳戶不支援佇列和資料表。 這些帳戶包括僅限 blob 的儲存體帳戶、Azure 進階儲存體，以及具有 ZRS 複寫的一般用途儲存體帳戶。 建立函數應用程式時，會從 [儲存體帳戶] 分頁中篩選出這些不受支援的帳戶。

若要深入了解儲存體帳戶類型，請參閱 [Azure 儲存體服務簡介](../storage/common/storage-introduction.md#azure-storage-services)。 

雖然您可以在函式應用程式中使用現有的儲存體帳戶，但您必須確定它符合這些需求。 在函式應用程式建立流程中建立的儲存體帳戶，保證會符合這些儲存體帳戶的需求。  

## <a name="storage-account-guidance"></a>儲存體帳戶指引

每個函式應用程式都需要有儲存體帳戶才能運作。 如果該帳戶遭到刪除，您的函式應用程式將不會執行。 若要針對儲存體相關的問題進行疑難排解，請參閱[如何針對儲存體相關的問題進行疑難排解](functions-recover-storage-account.md)。 下列其他考慮適用于函數應用程式所使用的儲存體帳戶。

### <a name="storage-account-connection-setting"></a>儲存體帳戶連接設定

儲存體帳戶連線會保留在[AzureWebJobsStorage 應用程式設定](./functions-app-settings.md#azurewebjobsstorage)中。 

當您重新產生儲存體金鑰時，必須更新儲存體帳戶連接字串。 [在這裡閱讀更多關於儲存體金鑰管理的資訊](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)。

### <a name="shared-storage-accounts"></a>共用的儲存體帳戶

有可能會有多個函式應用程式共用相同的儲存體帳戶，而不會有任何問題。 例如，在 Visual Studio 您可以使用 Azure 儲存體模擬器開發多個應用程式。 在此情況下，模擬器的作用就像單一儲存體帳戶。 您的函數應用程式所使用的相同儲存體帳戶也可以用來儲存您的應用程式資料。 不過，在生產環境中，這種方法不一定是好主意。

### <a name="optimize-storage-performance"></a>將儲存體效能優化

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

## <a name="storage-data-encryption"></a>儲存體資料加密

Azure 儲存體會加密待用儲存體帳戶中的所有資料。 如需詳細資訊，請參閱待用[資料的加密 Azure 儲存體](../storage/common/storage-service-encryption.md)。

根據預設，資料會使用 Microsoft 管理的金鑰進行加密。 若要進一步控制加密金鑰，您可以提供客戶管理的金鑰，以用於加密 blob 和檔案資料。 這些金鑰必須存在於 Azure Key Vault 中，函式才能存取儲存體帳戶。 若要深入瞭解，請參閱[使用 Azure 入口網站設定 Azure Key Vault 的客戶管理金鑰](../storage/common/storage-encryption-keys-portal.md)。  

## <a name="next-steps"></a>後續步驟

深入瞭解 Azure Functions 裝載選項。

> [!div class="nextstepaction"]
> [Azure Functions 的級別和裝載](functions-scale.md)



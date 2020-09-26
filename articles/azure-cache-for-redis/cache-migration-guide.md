---
title: 遷移至 Azure Cache for Redis
description: 瞭解如何將您現有的快取遷移至 Azure Cache for Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: yegu
ms.openlocfilehash: 4b196818ade1e703e24ed1ced6ebac1b44d0b083
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91372063"
---
# <a name="migrate-to-azure-cache-for-redis"></a>遷移至 Azure Cache for Redis
本文說明一些方法，可將內部部署或其他雲端服務中執行的現有 Redis 快取遷移至 Azure Cache for Redis。

## <a name="migration-scenarios"></a>移轉案例
開放原始碼 Redis 可以在許多計算環境中執行。 常見範例包括：

- 在私人資料中心內執行**的內部部署**Redis 快取。
- 以**雲端為基礎的 vm** -Redis 在 Azure vm 上執行的快取、AWS EC2 等等。
- **裝載服務** 管理的 Redis 服務，例如 AWS ElastiCache。
- **不同區域** -位於另一個 Azure 區域的 Redis 快取。

如果您有這類快取，就可以將它移至 Azure Cache for Redis，使中斷或停機時間降到一。

## <a name="migration-options"></a>移轉選項

有不同的方式可讓您從一個快取切換到另一個快取。 根據快取的位置，以及應用程式與應用程式互動的方式，其中一個方法會比其他方法更有用。 以下詳述一些常用的遷移策略。

   | 選項       | 優點 | 缺點 |
   | ------------ | ---------- | ------------- |
   | 建立新的快取 | 最簡單的方式是執行。 | 需要將資料重新擴展至新的快取，這可能無法用於許多應用程式。 |
   | 透過 RDB 檔案匯出和匯入資料 | 通常與任何 Redis 快取相容。 | 如果在產生 RDB 檔案之後，將資料寫入至現有的快取，可能會遺失部分資料。 | 
   | 雙寫入兩個快取的資料 | 沒有資料遺失或停機時間。 現有快取的不中斷作業。 更輕鬆地測試新的快取。 | 需要兩個快取一段很長的時間。 | 
   | 以程式設計方式遷移資料 | 完整控制資料的移動方式。 | 需要自訂程式碼。 | 

### <a name="create-a-new-azure-cache-for-redis"></a>建立新的 Azure Cache for Redis

此方法在技術上並不是遷移。 如果遺失資料並不重要，移至 Azure Cache for Redis 的最簡單方式就是建立快取實例，並將您的應用程式連接到該實例。 例如，如果您使用 Redis 做為資料庫記錄的查閱快取，就可以輕鬆地從頭重建快取。

執行此選項的一般步驟如下：

1. 建立新的 Azure Cache for Redis 實例。

2. 更新您的應用程式以使用新的實例。

3. 刪除舊的 Redis 實例。

### <a name="export-data-to-an-rdb-file-and-import-it-into-azure-cache-for-redis"></a>將資料匯出至 RDB 檔案，並將它匯入 Azure Cache for Redis

開放原始碼 Redis 會定義標準機制，以取得快取記憶體內部資料集的快照集，並將其儲存至檔案。 另一個 Redis 快取可以讀取此檔案（稱為 RDB）。 [Azure Cache for Redis](cache-overview.md#service-tiers) 進階層支援透過 RDB 檔案將資料匯入至快取實例。 您可以使用 RDB 檔案，將資料從現有的快取傳送至 Azure Cache for Redis。

> [!IMPORTANT]
> RDB 檔案格式可能會在 Redis 版本之間變更，而且可能不會維持回溯相容性。 您要匯出之快取的 Redis 版本應等於或小於 Azure Cache for Redis 所提供的版本。
>

執行此選項的一般步驟如下：

1. 在進階層中建立新的 Azure Cache for Redis 實例，其大小與 (或大於現有快取) 相同。

2. 儲存現有 Redis 快取的快照。 您可以 [設定 Redis 定期儲存快照](https://redis.io/topics/persistence) 集，或使用 [save](https://redis.io/commands/save) 或 [BGSAVE](https://redis.io/commands/bgsave) 命令手動執行進程。 RDB 檔案預設會命名為 "dump '，而且會位於 *redis* 設定檔中指定的路徑。

    > [!NOTE]
    > 如果您要在 Azure Cache for Redis 中遷移資料，請參閱 [這些指示以瞭解如何匯出 RDB](cache-how-to-import-export-data.md) 檔案，或改用 [PowerShell export Cmdlet](https://docs.microsoft.com/powershell/module/azurerm.rediscache/export-azurermrediscache?view=azurermps-6.13.0&viewFallbackFrom=azurermps-6.4.0) 。
    >

3. 將 RDB 檔案複製到新快取所在區域中的 Azure 儲存體帳戶。 您可以針對這項工作使用 AzCopy。

4. 使用這些匯 [入指示](cache-how-to-import-export-data.md) 或 [PowerShell 匯入 CMDLET](https://docs.microsoft.com/powershell/module/azurerm.rediscache/import-azurermrediscache?view=azurermps-6.13.0&viewFallbackFrom=azurermps-6.4.0)，將 RDB 檔案匯入至新的快取。

5. 更新您的應用程式以使用新的快取實例。

### <a name="write-to-two-redis-caches-simultaneously-during-migration-period"></a>在遷移期間同時寫入兩個 Redis 快取

您可以使用您的應用程式將資料寫入現有的快取和您要設定的新快取，而不是直接在快取之間移動資料。 應用程式一開始仍會從現有的快取讀取資料。 當新的快取有必要的資料時，您會將應用程式切換到該快取，並淘汰舊的。 例如，假設您使用 Redis 做為會話存放區，而應用程式會話有效期限為七天。 在每週寫入兩個快取之後，您會確定新的快取包含所有未過期的會話資訊。 您可以安全地依賴該點，而不需要擔心資料遺失。

執行此選項的一般步驟如下：

1. 在進階層中建立新的 Azure Cache for Redis 實例，其大小與 (或大於現有快取) 相同。

2. 修改應用程式程式碼，以寫入新的和原始的實例。

3. 繼續從原始實例讀取資料，直到新的實例充分填入資料為止。

4. 更新應用程式程式碼，僅從新的實例讀取和寫入。

5. 刪除原始的實例。

### <a name="migrate-programmatically"></a>以程式設計方式遷移

您可以建立自訂的遷移程式，方法是以程式設計方式從現有的快取讀取資料，並將它們寫入 Azure Cache for Redis。 您可以使用此 [開放原始碼工具](https://github.com/deepakverma/redis-copy) ，將資料從一個 Azure Cache for Redis 實例複製到另一個實例。 這項工具適用于在不同 Azure 快取區域的快取實例之間移動資料。 您也可以使用 [已編譯的版本](https://github.com/deepakverma/redis-copy/releases/download/alpha/Release.zip) 。 您也可能會發現原始程式碼是撰寫您自己的遷移工具的實用指南。

> [!NOTE]
> Microsoft 未正式支援這項工具。 
>

執行此選項的一般步驟如下：

1. 在現有快取所在的區域中建立 VM。 如果您的資料集很大，請選擇功能相當強大的 VM 來縮短複製時間。

2. 建立新的 Azure Cache for Redis 實例。

3. 清除新快取中的資料，以確保其為空白。 這是必要步驟，因為複製工具本身不會覆寫目標快取中的任何現有金鑰。

    > [!IMPORTANT]
    > 請務必不要從來源快取排清。
    >

4. 使用上述的開放原始碼工具之類的應用程式，自動將來源快取的資料複製到目標。 請記住，複製程式可能需要一段時間才能完成，視您的資料集大小而定。

## <a name="next-steps"></a>後續步驟
深入瞭解 Azure Cache for Redis 功能。

* [Azure Cache for Redis 服務層級](cache-overview.md#service-tiers)
* [匯入資料](cache-how-to-import-export-data.md#import)

---
title: 適用於 MySQL 的 Azure 資料庫中的主要版本升級-單一伺服器
description: 本文說明如何升級適用於 MySQL 的 Azure 資料庫單一伺服器的主要版本
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 1/13/2021
ms.openlocfilehash: b62f4ebc61ac27478788d8b2bae5e4145f87ac8b
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98630195"
---
# <a name="major-version-upgrade-in-azure-database-for-mysql-single-server"></a>適用於 MySQL 的 Azure 資料庫單一伺服器的主要版本升級

> [!IMPORTANT]
> 適用于 MySQL 的 Azure 資料庫單一伺服器的主要版本升級處於公開預覽狀態。

本文說明如何在適用於 MySQL 的 Azure 資料庫單一伺服器中就地升級 MySQL 主要版本。

這項功能可讓客戶在沒有任何資料移動或需要任何應用程式連接字串變更的情況下，透過按一下按鈕來執行 MySQL 5.6 伺服器至 MySQL 5.7 的就地升級。

> [!Note]
> * 主要版本升級僅適用于從 MySQL 5.6 升級至 MySQL 5.7 的主要版本<br>
> * 複本伺服器上目前不支援主要版本升級。
> * 在整個升級作業中，伺服器將無法使用。 因此建議您在規劃的維護期間執行升級。

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57-using-azure-portal"></a>使用 Azure 入口網站執行從 MySQL 5.6 升級至 MySQL 5.7 的主要版本

遵循下列步驟，使用 Azure 入口網站為您的 MySQL 5.6 伺服器的 Azure 資料庫執行主要版本升級

> [!IMPORTANT]
> 建議您先在伺服器的還原複本上執行升級，而不是直接升級生產環境。 請參閱 [如何執行時間點還原](howto-restore-server-portal.md#point-in-time-restore)。

1. 在 [ [Azure 入口網站](https://portal.azure.com/)中，選取您現有的適用於 MySQL 的 Azure 資料庫5.6 伺服器。

2. 在 [ **總覽** ] 頁面中，按一下工具列中的 [ **升級** ] 按鈕。

3. 在 [ **升級** ] 區段中，選取 **[確定]** ，將適用于 MySQL 的 Azure 資料庫5.6 伺服器升級至5.7 伺服器。

   :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="適用於 MySQL 的 Azure 資料庫-總覽-升級":::

4. 通知將會確認升級是否成功。


## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57-using-azure-cli"></a>使用 Azure CLI 執行從 MySQL 5.6 升級至 MySQL 5.7 的主要版本

遵循下列步驟，使用 Azure CLI 為您的 MySQL 5.6 伺服器的 Azure 資料庫執行主要版本升級

> [!IMPORTANT]
> 建議您先在伺服器的還原複本上執行升級，而不是直接升級生產環境。 請參閱 [如何執行時間點還原](howto-restore-server-cli.md#server-point-in-time-restore)。

1. 安裝 [適用于 Windows 的 Azure CLI](/cli/azure/install-azure-cli) ，或在 [Azure Cloud Shell](../cloud-shell/overview.md) 中使用 Azure CLI 來執行升級命令。 
 
   這項升級需要 Azure CLI 的版本2.16.0 或更新版本。 如果您是使用 Azure Cloud Shell，就已安裝最新版本。 執行 az version 以尋找已安裝的版本和相依程式庫。 若要升級至最新版本，請執行 az upgrade。

2. 登入之後，請執行 [az mysql server 升級](https://docs.microsoft.com/cli/azure/mysql/server?view=azure-cli-latest#az_mysql_server_upgrade&preserve-view=true) 命令：
    
   ```azurecli
   az mysql server upgrade --name testsvr --resource-group testgroup --subscription MySubscription --target-server-version 5.7"
   ```
   
   命令提示字元會顯示「-正在執行」訊息。 當此訊息不再顯示之後，版本升級就會完成。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="when-will-this-upgrade-feature-be-ga-as-we-have-mysql-v56-in-our-production-environment-that-we-need-to-upgrade"></a>這項升級功能何時會正式推出，因為我們在生產環境中有需要升級的 MySQL 5.6？

這項功能的 GA 是在 MySQL 5.6 淘汰之前規劃的。 不過，此功能已備妥生產環境並受到 Azure 的完整支援，因此您應該在您的環境中自信地執行此功能。 建議的最佳作法是，強烈建議您先在伺服器的還原複本上執行並測試，以便您可以在升級期間評估停機時間，以及執行應用程式相容性測試，然後再于生產環境中執行。 如需詳細資訊，請參閱 [如何執行時間點還原](howto-restore-server-portal.md#point-in-time-restore) ，以建立伺服器的時間點複本。 

### <a name="will-this-cause-downtime-of-the-server-and-if-so-how-long"></a>這會導致伺服器的停機時間（如果有的話）？

是的，在升級過程中，伺服器將無法使用，因此建議您在規劃的維護期間執行此操作。 預估的停機時間取決於資料庫大小、布建的 (IOPs 布建) ，以及資料庫上的資料表數目。 升級時間與伺服器上的資料表數目成正比。基本 SKU 伺服器的升級預期需要較長的時間，因為它是在標準儲存體平臺上。 若要預估伺服器環境的停機時間，建議您先在伺服器的還原複本上執行升級。  

### <a name="it-is-noted-that-it-is-not-supported-on-replica-server-yet-what-does-that-mean-concrete"></a>但請注意，複本伺服器上並不支援此功能。 這是什麼意思？

目前，複本伺服器不支援主要版本升級，這表示您不應針對 (來源或複本伺服器) 的複寫所涉及的伺服器執行它。 如果您想要在新增升級功能的複本支援之前測試複寫所涉及的伺服器升級，建議您執行下列步驟：

1. 在規劃的維護期間，請在捕獲其名稱和所有設定資訊 (防火牆設定、伺服器參數設定（如果不同于來源伺服器) ）後 [停止複寫並刪除複本伺服器](howto-read-replicas-portal.md) 。
2. 執行來源伺服器的升級。
3. 使用步驟1中所捕獲的相同名稱和設定來布建新的讀取複本伺服器。 當來源伺服器升級為5.7 版之後，新的複本伺服器會自動在5.7 版上。

### <a name="what-will-happen-if-we-do-not-choose-to-upgrade-our-mysql-v56-server-before-february-5-2021"></a>如果我們在2021年2月5日之前未選擇升級 MySQL 5.6 伺服器，會發生什麼事？

您仍然可以像之前一樣繼續執行 MySQL 5.6 伺服器。 Azure **永遠不會** 在您的伺服器上執行強制升級。 不過， [適用於 MySQL 的 Azure 資料庫版本控制原則](concepts-version-policy.md) 中記載的限制將會套用。

## <a name="next-steps"></a>下一步

瞭解 [適用於 MySQL 的 Azure 資料庫版本控制原則](concepts-version-policy.md)。

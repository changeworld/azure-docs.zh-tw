---
title: 如何管理 Azure Cache for Redis
description: 了解如何執行管理工作，例如重新啟動 Azure Cache for Redis 以及排程其更新作業
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/05/2017
ms.author: yegu
ms.openlocfilehash: 69686cad20bc4ce70bff2a92a216c9430522c301
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278840"
---
# <a name="how-to-administer-azure-cache-for-redis"></a>如何管理 Azure Cache for Redis
本主題說明如何執行管理工作，例如為 Azure Cache for Redis 執行個體進行[重新啟動](#reboot)和[排程更新](#schedule-updates)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reboot"></a>重新啟動
[重新啟動] **** 刀鋒視窗可讓您重新啟動快取的一或多個節點。 這個重新啟動的能力可讓您測試應用程式在快取節點失敗時的恢復功能。

![重新啟動](./media/cache-administration/redis-cache-administration-reboot.png)

選取要重新啟動的節點，然後按一下 [重新啟動]****。

![重新啟動](./media/cache-administration/redis-cache-reboot.png)

如果您的進階快取已啟用叢集，您可以選取要重新啟動的快取分區。

![重新啟動](./media/cache-administration/redis-cache-reboot-cluster.png)

若要重新啟動快取的一或多個節點，選取所需的節點，然後按一下 [重新啟動] ****。 如果您的進階快取已啟用叢集，選取要重新啟動的分區，然後按一下 [重新啟動]****。 稍候幾分鐘之後，選取的節點會重新啟動，並在幾分鐘之後重新上線。

對於用戶端應用程式的影響，會根據您重新啟動的節點而有所不同。

* **主要** - 重新啟動主要節點時，Azure Cache for Redis 會容錯移轉至複本節點，並將其升級為主要節點。 在此容錯移轉期間，可能會有一小段時間無法連接快取。
* **從屬** - 重新啟動從屬節點時，通常不會對快取用戶端產生任何影響。
* **主要和從屬** - 重新啟動這兩個快取節點時，在主要節點恢復上線之前，將會遺失快取中的所有資料且無法連接快取。 如果您已設定[資料持續性](cache-how-to-premium-persistence.md)，則當快取恢復連線時，將會還原最近一次備份，但在此備份後發生的任何快取寫入將會遺失。
* **已啟用叢集的進階快取節點** - 當您重新啟動一或多個已啟用叢集的進階快取節點時，所選節點的行為與您重新啟動回應節點或非叢集快取的節點時相同。

## <a name="reboot-faq"></a>重新啟動常見問題集
* [若要測試我的應用程式，我應該重新啟動哪一個節點？](#which-node-should-i-reboot-to-test-my-application)
* [我可以重新啟動快取來清除用戶端連線嗎？](#can-i-reboot-the-cache-to-clear-client-connections)
* [如果我重新啟動，將會遺失快取中的資料嗎？](#will-i-lose-data-from-my-cache-if-i-do-a-reboot)
* [我可以使用 PowerShell、CLI 或其他管理工具重新啟動我的快取嗎？](#can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools)

### <a name="which-node-should-i-reboot-to-test-my-application"></a>若要測試我的應用程式，我應該重新啟動哪一個節點？
若要針對快取的主要節點失敗測試應用程式復原功能，重新啟動 **主要** 節點。 若要針對次要節點失敗測試應用程式復原功能，重新啟動 **從屬** 節點。 若要針對整體快取失敗測試應用程式復原功能，重新啟動 **這兩個** 節點。

### <a name="can-i-reboot-the-cache-to-clear-client-connections"></a>我可以重新啟動快取來清除用戶端連線嗎？
是，如果您重新啟動快取，即會清除所有用戶端連線。 若發生所有用戶端連線都已用盡的情況 (因為發生邏輯錯誤或用戶端應用程式中發生錯誤)，重新啟動非常實用。 每個定價層對於各種不同大小都有不同的 [用戶端連線限制](cache-configure.md#default-redis-server-configuration) ，一旦觸達這些限制之後，就無法再接受任何用戶端連線。 重新啟動快取提供一種方式來清除所有用戶端連線。

> [!IMPORTANT]
> 如果您重新啟動您的快取來清除用戶端連線，StackExchange.Redis 會在 Redis 節點回到線上後自動重新連線。 如果沒有解決潛在問題，用戶端連線仍可能被用盡。
> 
> 

### <a name="will-i-lose-data-from-my-cache-if-i-do-a-reboot"></a>如果我重新啟動，將會遺失快取中的資料嗎？
如果同時重新開機**主**節點和**從節點**，則緩存中的所有資料（如果使用啟用群集的高級緩存，則緩存中的所有資料）可能會丟失，但不能保證這一點。 如果您已設定[資料持續性](cache-how-to-premium-persistence.md)，則當快取恢復連線時，將會還原最近一次備份，但在此備份後發生的任何快取寫入將會遺失。

如果您只重新啟動這其中一個節點，通常不會遺失資料，但仍有可能發生。 例如，如果重新啟動主要節點且快取寫入正在進行中，則來自快取寫入的資料即會遺失。 如果您重新啟動一個節點，而另一個節點剛好同時因為失敗而當機，也有可能造成資料遺失。 如需深入了解資料遺失的可能原因，請參閱[我在 Redis 中的資料怎麼了？](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md)

### <a name="can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools"></a>我可以使用 PowerShell、CLI 或其他管理工具重新啟動我的快取嗎？
是，如需 PowerShell 指示，請參閱[重新啟動 Azure Cache for Redis](cache-how-to-manage-redis-cache-powershell.md#to-reboot-an-azure-cache-for-redis)。

## <a name="schedule-updates"></a>更新排程
"**計畫更新**"邊欄選項卡允許您為緩存實例指定維護視窗。 若指定了維護期間，即會在此期間進行任何 Redis 伺服器更新。 

> [!NOTE] 
> 維護期間僅適用於 Redis 伺服器更新，不適用於任何 Azure 更新，或是在裝載快取的 VM 上更新作業系統。
>

![更新排程](./media/cache-administration/redis-schedule-updates.png)

若要指定維護期間，請檢查所需的天數，並指定每一天的維護期間開始小時，然後按一下 [確定] ****。 請注意，維護期間時間是 UTC。 

更新的預設、最短維護期間是 5 小時。 這個值不可以從 Azure 入口網站設定，但您可以在 PowerShell 中使用 [New-AzRedisCacheScheduleEntry](/powershell/module/az.rediscache/new-azrediscachescheduleentry) Cmdlet 的 `MaintenanceWindow` 參數來設定。 如需詳細資訊，請參閱我可以使用 PowerShell、CLI 或其他管理工具管理排程更新嗎？

## <a name="schedule-updates-faq"></a>排程更新常見問題集
* [如果我未使用排程更新功能，更新會在何時發生？](#when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature)
* [在排程維護期間，會進行何種類型的更新？](#what-type-of-updates-are-made-during-the-scheduled-maintenance-window)
* [我可以使用 PowerShell、CLI 或其他管理工具管理排程更新嗎？](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)

### <a name="when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature"></a>如果我未使用排程更新功能，更新會在何時發生？
如果您未指定維護期間，隨時都可進行更新。

### <a name="what-type-of-updates-are-made-during-the-scheduled-maintenance-window"></a>在排程維護期間，會進行何種類型的更新？
在排程維護期間，只會進行 Redis 伺服器更新。 維護期間不適用於 Azure 更新或 VM 作業系統的更新。

### <a name="can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools"></a>我可以使用 PowerShell、CLI 或其他管理工具管理排程更新嗎？
是，您可以使用下列 PowerShell Cmdlet 管理排程更新：

* [Get-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/get-azrediscachepatchschedule)
* [New-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/new-azrediscachepatchschedule)
* [New-AzRedisCacheScheduleEntry](/powershell/module/az.rediscache/new-azrediscachescheduleentry)
* [Remove-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/remove-azrediscachepatchschedule)

## <a name="next-steps"></a>後續步驟
* 瀏覽更多 [Azure Cache for Redis 進階層](cache-premium-tier-intro.md)功能。


---
title: '從網路效能監控遷移至連線監視器 (預覽) '
titleSuffix: Azure Network Watcher
description: 瞭解如何從網路效能監控遷移至連線監視器 (預覽版) 。
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/20/2020
ms.author: vinigam
ms.openlocfilehash: 69dbb1dd4017c5acf9c195f5104741caee38c2b7
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88701469"
---
# <a name="migrate-to-connection-monitor-preview-from-network-performance-monitor"></a>從網路效能監控遷移至連線監視器 (預覽) 

您可以從網路效能監控將測試遷移至全新和改良的連線監視器， (Preview) 只要按一下就能進行，而不需要停機。 若要深入瞭解這些權益，您可以閱讀連線 [監視器 (預覽版) ](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview)

>[!NOTE]
> 只有來自服務連線監視器的測試可以遷移至連線監視器 (preview) 。
>

## <a name="key-points-to-note"></a>要注意的重點

* 內部部署代理程式和防火牆設定將會依原樣運作。 不需要變更。 Azure 虛擬機器上安裝的 Log Analytics 代理程式必須以網路監看員延伸模組取代
* 現有的測試將會對應至連線監視器 (Preview) > 測試群組-> 測試格式。 使用者可以按一下 [ *編輯* ] 以查看和修改新連線監視器的內容，並下載範本以變更連線監視器，並透過 Azure Resource Manager 提交。
* 代理程式會將資料傳送到 Log Analytics 工作區和計量。
* 監視資料
    * Log Analytics 中的資料：所有資料移轉都繼續位於 NetworkMonitoring 資料表中設定 NPM 的工作區中。 遷移後，資料會移至相同工作區中的 NetworkMonitoring 資料表和 ConnectionMonitor_CL 資料表。 從 NPM 中停用測試之後，資料只會儲存在 ConnectionMonitor_CL 資料表中
    * 以記錄為基礎的警示、儀表板和整合–您將必須根據新的資料表 ConnectionMonitor_CL 手動編輯查詢。 您也可以使用此連結，在計量中重新建立警示。 即將推出在遷移過程中自動將 NetworkMonitoring 資料表的記錄式警示遷移至以計量為基礎的警示的功能
    
## <a name="prerequisites"></a>先決條件

*   確定已在 Log Analytics 工作區的訂用帳戶和區域中啟用網路監看員
*   已安裝 Log analytics 代理程式的 Azure 虛擬機器必須啟用網路監看員延伸模組

## <a name="steps-to-migrate-tests-from-network-performance-monitor-to-connection-monitor-preview"></a>將測試從網路效能監控遷移至連線監視器 (Preview) 的步驟

1.  按一下 [連線監視器]，流覽至 [從 NPM 遷移測試] 以將測試遷移至連線監視器 (預覽) 

    ![顯示將測試從 NPM 遷移至連線監視器預覽的螢幕擷取畫面](./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png)
    
1.  選取您要遷移的訂用帳戶、工作區和 NPM 功能。 您目前只能從服務連線監視器遷移測試。  
1.  按一下 [匯入] 以遷移測試
1.  開始遷移後，會發生下列變更： 
    1. 建立新的連線監視器資源
        1. 每個區域和訂用帳戶會建立一個連線監視。 針對內部部署代理程式的測試，新的連線監視器名稱格式為 <workspaceName> _"on-premises"。針對使用 Azure 代理程式的測試，新的連線監視器名稱格式 <workspaceName> _<Azure_region_name>
        1. 監視資料現在會儲存在已啟用 NPM 的相同 Log Analytics 工作區中，位於名為 Connectionmonitor_CL table 的新資料表中。 
        1. 測試名稱會執行到測試組名。 將不會遷移測試描述。
        1. 來源和目的地端點是在建立的測試群組中建立及使用。 若為內部部署代理程式，則會以 <workspaceName> _"endpoint"_ 格式命名端點 <FQDN of on-premises machine> 。針對 Azure，如果遷移測試包含代理程式未執行，您將需要啟用代理程式，然後再次遷移。
        1. 目的地埠和探查間隔會移至測試設定，亦即 "TC"_ <testname> "和" tc "_ <testname> _" AppThresholds "。 根據埠值，會設定通訊協定。 成功閾值和其他選擇性屬性會保留空白。
    1. 未停用 NPM。 因此，遷移的測試會繼續將資料傳送至 NetworkMonitoring 資料表以及 ConnectionMonitor_CL 資料表。 此步驟可確保現有的記錄式警示和整合不會受到影響。 在遷移過程中，會自動將 NetworkMonitoring 資料表上以記錄為基礎的警示遷移至以計量為基礎的警示。
    1. 新建立的連線監視器將會顯示在連線監視器 (預覽版中) 
1.  遷移後，您需要手動停用 NPM 中的測試。 在這麼做之前，您會繼續向您收取相同的費用。 停用 NPM 時，請確定您已在 ConnectionMonitor_CL 資料表上重新建立警示或使用計量。 此外，也請確定任何外部整合（例如 Power BI 中的儀表板、Grafana、與 SIEM 系統的整合）都必須遷移至 ConnectionMonitor_CL 資料表


## <a name="next-steps"></a>後續步驟

* 瞭解 [如何從連線監視器遷移至連線監視器 (預覽版) ](migrate-to-connection-monitor-preview-from-connection-monitor.md)
* 瞭解 [如何使用 Azure 入口網站建立連線監視器 (預覽版) ](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview-create-using-portal)

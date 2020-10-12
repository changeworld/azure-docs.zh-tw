---
title: '從網路效能監控遷移至連線監視器 (預覽) '
titleSuffix: Azure Network Watcher
description: 瞭解如何從網路效能監控遷移至連線監視器 (預覽版) 。
services: network-watcher
documentationcenter: na
author: vinynigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/20/2020
ms.author: vinigam
ms.openlocfilehash: dcbb82c1315e6150ddcfadbb52b2976447329b87
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89441828"
---
# <a name="migrate-to-connection-monitor-preview-from-network-performance-monitor"></a>從網路效能監控遷移至連線監視器 (預覽) 

您可以從網路效能監控 (NPM) 將測試遷移至新的、已改善的連線監視器 (預覽) 只要按一下，就會有零停機時間。 若要深入瞭解優點，請參閱連線 [監視器 (預覽) ](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview)。

>[!NOTE]
> 只有來自服務連線監視器的測試可以遷移至連線監視器 (Preview) 。
>

## <a name="key-points-to-note"></a>要注意的重點

遷移有助於產生下列結果：

* 內部部署代理程式和防火牆設定的運作方式相同。 不需要任何變更。 安裝在 Azure 虛擬機器上的 Log Analytics 代理程式必須以網路監看員延伸模組取代。
* 現有的測試會對應到連接監視器 (Preview) > 測試群組 > 測試格式。 您可以選取 [ **編輯**] 來查看和修改新連線監視器的內容 (預覽) 、下載範本以進行變更，以及透過 Azure Resource Manager 提交範本。
* 代理程式會將資料傳送到 Log Analytics 工作區和計量。
* 資料監視：
   * **Log Analytics 中的資料**：在遷移之前，資料會保留在工作區中，而該工作區中的 NPM 是在 NetworkMonitoring 資料表中設定。 遷移之後，資料會移至 NetworkMonitoring 資料表，並在相同的工作區中 ConnectionMonitor_CL 資料表。 在 NPM 中停用測試之後，資料只會儲存在 ConnectionMonitor_CL 資料表中。
   * 以**記錄為基礎的警示、儀表板和**整合：您必須根據新的 ConnectionMonitor_CL 資料表，手動編輯查詢。 若要在計量中重新建立警示，請參閱 [使用連線監視器 (預覽) 的網路連線監視 ](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#metrics-in-azure-monitor)。
    
## <a name="prerequisites"></a>必要條件

* 確定您的訂用帳戶中已啟用網路監看員，以及 Log Analytics 工作區的區域。
* 已安裝 Log Analytics 代理程式的 Azure 虛擬機器必須使用網路監看員擴充功能來啟用。

## <a name="migrate-the-tests"></a>遷移測試

若要將網路效能監控中的測試遷移至連線監視器 (Preview) ，請執行下列動作：

1. 在 [網路監看員] 中，選取 [連線 **監視器**]，然後選取 [ **從 NPM 遷移測試** ] 索引標籤。 

    ![螢幕擷取畫面，顯示網路監看員中的 [從 NPM 遷移測試] 窗格 |連接監視器 (預覽) 。](./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png)
    
1. 在下拉式清單中，選取您的訂用帳戶和工作區，然後選取您想要遷移的 NPM 功能。 目前，您只能從服務連線監視器遷移測試。  
1. 選取 [匯 **入** ] 以遷移測試。

開始遷移之後，會進行下列變更： 
* 建立新的連線監視器資源。
   * 每個區域和訂用帳戶會建立一個連線監視。 針對內部部署代理程式的測試，新的連接監視器名稱會格式化為 `<workspaceName>_"on-premises"` 。 針對使用 Azure 代理程式的測試，新的連接監視器名稱會格式化為 `<workspaceName>_<Azure_region_name>` 。
   * 監視資料現在會儲存在已啟用 NPM 的相同 Log Analytics 工作區中，位於名為 Connectionmonitor_CL 的新資料表中。 
   * 測試名稱會以測試組名的形式向前執行。 未遷移測試描述。
   * 來源和目的地端點是在新的測試群組中建立及使用。 若為內部部署代理程式，則會將端點格式化為 `<workspaceName>_"endpoint"_<FQDN of on-premises machine>` 。 針對 Azure，如果遷移測試包含未執行的代理程式，您必須啟用代理程式，然後再次遷移。
   * 目的地埠和探查間隔會移至名為*TC_ \<testname> *的測試設定，並*TC_ \<testname> _AppThresholds*。 此通訊協定是根據埠值設定。 成功閾值和其他選擇性屬性會保留空白。
* NPM 不會停用，因此遷移的測試可以繼續將資料傳送至 NetworkMonitoring 和 ConnectionMonitor_CL 資料表。 這種方法可確保現有以記錄為基礎的警示和整合不會受到影響。
* 新建立的連線監視器會顯示在連線監視器 (Preview) 中。

遷移之後，請務必：
* 手動停用 NPM 中的測試。 在您這麼做之前，將會繼續向您收取費用。 
* 當您停用 NPM 時，請在 ConnectionMonitor_CL 資料表上重新建立警示，或使用計量。 
* 將任何外部整合遷移至 ConnectionMonitor_CL 資料表。 外部整合的範例包括 Power BI 和 Grafana 的儀表板，以及與安全性資訊和事件管理 (SIEM) 系統的整合。


## <a name="next-steps"></a>後續步驟

若要深入瞭解連接監視器 (Preview) ，請參閱：
* [從連線監視器遷移至連線監視器 (預覽版) ](migrate-to-connection-monitor-preview-from-connection-monitor.md)
* [使用 Azure 入口網站建立連線監視器 (預覽) ](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview-create-using-portal)
